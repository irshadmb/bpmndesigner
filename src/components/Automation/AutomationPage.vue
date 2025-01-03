<template>
  <div class="automation-container">
    <button @click="saveFlow" class="save-button">Save Flow</button>
    <SideBar 
      class="sidebar" 
      @node-drag="handleNodeDrag"
      :available-nodes="availableNodes" 
    />
    <div class="flow-container">
      <VueFlow 
        v-model="elements"
        :default-zoom="0"
        :default-viewport="{ x: 0, y: 0, zoom: 1 }"
        @drop="onDrop"
        @dragover.prevent
        @connect="onConnect"
        :nodes="nodes"
        :edges="edges"
        :node-types="nodeTypes"
        :fit-view-on-init="shouldFitView"
        @nodeDataUpdate="handleNodeDataUpdate"
        @deleteNode="onDeleteNode"
      >
        <Background :size="1" :gap="10" pattern-color="#BDBDBD" />
        <Controls />
        <MiniMap />
      </VueFlow>
    </div>
  </div>
</template>

<script>
import { VueFlow, useVueFlow } from '@vue-flow/core'
import { Background } from '@vue-flow/background'
import { MiniMap } from '@vue-flow/minimap'
import { Controls } from '@vue-flow/controls'
import { ref, computed } from 'vue'
import SideBar from './sidebar/SideBar.vue'
import GetTicketNode from './nodes/GetTicketNode.vue'
import WebhookNode from './nodes/WebhookNode.vue'
import StartNode from './nodes/StartNode.vue' 
import EndNode from './nodes/EndNode.vue';
import GatewayNode from './nodes/GatewayNode.vue'; 

export default {
  name: 'AutomationPage',
  components: {
    VueFlow,
    Background,
    Controls,
    MiniMap,
    SideBar
  },
  setup() {
    const elements = ref([])
    const { project } = useVueFlow()
    const shouldFitView = ref(false)
    
    const nodeTypes = {
      getticket: GetTicketNode,
      webhook: WebhookNode,
      start: StartNode,
      end: EndNode,
      gateway: GatewayNode 
    };

    const nodes = computed(() => elements.value.filter(el => !el.source))
    const edges = computed(() => elements.value.filter(el => el.source))

    const availableNodes = [
  { type: 'start', label: 'Start Node' },
  { type: 'end', label: 'End Node' },
  { type: 'gateway', label: 'Gateway Node' }, 
  { type: 'getticket', label: 'GetTicket Node' },
  { type: 'webhook', label: 'Webhook Node' },
  { type: 'event', label: 'Event Node' },
  { type: 'pool', label: 'Pool Node' }
];

const onDeleteNode = (nodeId) => {
    elements.value = elements.value.filter(el => el.id !== nodeId);
    // Also remove any connected edges
    elements.value = elements.value.filter(el => 
      el.type === 'edge' ? 
      (el.source !== nodeId && el.target !== nodeId) : 
      true
    );
  };

    return {
      elements,
      nodeTypes,
      nodes,
      edges,
      availableNodes,
      project,
      shouldFitView,
      onDeleteNode
    }
  },
  methods: {
   
    handleNodeDataUpdate({ id, field, value }) {
      const nodeIndex = this.elements.findIndex(el => el.id === id);
      if (nodeIndex !== -1) {
        const updatedElements = [...this.elements];
        updatedElements[nodeIndex] = {
          ...updatedElements[nodeIndex],
          data: {
            ...updatedElements[nodeIndex].data,
            [field]: value
          }
        };
        const connectedEdges = this.edges.filter(edge => edge.source === id);
        connectedEdges.forEach(edge => {
          const targetIndex = updatedElements.findIndex(el => el.id === edge.target);
          if (targetIndex !== -1) {
            updatedElements[targetIndex] = {
              ...updatedElements[targetIndex],
              data: {
                ...updatedElements[targetIndex].data,
                [field]: value
              }
            };
          }
        });
        this.elements = updatedElements;
      }
    },

    onDrop(event) {
      const nodeType = event.dataTransfer.getData('application/nodeType')
      const { x, y } = this.getDropPosition(event)
      
      const newNode = {
        id: `${nodeType}-${Date.now()}`,
        type: nodeType,
        position: { x, y },
        data: {
          label: `${nodeType.charAt(0).toUpperCase() + nodeType.slice(1)} Node`,
          input: '',
          model: 'gpt-4o-mini',
          apiKey: '',
          temperature: 0.14
        },
        draggable: true,
        connectable: true,
      }
      
      this.elements.push(newNode)
    },

    getDropPosition(event) {
      const bounds = event.target.getBoundingClientRect()
      const position = {
        x: event.clientX - bounds.left,
        y: event.clientY - bounds.top,
      }
      return this.project({ x: position.x, y: position.y })
    },

    onConnect({ source, target }) {
      const edgeExists = this.edges.some(edge => 
        edge.source === source && edge.target === target
      );
      
      if (!edgeExists) {
        const sourceNode = this.nodes.find(node => node.id === source);
        const newEdge = {
          id: `edge-${source}-${target}`,
          source,
          target,
          type: 'smoothstep',
        }
        
        const updatedElements = [...this.elements, newEdge];
        
        if (sourceNode && sourceNode.data) {
          const targetIndex = updatedElements.findIndex(el => el.id === target);
          if (targetIndex !== -1) {
            updatedElements[targetIndex] = {
              ...updatedElements[targetIndex],
              data: {
                ...updatedElements[targetIndex].data,
                ...sourceNode.data
              }
            };
          }
        }
        
        this.elements = updatedElements;
      }
    },

    handleNodeDrag(nodeType) {
      console.log('Node dragged:', nodeType)
    },

    saveFlow() {
      const flowData = {
        nodes: this.nodes,
        edges: this.edges,
        elements: this.elements
      };
      console.log('Flow data:', JSON.stringify(flowData));
    }
  }
}
</script>

<style>
.save-button {
  position: absolute;
  top: 10%;
  right: 20px;
  padding: 8px 16px;
  background-color: #4fdb82;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  z-index: 1000;
}

.save-button:hover {
  background-color: #2ccc66;
}

.automation-container {
  display: flex;
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

.sidebar {
  width: 250px;
  border-right: 1px solid #ccc;
  background: #f5f5f5;
}

.flow-container {
  flex-grow: 1;
  height: 100%;
  position: relative;
}

:root {
  --bg-color: #f8fafc;
}

.vue-flow {
  background-color: var(--bg-color);
}

.vue-flow__node {
  background: white;
  border-radius: 16px;
  color: #1a1a1a;
  font-family: 'Inter', system-ui, sans-serif;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  border: 1px solid #f0f0f0;
}

.vue-flow__handle {
  width: 12px;
  height: 12px;
  border-radius: 50%;
  transition: transform 0.2s;
}

.vue-flow__handle:hover {
  transform: scale(1.2);
}

.vue-flow__handle-left {
  background: #6366f1;
  border: 2px solid #4f46e5;
}

.vue-flow__handle-right {
  background: #ec4899;
  border: 2px solid #db2777;
}

.vue-flow__edge-path {
  stroke: #6366f1;
  stroke-width: 2;
}

.vue-flow__edge-text {
  font-size: 12px;
}

.vue-flow__node-default {
  padding: 12px;
  background: white;
  border: 2px solid #f3f4f6;
  border-radius: 12px;
}
</style>
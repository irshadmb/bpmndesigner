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
      >
        <Background :size="1" :gap="10" pattern-color="#BDBDBD" />
        <Controls />
        <MiniMap />
      </VueFlow>
    </div>
  </div>
</template>

<script>
import { VueFlow } from '@vue-flow/core'
import { Background, Controls } from '@vue-flow/background'
import { MiniMap } from '@vue-flow/minimap'
import { ref, computed } from 'vue'
import { useVueFlow } from '@vue-flow/core'
import InputNode from './nodes/InputNode.vue'
import OutputNode from './nodes/OutputNode.vue'
import SideBar from './sidebar/SideBar.vue'
import TaskNode from './nodes/TaskNode.vue'

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
      input: InputNode,
      output: OutputNode,
      task: TaskNode
    }

    const nodes = computed(() => elements.value.filter(el => !el.source))
    const edges = computed(() => elements.value.filter(el => el.source))

    const availableNodes = [
      { type: 'input', label: 'Input Node' },
      { type: 'output', label: 'Output Node' },
      { type: 'task', label: 'Task Node' },
      { type: 'gateway', label: 'Gateway Node' },
      { type: 'event', label: 'Event Node' },
      { type: 'pool', label: 'Pool Node' }
    ]

    return {
      elements,
      nodeTypes,
      nodes,
      edges,
      availableNodes,
      project,
      shouldFitView
    }
  },
  methods: {
    handleNodeDataUpdate({ id, field, value }) {
      // Find the node that was updated
      const nodeIndex = this.elements.findIndex(el => el.id === id);
      if (nodeIndex !== -1) {
        // Create a new copy of elements array
        const updatedElements = [...this.elements];
        
        // Update the node's data
        updatedElements[nodeIndex] = {
          ...updatedElements[nodeIndex],
          data: {
            ...updatedElements[nodeIndex].data,
            [field]: value
          }
        };
        
        // Find all edges where this node is the source
        const connectedEdges = this.edges.filter(edge => edge.source === id);
        
        // Update all connected target nodes
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
        
        // Update the elements array
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
      // Check if an edge already exists between these nodes
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
        
        // Create a new copy of elements array with the new edge
        const updatedElements = [...this.elements, newEdge];
        
        // If source node has data, update target node immediately
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

.vue-flow__node {
  border-radius: 15px;
}
</style>
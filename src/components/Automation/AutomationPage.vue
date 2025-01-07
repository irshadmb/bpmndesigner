<!-- AutomationPage.vue -->
<template>
  <div class="automation-container">
    <button @click="saveFlow" class="save-button">Save Flow</button>
    <SideBar 
      class="sidebar" 
      @node-drag="handleNodeDrag"
    />
    <div class="flow-container">
      <div v-if="errorMessage" class="error-message">
        <span>{{ errorMessage }}</span>
        <button class="close-error" @click="clearError">×</button>
      </div>
      <VueFlow 
        v-model="elements"
        :default-zoom="0"
        :default-viewport="{ x: 0, y: 0, zoom: 0 }"
        @node-click="onSelectNode"  
        @drop="onDrop"
        @dragover.prevent
        @connect="onConnect"
        :nodes="nodes"
        :edges="edges"
        :node-types="nodeTypes"
        :fit-view-on-init="shouldFitView"
        @nodeDataUpdate="handleNodeDataUpdate"
        @deleteNode="onDeleteNode"
        @keydown.delete="onKeyDown"
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
import { ref, computed, markRaw } from 'vue'
import SideBar from './sidebar/SideBar.vue'
import GetTicketNode from './nodes/GetTicketNode.vue'
import WebhookNode from './nodes/WebhookNode.vue'
import StartNode from './nodes/StartNode.vue' 
import EndNode from './nodes/EndNode.vue'
import GatewayNode from './nodes/GatewayNode.vue'
import PDFNode from './nodes/PDFNode.vue';
import SQLNode from './nodes/SQLNode.vue';
import EmailNode from './nodes/EmailNode.vue';

// Node type configurations
const NODE_CONFIGS = {
  getticket: {
    label: 'Get Ticket',
    defaultData: {
      ticketId: '',
      source: 'sapphire',
      fields: [],
      useRequestId: false, 
      filters: {
        status: [],
        priority: [],
        tags: []
      },
      transformation: {
        enabled: false,
        script: ''
      }
    }
  },
  webhook: {
    label: 'Webhook',
    defaultData: {
      url: '',
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: '',
      timeout: 5000,
      retryConfig: {
        maxRetries: 3,
        retryDelay: 1000
      },
      authentication: {
        type: 'none',
        credentials: {}
      }
    }
  },
  start: {
    label: 'Start',
    defaultData: {
      triggerType: 'manual',
      schedule: null,
      inputParameters: [],
      description: 'Flow starting point',
      validation: {
        enabled: false,
        rules: []
      }
    }
  },
  end: {
    label: 'End',
    defaultData: {
      status: 'success',
      message: '',
      outputMapping: {},
      cleanup: {
        enabled: false,
        actions: []
      }
    }
  },
  gateway: {
    label: 'Gateway',
    defaultData: {
      condition: '',
      defaultPath: 'true',
      paths: ['true', 'false'],
      evaluation: {
        mode: 'sync',
        timeout: 3000
      },
      errorHandling: {
        fallbackPath: 'false',
        retryEnabled: false
      }
    }
  },
  pdf: {
    label: 'PDF Generator',
    defaultData: {
      templateName: '',
      attachmentName: '',
      template: '',
      metadata: {
        format: 'pdf',
        version: '1.0'
      }
    }
  },
  sql: {
    label: 'SQL Query',
    defaultData: {
      templateName: '',
      sqlQuery: '',
      metadata: {
        type: 'sql',
        version: '1.0'
      }
    }
  },
  email: {
    label: 'Email',
    defaultData: {
      templateName: '',
      emailSubject: '',
      emailTo: '',
      emailCC: '',
      emailBody: '',
      metadata: {
        type: 'email',
        version: '1.0'
      }
    }
  }
};

export default {
  name: 'AutomationPage',
  components: {
    VueFlow,
    Background,
    Controls,
    MiniMap,
    SideBar
  },
  // In AutomationPage.vue, modify the setup() function:
setup() {
    const elements = ref([])
    const { project } = useVueFlow()
    const shouldFitView = ref(false)
    const selectedNode = ref(null)
    const errorMessage = ref('') // Add this line
    
    const clearError = () => {
      errorMessage.value = ''
    }
    
    // Define handleDirectNodeUpdate here in setup
    const handleDirectNodeUpdate = (payload) => {
      const nodeIndex = elements.value.findIndex(el => el.id === payload.id);
      if (nodeIndex === -1) return;

      // Create updated node with new data
      const updatedNode = {
        ...elements.value[nodeIndex],
        data: {
          ...elements.value[nodeIndex].data,
          [payload.field]: payload.value,
          updatedAt: new Date().toISOString()
        }
      };

      // Update elements array
      elements.value = [
        ...elements.value.slice(0, nodeIndex),
        updatedNode,
        ...elements.value.slice(nodeIndex + 1)
      ];
    };
    
    const nodeTypes = {
      getticket: markRaw({
        ...GetTicketNode,
        props: ['id', 'data'],
        emits: ['update:data', 'nodeDataUpdate'],
        setup(props, context) {
          const originalSetup = GetTicketNode.setup(props, {
            ...context,
            emit: (event, payload) => {
              context.emit(event, payload);
              if (event === 'nodeDataUpdate') {
                // Now handleDirectNodeUpdate is in scope
                handleDirectNodeUpdate(payload);
              }
            },
          });
          return originalSetup;
        },
      }),
      webhook: markRaw({
        ...WebhookNode,
        props: ['id', 'data'],
        emits: ['update:data', 'nodeDataUpdate'],
        setup(props, context) {
          const originalSetup = WebhookNode.setup(props, {
            ...context,
            emit: (event, payload) => {
              context.emit(event, payload);
              if (event === 'nodeDataUpdate') {
                // Now handleDirectNodeUpdate is in scope
                handleDirectNodeUpdate(payload);
              }
            },
          });
          return originalSetup;
        },
      }),
      start: markRaw(StartNode),
      end: markRaw(EndNode),
      gateway: markRaw({
        ...GatewayNode,
        props: ['id', 'data'],
        emits: ['update:data', 'nodeDataUpdate'],
        setup(props, context) {
          const originalSetup = GatewayNode.setup(props, {
            ...context,
            emit: (event, payload) => {
              context.emit(event, payload);
              if (event === 'nodeDataUpdate') {
                // Now handleDirectNodeUpdate is in scope
                handleDirectNodeUpdate(payload);
              }
            },
          });
          return originalSetup;
        },
      }),
      pdf: markRaw({
    ...PDFNode,
    props: ['id', 'data'],
    emits: ['update:data', 'nodeDataUpdate'],
    setup(props, context) {
      const originalSetup = PDFNode.setup(props, {
        ...context,
        emit: (event, payload) => {
          context.emit(event, payload);
          if (event === 'nodeDataUpdate') {
            handleDirectNodeUpdate(payload);
          }
        },
      });
      return originalSetup;
    },
  }),
  sql: markRaw({
    ...SQLNode,
    props: ['id', 'data'],
    emits: ['update:data', 'nodeDataUpdate'],
    setup(props, context) {
      const originalSetup = SQLNode.setup(props, {
        ...context,
        emit: (event, payload) => {
          context.emit(event, payload);
          if (event === 'nodeDataUpdate') {
            handleDirectNodeUpdate(payload);
          }
        },
      });
      return originalSetup;
    },
  }),
  email: markRaw({
    ...EmailNode,
    props: ['id', 'data'],
    emits: ['update:data', 'nodeDataUpdate'],
    setup(props, context) {
      const originalSetup = EmailNode.setup(props, {
        ...context,
        emit: (event, payload) => {
          context.emit(event, payload);
          if (event === 'nodeDataUpdate') {
            handleDirectNodeUpdate(payload);
          }
        },
      });
      return originalSetup;
    },
  })
    }

    const nodes = computed(() => elements.value.filter(el => !el.source))
    const edges = computed(() => elements.value.filter(el => el.source))

    return {
      elements,
      nodeTypes,
      nodes,
      edges,
      project,
      shouldFitView,
      selectedNode,
      handleDirectNodeUpdate,
      clearError,
      errorMessage
    }
},
  methods: {
    validateNodePlacement(nodeType) {
      // Validate node placement rules
      switch (nodeType) {
        case 'start':
          if (this.nodes.some(node => node.type === 'start')) {
            this.errorMessage = 'Only one start node is allowed in the flow';
            throw new Error('Only one start node is allowed in the flow');
          }
          break;
        case 'end':
          // Allow multiple end nodes
          break;
        case 'gateway':{
          const gatewayNodes = this.nodes.filter(node => node.type === 'gateway');
          if (gatewayNodes.length > 0) {
            // Check if existing gateways have valid connections
            for (const gateway of gatewayNodes) {
              const sourceConnections = this.edges.filter(edge => edge.source === gateway.id);
              const targetConnections = this.edges.filter(edge => edge.target === gateway.id);
              
              if (sourceConnections.length > 3) {
                throw new Error('Gateway nodes can have maximum 3 source connections');
              }
              if (targetConnections.length > 1) {
                throw new Error('Gateway nodes can have only 1 target connection');
              }
            }
          }
        }
        break;
        default:
          // General validation for other nodes
          break;
      }
      return true;
    },

    onDrop(event) {
      const nodeType = event.dataTransfer.getData('application/nodeType');
      const { x, y } = this.getDropPosition(event);
      
      try {
        // Validate node placement
        this.validateNodePlacement(nodeType);
        
        // Get node configuration
        const nodeConfig = NODE_CONFIGS[nodeType];
        if (!nodeConfig) {
          throw new Error(`Invalid node type: ${nodeType}`);
        }
        
        const newNode = {
          id: `${nodeType}-${Date.now()}`,
          type: nodeType,
          position: { x, y },
          data: {
            label: nodeConfig.label,
            ...nodeConfig.defaultData,
            // Common fields
            description: '',
            isEnabled: true,
            createdAt: new Date().toISOString(),
            updatedAt: new Date().toISOString(),
            metadata: {
              author: 'system',
              version: '1.0'
            }
          },
          draggable: true,
          connectable: true,
          // Style configurations
          style: {
            border: '1px solid #ddd',
            padding: '10px',
            borderRadius: '5px'
          }
        };
        
        this.elements.push(newNode);
        this.selectedNode = newNode;
        
        return newNode;
      } catch (error) {
        console.error('Error creating node:', error);
        // Handle error (could show user notification)
        return null;
      }
    },

    getDropPosition(event) {
      const bounds = event.target.getBoundingClientRect()
      const position = {
        x: event.clientX - bounds.left,
        y: event.clientY - bounds.top,
      }
      return this.project({ x: position.x, y: position.y })
    },

    onConnect({ source, target, sourceHandle, targetHandle }) {
  // Validate connection
  const sourceNode = this.nodes.find(node => node.id === source);
  const targetNode = this.nodes.find(node => node.id === target);
  
  if (!sourceNode || !targetNode) {
    console.error('Invalid connection: nodes not found');
    return;
  }
  
  // Special handling for Gateway nodes
  if (sourceNode.type === 'gateway') {
    // Check which handle was used (top, right, or bottom)
    const existingSourceConnections = this.edges.filter(edge => 
      edge.source === source && edge.sourceHandle === sourceHandle
    );

    // Prevent multiple connections from the same handle
    if (existingSourceConnections.length > 0) {
      console.error('This gateway output is already connected');
      return;
    }

    // Validate total number of output connections
    const totalSourceConnections = this.edges.filter(edge => edge.source === source);
    if (totalSourceConnections.length >= 3) {
      console.error('Gateway node can have maximum 3 output connections');
      return;
    }
  }
  
  // Handle target connections for Gateway
  if (targetNode.type === 'gateway') {
    const existingTargetConnections = this.edges.filter(edge => edge.target === target);
    if (existingTargetConnections.length >= 1) {
      console.error('Gateway node can have only 1 input connection');
      return;
    }
  }
  
  // Check if connection already exists
  const edgeExists = this.edges.some(edge => 
    edge.source === source && 
    edge.target === target &&
    edge.sourceHandle === sourceHandle &&
    edge.targetHandle === targetHandle
  );
  
  if (edgeExists) {
    console.warn('Connection already exists');
    return;
  }
  
  // Create new edge with metadata and handle information
  const newEdge = {
    id: `edge-${source}-${sourceHandle}-${target}-${Date.now()}`,
    source,
    target,
    sourceHandle, // Include source handle ID
    targetHandle, // Include target handle ID
    type: 'smoothstep',
    data: {
      label: '',
      condition: '',
      priority: 1,
      metadata: {
        createdAt: new Date().toISOString(),
        updatedAt: new Date().toISOString()
      }
    },
    style: {
      stroke: '#6366f1',
      strokeWidth: 4
    }
  };
  
  // Add specific styling for gateway connections
  if (sourceNode.type === 'gateway') {
    newEdge.style = {
      ...newEdge.style,
      strokeDasharray: '5 5', // Makes the line dashed for gateway connections
      animated: true // Optional: adds animation to the line
    };
    
    // Add condition based on which handle was used
    switch (sourceHandle) {
      case 'source-top':
        newEdge.data.condition = 'condition1';
        newEdge.style.stroke = '#22c55e'; // Green for top
        break;
      case 'source-right':
        newEdge.data.condition = 'condition2';
        newEdge.style.stroke = '#3b82f6'; // Blue for right
        break;
      case 'source-bottom':
        newEdge.data.condition = 'condition3';
        newEdge.style.stroke = '#ef4444'; // Red for bottom
        break;
    }
  }
  
  // Update elements with new edge
  this.elements = [...this.elements, newEdge];
},

    onDeleteNode(nodeId) {
      // Find the node and its connected edges
      const nodeToDelete = this.nodes.find(node => node.id === nodeId);
      if (!nodeToDelete) return;

      // Remove node and connected edges
      this.elements = this.elements.filter(el => {
        if (el.type === 'edge') {
          return el.source !== nodeId && el.target !== nodeId;
        }
        return el.id !== nodeId;
      });

      // Clear selection if deleted node was selected
      if (this.selectedNode?.id === nodeId) {
        this.selectedNode = null;
      }

      // Validate flow integrity after deletion
      this.validateFlowIntegrity();
    },

    validateFlowIntegrity() {
      // Check if flow still has a start node
      const hasStart = this.nodes.some(node => node.type === 'start');
      if (!hasStart) {
        this.errorMessage = 'Flow has no start node';
      }

      // Check for orphaned nodes
      const orphanedNodes = this.nodes.filter(node => {
        if (node.type === 'start') return false;
        return !this.edges.some(edge => edge.target === node.id);
      });

      if (orphanedNodes.length > 0) {
        this.errorMessage = 'Flow has orphaned nodes';
        console.warn('Flow has orphaned nodes:', orphanedNodes);
      }
    },

    onKeyDown(event) {
      if (event.key === 'Delete' && this.selectedNode) {
        this.onDeleteNode(this.selectedNode.id);
      }
    },
    
    onSelectNode({ node }) {
      this.selectedNode = this.elements.find(el => el.id === node.id);
    },

    handleNodeDataUpdate({ id, field, value }) {
      const nodeIndex = this.elements.findIndex(el => el.id === id);
      if (nodeIndex === -1) return;

      // Create updated node with new data
      const updatedNode = {
        ...this.elements[nodeIndex],
        data: {
          ...this.elements[nodeIndex].data,
          [field]: value,
          updatedAt: new Date().toISOString()
        }
      };

      // Update elements array
      const updatedElements = [...this.elements];
      updatedElements[nodeIndex] = updatedNode;

      // Update connected nodes if needed
      const connectedEdges = this.edges.filter(edge => edge.source === id);
      connectedEdges.forEach(edge => {
        const targetIndex = updatedElements.findIndex(el => el.id === edge.target);
        if (targetIndex !== -1) {
          updatedElements[targetIndex] = {
            ...updatedElements[targetIndex],
            data: {
              ...updatedElements[targetIndex].data,
              // Update only specific inherited fields
              ...(field in updatedElements[targetIndex].data.inheritedConfig ? {
                [field]: value
              } : {})
            }
          };
        }
      });

      this.elements = updatedElements;
    },

    handleNodeDrag(nodeType) {
      console.log('Node dragged:', nodeType);
    },

    saveFlow() {
      try {
        if (!this.validateFlow()) {
          throw new Error('Flow validation failed');
        }

        // Create a deep copy of nodes to avoid reference issues
        const processedNodes = this.nodes.map(node => {

          
          
          // Process based on node type
          switch (node.type) {
            case 'webhook':
              return {
                ...node,
                data: {
                  ...node.data,
                  url: node.data.url || '',
                  method: node.data.method || 'POST',
                  headers: node.data.headers || {},
                  body: node.data.body || '',
                  timeout: node.data.timeout || 5000
                }
              };

              case 'getticket':
                return {
                  ...node,
                  data: {
                    ...node.data,
                    useRequestId: node.data.useRequestId || false, // Default to false if not provided
                    ticketId: node.data.ticketId || '', // Default to empty string if not provided                  
                    source: node.data.source || 'sapphire', // Default to 'sapphire' if not provided
                    fields: node.data.fields || [], // Default to empty array if not provided
                    filters: node.data.filters || { // Default to empty filters if not provided
                      status: [],
                      priority: [],
                      tags: []
                    }
                  }
            };
            case 'gateway':
              return {
                ...node,
                data: {
                  ...node.data,
                  condition: node.data.condition || '',
                  defaultPath: node.data.defaultPath || 'true',
                  paths: node.data.paths || ['true', 'false']
                }
              };

            case 'start':
              return {
                ...node,
                data: {
                  ...node.data,
                  triggerType: node.data.triggerType || 'manual',
                  schedule: node.data.schedule || null,
                  inputParameters: node.data.inputParameters || []
                }
              };

            case 'end':
              return {
                ...node,
                data: {
                  ...node.data,
                  status: node.data.status || 'success',
                  message: node.data.message || '',
                  outputMapping: node.data.outputMapping || {}
                }
              };

            default:
              return node;
          }
        });

        // Process edges to include any conditions or metadata
        const processedEdges = this.edges.map(edge => ({
          ...edge,
          data: {
            ...edge.data,
            condition: edge.data?.condition || '',
            priority: edge.data?.priority || 1
          }
        }));

        const flowData = {
      nodes: processedNodes,
      edges: processedEdges
    };

    // Log the simplified flow data
    console.log('Simplified flow data:', JSON.stringify(flowData, null, 2));
    return flowData;

      } catch (error) {
        console.error('Error saving flow:', error);
        // Handle error (could show user notification)
        return null;
      }
    },

    // Optional: Add method to save to backend
    async saveFlowToBackend(flowData) {
      try {
        const response = await fetch('/api/flows', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(flowData)
        });

        if (!response.ok) {
          throw new Error('Failed to save flow');
        }

        const result = await response.json();
        console.log('Flow saved successfully:', result);
        return result;
      } catch (error) {
        console.error('Error saving flow to backend:', error);
        throw error;
      }
    },

    // Add method to validate node data before saving
    validateNodeData(node) {
      switch (node.type) {
        case 'webhook':
          if (!node.data.url) {
            throw new Error(`Webhook node ${node.id} missing URL`);
          }
          break;
        case 'getticket':
          if (!node.data.source) {
            throw new Error(`GetTicket node ${node.id} missing source`);
          }
          break;
        // Add validation for other node types
      }
      return true;
    },

    validateFlow() {
      // Basic flow validation
      const hasStart = this.nodes.some(node => node.type === 'start');
      const hasEnd = this.nodes.some(node => node.type === 'end');
      
      if (!hasStart || !hasEnd) {        
        this.errorMessage = 'Flow must have at least one start and one end node';
        console.error('Flow must have at least one start and one end node');
        return false;
      }

      // Validate node connections
      const orphanedNodes = this.nodes.filter(node => {
        if (node.type === 'start') return false;
        return !this.edges.some(edge => edge.target === node.id);
      });

      if (orphanedNodes.length > 0) {
        this.errorMessage = 'Flow has orphaned nodes';
        console.error('Flow contains orphaned nodes');
        return false;
      }

      return true;
    }
  }
}
</script>

<style>
.automation-container {
  display: flex;
  width: 100%;
  height: 100vh;
  overflow: hidden;
}

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
  transition: background-color 0.2s ease;
}

.save-button:hover {
  background-color: #2ccc66;
}

.save-button:active {
  transform: translateY(1px);
}

.sidebar {
  width: 250px;
  border-right: 1px solid #ccc;
  background: #f5f5f5;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
}

.flow-container {
  flex-grow: 1;
  height: 100%;
  position: relative;
}

:root {
  --bg-color: #f8fafc;
  --node-border-radius: 8px;
  --node-box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.vue-flow {
  background-color: var(--bg-color);
}

.vue-flow__node {
  background: white;
  border-radius: var(--node-border-radius);
  color: #1a1a1a;
  font-family: 'Inter', system-ui, sans-serif;
  box-shadow: var(--node-box-shadow);
  border: 1px solid #f0f0f0;
  transition: all 0.2s ease;
}

.error-message {
  position: fixed;
  top: 20px;
  right: 20px;
  background-color: #fee2e2;
  border: 1px solid #ef4444;
  color: #dc2626;
  padding: 12px 20px;
  border-radius: 8px;
  z-index: 1000;
  display: flex;
  align-items: center;
  gap: 12px;
  animation: slideIn 0.3s ease-out;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

.close-error {
  background: none;
  border: none;
  color: #dc2626;
  font-size: 20px;
  cursor: pointer;
  padding: 0 4px;
}

.close-error:hover {
  color: #b91c1c;
}
</style>

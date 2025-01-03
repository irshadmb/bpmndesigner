<template>
    <NodeToolbar :is-visible="data.toolbarVisible" :position="data.toolbarPosition">
      <button @click="deleteNode"><i class="fas fa-trash"></i> Delete</button>
      <button @click="copyNode"><i class="fas fa-copy"></i> Copy</button>
    </NodeToolbar>
    <div class="start-node">
      <div class="node-circle">
        <span class="node-icon">🚀</span>
      </div>
      <Handle type="source" position="right" id="right" class="handle-right" />
    </div>
  </template>
  
  <script>
  import { Handle } from '@vue-flow/core';
  import { NodeToolbar } from '@vue-flow/node-toolbar';
  
  export default {
    name: 'StartNode',
    components: { Handle, NodeToolbar },
    props: {
      data: {
        type: Object,
        required: true,
      },
    },
    methods: {
      deleteNode() {
        this.$emit('deleteNode', this.data.id);
      },
      copyNode() {
        this.$emit('copyNode', this.data.id);
      },
    },
  };
  </script>
  
  <style scoped>
  .start-node {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 80px;
    height: 80px;
    background: linear-gradient(135deg, #4caf50, #66bb6a);
    border-radius: 50%;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    position: relative;
  }
  
  .node-circle {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    height: 100%;
  }
  
  .node-icon {
    font-size: 1.5em;
  }
  
  .handle-right {
    width: 12px;
    height: 12px;
    background: #4caf50;
    border-radius: 50%;
    border: 2px solid #388e3c;
    position: absolute;
    right: -6px;
    top: 50%;
    transform: translateY(-50%);
    transition: transform 0.2s;
  }
  
  .handle-right:hover {
    transform: translateY(-50%) scale(1.2);
  }

  .vue-flow__node {    
    min-width: 1px;
  }
  </style>
<template>
    <NodeToolbar :is-visible="data.toolbarVisible" :position="data.toolbarPosition">
      <button @click="deleteNode"><i class="fas fa-trash"></i> Delete</button>
      <button @click="copyNode"><i class="fas fa-copy"></i> Copy</button>
    </NodeToolbar>
    <div class="end-node">
      <div class="node-circle">
        <span class="node-icon">🏁</span>
      </div>
      <Handle type="target" position="left" id="left" class="handle-left" />
    </div>
  </template>
  
  <script>
  import { Handle } from '@vue-flow/core';
  import { NodeToolbar } from '@vue-flow/node-toolbar';
  
  export default {
    name: 'EndNode',
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
  .end-node {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 80px;
    height: 80px;
    background: linear-gradient(135deg, #f43f5e, #ec4899);
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
  
  .handle-left {
    width: 12px;
    height: 12px;
    background: #f43f5e;
    border-radius: 50%;
    border: 2px solid #e11d48;
    position: absolute;
    left: -6px;
    top: 50%;
    transform: translateY(-50%);
    transition: transform 0.2s;
  }
  
  .handle-left:hover {
    transform: translateY(-50%) scale(1.2);
  }

  .vue-flow__node {    
    min-width: 1px;
  }

  </style>
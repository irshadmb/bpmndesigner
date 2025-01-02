<!-- OutputNode.vue -->
<template>
    <div class="output-node">
      <div class="node-header">{{ data.label }}</div>
      <div class="node-content">
        <div class="output-value">{{ data.value || 'Waiting for input...' }}</div>
      </div>
      <Handle
        type="target"
        position="left"
        id="left"
        :style="{ background: '#555' }"
      />
    </div>
  </template>
  
  <script>
  import { Handle } from '@vue-flow/core'
  
  export default {
    name: 'OutputNode',
    components: {
      Handle
    },
    props: {
      data: {
        type: Object,
        required: true
      }
    }
  }
  </script>
  
  <style scoped>
  .output-node {
    position: relative;
    min-width: 120px;
  }
  
  .node-header {
    font-weight: bold;
    margin-bottom: 8px;
    padding: 4px;
    background: #f5f5f5;
    border-bottom: 1px solid #ddd;
  }
  
  .node-content {
    padding: 8px;
  }
  
  .output-value {
    padding: 4px;
    background: #f9f9f9;
    border: 1px solid #ddd;
    border-radius: 2px;
    min-height: 28px;
  }
  </style>
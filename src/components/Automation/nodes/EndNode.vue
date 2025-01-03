<template>
    <div class="end-node">
      <div class="node-circle">
        <span class="node-icon">🏁</span>
      </div>
      <Handle type="target" position="left" id="left" class="handle-left" />
    </div>
  </template>
  
  <script>
  import { Handle } from '@vue-flow/core';
  
  export default {
    name: 'EndNode',
    components: { Handle },
    props: {
    id: {
      type: String,
      required: true
    },
    data: {
      type: Object,
      required: true
    },
  },
    methods: {
     
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

  
button {
  padding: 6px 12px;
  margin: 0 4px;
  background: white;
  border: 1px solid #e5e7eb;
  border-radius: 6px;
  color: #4b5563;
  font-size: 0.9em;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
}

button:hover {
  background: #f9fafb;
  border-color: #6366f1;
  color: #6366f1;
  box-shadow: 0 2px 4px rgba(99, 102, 241, 0.1);
}

  </style>
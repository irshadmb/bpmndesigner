<template>
    <div class="gateway-node">
      <div class="node-diamond">
        <span class="node-icon">🔀</span>
      </div>
      <Handle type="target-a" position="top" id="top" class="handle-top" />
      <Handle type="source-a" position="bottom" id="bottom" class="handle-bottom" />
      <Handle type="target-b" position="left" id="left" class="handle-left" />
      <Handle type="source-c" position="right" id="right" class="handle-right" />
    </div>
  </template>
  
  <script>
  import { Handle } from '@vue-flow/core';
  
  export default {
    name: 'GatewayNode',
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
  .gateway-node {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 80px;
    height: 80px;
    position: relative;
  }
  
  .node-diamond {
    width: 60px;
    height: 60px;
    background: linear-gradient(135deg, #6366f1, #8b5cf6);
    transform: rotate(45deg);
    display: flex;
    align-items: center;
    justify-content: center;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  }
  
  .node-icon {
    transform: rotate(-45deg);
    font-size: 1.5em;
    color: white;
  }
  
  .handle-top,
  .handle-bottom,
  .handle-left,
  .handle-right {
    width: 12px;
    height: 12px;
    background: #6366f1;
    border-radius: 50%;
    border: 2px solid #4f46e5;
    position: absolute;
    transition: transform 0.2s;
  }
  
  .handle-top {
    top: -6px;
    left: 50%;
    transform: translateX(-50%);
  }
  
  .handle-bottom {
    bottom: -6px;
    left: 50%;
    transform: translateX(-50%);
  }
  
  .handle-left {
    left: -6px;
    top: 50%;
    transform: translateY(-50%);
  }
  
  .handle-right {
    right: -6px;
    top: 50%;
    transform: translateY(-50%);
  }
  
  .handle-top:hover,
  .handle-bottom:hover,
  .handle-left:hover,
  .handle-right:hover {
    transform: scale(1.2);
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
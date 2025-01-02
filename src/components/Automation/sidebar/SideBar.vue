<template>
    <div class="sidebar">
      <h3>Available Nodes</h3>
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'input')"
      >
        Input Node
      </div>
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'output')"
      >
        Output Node
      </div>
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'task')"
      >
        Task Node
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'SideBar',
    methods: {
      onDragStart(event, nodeType) {
        event.dataTransfer.setData('application/nodeType', nodeType)
      }
    }
  }
  </script>
  
  <style scoped>
  .sidebar {
    padding: 20px;
  }
  
  .node-item {
    padding: 10px;
    margin: 8px 0;
    border: 1px solid #ccc;
    border-radius: 4px;
    cursor: move;
    background: white;
  }
  
  .node-item:hover {
    background: #f5f5f5;
  }
  </style>
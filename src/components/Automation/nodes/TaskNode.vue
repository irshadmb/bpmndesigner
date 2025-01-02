<!-- TaskNode.vue -->
<template>
    <div class="task-node">
      <div class="node-header">
        {{ data.label }}
      </div>
      <div class="node-content">
        <div class="field-group">
          <label>Task Name</label>
          <input 
            type="text" 
            :value="nodeData.name" 
            @input="updateField('name', $event.target.value)"
            placeholder="Enter task name..."
          />
        </div>
        <div class="field-group">
          <label>Description</label>
          <textarea
            :value="nodeData.description"
            @input="updateField('description', $event.target.value)"
            placeholder="Task description..."
          ></textarea>
        </div>
      </div>
      <div class="node-footer">
        <Handle type="target" position="left" id="left" />
        <Handle type="source" position="right" id="right" />
      </div>
    </div>
  </template>
  <script>
  import { Handle } from '@vue-flow/core'
  
  export default {
    name: 'TaskNode',
    components: {
      Handle
    },
    props: {
      data: {
        type: Object,
        required: true
      }
    },
    data() {
      return {
        nodeData: {
          // Node specific data properties
          id: this.data.id
        }
      }
    },
    methods: {
      updateField(field, value) {
        this.nodeData[field] = value;
        this.$emit('update:data', { ...this.nodeData });
        this.$emit('nodeDataUpdate', {
          id: this.nodeData.id,
          field,
          value
        });
      }
    }
  }
  </script>
  <style scoped>
  .task-node, .gateway-node, .event-node, .pool-node {
    background: #1a1a1a;
    border-radius: 8px;
    min-width: 300px;
    color: white;
    font-family: system-ui, -apple-system, sans-serif;
  }
  
  /* Add other styles similar to InputNode.vue */
  </style>
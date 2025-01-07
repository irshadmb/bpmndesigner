<template>
    <div class="sql-node">
      <div class="node-header">
        <span class="header-text">{{ data.label }}</span>
      </div>
      
      <div class="node-content">
        <div class="field-group">
          <label>Template Name</label>
          <div class="input-wrapper">
            <input 
              type="text" 
              :value="data.templateName" 
              @input="updateField('templateName', $event.target.value)"
              placeholder="Enter template name..."
            />
          </div>
        </div>
  
        <div class="field-group">
          <label>SQL Query</label>
          <textarea
            :value="data.sqlQuery"
            @input="updateField('sqlQuery', $event.target.value)"
            placeholder="Enter your SQL query here..."
            rows="6"
            class="sql-textarea"
          ></textarea>
        </div>
      </div>
  
      <div class="node-footer">
        <Handle type="target" position="left" id="left" class="handle-left" />
        <Handle type="source" position="right" id="right" class="handle-right" />
      </div>
    </div>
  </template>
  
  <script>
  import { Handle, useVueFlow } from '@vue-flow/core'
  
  export default {
    name: 'SQLNode',
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
    emits: ['update:data', 'nodeDataUpdate'],
    setup(props, { emit }) {
      const { updateNode } = useVueFlow()
  
      function updateField(field, value) {
        const updatedData = { ...props.data, [field]: value }
        emit('update:data', updatedData)
        emit('nodeDataUpdate', {
          id: props.id,
          field,
          value
        })
        updateNode({ id: props.id, data: updatedData })
      }
  
      return {
        updateField
      }
    }
  }
  </script>
  
  <style scoped>
  .sql-node {
    background: white;
    border-radius: 16px;
    min-width: 320px;
    color: #1a1a1a;
    font-family: 'Inter', system-ui, sans-serif;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
    border: 1px solid #f0f0f0;
  }
  
  .node-header {
    padding: 16px;
    background: linear-gradient(135deg, #64f321, #5bff0f);
    border-radius: 16px 16px 0 0;
  }
  
  .header-text {
    color: white;
    font-weight: 600;
    font-size: 1.1em;
  }
  
  .node-content {
    padding: 20px;
  }
  
  .field-group {
    margin-bottom: 20px;
  }
  
  .field-group label {
    display: block;
    margin-bottom: 8px;
    color: #4b5563;
    font-size: 0.9em;
    font-weight: 500;
  }
  
  .input-wrapper {
    position: relative;
    margin-top: 6px;
  }
  
  input, .sql-textarea {
    width: 100%;
    padding: 12px 16px;
    background: #f9fafb;
    border: 2px solid #e5e7eb;
    border-radius: 12px;
    color: #1f2937;
    font-size: 0.95em;
    transition: all 0.2s;
  }
  
  input:focus, .sql-textarea:focus {
    outline: none;
    border-color: #2196F3;
    background: white;
    box-shadow: 0 0 0 4px rgba(33, 150, 243, 0.1);
  }
  
  .sql-textarea {
    resize: vertical;
    font-family: 'Fira Code', monospace;
    line-height: 1.5;
    min-height: 120px;
  }
  
  .node-footer {
    border-top: 1px solid #f0f0f0;
    padding: 16px;
    height: 50px;
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  
  .handle-left {
    width: 12px;
    height: 12px;
    background: #2196F3;
    border-radius: 50%;
    border: 2px solid #1976D2;
    transition: transform 0.2s;
  }
  
  .handle-right {
    width: 12px;
    height: 12px;
    background: #1976D2;
    border-radius: 50%;
    border: 2px solid #1565C0;
    transition: transform 0.2s;
  }
  
  .handle-left:hover, .handle-right:hover {
    transform: scale(1.2);
  }
  </style>
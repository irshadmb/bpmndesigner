<template>
  <div class="webhook-node">
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
            placeholder="template_name"
          />
        </div>
      </div>

      <div class="field-group">
        <label>Webhook URL</label>
        <div class="input-wrapper">
          <input 
            type="text" 
            :value="data.url" 
            @input="updateField('url', $event.target.value)"
            placeholder="https://api.example.com/webhook"
          />
        </div>
      </div>

      

      <div class="field-group">
        <label>Method</label>
        <select 
          :value="data.method" 
          @change="updateField('method', $event.target.value)"
          class="method-select"
        >
          <option value="POST">POST</option>
          <option value="GET">GET</option>
          <option value="PUT">PUT</option>
          <option value="DELETE">DELETE</option>
        </select>
      </div>

      <div class="field-group">
        <label>Headers</label>
        <textarea
          :value="JSON.stringify(data.headers, null, 2)"
          @input="updateField('headers', $event.target.value)"
          placeholder="{Content-Type: application/json}"
          rows="3"
        ></textarea>
      </div>

      <div class="field-group">
        <label>Body</label>
        <textarea
          :value="data.body"
          @input="updateField('body', $event.target.value)"
          placeholder="{}"
          rows="4"
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
  name: 'WebhookNode',
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
 .webhook-node {
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
  background: linear-gradient(135deg, #6366f1, #8b5cf6);
  border-radius: 16px 16px 0 0;
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

.node-toolbar button:active {
  transform: translateY(1px);
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

input, select, textarea {
  width: 100%;
  padding: 12px 16px;
  background: #f9fafb;
  border: 2px solid #e5e7eb;
  border-radius: 12px;
  color: #1f2937;
  font-size: 0.95em;
  transition: all 0.2s;
}

input:focus, select:focus, textarea:focus {
  outline: none;
  border-color: #6366f1;
  background: white;
  box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.1);
}

textarea {
  resize: vertical;
  font-family: 'Fira Code', monospace;
  line-height: 1.5;
}

.method-select {
  cursor: pointer;
  appearance: none;
  padding-right: 36px;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' fill='none' viewBox='0 0 24 24' stroke='%236b7280'%3E%3Cpath stroke-linecap='round' stroke-linejoin='round' stroke-width='2' d='M19 9l-7 7-7-7'%3E%3C/path%3E%3C/svg%3E");
  background-repeat: no-repeat;
  background-position: right 12px center;
  background-size: 16px;
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
  background: #6366f1;
  border-radius: 50%;
  border: 2px solid #4f46e5;
  transition: transform 0.2s;
}

.handle-right {
  width: 12px;
  height: 12px;
  background: #ec4899;
  border-radius: 50%;
  border: 2px solid #db2777;
  transition: transform 0.2s;
}

.handle-left:hover, .handle-right:hover {
  transform: scale(1.2);
}
  </style>
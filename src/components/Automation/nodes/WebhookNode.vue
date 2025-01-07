<template>
  <div class="webhook-node">
    <div class="node-header">
      <span class="header-text">{{ data.label }}</span>
    </div>
    
    <div class="node-content">
      <div class="field-group">
        <label>Template Name</label>
        <div class="template-control">
          <select 
            v-model="selectedTemplate"
            class="template-select"
            @change="applyTemplate"
          >
            <option value="">Select Template</option>
            <option 
              v-for="template in data.templates" 
              :key="template.id"
              :value="template.id"
            >
              {{ template.name }}
            </option>
          </select>
          <button 
            class="add-template-btn"
            @click="showAddTemplate = true"
            title="Add New Template"
          >
            +
          </button>
        </div>

        <!-- Add Template Modal -->
        <div v-if="showAddTemplate" class="template-modal">
          <div class="modal-content">
            <h3>Add New Template</h3>
            <div class="modal-form">
              <div class="field-group">
                <label>Template Name</label>
                <input 
                  v-model="newTemplate.name"
                  type="text"
                  placeholder="Enter template name"
                />
              </div>
              <div class="field-group">
                <label>Webhook URL</label>
                <input 
                  v-model="newTemplate.url"
                  type="text"
                  placeholder="https://api.example.com/webhook"
                />
              </div>
              <div class="field-group">
                <label>Method</label>
                <select 
                  v-model="newTemplate.method"
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
                  v-model="newTemplate.headers"
                  placeholder="{Content-Type: application/json}"
                  rows="3"
                ></textarea>
              </div>
              <div class="field-group">
                <label>Body</label>
                <textarea
                  v-model="newTemplate.body"
                  placeholder="{}"
                  rows="4"
                ></textarea>
              </div>
              <div class="modal-actions">
                <button @click="addNewTemplate" class="save-btn">Save</button>
                <button @click="showAddTemplate = false" class="cancel-btn">Cancel</button>
              </div>
            </div>
          </div>
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
            :readonly="isReadOnly"
          />
        </div>
      </div>

      <div class="field-group">
        <label>Method</label>
        <select 
          :value="data.method" 
          @change="updateField('method', $event.target.value)"
          class="method-select"
          :disabled="isReadOnly"
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
          :readonly="isReadOnly"
        ></textarea>
      </div>

      <div class="field-group">
        <label>Body</label>
        <textarea
          :value="data.body"
          @input="updateField('body', $event.target.value)"
          placeholder="{}"
          rows="4"
          :readonly="isReadOnly"
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
import { ref } from 'vue'

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
    const showAddTemplate = ref(false)
    const selectedTemplate = ref('')
    const isReadOnly = ref(false)
    const newTemplate = ref({
      name: '',
      url: '',
      method: 'POST',
      headers: '{}',
      body: '{}'
    })

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

    function addNewTemplate() {
      const templates = [...(props.data.templates || [])]
      const newTemplateObj = {
        id: Date.now().toString(),
        name: newTemplate.value.name,
        url: newTemplate.value.url,
        method: newTemplate.value.method,
        headers: newTemplate.value.headers,
        body: newTemplate.value.body
      }
      
      templates.push(newTemplateObj)
      updateField('templates', templates)
      
      // Reset form
      newTemplate.value = { name: '', url: '', method: 'POST', headers: '{}', body: '{}' }
      showAddTemplate.value = false
      selectedTemplate.value = newTemplateObj.id
    }

    function applyTemplate() {
      if (selectedTemplate.value) {
        const template = props.data.templates.find(t => t.id === selectedTemplate.value)
        if (template) {
          // Update webhook fields with template values
          updateField('url', template.url)
          updateField('method', template.method)
          updateField('headers', template.headers)
          updateField('body', template.body)

          // Make fields read-only
          isReadOnly.value = true
        }
      } else {
        // Reset fields and make them editable
        updateField('url', '')
        updateField('method', 'POST')
        updateField('headers', '{}')
        updateField('body', '{}')
        isReadOnly.value = false
      }
    }

    return {
      updateField,
      showAddTemplate,
      selectedTemplate,
      newTemplate,
      isReadOnly,
      addNewTemplate,
      applyTemplate
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

/* Template Control Styles */
.template-control {
  display: flex;
  align-items: center;
  gap: 8px;
}

.template-select {
  flex: 1;
}

.add-template-btn {
  padding: 8px 12px;
  background: #6366f1;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}

.add-template-btn:hover {
  background: #4f46e5;
}

/* Modal Styles */
.template-modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: white;
  border-radius: 16px;
  padding: 24px;
  width: 90%;
  max-width: 500px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.modal-content h3 {
  margin-top: 0;
  font-size: 1.2em;
  color: #1a1a1a;
}

.modal-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
}

.save-btn {
  background: #6366f1;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}

.save-btn:hover {
  background: #4f46e5;
}

.cancel-btn {
  background: #f0f0f0;
  color: #4b5563;
  border: none;
  padding: 8px 16px;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}

.cancel-btn:hover {
  background: #e5e7eb;
}

/* Read-only Styles */
input[readonly], textarea[readonly], select[disabled] {
  background: #f0f0f0;
  border-color: #e5e7eb;
  cursor: not-allowed;
}

select[disabled] {
  opacity: 0.7;
}
</style>
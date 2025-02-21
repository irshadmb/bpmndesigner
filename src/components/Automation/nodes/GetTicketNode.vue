<template>
  <div class="ticket-node">
    <div class="node-header">
      <span class="header-text">{{ data.label }}</span>
    </div>
    
    <div class="node-content">
      <div class="field-group">
        <label>Search By</label>
        <div class="toggle-switch">
          <input 
            type="checkbox" 
            :checked="data.useRequestId"
            @change="updateField('useRequestId', $event.target.checked)"
            id="idToggle" 
          />
          <label for="idToggle" class="toggle-label">
            <span>Ticket ID</span>
            <span>Request ID</span>
          </label>
        </div>
      </div>

      <div class="field-group">
        <label>{{ data.useRequestId ? 'Request ID' : 'Ticket ID' }}</label>
        <div class="input-wrapper">
          <input 
            type="text"
            :value="data.ticketId"
            @input="updateField('ticketId', $event.target.value)"
            :placeholder="data.useRequestId ? 'Enter Request ID...' : 'Enter Ticket ID...'"
          />
          <div class="input-highlight"></div>
        </div>
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
  name: 'GetTicketNode',
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
.ticket-node {
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
  background: linear-gradient(135deg, #c963f1, #f65cc8);
  border-radius: 16px 16px 0 0;
  position: relative;
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

input {
  width: 100%;
  padding: 12px 16px;
  background: #f9fafb;
  border: 2px solid #e5e7eb;
  border-radius: 12px;
  color: #1f2937;
  font-size: 0.95em;
  transition: all 0.2s;
}

input:focus {
  outline: none;
  border-color: #6366f1;
  background: white;
  box-shadow: 0 0 0 4px rgba(99, 102, 241, 0.1);
}

.toggle-switch {
  position: relative;
  width: 100%;
  height: 40px;
}

.toggle-label {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #f3f4f6;
  border-radius: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 4px;
  border: 2px solid #e5e7eb;
}

.toggle-label span {
  flex: 1;
  text-align: center;
  padding: 8px 12px;
  border-radius: 8px;
  color: #6b7280;
  font-weight: 500;
  font-size: 0.9em;
  transition: all 0.3s;
}

.toggle-switch input:checked + .toggle-label span:last-child {
  background: #6366f1;
  color: white;
}

.toggle-switch input:not(:checked) + .toggle-label span:first-child {
  background: #6366f1;
  color: white;
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
  transition: all 0.2s;
}

.handle-right {
  width: 12px;
  height: 12px;
  background: #ec4899;
  border-radius: 50%;
  border: 2px solid #db2777;
  transition: all 0.2s;
}

.handle-left:hover, .handle-right:hover {
  transform: scale(1.2);
}

@keyframes gradient {
  0% {background-position: 0% 50%}
  50% {background-position: 100% 50%}
  100% {background-position: 0% 50%}
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
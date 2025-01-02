<template>
  <div class="input-node">
    <div class="node-header">
      {{ data.label }}
    </div>
    
    <div class="node-content">
      <!-- Input Field -->
      <div class="field-group">
        <label>Input</label>
        <div class="input-with-icons">
          <input 
            type="text" 
            :value="nodeData.input" 
            @input="updateField('input', $event.target.value)"
            placeholder="Type something..."
          />
          <div class="action-icons">
            <span class="globe-icon">🌐</span>
            <span class="magic-icon">✨</span>
          </div>
        </div>
      </div>

      <!-- Model Selection -->
      <div class="field-group">
        <label>Model</label>
        <div class="model-select">
          <div class="model-tag">4α</div>
          <select 
            :value="nodeData.model" 
            @change="updateField('model', $event.target.value)"
          >
            <option value="gpt-4o-mini">gpt-4o-mini</option>
            <option value="gpt-4">gpt-4</option>
            <option value="gpt-3.5">gpt-3.5</option>
          </select>
        </div>
      </div>

      <!-- API Key -->
      <div class="field-group">
        <label>API Key</label>
        <input 
          type="password" 
          :value="nodeData.apiKey" 
          @input="updateField('apiKey', $event.target.value)"
          placeholder="Enter API key..."
        />
      </div>

      <!-- Temperature Slider -->
      <div class="field-group">
        <div class="temperature-header">
          <label>Temperature</label>
          <span class="temperature-value">{{ nodeData.temperature || '0.14' }}</span>
        </div>
        <div class="slider-container">
          <input 
            type="range" 
            min="0" 
            max="1" 
            step="0.01" 
            :value="nodeData.temperature" 
            @input="updateField('temperature', $event.target.value)"
            class="temperature-slider"
          />
          <div class="slider-labels">
            <span>Precise</span>
            <span>Creative</span>
          </div>
        </div>
      </div>
    </div>

    <div class="node-footer">
      <Handle 
        type="source" 
        position="right" 
        id="right"
        class="handle-right"
      />
    </div>
  </div>
</template>

<script>
import { Handle } from '@vue-flow/core'

export default {
  name: 'InputNode',
  components: {
    Handle
  },
  props: {
    data: {
      type: Object,
      required: true
    },
  },
  data() {
    return {
      nodeData: {
        input: this.data.input || '',
        model: this.data.model || 'gpt-4o-mini',
        apiKey: this.data.apiKey || '',
        temperature: this.data.temperature || 0.14,
        id: this.data.id
      }
    }
  },
  watch: {
    data: {
      deep: true,
      handler(newData) {
        this.nodeData = {
          ...this.nodeData,
          ...newData
        }
      }
    }
  },
  methods: {
    updateField(field, value) {
      this.nodeData[field] = value;
      
      // Emit the update to parent
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
.input-node {
  background: #1a1a1a;
  border-radius: 8px;
  min-width: 300px;
  color: white;
  font-family: system-ui, -apple-system, sans-serif;
}

.node-header {
  padding: 12px;
  font-weight: 500;
  border-bottom: 1px solid #333;
  position: relative;
}

.node-content {
  padding: 16px;
}

.field-group {
  margin-bottom: 16px;
}

.field-group label {
  display: block;
  margin-bottom: 8px;
  color: #888;
  font-size: 0.9em;
}

input, select {
  width: 100%;
  padding: 8px 12px;
  background: #333;
  border: 1px solid #444;
  border-radius: 4px;
  color: white;
  font-size: 0.9em;
}

.input-with-icons {
  position: relative;
  display: flex;
  align-items: center;
}

.action-icons {
  position: absolute;
  right: 8px;
  display: flex;
  gap: 8px;
}

.globe-icon, .magic-icon {
  cursor: pointer;
  opacity: 0.6;
}

.model-select {
  position: relative;
  display: flex;
  align-items: center;
  gap: 8px;
}

.model-tag {
  background: #444;
  padding: 2px 6px;
  border-radius: 4px;
  font-size: 0.8em;
}

.temperature-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 8px;
}

.temperature-value {
  color: #888;
  font-size: 0.9em;
}

.slider-container {
  position: relative;
  padding: 10px 0;
}

/* Reset default styles */
.temperature-slider {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 4px;
  border-radius: 2px;
  background: transparent;
  outline: none;
  position: relative;
}

/* Track styles */
.temperature-slider::before {
  content: '';
  position: absolute;
  top: 50%;
  left: 0;
  right: 0;
  height: 4px;
  background: linear-gradient(to right, #7c3aed, #3b82f6);
  border-radius: 2px;
  transform: translateY(-50%);
  pointer-events: none;
}

/* Thumb styles for Webkit (Chrome, Safari, etc.) */
.temperature-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  border: none;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  margin-top: -6px;
  position: relative;
  z-index: 1;
}

/* Thumb styles for Mozilla Firefox */
.temperature-slider::-moz-range-thumb {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  background: white;
  cursor: pointer;
  border: none;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  position: relative;
  z-index: 1;
}

/* Track styles for Mozilla Firefox */
.temperature-slider::-moz-range-track {
  width: 100%;
  height: 4px;
  background: linear-gradient(to right, #7c3aed, #3b82f6);
  border-radius: 2px;
  border: none;
}

.slider-labels {
  display: flex;
  justify-content: space-between;
  margin-top: 8px;
  color: #888;
  font-size: 0.8em;
}

.node-footer {
  border-top: 1px solid #333;
  padding: 12px;
  position: relative;
  height: 40px;
  display: flex;
  align-items: center;
  justify-content: flex-end;
}

.handle-right {
  width: 10px;
  height: 10px;
  background: #d820b9;
  border-radius: 50%;
  border: 2px solid #e41795;
}
</style>
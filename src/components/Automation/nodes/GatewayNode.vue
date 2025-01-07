<template>
  <div class="gateway-node">
    <div class="node-diamond">
      <span class="node-icon">⚡</span>
    </div>
    <!-- One target handle -->
    <Handle type="target" position="left" id="target-left" class="handle-left" />
    
    <!-- Three source handles -->
    <Handle type="source" position="right" id="source-right" class="handle-right" />
    <Handle type="source" position="top" id="source-top" class="handle-top" />
    <Handle type="source" position="bottom" id="source-bottom" class="handle-bottom" />
  </div>
</template>

<script>
import { Handle,useVueFlow } from '@vue-flow/core';

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

/* Style target handle differently */
.handle-left {
  left: -6px;
  top: 50%;
  transform: translateY(-50%);
  background: #ec4899;
  border: 2px solid #db2777;
}

/* Style source handles */
.handle-right {
  right: -6px;
  top: 50%;
  transform: translateY(-50%);
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
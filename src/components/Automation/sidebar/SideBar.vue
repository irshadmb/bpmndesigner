<template>
  <div class="sidebar">
    <h3>Available Nodes</h3>
    <div class="nodes-container">
      <!-- Start Node -->
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'start')"
      >
        <div class="node-icon start-icon">🚀</div>
        <div class="node-details">
          <span class="node-name">Start Node</span>
        </div>
      </div>

      <!-- End Node -->
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'end')"
      >
        <div class="node-icon end-icon">🏁</div>
        <div class="node-details">
          <span class="node-name">End Node</span>
        </div>
      </div>

      <div
  class="node-item"
  draggable="true"
  @dragstart="onDragStart($event, 'gateway')"
>
  <div class="node-icon gateway-icon">🔀</div>
  <div class="node-details">
    <span class="node-name">Gateway Node</span>
  </div>
</div>

      <!-- GetTicket Node -->
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'getticket')"
      >
        <div class="node-icon ticket-icon">🎫</div>
        <div class="node-details">
          <span class="node-name">GetTicket Node</span>
        </div>
      </div>

      <!-- Webhook Node -->
      <div
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'webhook')"
      >
        <div class="node-icon webhook-icon">🔗</div>
        <div class="node-details">
          <span class="node-name">Webhook Node</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SideBar',
  methods: {
    onDragStart(event, nodeType) {
      event.dataTransfer.setData('application/nodeType', nodeType);
      event.target.classList.add('dragging');
    },
  },
};
</script>

<style scoped>
.sidebar {
  padding: 16px;
  background: #ffffff;
  border-right: 1px solid #e0e0e0;
  height: 100%;
  box-shadow: 2px 0 8px rgba(0, 0, 0, 0.05);
}

h3 {
  color: #333333; /* Darker color for better contrast */
  font-size: 1.1em;
  font-weight: 600;
  margin-bottom: 16px;
  padding-bottom: 8px;
  border-bottom: 2px solid #e0e0e0;
}

.nodes-container {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.node-item {
  display: flex;
  align-items: center;
  padding: 8px;
  background: #ffffff;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  cursor: move;
  transition: all 0.2s ease;
  user-select: none;
}

.node-item:hover {
  background: #f8f9fa;
  border-color: #6366f1;
  transform: translateY(-1px);
  box-shadow: 0 2px 8px rgba(99, 102, 241, 0.1);
}

.node-item.dragging {
  transform: scale(0.95);
}

.node-icon {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 8px;
  margin-right: 8px;
  font-size: 1.2em;
  transition: transform 0.2s ease;
}

.node-icon:hover {
  transform: scale(1.05);
}

.start-icon {
  background: linear-gradient(135deg, #4caf50, #66bb6a);
  color: white;
}

.end-icon {
  background: linear-gradient(135deg, #f43f5e, #ec4899);
  color: white;
}

.ticket-icon {
  background: linear-gradient(135deg, #f59e0b, #fbbf24);
  color: white;
}

.webhook-icon {
  background: linear-gradient(135deg, #8b5cf6, #a855f7);
  color: white;
}

.gateway-icon {
  background: linear-gradient(135deg, #6366f1, #8b5cf6);
  color: white;
}

.node-details {
  flex: 1;
}

.node-name {
  display: block;
  color: #333333; /* Darker color for better readability */
  font-weight: 500;
  font-size: 0.9em;
}

@media (hover: hover) {
  .node-item:active {
    transform: scale(0.98);
  }
}
</style>
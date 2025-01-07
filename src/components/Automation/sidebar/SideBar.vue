<template>
  <div class="sidebar">
    <h3>Available Nodes</h3>
    
    <!-- Add search input -->
    <div class="search-container">
      <input 
        type="text"
        v-model="searchQuery"
        placeholder="Search nodes..."
        class="search-input"
      />
      <span v-if="searchQuery" 
            class="clear-search" 
            @click="searchQuery = ''"
      >×</span>
    </div>

    <div class="nodes-container">
      <!-- Start Node -->
      <div
        v-if="isNodeVisible('Start Node')"
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
        v-if="isNodeVisible('End Node')"
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'end')"
      >
        <div class="node-icon end-icon">🏁</div>
        <div class="node-details">
          <span class="node-name">End Node</span>
        </div>
      </div>

      <!-- Gateway Node -->
      <div
        v-if="isNodeVisible('Gateway Node')"
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
        v-if="isNodeVisible('GetTicket Node')"
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
        v-if="isNodeVisible('Webhook Node')"
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'webhook')"
      >
        <div class="node-icon webhook-icon">🔗</div>
        <div class="node-details">
          <span class="node-name">Webhook Node</span>
        </div>
      </div>

      <!-- PDF Generator Node -->
      <div 
        v-if="isNodeVisible('PDF Generator')"
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'pdf')"
      >
        <span class="node-icon pdf-icon">📄</span>
        <span class="node-details">PDF Generator</span>
      </div>

      <!-- SQL Query Node -->
      <div 
        v-if="isNodeVisible('SQL Query')"
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'sql')"
      >
        <div class="node-icon sql-icon">💾</div>
        <div class="node-details">
          <span class="node-name">SQL Query</span>
        </div>
      </div>

      <!-- Email Node -->
      <div 
        v-if="isNodeVisible('Email')"
        class="node-item"
        draggable="true"
        @dragstart="onDragStart($event, 'email')"
      >
        <div class="node-icon email-icon">✉️</div>
        <div class="node-details">
          <span class="node-name">Email</span>
        </div>
      </div>

      <!-- No results message -->
      <div v-if="!hasVisibleNodes" class="no-results">
        No nodes match your search
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SideBar',
  data() {
    return {
      searchQuery: '',
      nodes: [
        { name: 'Start Node', type: 'start' },
        { name: 'End Node', type: 'end' },
        { name: 'Gateway Node', type: 'gateway' },
        { name: 'GetTicket Node', type: 'getticket' },
        { name: 'Webhook Node', type: 'webhook' },
        { name: 'PDF Generator', type: 'pdf' },
        { name: 'SQL Query', type: 'sql' },
        { name: 'Email', type: 'email' }
      ]
    }
  },
  computed: {
    hasVisibleNodes() {
      return this.nodes.some(node => this.isNodeVisible(node.name))
    }
  },
  methods: {
    onDragStart(event, nodeType) {
      event.dataTransfer.setData('application/nodeType', nodeType);
      event.target.classList.add('dragging');
    },
    isNodeVisible(nodeName) {
      if (!this.searchQuery) return true;
      return nodeName.toLowerCase().includes(this.searchQuery.toLowerCase());
    }
  },
};
</script>

<style scoped>
/* Add these new styles to your existing styles */
.search-container {
  position: relative;
  padding: 0 0 16px 0;
  margin-bottom: 16px;
  border-bottom: 1px solid #e0e0e0;
}

.search-input {
  width: 100%;
  padding: 8px 32px 8px 12px;
  border: 2px solid #e5e7eb;
  border-radius: 8px;
  font-size: 0.9em;
  color: #1f2937;
  background: #f9fafb;
  transition: all 0.2s ease;
}

.search-input:focus {
  outline: none;
  border-color: #6366f1;
  background: white;
  box-shadow: 0 0 0 3px rgba(99, 102, 241, 0.1);
}

.clear-search {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  cursor: pointer;
  color: #9ca3af;
  font-size: 1.2em;
  padding: 4px;
  transition: color 0.2s ease;
}

.clear-search:hover {
  color: #4b5563;
}

.no-results {
  padding: 16px;
  text-align: center;
  color: #6b7280;
  font-size: 0.9em;
  font-style: italic;
}

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

.pdf-icon {
  background: linear-gradient(135deg, #6366f1, #8b5cf6);
  color: white;
}

.sql-icon {
  background: linear-gradient(135deg, #2196F3, #1976D2);
  color: white;
}

.email-icon {
  background: linear-gradient(135deg, #3B82F6, #60A5FA);
  color: white;
}
</style>
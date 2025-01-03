# BPMN Designer

A Vue.js-based Business Process Model and Notation (BPMN) designer that allows users to create and manage workflow diagrams through an interactive interface.

## Features

- Interactive drag-and-drop interface for creating BPMN diagrams
- Multiple node types supported:
  - Start Node (🚀) - Entry point for processes
  - End Node (🏁) - Terminal point for processes
  - Gateway Node (🔀) - Handles multiple connections and flow control
  - GetTicket Node (🎫) - Retrieves ticket information based on Ticket/Request ID
  - Webhook Node (🔗) - Configures webhook connections with customizable parameters

## Prerequisites

- Node.js (Latest LTS version recommended)
- npm (Node Package Manager)

## Project Setup

1. Clone the repository:
```bash
git clone https://github.com/irshadmb/bpmndesigner.git
cd bpmndesigner
```
2. Install dependencies:
```bash
npm install
```
3. Run development server:
```bash
npm run serve
```
4. Build for production:
```bash
npm run build
```
5. Lint and fix files:
```bash
npm run lint
```

## Project Structure

src/
├── components/
│   └── Automation/
│       ├── nodes/           # BPMN node components
│       │   ├── EndNode.vue
│       │   ├── GatewayNode.vue
│       │   ├── GetTicketNode.vue
│       │   ├── StartNode.vue
│       │   └── WebhookNode.vue
│       └── sidebar/         # Sidebar component for node selection
│           └── SideBar.vue
├── App.vue                  # Main application component
└── main.js                 # Application entry point

## Usage
Launch the application using npm run serve
Use the sidebar to select available nodes
Drag and drop nodes onto the canvas
Connect nodes to create your workflow
Configure node properties as needed

## Customization
For custom configuration options, refer to the Vue CLI Configuration Reference.

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Author
Irshad Ahamed M B

## Acknowledgments
Built with Vue.js
Uses BPMN standards for workflow notation
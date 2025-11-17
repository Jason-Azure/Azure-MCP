# Azure-MCP

A collection of Model Context Protocol (MCP) servers designed to run with GitHub Copilot, enabling AI-powered interactions with Azure services.

## 📋 Table of Contents

- [What is MCP?](#what-is-mcp)
- [Installation](#installation)
- [Usage](#usage)
- [Examples](#examples)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)
- [Contributing](#contributing)

## 🤔 What is MCP?

The Model Context Protocol (MCP) is an open protocol that standardizes how applications provide context to Large Language Models (LLMs). MCP servers expose resources, tools, and prompts that AI assistants like GitHub Copilot can use to enhance their capabilities.

## 📦 Installation

### Prerequisites

Before installing Azure-MCP servers, ensure you have:

- **Node.js**: Version 18.x or higher
- **npm**: Version 9.x or higher (comes with Node.js)
- **GitHub Copilot**: Active subscription with MCP support
- **Azure Account**: For accessing Azure services (if using Azure-specific servers)

### Step 1: Clone the Repository

```bash
# Clone the repository
git clone https://github.com/Jason-Azure/Azure-MCP.git

# Navigate to the directory
cd Azure-MCP
```

### Step 2: Install Dependencies

```bash
# Install all required dependencies
npm install
```

### Step 3: Configure GitHub Copilot

Add the MCP server configuration to your GitHub Copilot settings:

**For VS Code:**

1. Open VS Code Settings (⌘+, on Mac or Ctrl+, on Windows/Linux)
2. Search for "GitHub Copilot MCP"
3. Add your server configuration in `settings.json`:

```json
{
  "github.copilot.mcp.servers": {
    "azure-mcp": {
      "command": "node",
      "args": ["/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "your-subscription-id",
        "AZURE_TENANT_ID": "your-tenant-id"
      }
    }
  }
}
```

## 🚀 Usage

### Basic Usage

Once installed and configured, the Azure-MCP servers will automatically integrate with GitHub Copilot. You can interact with Azure services through natural language queries in your code editor.

### Using in Chat

Open GitHub Copilot Chat and use the MCP servers:

```
@copilot What are my Azure resources in the westus region?
```

```
@copilot Create a new Azure Storage account in my subscription
```

### Using in Code

The MCP servers provide context-aware suggestions while coding:

```javascript
// Type a comment describing what you want to do
// Example: List all virtual machines in my Azure subscription

// Copilot will suggest code using the MCP server context
```

## 💡 Examples

### Example 1: Querying Azure Resources

**Question:** "Show me all my Azure resources"

**MCP Server Response:**
```javascript
// The MCP server provides context about your Azure subscription
// and enables Copilot to suggest appropriate Azure SDK code

const { DefaultAzureCredential } = require("@azure/identity");
const { ResourceManagementClient } = require("@azure/arm-resources");

// Authenticate with Azure
const credential = new DefaultAzureCredential();
const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID;
const client = new ResourceManagementClient(credential, subscriptionId);

// List all resources
async function listResources() {
  const resources = [];
  for await (const resource of client.resources.list()) {
    resources.push({
      name: resource.name,
      type: resource.type,
      location: resource.location
    });
  }
  return resources;
}

listResources().then(console.log);
```

### Example 2: Managing Azure Storage

**Question:** "How do I upload a file to Azure Blob Storage?"

**MCP Server Response:**
```javascript
// MCP server provides Azure Storage context and credentials
const { BlobServiceClient } = require("@azure/storage-blob");

async function uploadFileToBlob(containerName, filePath, blobName) {
  // Connection string from Azure Portal or MCP server context
  const connectionString = process.env.AZURE_STORAGE_CONNECTION_STRING;
  
  // Create BlobServiceClient
  const blobServiceClient = BlobServiceClient.fromConnectionString(connectionString);
  
  // Get container client
  const containerClient = blobServiceClient.getContainerClient(containerName);
  
  // Ensure container exists
  await containerClient.createIfNotExists({
    access: 'container' // Public access level
  });
  
  // Get block blob client
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);
  
  // Upload file
  const uploadResponse = await blockBlobClient.uploadFile(filePath);
  console.log(`Upload successful: ${uploadResponse.requestId}`);
  
  return blockBlobClient.url;
}

// Usage
uploadFileToBlob("my-container", "./local-file.txt", "remote-file.txt")
  .then(url => console.log(`File uploaded to: ${url}`))
  .catch(err => console.error("Error uploading file:", err));
```

### Example 3: Working with Azure Functions

**Question:** "Create an Azure Function to process messages"

**MCP Server Response:**
```javascript
// MCP server provides Azure Functions context
const { app } = require('@azure/functions');

// HTTP triggered function
app.http('processMessage', {
    methods: ['GET', 'POST'],
    authLevel: 'anonymous',
    handler: async (request, context) => {
        context.log('Processing message...');
        
        // Get message from request body or query
        const message = await request.text() || request.query.get('message');
        
        // Process the message (example: convert to uppercase)
        const processedMessage = {
            original: message,
            processed: message?.toUpperCase(),
            timestamp: new Date().toISOString(),
            status: 'success'
        };
        
        // Return response
        return {
            status: 200,
            jsonBody: processedMessage
        };
    }
});
```

## ⚙️ Configuration

### Environment Variables

Configure the following environment variables for Azure authentication:

```bash
# Required for Azure authentication
export AZURE_SUBSCRIPTION_ID="your-subscription-id"
export AZURE_TENANT_ID="your-tenant-id"
export AZURE_CLIENT_ID="your-client-id"           # For service principal auth
export AZURE_CLIENT_SECRET="your-client-secret"   # For service principal auth

# Optional: For specific services
export AZURE_STORAGE_CONNECTION_STRING="your-connection-string"
export AZURE_STORAGE_ACCOUNT_NAME="your-storage-account"
```

### Configuration File

Create a `.env` file in the project root:

```env
# Azure Configuration
AZURE_SUBSCRIPTION_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
AZURE_TENANT_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
AZURE_CLIENT_ID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
AZURE_CLIENT_SECRET=your-secret-here

# MCP Server Configuration
MCP_SERVER_PORT=3000
MCP_LOG_LEVEL=info
```

**⚠️ Security Note:** Never commit the `.env` file to version control. Add it to `.gitignore`:

```bash
echo ".env" >> .gitignore
```

## 🔧 Troubleshooting

### Common Issues and Solutions

#### Issue 1: "MCP server not found"

**Problem:** GitHub Copilot cannot connect to the MCP server.

**Solution:**
```bash
# Verify the server path is correct
which node
# Update the path in your Copilot settings

# Check if the server file exists
ls -la /path/to/Azure-MCP/server.js

# Test the server manually
node /path/to/Azure-MCP/server.js
```

#### Issue 2: "Azure authentication failed"

**Problem:** Cannot authenticate with Azure services.

**Solution:**
```bash
# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Login to Azure
az login

# Verify your subscription
az account show

# Set the correct subscription
az account set --subscription "your-subscription-id"
```

#### Issue 3: "Module not found"

**Problem:** Required Node.js modules are missing.

**Solution:**
```bash
# Clean install dependencies
rm -rf node_modules package-lock.json
npm install

# If using specific Azure SDKs, install them
npm install @azure/identity @azure/arm-resources @azure/storage-blob
```

#### Issue 4: "Permission denied"

**Problem:** Insufficient permissions to access Azure resources.

**Solution:**
- Ensure your Azure account has the necessary role assignments
- Check RBAC (Role-Based Access Control) settings in Azure Portal
- Verify service principal has correct permissions if using one

```bash
# Assign Reader role to your account
az role assignment create \
  --assignee your-email@domain.com \
  --role Reader \
  --scope /subscriptions/your-subscription-id
```

## ❓ FAQ

### Q1: What is the Model Context Protocol (MCP)?

**A:** MCP is an open protocol that enables AI assistants to access external data sources and tools. It provides a standardized way for applications to expose resources, tools, and prompts that LLMs can use to provide better assistance.

### Q2: Do I need an Azure subscription to use Azure-MCP?

**A:** Yes, to interact with actual Azure services, you need an active Azure subscription. However, you can explore the code examples and understand the patterns without one.

### Q3: Which Azure services are supported?

**A:** Azure-MCP servers can support any Azure service with a JavaScript/TypeScript SDK, including:
- Azure Resource Manager (ARM)
- Azure Storage (Blob, Queue, Table, File)
- Azure Functions
- Azure App Service
- Azure SQL Database
- Azure Cosmos DB
- Azure Key Vault
- And many more...

### Q4: How do I add support for a new Azure service?

**A:** Create a new MCP server module that:
1. Imports the appropriate Azure SDK
2. Implements authentication using `@azure/identity`
3. Exposes resources and tools through MCP protocol
4. Registers the server in your Copilot configuration

### Q5: Is my Azure credentials information secure?

**A:** Yes, if configured properly:
- Use environment variables or Azure Key Vault for secrets
- Never commit credentials to version control
- Use Managed Identity when running on Azure
- Follow the principle of least privilege for service principals

### Q6: Can I use this with other AI assistants besides GitHub Copilot?

**A:** Yes! MCP is an open protocol. Any AI assistant that supports MCP can use these servers. Current support includes:
- GitHub Copilot
- Claude Desktop
- Other MCP-compatible AI tools

### Q7: How do I debug MCP server issues?

**A:** Enable logging and test the server directly:

```bash
# Set log level to debug
export MCP_LOG_LEVEL=debug

# Run server with verbose output
node server.js --verbose

# Check logs
tail -f logs/mcp-server.log
```

### Q8: Can I use Azure-MCP in a corporate environment?

**A:** Yes, but ensure:
- Your organization's security policies allow MCP usage
- Network policies permit communication with Azure services
- You comply with data governance requirements
- You use appropriate authentication methods (service principals, managed identity)

### Q9: How do I update to the latest version?

**A:** Pull the latest changes from the repository:

```bash
# Fetch latest changes
git fetch origin

# Update to latest
git pull origin main

# Reinstall dependencies if needed
npm install
```

### Q10: Where can I get help or report issues?

**A:** 
- **Issues:** Report bugs or request features on [GitHub Issues](https://github.com/Jason-Azure/Azure-MCP/issues)
- **Discussions:** Ask questions on [GitHub Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)
- **Documentation:** Check the [Azure SDK Documentation](https://docs.microsoft.com/azure/developer/)
- **MCP Spec:** Read the [Model Context Protocol Specification](https://modelcontextprotocol.io/)

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Development Guidelines

- Follow existing code style and conventions
- Add tests for new features
- Update documentation for any changes
- Ensure all tests pass before submitting PR
- Add helpful comments to explain complex logic

## 📄 License

This project is open source. Please check the LICENSE file for details.

## 🙏 Acknowledgments

- [Model Context Protocol](https://modelcontextprotocol.io/) - For the MCP specification
- [GitHub Copilot](https://github.com/features/copilot) - For AI-powered coding assistance
- [Azure SDK for JavaScript](https://github.com/Azure/azure-sdk-for-js) - For Azure service integration

---

**Note:** This is a community-driven project. It is not officially endorsed by Microsoft or GitHub.

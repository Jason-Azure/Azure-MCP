# Quick Start Guide

Get started with Azure-MCP in minutes! This guide provides the fastest path to using MCP servers with GitHub Copilot and Azure.

## 🚀 5-Minute Setup

### Step 1: Install Prerequisites (2 minutes)

```bash
# Check if Node.js is installed (need v18+)
node --version

# If not installed, install Node.js:
# macOS:
brew install node

# Windows:
# Download from https://nodejs.org/

# Linux:
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Step 2: Clone and Setup (1 minute)

```bash
# Clone the repository
git clone https://github.com/Jason-Azure/Azure-MCP.git
cd Azure-MCP

# Install dependencies
npm install

# Copy environment template
cp .env.example .env
```

### Step 3: Configure Azure (1 minute)

**Option A: Use Azure CLI (Recommended for development)**
```bash
# Install Azure CLI if needed
brew install azure-cli  # macOS
# or download from https://aka.ms/installazurecliwindows for Windows

# Login
az login

# Get your subscription ID
az account show --query id -o tsv
```

**Option B: Use Service Principal (For production)**
```bash
# Create service principal
az ad sp create-for-rbac --name "azure-mcp" --role Contributor

# Copy the output values to .env file
```

Edit `.env` file:
```bash
# Minimum required configuration
AZURE_SUBSCRIPTION_ID=your-subscription-id-here
```

### Step 4: Configure GitHub Copilot (1 minute)

Add to VS Code `settings.json` (⌘+, or Ctrl+,, then search for "settings.json"):

```json
{
  "github.copilot.mcp.servers": {
    "azure-mcp": {
      "command": "node",
      "args": ["/full/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "your-subscription-id"
      }
    }
  }
}
```

### Step 5: Test It! (30 seconds)

Open GitHub Copilot Chat in VS Code and try:

```
@copilot List my Azure resources
```

## 🎯 Common Tasks

### List Resources

```javascript
// Ask Copilot:
// "Show me all my Azure resources"

// Or use directly:
const { ResourceManagementClient } = require("@azure/arm-resources");
const { DefaultAzureCredential } = require("@azure/identity");

const credential = new DefaultAzureCredential();
const client = new ResourceManagementClient(credential, process.env.AZURE_SUBSCRIPTION_ID);

for await (const resource of client.resources.list()) {
  console.log(resource.name);
}
```

### Upload to Blob Storage

```javascript
// Ask Copilot:
// "Upload a file to Azure Blob Storage"

const { BlobServiceClient } = require("@azure/storage-blob");

const blobServiceClient = BlobServiceClient.fromConnectionString(
  process.env.AZURE_STORAGE_CONNECTION_STRING
);

const containerClient = blobServiceClient.getContainerClient("my-container");
await containerClient.createIfNotExists();

const blockBlobClient = containerClient.getBlockBlobClient("file.txt");
await blockBlobClient.uploadFile("./local-file.txt");
```

### List Virtual Machines

```javascript
// Ask Copilot:
// "Show me all my virtual machines with their status"

const { ComputeManagementClient } = require("@azure/arm-compute");
const { DefaultAzureCredential } = require("@azure/identity");

const credential = new DefaultAzureCredential();
const client = new ComputeManagementClient(credential, process.env.AZURE_SUBSCRIPTION_ID);

for await (const vm of client.virtualMachines.listAll()) {
  console.log(`${vm.name}: ${vm.location}`);
}
```

## 🔧 Troubleshooting

### Problem: "Cannot find module '@azure/identity'"

**Solution:**
```bash
npm install @azure/identity @azure/arm-resources
```

### Problem: "Authentication failed"

**Solution:**
```bash
# Make sure you're logged in with Azure CLI
az login

# Verify your subscription
az account show

# Or set environment variables for service principal
export AZURE_TENANT_ID="..."
export AZURE_CLIENT_ID="..."
export AZURE_CLIENT_SECRET="..."
```

### Problem: "MCP server not found"

**Solution:**
1. Check the path in VS Code settings is absolute
2. Verify Node.js is in your PATH: `which node`
3. Use full path: `"/usr/local/bin/node"` instead of `"node"`

### Problem: "No resources found"

**Solution:**
```bash
# Verify you're using the correct subscription
az account show

# Switch subscription if needed
az account set --subscription "your-subscription-id"
```

## 📚 Next Steps

1. **Explore Examples**: Check [EXAMPLES.md](./EXAMPLES.md) for detailed code examples
2. **Read the FAQ**: See [QA.md](./QA.md) for answers to common questions
3. **Full Documentation**: Read [README.md](./README.md) for comprehensive information
4. **Contribute**: See [CONTRIBUTING.md](./CONTRIBUTING.md) to contribute

## 💡 Pro Tips

### Tip 1: Use Descriptive Queries

Instead of:
```
@copilot Azure resources
```

Try:
```
@copilot List all my Azure resources in the westus region with their tags
```

### Tip 2: Cache Credentials

For faster development, authenticate once with Azure CLI:
```bash
az login
# Credentials are cached, no need for service principal in .env
```

### Tip 3: Use Environment-Specific Configs

Create multiple MCP server configs for different environments:
```json
{
  "github.copilot.mcp.servers": {
    "azure-mcp-dev": {
      "command": "node",
      "args": ["/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "dev-subscription-id"
      }
    },
    "azure-mcp-prod": {
      "command": "node",
      "args": ["/path/to/Azure-MCP/server.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "prod-subscription-id"
      }
    }
  }
}
```

### Tip 4: Enable Debug Logging

When troubleshooting, enable detailed logs:
```bash
export MCP_LOG_LEVEL=debug
export DEBUG=mcp:*
```

### Tip 5: Use Copilot for Complex Tasks

Let Copilot help with complex Azure operations:
```
@copilot Create an Azure Function that processes messages from a queue, 
saves results to Cosmos DB, and sends notifications via SendGrid
```

## 🎓 Learning Resources

- **Azure SDK Docs**: https://docs.microsoft.com/azure/developer/javascript/
- **MCP Specification**: https://modelcontextprotocol.io/
- **GitHub Copilot**: https://github.com/features/copilot
- **Azure Free Account**: https://azure.microsoft.com/free/

## ❓ Need Help?

- 📖 Check the [FAQ](./QA.md)
- 🐛 Report issues on [GitHub](https://github.com/Jason-Azure/Azure-MCP/issues)
- 💬 Ask questions in [Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)

---

**Ready to dive deeper?** Check out the [full README](./README.md) for comprehensive documentation!

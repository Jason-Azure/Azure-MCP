# MCP Installation Guide

## What is MCP?

MCP (Model Context Protocol) is an open protocol that enables seamless integration between LLM applications and external data sources and tools. It allows AI assistants like GitHub Copilot to access various services and data through a standardized protocol.

## Prerequisites

Before installing MCP servers, ensure your system meets the following requirements:

- **Node.js**: Version 18 or higher
- **npm** or **pnpm**: Latest version
- **GitHub Copilot**: Installed and activated GitHub Copilot extension
- **Supported IDE**: 
  - Visual Studio Code (recommended)
  - Visual Studio
  - Other editors that support GitHub Copilot

## Installation Steps

### 1. Install Node.js

If you haven't installed Node.js yet, download and install it from the official website:

```bash
# Check Node.js version
node --version

# Check npm version
npm --version
```

Ensure Node.js version is 18 or higher.

### 2. Install MCP Server

There are multiple ways to install MCP servers:

#### Option A: Global Installation with npm

```bash
# Install MCP server package
npm install -g @modelcontextprotocol/server-azure
```

#### Option B: Run with npx (Recommended for Testing)

```bash
# Run without installation
npx @modelcontextprotocol/server-azure
```

#### Option C: Clone and Build Locally

```bash
# Clone MCP server repository (replace URL with actual MCP server repository)
git clone https://github.com/<your-organization>/mcp-server-azure.git
cd mcp-server-azure

# Install dependencies
npm install

# Build project
npm run build
```

### 3. Configure GitHub Copilot to Use MCP

#### Configure in Visual Studio Code

1. Open VS Code settings (`Ctrl/Cmd + ,`)
2. Search for "GitHub Copilot"
3. Find "GitHub Copilot: Enable MCP" and enable it

#### Configure MCP Server

Create an `mcp.json` configuration file in your project root or user config directory:

```json
{
  "mcpServers": {
    "azure": {
      "command": "node",
      "args": ["/path/to/mcp-server-azure/dist/index.js"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "your-subscription-id",
        "AZURE_TENANT_ID": "your-tenant-id",
        "AZURE_CLIENT_ID": "your-client-id",
        "AZURE_CLIENT_SECRET": "your-client-secret"
      }
    }
  }
}
```

Or, if using npx:

```json
{
  "mcpServers": {
    "azure": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-azure"],
      "env": {
        "AZURE_SUBSCRIPTION_ID": "your-subscription-id",
        "AZURE_TENANT_ID": "your-tenant-id"
      }
    }
  }
}
```

### 4. Configure Azure Credentials

MCP servers require Azure credentials to access Azure resources. You can configure them in the following ways:

#### Option A: Environment Variables

Set environment variables in your shell configuration:

```bash
# Add to ~/.bashrc or ~/.zshrc
export AZURE_SUBSCRIPTION_ID="your-subscription-id"
export AZURE_TENANT_ID="your-tenant-id"
export AZURE_CLIENT_ID="your-client-id"
export AZURE_CLIENT_SECRET="your-client-secret"
```

#### Option B: Azure CLI

```bash
# Install Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Login to Azure
az login

# Set default subscription
az account set --subscription "your-subscription-id"
```

#### Option C: Use Managed Identity (Recommended for Azure VMs)

If you're running on an Azure VM or other services that support managed identity, MCP servers can automatically use managed identity.

### 5. Verify Installation

Restart VS Code or your IDE, then:

1. Open command palette (`Ctrl/Cmd + Shift + P`)
2. Run "GitHub Copilot: Show MCP Status"
3. Confirm that Azure MCP server shows as "Connected"

Or test in terminal:

```bash
# Test MCP server
node /path/to/mcp-server-azure/dist/index.js --test
```

## Available Azure MCP Servers

This repository collects the following Azure MCP servers:

### Azure Resource Manager
- Manage Azure resources
- Query resource status
- Execute resource operations

### Azure Storage
- Access Blob storage
- Manage files and containers
- Execute storage operations

### Azure AI Services
- Call Azure OpenAI
- Use Cognitive Services
- Access machine learning models

## Advanced Configuration

### Using Multiple MCP Servers

You can configure multiple servers in `mcp.json`:

```json
{
  "mcpServers": {
    "azure-resources": {
      "command": "node",
      "args": ["/path/to/azure-resources-server/dist/index.js"]
    },
    "azure-storage": {
      "command": "node",
      "args": ["/path/to/azure-storage-server/dist/index.js"]
    },
    "azure-ai": {
      "command": "node",
      "args": ["/path/to/azure-ai-server/dist/index.js"]
    }
  }
}
```

### Debug Mode

Enable debug output to troubleshoot issues:

```json
{
  "mcpServers": {
    "azure": {
      "command": "node",
      "args": ["/path/to/mcp-server-azure/dist/index.js"],
      "env": {
        "DEBUG": "mcp:*",
        "LOG_LEVEL": "debug"
      }
    }
  }
}
```

## Troubleshooting

### Issue 1: MCP Server Won't Start

**Symptom**: VS Code shows MCP server offline or unable to connect

**Solution**:
- Check if Node.js is properly installed (`node --version`)
- Verify paths in `mcp.json` are correct
- Check error logs in VS Code output panel
- Ensure firewall isn't blocking connections

### Issue 2: Azure Authentication Failure

**Symptom**: MCP server starts but cannot access Azure resources

**Solution**:
- Verify Azure credentials are correctly set
- Run `az login` to ensure Azure CLI is authenticated
- Check if service principal has necessary permissions
- Confirm subscription ID and tenant ID are correct

### Issue 3: GitHub Copilot Doesn't Recognize MCP

**Symptom**: Copilot doesn't show MCP features

**Solution**:
- Ensure GitHub Copilot extension is up to date
- Enable MCP support in settings
- Restart VS Code
- Check if `mcp.json` is in the correct location

### View Logs

```bash
# View VS Code output logs
# In VS Code: View > Output > Select "GitHub Copilot" or "MCP"

# Or run server directly to see logs
node /path/to/mcp-server-azure/dist/index.js
```

## Updating MCP Servers

Regularly update MCP servers to get the latest features and fixes:

```bash
# If using npm global install
npm update -g @modelcontextprotocol/server-azure

# If using local clone
cd mcp-server-azure
git pull
npm install
npm run build
```

## Security Best Practices

1. **Never hardcode credentials**: Always use environment variables or Azure managed identity
2. **Use principle of least privilege**: Only grant minimum Azure permissions needed by MCP server
3. **Rotate keys regularly**: Periodically update Azure service principal keys
4. **Enable audit logging**: Enable auditing in Azure to track MCP operations
5. **Use private networks**: When possible, access resources through Azure Private Link

## Next Steps

- Read [MCP Protocol Documentation](https://modelcontextprotocol.io/)
- Explore community-provided Azure MCP server implementations
- Join [Community Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)
- Contribute your own MCP servers

## Need Help?

If you encounter issues during installation:

1. Review the troubleshooting section above
2. Search or create a [GitHub Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
3. Participate in [Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)

## Related Resources

- [MCP Official Website](https://modelcontextprotocol.io/)
- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [Azure SDK for JavaScript](https://docs.microsoft.com/azure/developer/javascript/)
- [Azure Authentication Documentation](https://docs.microsoft.com/azure/active-directory/develop/)

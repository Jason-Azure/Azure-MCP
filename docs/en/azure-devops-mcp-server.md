# Azure DevOps MCP Server Installation Guide

> Azure DevOps MCP Server allows GitHub Copilot to interact with Azure DevOps work items, Pull Requests, builds, and releases.

## 📖 Overview

**Azure DevOps MCP Server** is Microsoft's latest MCP server (public preview), enabling GitHub Copilot to:

- 📋 Read and create work items
- 🔀 Manage Pull Requests
- 🏗️ View build status and history
- 🧪 Access test plans and results
- 🚀 Manage release pipelines
- 📚 Search and access documentation

**Features:**
- ✅ Runs locally, data stays secure
- ✅ Supports complete Azure DevOps API
- ✅ Simple configuration process
- ✅ Deep integration with VS Code

---

## 🔧 Prerequisites

Before starting, make sure you have:

1. **Visual Studio Code** (latest version)
   - Download: https://code.visualstudio.com/

2. **GitHub Copilot Extension**
   - Search and install "GitHub Copilot" in VS Code extension marketplace

3. **Node.js 18+**
   - Download: https://nodejs.org/
   - Verify installation: `node --version`

4. **Azure CLI**
   - Used for authentication
   - Download: https://learn.microsoft.com/cli/azure/install-azure-cli

5. **Azure DevOps Account**
   - Visit: https://dev.azure.com/
   - Ensure you have access to your organization

---

## 📥 Installation Steps

### Step 1: Install Azure CLI

**Windows:**
```powershell
# Download and run the installer
# https://aka.ms/installazurecliwindows
```

**macOS:**
```bash
brew update && brew install azure-cli
```

**Linux (Ubuntu/Debian):**
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

**Verify installation:**
```bash
az --version
```

### Step 2: Log in to Azure

```bash
az login
```

This will open a browser window for you to log in to your Azure account. After successful login, the command line will display your subscription list.

### Step 3: Create MCP Configuration File

Create or edit `.vscode/mcp.json` in your project root directory:

**Method 1: Using VS Code**
1. Right-click in the project root directory
2. Select "New Folder", create `.vscode` folder
3. Create `mcp.json` file in the `.vscode` folder

**Method 2: Using Command Line**
```bash
mkdir -p .vscode
cd .vscode
touch mcp.json
```

### Step 4: Add Azure DevOps MCP Configuration

Add the following content to the `mcp.json` file:

```json
{
  "inputs": [
    {
      "id": "ado_org",
      "type": "promptString",
      "description": "Azure DevOps organization name"
    }
  ],
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "${input:ado_org}"]
    }
  }
}
```

**Configuration explanation:**
- `ado_org`: Your Azure DevOps organization name (in the URL, like `dev.azure.com/your-org-name`)
- `npx -y`: Automatically install and run the latest version of Azure DevOps MCP server
- `${input:ado_org}`: VS Code will prompt you to enter the organization name at startup

**If you already know the organization name, you can write it directly in the configuration:**
```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "your-organization-name"]
    }
  }
}
```

---

## ✅ Verify Installation

### 1. Restart VS Code

After saving the configuration file, close and reopen VS Code.

### 2. Check MCP Server Status

1. Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
2. Type "MCP: Show Server Status"
3. Check if Azure DevOps MCP server shows as "Running"

### 3. Test Functionality

Open Copilot chat (`Ctrl+Shift+I` / `Cmd+Shift+I`) and try the following commands:

**Query work items:**
```
Show my recently created work items
```

**View Pull Requests:**
```
List all open Pull Requests in the current project
```

**Create work item:**
```
Create a new Bug work item with title "Fix login issue"
```

**Query build status:**
```
Show recent build status
```

---

## 🎯 Use Cases

### Scenario 1: Work Item Management

**View my work items:**
```
@azure-devops Show all work items assigned to me
```

**Create new task:**
```
@azure-devops Create a new task: Implement user login feature, priority high
```

**Update work item status:**
```
@azure-devops Update work item #12345 status to "Done"
```

### Scenario 2: Pull Request Management

**View PRs:**
```
@azure-devops List all Pull Requests I created
```

**Create PR:**
```
@azure-devops Create a Pull Request to merge feature/login to main
```

**Review PR:**
```
@azure-devops Show details and comments for PR #678
```

### Scenario 3: Build and Deployment

**View build history:**
```
@azure-devops Show status of the last 10 builds
```

**Trigger build:**
```
@azure-devops Trigger build on main branch
```

**View releases:**
```
@azure-devops Show recent release status
```

---

## ⚙️ Advanced Configuration

### Configure Multiple Organizations

If you need to access multiple Azure DevOps organizations:

```json
{
  "servers": {
    "azure-devops-org1": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "organization1"]
    },
    "azure-devops-org2": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "organization2"]
    }
  }
}
```

### Set Default Project

You can set a default project via environment variables:

```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "your-org"],
      "env": {
        "ADO_PROJECT": "your-project-name"
      }
    }
  }
}
```

---

## 🔍 Troubleshooting

### Issue 1: Cannot Connect to Azure DevOps

**Symptoms:** Authentication failure or cannot access organization

**Solutions:**
1. Confirm you're logged in to Azure CLI: `az login`
2. Check account permissions: `az account show`
3. Confirm organization name is correct
4. Try accessing manually: https://dev.azure.com/your-org

### Issue 2: MCP Server Fails to Start

**Symptoms:** VS Code shows MCP server error

**Solutions:**
1. Check Node.js version: `node --version` (requires 18+)
2. Clear npm cache: `npm cache clean --force`
3. Manually install server: `npm install -g @azure-devops/mcp`
4. Check error logs in VS Code output panel

---

## 🔗 Related Resources

- [Azure DevOps MCP Official Docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/mcp-server-overview)
- [Azure DevOps REST API Reference](https://learn.microsoft.com/en-us/rest/api/azure/devops/)
- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)

---

## 🆘 Get Help

- Check [Troubleshooting Guide](./troubleshooting.md)
- Check [FAQ](./faq.md)
- Submit an [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- Refer to [Official Documentation](https://learn.microsoft.com/en-us/azure/devops/mcp-server/)

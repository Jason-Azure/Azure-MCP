# Getting Started Guide

> This guide will help you configure and use GitHub Copilot's MCP servers step by step, from scratch. Even if you have no programming experience, you can easily get started.

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation Steps](#installation-steps)
3. [Configure Your First MCP Server](#configure-your-first-mcp-server)
4. [Testing and Verification](#testing-and-verification)
5. [Next Steps](#next-steps)

---

## Prerequisites

Before starting, please make sure you have the following:

### 1. Install Visual Studio Code

Visual Studio Code (VS Code for short) is a free code editor.

**Download and Install:**
1. Visit [VS Code official website](https://code.visualstudio.com/)
2. Click the download button and select the version for your operating system
3. After downloading, double-click the installer to install
4. Follow the installation wizard to complete the installation

### 2. Get GitHub Copilot Subscription

**If you don't have GitHub Copilot yet:**
1. Visit [GitHub Copilot page](https://github.com/features/copilot)
2. Click "Start free trial" or "Buy"
3. Log in to your GitHub account (if you don't have one, register first)
4. Complete the subscription process

**Subscription types:**
- Individual: Suitable for individual developers
- Business: Suitable for teams and enterprises

### 3. Install Node.js

Node.js is the required environment for running MCP servers.

**Installation steps:**
1. Visit [Node.js official website](https://nodejs.org/)
2. Download the LTS (Long Term Support) version (recommended version 18 or higher)
3. Double-click the installer and follow the wizard to complete the installation
4. Verify installation:
   - Open Command Prompt (Windows) or Terminal (Mac/Linux)
   - Type: `node --version`
   - If it shows a version number (like v18.x.x), the installation is successful

---

## Installation Steps

### Step 1: Install GitHub Copilot Extension in VS Code

1. Open VS Code
2. Click the "Extensions" icon in the left activity bar (or press `Ctrl+Shift+X` / `Cmd+Shift+X`)
3. Type "GitHub Copilot" in the search box
4. Find the "GitHub Copilot" extension (published by GitHub)
5. Click the "Install" button
6. After installation, VS Code will prompt you to log in to GitHub
7. Click "Sign in to GitHub" and complete authorization in the browser

### Step 2: Confirm Copilot is Working

1. Create a new file in VS Code (`Ctrl+N` / `Cmd+N`)
2. Save the file as `test.js`
3. Type: `// Write a function to calculate the sum of two numbers`
4. Wait a moment, Copilot should automatically suggest code
5. If you see code suggestions, Copilot is working properly

---

## Configure Your First MCP Server

We'll use **Azure DevOps MCP Server** as the first example because it's powerful and easy to configure.

### Step 1: Create Configuration File

1. In VS Code, open your project folder (if you don't have one, create a new folder)
2. Create a folder named `.vscode` in the project root directory (note the dot at the beginning)
3. Create a file named `mcp.json` in the `.vscode` folder

**Method 1: Via VS Code Interface**
- Right-click the project root directory in the Explorer
- Select "New Folder", enter `.vscode`
- Right-click the `.vscode` folder
- Select "New File", enter `mcp.json`

**Method 2: Via Command Line**
```bash
mkdir .vscode
cd .vscode
touch mcp.json
```

### Step 2: Add Configuration

Open the `mcp.json` file and copy the following content:

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
- `ado_org`: This is an input variable, VS Code will prompt you to enter the Azure DevOps organization name at startup
- `npx`: This command will automatically download and run the Azure DevOps MCP server
- `-y`: Indicates automatic confirmation of installation

### Step 3: Install Azure CLI (for authentication)

Azure DevOps MCP needs Azure CLI for authentication.

**Windows:**
1. Download [Azure CLI installer](https://aka.ms/installazurecliwindows)
2. Run the installer and complete the installation

**Mac:**
```bash
brew update && brew install azure-cli
```

**Linux:**
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### Step 4: Log in to Azure

Open Command Prompt or Terminal and enter:

```bash
az login
```

This will open a browser for you to log in to your Azure account. After completing the login, the command line will display your subscription information.

---

## Testing and Verification

### 1. Restart VS Code

After completing the configuration, please close and reopen VS Code to ensure the configuration takes effect.

### 2. Enable Copilot Agent Mode

1. In VS Code, press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac) to open the command palette
2. Type "Copilot: Enable Agent Mode"
3. Select the command and confirm

### 3. Test MCP Server

1. Open Copilot chat in VS Code (press `Ctrl+Shift+I` / `Cmd+Shift+I`)
2. Enter questions related to Azure DevOps, for example:
   - "Show my recent work items"
   - "List all Pull Requests in the current project"
   - "Create a new Bug work item"

If the MCP server is configured correctly, Copilot should be able to interact with Azure DevOps and return results.

---

## Next Steps

Congratulations! You have successfully configured your first MCP server. Next, you can:

### Explore More MCP Servers

- **[GitHub MCP Server](./github-mcp-server.md)** - Manage GitHub repositories, Issues, and PRs
- **[Browser MCP](../zh/browser-mcp.md)** - Browser automation and web testing (中文)
- **[Datadog MCP](../zh/datadog-mcp-server.md)** - Monitor and analyze system performance (中文)

### Learn Advanced Features

- **[Best Practices](../zh/best-practices.md)** - Optimize your MCP configuration (中文)
- **[Troubleshooting](../zh/troubleshooting.md)** - Solve common problems (中文)

### Customize Configuration

You can add multiple MCP servers in `mcp.json`. For example:

```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "your-org-name"]
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"]
    }
  }
}
```

---

## 💡 Tips

- **Be patient**: The first time you install, `npx` needs to download the MCP server, which may take a few minutes
- **Check network**: Make sure your network connection is working and can access npm and Azure services
- **View logs**: If you encounter problems, check the MCP server logs in VS Code's output panel
- **Update software**: Regularly update VS Code, Node.js, and Copilot extension for the best experience

---

## 🆘 Need Help?

- Check [FAQ](../zh/faq.md) (中文)
- Check [Troubleshooting Guide](../zh/troubleshooting.md) (中文)
- Submit an [Issue](https://github.com/Jason-Azure/Azure-MCP/issues) in this project

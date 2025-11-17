# Example MCP Server Configurations

This directory contains example configuration files for various MCP server setups.

## 📁 Files

### Simple Configurations

1. **azure-devops-simple.json** - Azure DevOps only
2. **github-simple.json** - GitHub only

### Combined Configurations

3. **multi-server.json** - Azure DevOps + GitHub + Browser
4. **complete-setup.json** - All servers (Azure DevOps + GitHub + Browser + Datadog)

## 🚀 How to Use

1. Choose the configuration that matches your needs
2. Copy the content to `.vscode/mcp.json` in your project
3. Update the values (organization names, tokens, etc.)
4. Restart VS Code

## 📝 Configuration Details

### Azure DevOps Simple

```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "your-org-name"]
    }
  }
}
```

**What to change:**
- Replace `your-org-name` with your Azure DevOps organization name

### GitHub Simple

```json
{
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${env:GITHUB_TOKEN}"
      }
    }
  }
}
```

**What to setup:**
1. Create a GitHub Personal Access Token
2. Set environment variable: `export GITHUB_TOKEN="your_token"`

### Multi-Server

Includes Azure DevOps, GitHub, and Browser MCP servers.

**Prerequisites:**
- Azure CLI installed and logged in (`az login`)
- GitHub token set as environment variable
- Node.js 18+

### Complete Setup

Includes all servers: Azure DevOps, GitHub, Browser, and Datadog.

**Additional Prerequisites:**
- Datadog API and App keys set as environment variables:
  ```bash
  export DATADOG_API_KEY="your_api_key"
  export DATADOG_APP_KEY="your_app_key"
  ```

## 🔐 Environment Variables

Create a `.env` file (don't commit to Git):

```bash
# GitHub
GITHUB_TOKEN=your_github_token

# Datadog
DATADOG_API_KEY=your_datadog_api_key
DATADOG_APP_KEY=your_datadog_app_key
```

Then source it:
```bash
source .env
```

## 💡 Tips

- Start with a simple configuration and add servers as needed
- Keep sensitive tokens in environment variables
- Use `.env.example` file for team sharing (without actual tokens)
- Test each server individually before combining

## 🔗 More Information

- [Getting Started Guide](../docs/en/getting-started.md)
- [Quick Reference](../docs/zh/quick-reference.md)
- [Best Practices](../docs/en/best-practices.md)

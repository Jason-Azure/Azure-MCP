# GitHub MCP Server Installation Guide

> GitHub MCP Server is the official MCP server that allows GitHub Copilot to interact with GitHub repositories, Issues, Pull Requests, and more.

## 📖 Overview

**GitHub MCP Server** provides 100+ tools, enabling Copilot to:

- 🔍 Search code, repositories, Issues, and PRs
- 📋 Manage Issues (create, update, close)
- 🔀 Manage Pull Requests (create, review, merge)
- 📊 View repository statistics and analytics
- 🏷️ Manage labels and milestones
- 👥 Manage collaborators and permissions
- 🔐 Integrate with GitHub Actions

**Features:**
- ✅ Officially supported, stable and reliable
- ✅ Comprehensive coverage of GitHub API
- ✅ Multiple authentication methods
- ✅ Real-time data synchronization

---

## 🔧 Prerequisites

1. **Visual Studio Code** (latest version)
2. **GitHub Copilot Extension**
3. **Node.js 18+**
4. **GitHub Account** and Personal Access Token

---

## 📥 Installation Steps

### Step 1: Create GitHub Personal Access Token

1. Log in to GitHub
2. Visit [Settings > Developer settings > Personal access tokens > Tokens (classic)](https://github.com/settings/tokens)
3. Click "Generate new token" > "Generate new token (classic)"
4. Configure:
   - **Note**: `MCP Server for Copilot`
   - **Expiration**: Choose expiration time
   - **Scopes**: Select the following permissions:
     - ✅ `repo` (full repository access)
     - ✅ `read:org` (read organization info)
     - ✅ `read:user` (read user info)
     - ✅ `workflow` (access GitHub Actions)
5. Click "Generate token"
6. **Important**: Copy the generated token and save it securely

### Step 2: Configure Environment Variable

**Windows:**
```powershell
# Temporary (current session only)
$env:GITHUB_TOKEN="your_token_here"

# Permanent
[System.Environment]::SetEnvironmentVariable('GITHUB_TOKEN', 'your_token_here', 'User')
```

**macOS/Linux:**
```bash
# Add to ~/.bashrc or ~/.zshrc
echo 'export GITHUB_TOKEN="your_token_here"' >> ~/.bashrc
source ~/.bashrc

# Or add to ~/.zshrc
echo 'export GITHUB_TOKEN="your_token_here"' >> ~/.zshrc
source ~/.zshrc
```

### Step 3: Create MCP Configuration File

Create `.vscode/mcp.json` in project root:

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

**Configuration explanation:**
- `@modelcontextprotocol/server-github`: Official GitHub MCP server package
- `${env:GITHUB_TOKEN}`: Reads GitHub token from environment variable

### Step 4: Restart VS Code

After saving the configuration, restart VS Code to load the MCP server.

---

## ✅ Verify Installation

Open Copilot chat and try the following commands:

**Search repositories:**
```
@github Search for Python machine learning repositories
```

**View Issues:**
```
@github List Issues I created in microsoft/vscode
```

**Create Issue:**
```
@github Create an Issue in owner/repo with title "Add new feature"
```

---

## 🎯 Use Cases

### Scenario 1: Repository Management

**Search code:**
```
@github Search for code containing "useState" in facebook/react repository
```

**View repository info:**
```
@github Show basic information and statistics for tensorflow/tensorflow
```

**List branches:**
```
@github List all branches in myorg/myrepo
```

### Scenario 2: Issue Management

**Query Issues:**
```
@github Show all open Issues labeled "bug" in vuejs/vue
```

**Create Issue:**
```
@github Create an Issue in current repository:
Title: Fix login page error
Description: Users cannot log in using Chrome browser
Labels: bug, high-priority
```

**Update Issue:**
```
@github Add comment to Issue #123: "Fixed, please test"
```

**Close Issue:**
```
@github Close Issue #456
```

### Scenario 3: Pull Request Management

**View PRs:**
```
@github List all open Pull Requests in microsoft/TypeScript
```

**Create PR:**
```
@github Create a Pull Request:
From: feature/new-ui
To: main
Title: Add new user interface
Description: Implemented brand new responsive UI design
```

**Review PR:**
```
@github Show all comments and reviews for PR #789
```

**Merge PR:**
```
@github Merge PR #789 using squash mode
```

---

## ⚙️ Advanced Configuration

### Configure Multiple Repository Access

```json
{
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${env:GITHUB_TOKEN}",
        "GITHUB_DEFAULT_REPO": "owner/repo"
      }
    }
  }
}
```

### Use GitHub Enterprise

If you use GitHub Enterprise Server:

```json
{
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${env:GITHUB_TOKEN}",
        "GITHUB_API_URL": "https://github.your-company.com/api/v3"
      }
    }
  }
}
```

---

## 🔍 Troubleshooting

### Issue 1: Authentication Failed

**Symptoms:** "Authentication failed" or "Bad credentials"

**Solutions:**
1. Check if environment variable is set correctly:
   ```bash
   # Windows
   echo $env:GITHUB_TOKEN
   
   # macOS/Linux
   echo $GITHUB_TOKEN
   ```
2. Confirm token has sufficient permissions
3. Check if token has expired
4. Regenerate token

### Issue 2: Cannot Access Private Repository

**Symptoms:** "Repository not found" or "404"

**Solutions:**
1. Confirm token includes `repo` permission
2. Check repository name is correct (format: `owner/repo`)
3. Confirm you have access to the repository

---

## 🔗 Related Resources

- [GitHub REST API Documentation](https://docs.github.com/en/rest)
- [Personal Access Token Guide](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [MCP Protocol Specification](https://modelcontextprotocol.io/)

---

## 🆘 Get Help

- Check [Troubleshooting Guide](./troubleshooting.md)
- Check [FAQ](./faq.md)
- Submit an [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- Refer to [GitHub Official Documentation](https://docs.github.com/)

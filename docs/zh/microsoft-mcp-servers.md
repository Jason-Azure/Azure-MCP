# Microsoft MCP Servers 安装指南

> Microsoft 提供了一系列 MCP 服务器，用于集成 Azure、Outlook、Teams 等服务。

## 📖 概述

**Microsoft MCP Servers** 包括：

- ☁️ **Azure Storage MCP** - 访问 Azure 存储
- 📧 **Outlook MCP** - 管理邮件和日历
- 💬 **Teams MCP** - Teams 消息和协作
- 🔐 **Azure Resources MCP** - 管理 Azure 资源

## 可用服务器

### 1. Azure Storage MCP

访问和管理 Azure Blob Storage、Files 等。

**配置示例：**
```json
{
  "servers": {
    "azure-storage": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@microsoft/mcp-azure-storage"]
    }
  }
}
```

### 2. Outlook MCP

管理邮件、日历和联系人。

**配置示例：**
```json
{
  "servers": {
    "outlook": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@microsoft/mcp-outlook"]
    }
  }
}
```

### 3. Teams MCP

发送消息、管理频道和会议。

**配置示例：**
```json
{
  "servers": {
    "teams": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@microsoft/mcp-teams"]
    }
  }
}
```

## 🔧 认证

所有 Microsoft MCP 服务器都需要 Azure AD 认证：

```bash
az login
```

## 🔗 相关资源

- [Microsoft MCP 官方博客](https://developer.microsoft.com/blog/10-microsoft-mcp-servers-to-accelerate-your-development-workflow)
- [Azure 文档](https://learn.microsoft.com/azure/)

---

**注意：** 本指南为简化版本。详细配置请参考官方文档。

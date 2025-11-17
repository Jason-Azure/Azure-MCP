# Qdrant Vector MCP Server 安装指南

> Qdrant Vector MCP Server 提供语义搜索和向量数据库功能，支持持久化记忆和检索增强生成（RAG）。

## 📖 概述

**Qdrant Vector MCP Server** 让 GitHub Copilot 能够：

- 🔍 执行语义搜索
- 💾 存储和检索向量嵌入
- 🧠 实现持久化记忆功能
- 📊 进行相似度搜索
- 🔗 支持 RAG（检索增强生成）应用

## 🔧 前置要求

1. Visual Studio Code（最新版本）
2. GitHub Copilot 扩展
3. Node.js 18+
4. Qdrant 实例（本地或云端）

## 📥 安装步骤

### 步骤 1：安装 Qdrant

**使用 Docker（推荐）：**
```bash
docker pull qdrant/qdrant
docker run -p 6333:6333 -p 6334:6334 qdrant/qdrant
```

**或使用 Qdrant Cloud：**
访问 [cloud.qdrant.io](https://cloud.qdrant.io/) 创建免费集群

### 步骤 2：配置 MCP 服务器

在 `.vscode/mcp.json` 中添加：

```json
{
  "servers": {
    "qdrant": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@qdrant/mcp-server"],
      "env": {
        "QDRANT_URL": "http://localhost:6333",
        "QDRANT_API_KEY": "${env:QDRANT_API_KEY}"
      }
    }
  }
}
```

## 🎯 使用场景

- 语义代码搜索
- 知识库检索
- 智能文档查询
- 上下文记忆

## 🔗 相关资源

- [Qdrant 官方文档](https://qdrant.tech/documentation/)
- [向量搜索指南](https://qdrant.tech/documentation/concepts/search/)

---

**注意：** 本指南为简化版本。完整文档正在编写中。

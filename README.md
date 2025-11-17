# GitHub Copilot MCP 服务器安装指南 / MCP Servers Installation Guide

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![GitHub issues](https://img.shields.io/github/issues/Jason-Azure/Azure-MCP)](https://github.com/Jason-Azure/Azure-MCP/issues)

> 为 GitHub Copilot 收集和整理可用的 MCP 服务器，提供简洁明了的安装和使用说明  
> Collection of MCP servers for GitHub Copilot with clear installation and usage instructions

[English](#english) | [中文](#中文)

---

## 中文

### 📚 什么是 MCP 服务器？

MCP（Model Context Protocol，模型上下文协议）服务器是一种新兴架构，让 AI 助手（如 GitHub Copilot）能够安全、灵活地与专业工具、API 和数据源进行交互。通过 MCP 服务器，Copilot 可以：

- 访问和管理工作项、代码仓库、Azure DevOps 等
- 执行实时数据查询和分析
- 自动化 DevOps 工作流程
- 与各种云服务和工具集成

**重要特性：**
- 🔒 **安全**：本地运行，敏感数据不离开您的环境
- 🔧 **可扩展**：支持多种工具和服务
- 🤖 **智能化**：AI 可以对真实数据执行操作，而不仅仅是代码建议
- ⚙️ **可定制**：支持项目、组织或用户级别的配置

### 📋 可用的 MCP 服务器列表

本项目整理了以下常用的 MCP 服务器：

#### 核心服务器
1. **[GitHub MCP Server](./docs/zh/github-mcp-server.md)** - GitHub 集成
2. **[Azure DevOps MCP Server](./docs/zh/azure-devops-mcp-server.md)** - Azure DevOps 集成 ⭐ 推荐
3. **[Browser MCP](./docs/zh/browser-mcp.md)** - 浏览器自动化

#### 实时智能和数据分析
4. **[Datadog MCP Server](./docs/zh/datadog-mcp-server.md)** - 监控和分析
5. **[Qdrant Vector MCP](./docs/zh/qdrant-mcp-server.md)** - 向量搜索和语义分析

#### Microsoft 生态系统
6. **[Microsoft MCP Servers](./docs/zh/microsoft-mcp-servers.md)** - Azure、Outlook、Teams 集成

#### 云服务
7. **[AWS Labs MCP](./docs/zh/aws-mcp-server.md)** - AWS 服务集成

### 🚀 快速开始

详细的入门指南请查看：**[快速开始指南](./docs/zh/getting-started.md)**

#### 基本要求
- ✅ Visual Studio Code（最新版本）
- ✅ GitHub Copilot 订阅（个人版或企业版）
- ✅ Node.js 18 或更高版本

#### 简单三步安装

1. **安装 VS Code 和 GitHub Copilot**
   - 下载并安装 [Visual Studio Code](https://code.visualstudio.com/)
   - 安装 [GitHub Copilot 扩展](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)

2. **配置 MCP 服务器**
   - 在项目根目录创建 `.vscode/mcp.json` 文件
   - 根据您需要的服务器添加配置

3. **启用并使用**
   - 重启 VS Code
   - 进入 Copilot 代理模式开始使用

### 📖 详细文档

- [快速开始指南](./docs/zh/getting-started.md) - 零基础入门教程
- [快速参考](./docs/zh/quick-reference.md) - 常用配置和命令速查 ⚡
- [配置示例](./examples/) - 开箱即用的配置文件
- [常见问题解答](./docs/zh/faq.md) - 常见问题和解决方案
- [故障排除](./docs/zh/troubleshooting.md) - 遇到问题时的解决方法
- [最佳实践](./docs/zh/best-practices.md) - 使用建议和技巧

### 💡 使用场景示例

- **Azure DevOps 集成**：在 VS Code 中直接管理工作项、PR 和构建
- **实时监控**：通过 Datadog 查询系统性能和日志
- **代码仓库管理**：使用 GitHub MCP 自动化 issue 和 PR 流程
- **浏览器自动化**：通过 Browser MCP 进行网页测试和数据提取

### 🤝 贡献

欢迎提交 Issue 和 Pull Request！如果您发现新的有用的 MCP 服务器，请随时添加。

查看 [贡献指南](./CONTRIBUTING.md) 了解如何参与项目。

### 📝 许可证

MIT License

---

## English

### 📚 What are MCP Servers?

MCP (Model Context Protocol) servers are an emerging architecture that enables AI assistants like GitHub Copilot to securely and flexibly interact with specialized tools, APIs, and data sources. With MCP servers, Copilot can:

- Access and manage work items, repositories, Azure DevOps, and more
- Execute real-time data queries and analytics
- Automate DevOps workflows
- Integrate with various cloud services and tools

**Key Features:**
- 🔒 **Secure**: Runs locally, keeping sensitive data in your environment
- 🔧 **Extensible**: Supports multiple tools and services
- 🤖 **Intelligent**: AI can act on real data, not just code suggestions
- ⚙️ **Customizable**: Per-project, per-org, or per-user configuration

### 📋 Available MCP Servers

This project catalogs the following commonly used MCP servers:

#### Core Servers
1. **[GitHub MCP Server](./docs/en/github-mcp-server.md)** - GitHub integration
2. **[Azure DevOps MCP Server](./docs/en/azure-devops-mcp-server.md)** - Azure DevOps integration ⭐ Recommended
3. **[Browser MCP](./docs/en/browser-mcp.md)** - Browser automation

#### Real-time Intelligence & Data Analytics
4. **[Datadog MCP Server](./docs/en/datadog-mcp-server.md)** - Monitoring and analytics
5. **[Qdrant Vector MCP](./docs/en/qdrant-mcp-server.md)** - Vector search and semantic analysis

#### Microsoft Ecosystem
6. **[Microsoft MCP Servers](./docs/en/microsoft-mcp-servers.md)** - Azure, Outlook, Teams integration

#### Cloud Services
7. **[AWS Labs MCP](./docs/en/aws-mcp-server.md)** - AWS services integration

### 🚀 Quick Start

For detailed setup instructions, see: **[Getting Started Guide](./docs/en/getting-started.md)**

#### Prerequisites
- ✅ Visual Studio Code (latest version)
- ✅ GitHub Copilot subscription (Individual or Business)
- ✅ Node.js 18 or higher

#### Three Simple Steps

1. **Install VS Code and GitHub Copilot**
   - Download and install [Visual Studio Code](https://code.visualstudio.com/)
   - Install [GitHub Copilot extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)

2. **Configure MCP Server**
   - Create `.vscode/mcp.json` in your project root
   - Add configuration based on your needs

3. **Enable and Use**
   - Restart VS Code
   - Enter Copilot agent mode to start using

### 📖 Documentation

- [Getting Started Guide](./docs/en/getting-started.md) - Beginner-friendly tutorial
- [Quick Reference](./docs/zh/quick-reference.md) - Command and config cheat sheet ⚡
- [Example Configurations](./examples/) - Ready-to-use config files
- [FAQ](./docs/en/faq.md) - Frequently asked questions
- [Troubleshooting](./docs/en/troubleshooting.md) - Problem-solving guide
- [Best Practices](./docs/en/best-practices.md) - Tips and recommendations

### 💡 Use Case Examples

- **Azure DevOps Integration**: Manage work items, PRs, and builds directly in VS Code
- **Real-time Monitoring**: Query system performance and logs via Datadog
- **Repository Management**: Automate issues and PR workflows with GitHub MCP
- **Browser Automation**: Web testing and data extraction with Browser MCP

### 🤝 Contributing

Issues and Pull Requests are welcome! If you discover new useful MCP servers, feel free to add them.

See [Contributing Guide](./CONTRIBUTING.md) for how to get involved.

### 📝 License

MIT License

---

## 🔗 Useful Links

- [Official MCP Documentation](https://modelcontextprotocol.io/)
- [GitHub MCP Registry](https://github.blog/ai-and-ml/generative-ai/how-to-find-install-and-manage-mcp-servers-with-the-github-mcp-registry/)
- [VS Code MCP Documentation](https://code.visualstudio.com/docs/copilot/customization/mcp-servers)
- [Azure DevOps MCP Official Docs](https://learn.microsoft.com/en-us/azure/devops/mcp-server/mcp-server-overview)

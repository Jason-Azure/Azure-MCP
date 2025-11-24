# OneNote MCP Servers for GitHub Copilot

这是一份支持在 GitHub Copilot 和其他 AI 助手中使用的 OneNote MCP 服务器列表。

This is a collection of OneNote MCP (Model Context Protocol) servers that work with GitHub Copilot and other AI assistants.

## 推荐服务器 (Recommended Servers)

### 1. danosb/onenote-mcp ⭐ 推荐 (Recommended)

**GitHub**: [danosb/onenote-mcp](https://github.com/danosb/onenote-mcp)
- **Stars**: 25+ ⭐
- **Language**: JavaScript
- **Authentication**: 设备代码流 (Device Code Flow) - 无需 Azure 设置

#### 特点 (Features):
- ✅ 无需 Azure 应用注册 (No Azure setup required)
- ✅ 简单的设备代码认证 (Simple device code authentication)
- ✅ 支持 Cursor 和 Claude Desktop
- ✅ 完整的 OneNote API 支持 (Full OneNote API support)
- ✅ 可以创建、读取、搜索页面

#### 安装 (Installation):

```bash
git clone https://github.com/danosb/onenote-mcp.git
cd onenote-mcp
npm install

# 下载并构建 TypeScript SDK
git clone https://github.com/modelcontextprotocol/typescript-sdk.git
cd typescript-sdk
npm install
npm run build
cd ..

# 启动服务器
npm start
```

#### Cursor 配置 (Cursor Configuration):

在 Cursor 设置中添加 MCP 服务器:

```json
{
  "mcpServers": {
    "onenote": {
      "command": "node",
      "args": ["/absolute/path/to/your/onenote-mcp.mjs"],
      "env": {}
    }
  }
}
```

#### 使用示例 (Usage Examples):

```
你: 显示我的 OneNote 笔记本
AI: (使用 listNotebooks) 找到 3 个笔记本: "工作", "个人", "项目"

你: 在项目笔记本中创建一个新页面
AI: (使用 createPage) 已在您的项目笔记本中创建新页面

你: 搜索关于机器学习的笔记
AI: (使用 searchPages) 找到 5 个与机器学习相关的页面...
```

---

### 2. ZubeidHendricks/azure-onenote-mcp-server

**GitHub**: [ZubeidHendricks/azure-onenote-mcp-server](https://github.com/ZubeidHendricks/azure-onenote-mcp-server)
- **Stars**: 19+ ⭐
- **Language**: TypeScript
- **NPM Package**: `mcp-server-onenote`
- **Authentication**: Azure 应用注册 (Azure App Registration)

#### 特点 (Features):
- ✅ 发布在 NPM 注册表
- ✅ 完整的笔记本、节和页面管理
- ✅ Docker 支持
- ✅ 企业级认证

#### 安装 (Installation):

**通过 NPM 安装 (From NPM)**:
```bash
npm install -g mcp-server-onenote
```

**通过 Smithery 安装 (Using Smithery)**:
```bash
npx -y @smithery/cli install @modelcontextprotocol/server-onenote --client claude
```

#### Claude Desktop 配置:

```json
{
  "mcpServers": {
    "onenote": {
      "command": "mcp-server-onenote",
      "env": {
        "AZURE_TENANT_ID": "<YOUR_TENANT_ID>",
        "AZURE_CLIENT_ID": "<YOUR_CLIENT_ID>",
        "AZURE_CLIENT_SECRET": "<YOUR_CLIENT_SECRET>"
      }
    }
  }
}
```

#### Azure 设置步骤 (Azure Setup):
1. 访问 Azure Portal 并导航到应用注册
2. 创建新的注册
3. 添加 Microsoft Graph API 权限:
   - Notes.ReadWrite.All
   - Notes.Read.All
4. 创建客户端密钥
5. 复制租户 ID、客户端 ID 和客户端密钥用于配置

---

### 3. rajvirtual/MCP-Servers (OneNote)

**GitHub**: [rajvirtual/MCP-Servers/onenote](https://github.com/rajvirtual/MCP-Servers)
- **Stars**: 14+ ⭐
- **Language**: TypeScript
- **Authentication**: Azure MSAL 设备代码流

#### 特点 (Features):
- ✅ 设备代码流认证
- ✅ Docker 支持
- ✅ 详细的文档和演示
- ✅ 支持 HTML 内容转换

#### 安装 (Installation):

```bash
git clone https://github.com/rajvirtual/MCP-Servers.git
cd MCP-Servers/onenote
npm install
npm run build
```

#### 配置 Claude Desktop:

```json
{
  "mcpServers": {
    "onenote": {
      "command": "npm",
      "args": ["start"],
      "cwd": "/path/to/MCP-Servers/onenote",
      "env": {
        "CLIENT_ID": "your-azure-client-id"
      }
    }
  }
}
```

---

### 4. OfficeMCP/OfficeMCP

**GitHub**: [OfficeMCP/OfficeMCP](https://github.com/OfficeMCP/OfficeMCP)
- **Stars**: 31+ ⭐
- **Language**: Python
- **Platform**: 仅 Windows (Windows Only)
- **Authentication**: COM 接口 (不需要 Azure)

#### 特点 (Features):
- ✅ 支持所有 Microsoft Office 应用 (Word, Excel, PowerPoint, OneNote, Visio 等)
- ✅ 通过 COM 接口直接控制
- ✅ Python 代码执行功能
- ✅ 支持 WPS Office
- ⚠️ 仅限 Windows 系统

#### 安装 (Installation):

**要求 (Requirements)**:
- Windows 操作系统
- Python 3.1+
- uv 包管理器

```bash
pip install uv
```

#### MCP 配置 (stdio 模式):

```json
{
  "mcpServers": {
    "OfficeMCP": {
      "type": "stdio",
      "command": "uvx",
      "args": ["officemcp"]
    }
  }
}
```

#### MCP 配置 (SSE 服务器模式):

启动服务器:
```bash
uvx officemcp sse --port 8888 --host 127.0.0.1
```

配置:
```json
{
  "servers": {
    "OfficeMCP": {
      "url": "http://127.0.0.1:8888/sse"
    }
  }
}
```

---

## 其他 OneNote MCP 服务器 (Other OneNote MCP Servers)

以下是其他可用的 OneNote MCP 服务器选项:

### 5. swax/OneNoteMCP
- **GitHub**: [swax/OneNoteMCP](https://github.com/swax/OneNoteMCP)
- **Stars**: 2 ⭐
- **Language**: TypeScript

### 6. hwillGIT/onenote-mcp
- **GitHub**: [hwillGIT/onenote-mcp](https://github.com/hwillGIT/onenote-mcp)
- **Stars**: 4 ⭐
- **Language**: JavaScript
- **特点**: 使用 browser-use 自动化的 OneNote Web 应用

### 7. FRESHSK/onenote-mcp-server
- **GitHub**: [FRESHSK/onenote-mcp-server](https://github.com/FRESHSK/onenote-mcp-server)
- **Stars**: 2 ⭐
- **Language**: JavaScript
- **特点**: 为 Claude Desktop 集成

### 8. Softeria/ms-365-mcp-server
- **GitHub**: [Softeria/ms-365-mcp-server](https://github.com/Softeria/ms-365-mcp-server)
- **Stars**: 348+ ⭐
- **Language**: TypeScript
- **特点**: 通过 Graph API 访问所有 Microsoft 365 服务（包括 OneNote）

---

## 服务器对比 (Comparison Table)

| 服务器 (Server) | Stars | 语言 (Language) | 认证 (Auth) | 平台 (Platform) | 易用性 (Ease of Use) |
|----------------|-------|----------------|------------|----------------|-------------------|
| danosb/onenote-mcp | 25+ | JavaScript | 设备代码 | 跨平台 | ⭐⭐⭐⭐⭐ 最简单 |
| azure-onenote-mcp-server | 19+ | TypeScript | Azure App | 跨平台 | ⭐⭐⭐ 需要 Azure |
| rajvirtual/MCP-Servers | 14+ | TypeScript | Azure MSAL | 跨平台 | ⭐⭐⭐⭐ |
| OfficeMCP | 31+ | Python | COM | 仅 Windows | ⭐⭐⭐⭐ |
| ms-365-mcp-server | 348+ | TypeScript | Graph API | 跨平台 | ⭐⭐⭐ 功能最全 |

---

## 选择指南 (Selection Guide)

### 🎯 最简单快速上手 (Easiest to Get Started)
➡️ **danosb/onenote-mcp**
- 无需 Azure 设置
- 设备代码认证
- 详细的文档

### 🏢 企业使用 (Enterprise Use)
➡️ **ZubeidHendricks/azure-onenote-mcp-server**
- NPM 包
- Azure 认证
- 生产就绪

### 🖥️ Windows 本地控制 (Windows Native Control)
➡️ **OfficeMCP/OfficeMCP**
- 直接 COM 控制
- 支持所有 Office 应用
- 无需云认证

### 🌐 完整 Microsoft 365 集成 (Full M365 Integration)
➡️ **Softeria/ms-365-mcp-server**
- 访问所有 M365 服务
- 最活跃的开发
- 最多的 stars

---

## 常见问题 (FAQ)

### Q: 哪个服务器最适合 GitHub Copilot?
**A**: 所有这些服务器都与 GitHub Copilot 兼容。如果您想要最简单的设置，推荐使用 **danosb/onenote-mcp**。

### Q: 我需要 Azure 账户吗?
**A**: 这取决于您选择的服务器:
- **不需要 Azure**: danosb/onenote-mcp, OfficeMCP
- **需要 Azure**: azure-onenote-mcp-server, rajvirtual/MCP-Servers

### Q: 这些服务器安全吗?
**A**: 是的，它们使用官方的 Microsoft 认证方法:
- OAuth 设备代码流
- Azure AD 认证
- COM 接口（本地）

### Q: 可以同时使用多个服务器吗?
**A**: 可以！您可以在 MCP 配置中添加多个服务器，只需使用不同的名称。

---

## 贡献 (Contributing)

如果您发现了其他优秀的 OneNote MCP 服务器，欢迎提交 PR！

If you find other great OneNote MCP servers, feel free to submit a PR!

---

## 许可证 (License)

本文档采用 MIT 许可证。各个 MCP 服务器请参考其各自的许可证。

This document is licensed under MIT. Please refer to individual MCP servers for their licenses.

---

## 相关资源 (Related Resources)

- [Model Context Protocol Documentation](https://modelcontextprotocol.io)
- [GitHub Copilot Documentation](https://docs.github.com/copilot)
- [Microsoft Graph API - OneNote](https://learn.microsoft.com/graph/onenote-concept-overview)
- [Awesome MCP Servers](https://github.com/PipedreamHQ/awesome-mcp-servers)

# MCP 安装指南

## 什么是 MCP？

MCP (Model Context Protocol) 是一个开放协议，用于在 LLM 应用与外部数据源和工具之间实现无缝集成。它允许 AI 助手（如 GitHub Copilot）通过标准化协议访问各种服务和数据。

## 前置要求

在安装 MCP 服务器之前，请确保您的系统满足以下要求：

- **Node.js**: 版本 18 或更高
- **npm** 或 **pnpm**: 最新版本
- **GitHub Copilot**: 已安装并激活的 GitHub Copilot 扩展
- **支持的 IDE**: 
  - Visual Studio Code (推荐)
  - Visual Studio
  - 其他支持 GitHub Copilot 的编辑器

## 安装步骤

### 1. 安装 Node.js

如果您还没有安装 Node.js，请从官方网站下载并安装：

```bash
# 检查 Node.js 版本
node --version

# 检查 npm 版本
npm --version
```

确保 Node.js 版本为 18 或更高。

### 2. 安装 MCP 服务器

有多种方式可以安装 MCP 服务器：

#### 方式 A: 使用 npm 全局安装

```bash
# 安装 MCP 服务器包
npm install -g @modelcontextprotocol/server-azure
```

#### 方式 B: 使用 npx 运行（推荐用于测试）

```bash
# 无需安装，直接运行
npx @modelcontextprotocol/server-azure
```

#### 方式 C: 克隆并本地构建

```bash
# 克隆 MCP 服务器仓库（将 URL 替换为实际的 MCP 服务器仓库地址）
git clone https://github.com/<your-organization>/mcp-server-azure.git
cd mcp-server-azure

# 安装依赖
npm install

# 构建项目
npm run build
```

### 3. 配置 GitHub Copilot 以使用 MCP

#### 在 Visual Studio Code 中配置

1. 打开 VS Code 设置（`Ctrl/Cmd + ,`）
2. 搜索 "GitHub Copilot"
3. 找到 "GitHub Copilot: Enable MCP" 并启用

#### 配置 MCP 服务器

在您的项目根目录或用户配置目录中创建 `mcp.json` 配置文件：

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

或者，如果使用 npx：

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

### 4. 配置 Azure 凭据

MCP 服务器需要 Azure 凭据才能访问 Azure 资源。您可以通过以下方式配置：

#### 方式 A: 环境变量

在您的 shell 配置文件中设置环境变量：

```bash
# 在 ~/.bashrc 或 ~/.zshrc 中添加
export AZURE_SUBSCRIPTION_ID="your-subscription-id"
export AZURE_TENANT_ID="your-tenant-id"
export AZURE_CLIENT_ID="your-client-id"
export AZURE_CLIENT_SECRET="your-client-secret"
```

#### 方式 B: Azure CLI

```bash
# 安装 Azure CLI
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# 登录 Azure
az login

# 设置默认订阅
az account set --subscription "your-subscription-id"
```

#### 方式 C: 使用托管身份（推荐用于 Azure VM）

如果您在 Azure VM 或其他支持托管身份的服务上运行，MCP 服务器可以自动使用托管身份。

### 5. 验证安装

重启 VS Code 或您的 IDE，然后：

1. 打开命令面板（`Ctrl/Cmd + Shift + P`）
2. 运行 "GitHub Copilot: Show MCP Status"
3. 确认 Azure MCP 服务器显示为"已连接"

或者在终端中测试：

```bash
# 测试 MCP 服务器
node /path/to/mcp-server-azure/dist/index.js --test
```

## 可用的 Azure MCP 服务器

此仓库收集了以下 Azure MCP 服务器：

### Azure Resource Manager
- 管理 Azure 资源
- 查询资源状态
- 执行资源操作

### Azure Storage
- 访问 Blob 存储
- 管理文件和容器
- 执行存储操作

### Azure AI Services
- 调用 Azure OpenAI
- 使用认知服务
- 访问机器学习模型

## 高级配置

### 使用多个 MCP 服务器

您可以在 `mcp.json` 中配置多个服务器：

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

### 调试模式

启用调试输出以排查问题：

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

## 故障排查

### 问题 1: MCP 服务器无法启动

**症状**: VS Code 显示 MCP 服务器离线或无法连接

**解决方案**:
- 检查 Node.js 是否正确安装（`node --version`）
- 验证 `mcp.json` 中的路径是否正确
- 查看 VS Code 输出面板的错误日志
- 确保防火墙没有阻止连接

### 问题 2: Azure 认证失败

**症状**: MCP 服务器启动但无法访问 Azure 资源

**解决方案**:
- 验证 Azure 凭据是否正确设置
- 运行 `az login` 确保 Azure CLI 已认证
- 检查服务主体是否具有必要的权限
- 确认订阅 ID 和租户 ID 是否正确

### 问题 3: GitHub Copilot 无法识别 MCP

**症状**: Copilot 不显示 MCP 功能

**解决方案**:
- 确保 GitHub Copilot 扩展是最新版本
- 在设置中启用 MCP 支持
- 重启 VS Code
- 检查 `mcp.json` 是否在正确的位置

### 查看日志

```bash
# 查看 VS Code 输出日志
# 在 VS Code 中: View > Output > 选择 "GitHub Copilot" 或 "MCP"

# 或直接运行服务器查看日志
node /path/to/mcp-server-azure/dist/index.js
```

## 更新 MCP 服务器

定期更新 MCP 服务器以获取最新功能和修复：

```bash
# 如果使用 npm 全局安装
npm update -g @modelcontextprotocol/server-azure

# 如果使用本地克隆
cd mcp-server-azure
git pull
npm install
npm run build
```

## 安全最佳实践

1. **不要在代码中硬编码凭据**: 始终使用环境变量或 Azure 托管身份
2. **使用最小权限原则**: 仅授予 MCP 服务器所需的最小 Azure 权限
3. **定期轮换密钥**: 定期更新 Azure 服务主体密钥
4. **启用审计日志**: 在 Azure 中启用审计以跟踪 MCP 操作
5. **使用专用网络**: 如可能，通过 Azure Private Link 访问资源

## 后续步骤

- 阅读 [MCP 协议文档](https://modelcontextprotocol.io/)
- 探索社区提供的 Azure MCP 服务器实现
- 加入 [社区讨论](https://github.com/Jason-Azure/Azure-MCP/discussions)
- 贡献您自己的 MCP 服务器

## 需要帮助？

如果您在安装过程中遇到问题：

1. 查看上面的故障排查部分
2. 搜索或创建 [GitHub Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
3. 参与 [讨论区](https://github.com/Jason-Azure/Azure-MCP/discussions)

## 相关资源

- [MCP 官方网站](https://modelcontextprotocol.io/)
- [GitHub Copilot 文档](https://docs.github.com/copilot)
- [Azure SDK for JavaScript](https://docs.microsoft.com/azure/developer/javascript/)
- [Azure 认证文档](https://docs.microsoft.com/azure/active-directory/develop/)

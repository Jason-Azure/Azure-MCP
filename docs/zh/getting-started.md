# 快速开始指南

> 本指南将帮助您从零开始，一步步配置和使用 GitHub Copilot 的 MCP 服务器。即使您没有编程经验，也能轻松上手。

## 📋 目录

1. [前置准备](#前置准备)
2. [安装步骤](#安装步骤)
3. [配置您的第一个 MCP 服务器](#配置您的第一个-mcp-服务器)
4. [测试和验证](#测试和验证)
5. [下一步](#下一步)

---

## 前置准备

在开始之前，请确保您已经准备好以下内容：

### 1. 安装 Visual Studio Code

Visual Studio Code（简称 VS Code）是一个免费的代码编辑器。

**下载和安装：**
1. 访问 [VS Code 官网](https://code.visualstudio.com/)
2. 点击下载按钮，选择适合您操作系统的版本
3. 下载完成后，双击安装包进行安装
4. 按照安装向导完成安装

### 2. 获取 GitHub Copilot 订阅

**如果您还没有 GitHub Copilot：**
1. 访问 [GitHub Copilot 页面](https://github.com/features/copilot)
2. 点击"开始免费试用"或"购买"
3. 登录您的 GitHub 账户（如果没有，需要先注册）
4. 完成订阅流程

**订阅类型：**
- 个人版：适合个人开发者
- 企业版：适合团队和企业使用

### 3. 安装 Node.js

Node.js 是运行 MCP 服务器所必需的环境。

**安装步骤：**
1. 访问 [Node.js 官网](https://nodejs.org/)
2. 下载 LTS（长期支持）版本（推荐版本 18 或更高）
3. 双击安装包，按照向导完成安装
4. 验证安装：
   - 打开命令提示符（Windows）或终端（Mac/Linux）
   - 输入：`node --version`
   - 如果显示版本号（如 v18.x.x），说明安装成功

---

## 安装步骤

### 步骤 1：在 VS Code 中安装 GitHub Copilot 扩展

1. 打开 VS Code
2. 点击左侧活动栏的"扩展"图标（或按 `Ctrl+Shift+X` / `Cmd+Shift+X`）
3. 在搜索框中输入 "GitHub Copilot"
4. 找到 "GitHub Copilot" 扩展（由 GitHub 发布）
5. 点击"安装"按钮
6. 安装完成后，VS Code 会提示您登录 GitHub
7. 点击"登录到 GitHub"，在浏览器中完成授权

### 步骤 2：确认 Copilot 正常工作

1. 在 VS Code 中创建一个新文件（`Ctrl+N` / `Cmd+N`）
2. 将文件保存为 `test.js`
3. 输入：`// 写一个函数计算两个数的和`
4. 等待片刻，Copilot 应该会自动建议代码
5. 如果看到代码建议，说明 Copilot 工作正常

---

## 配置您的第一个 MCP 服务器

我们将使用 **Azure DevOps MCP Server** 作为第一个示例，因为它功能强大且易于配置。

### 步骤 1：创建配置文件

1. 在 VS Code 中，打开您的项目文件夹（如果没有，可以创建一个新文件夹）
2. 在项目根目录创建一个名为 `.vscode` 的文件夹（注意前面有个点）
3. 在 `.vscode` 文件夹中创建一个名为 `mcp.json` 的文件

**方法一：通过 VS Code 界面**
- 在资源管理器中右键点击项目根目录
- 选择"新建文件夹"，输入 `.vscode`
- 右键点击 `.vscode` 文件夹
- 选择"新建文件"，输入 `mcp.json`

**方法二：通过命令行**
```bash
mkdir .vscode
cd .vscode
touch mcp.json
```

### 步骤 2：添加配置

打开 `mcp.json` 文件，复制以下内容：

```json
{
  "inputs": [
    {
      "id": "ado_org",
      "type": "promptString",
      "description": "Azure DevOps 组织名称"
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

**配置说明：**
- `ado_org`：这是一个输入变量，VS Code 会在启动时提示您输入 Azure DevOps 组织名称
- `npx`：这个命令会自动下载并运行 Azure DevOps MCP 服务器
- `-y`：表示自动确认安装

### 步骤 3：安装 Azure CLI（用于身份验证）

Azure DevOps MCP 需要 Azure CLI 进行身份验证。

**Windows：**
1. 下载 [Azure CLI 安装程序](https://aka.ms/installazurecliwindows)
2. 运行安装程序并完成安装

**Mac：**
```bash
brew update && brew install azure-cli
```

**Linux：**
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

### 步骤 4：登录 Azure

打开命令提示符或终端，输入：

```bash
az login
```

这将打开浏览器，让您登录到 Azure 账户。完成登录后，命令行会显示您的订阅信息。

---

## 测试和验证

### 1. 重启 VS Code

完成配置后，请关闭并重新打开 VS Code，以确保配置生效。

### 2. 启用 Copilot 代理模式

1. 在 VS Code 中按 `Ctrl+Shift+P`（Windows/Linux）或 `Cmd+Shift+P`（Mac）打开命令面板
2. 输入 "Copilot: Enable Agent Mode"
3. 选择该命令并确认

### 3. 测试 MCP 服务器

1. 在 VS Code 中打开 Copilot 聊天（按 `Ctrl+Shift+I` / `Cmd+Shift+I`）
2. 输入与 Azure DevOps 相关的问题，例如：
   - "显示我最近的工作项"
   - "列出当前项目的所有 Pull Request"
   - "创建一个新的 Bug 工作项"

如果 MCP 服务器配置正确，Copilot 应该能够与 Azure DevOps 交互并返回结果。

---

## 下一步

恭喜！您已经成功配置了第一个 MCP 服务器。接下来您可以：

### 探索更多 MCP 服务器

- **[GitHub MCP Server](./github-mcp-server.md)** - 管理 GitHub 仓库、Issue 和 PR
- **[Browser MCP](./browser-mcp.md)** - 浏览器自动化和网页测试
- **[Datadog MCP](./datadog-mcp-server.md)** - 监控和分析系统性能

### 学习高级功能

- **[最佳实践](./best-practices.md)** - 优化您的 MCP 配置
- **[故障排除](./troubleshooting.md)** - 解决常见问题

### 自定义配置

您可以在 `mcp.json` 中添加多个 MCP 服务器。例如：

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

## 💡 提示

- **保持耐心**：第一次安装时，`npx` 需要下载 MCP 服务器，可能需要几分钟
- **检查网络**：确保您的网络连接正常，能够访问 npm 和 Azure 服务
- **查看日志**：如果遇到问题，在 VS Code 的输出面板中查看 MCP 服务器的日志
- **更新软件**：定期更新 VS Code、Node.js 和 Copilot 扩展以获得最佳体验

---

## 🆘 需要帮助？

- 查看 [常见问题解答](./faq.md)
- 查看 [故障排除指南](./troubleshooting.md)
- 在本项目提交 [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)

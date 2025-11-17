# Azure DevOps MCP Server 安装指南

> Azure DevOps MCP Server 让 GitHub Copilot 能够与 Azure DevOps 工作项、Pull Request、构建和发布进行交互。

## 📖 概述

**Azure DevOps MCP Server** 是微软最新推出的 MCP 服务器（公开预览版），它使 GitHub Copilot 能够：

- 📋 读取和创建工作项（Work Items）
- 🔀 管理 Pull Request
- 🏗️ 查看构建状态和历史
- 🧪 访问测试计划和结果
- 🚀 管理发布管道
- 📚 搜索和访问文档

**特点：**
- ✅ 本地运行，数据安全
- ✅ 支持完整的 Azure DevOps API
- ✅ 简单的配置过程
- ✅ 与 VS Code 深度集成

---

## 🔧 前置要求

在开始之前，请确保您已安装：

1. **Visual Studio Code**（最新版本）
   - 下载地址：https://code.visualstudio.com/

2. **GitHub Copilot 扩展**
   - 在 VS Code 扩展市场搜索并安装 "GitHub Copilot"

3. **Node.js 18+**
   - 下载地址：https://nodejs.org/
   - 验证安装：`node --version`

4. **Azure CLI**
   - 用于身份验证
   - 下载地址：https://learn.microsoft.com/cli/azure/install-azure-cli

5. **Azure DevOps 账户**
   - 访问：https://dev.azure.com/
   - 确保您有组织的访问权限

---

## 📥 安装步骤

### 步骤 1：安装 Azure CLI

**Windows：**
```powershell
# 下载并运行安装程序
# https://aka.ms/installazurecliwindows
```

**macOS：**
```bash
brew update && brew install azure-cli
```

**Linux (Ubuntu/Debian)：**
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

**验证安装：**
```bash
az --version
```

### 步骤 2：登录 Azure

```bash
az login
```

这将打开浏览器窗口，让您登录 Azure 账户。登录成功后，命令行会显示您的订阅列表。

### 步骤 3：创建 MCP 配置文件

在您的项目根目录创建或编辑 `.vscode/mcp.json` 文件：

**方法 1：使用 VS Code**
1. 在项目根目录右键点击
2. 选择"新建文件夹"，创建 `.vscode` 文件夹
3. 在 `.vscode` 文件夹中创建 `mcp.json` 文件

**方法 2：使用命令行**
```bash
mkdir -p .vscode
cd .vscode
touch mcp.json
```

### 步骤 4：添加 Azure DevOps MCP 配置

在 `mcp.json` 文件中添加以下内容：

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
- `ado_org`：您的 Azure DevOps 组织名称（在 URL 中，如 `dev.azure.com/你的组织名`）
- `npx -y`：自动安装并运行最新版本的 Azure DevOps MCP 服务器
- `${input:ado_org}`：VS Code 会在启动时提示您输入组织名称

**如果您已知组织名称，可以直接写入配置：**
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

## ✅ 验证安装

### 1. 重启 VS Code

保存配置文件后，关闭并重新打开 VS Code。

### 2. 检查 MCP 服务器状态

1. 按 `Ctrl+Shift+P`（Windows/Linux）或 `Cmd+Shift+P`（Mac）
2. 输入 "MCP: Show Server Status"
3. 查看 Azure DevOps MCP 服务器是否显示为"运行中"

### 3. 测试功能

打开 Copilot 聊天（`Ctrl+Shift+I` / `Cmd+Shift+I`），尝试以下命令：

**查询工作项：**
```
显示我最近创建的工作项
```

**查看 Pull Request：**
```
列出当前项目的所有开放的 Pull Request
```

**创建工作项：**
```
创建一个新的 Bug 工作项，标题是"修复登录问题"
```

**查询构建状态：**
```
显示最近的构建状态
```

---

## 🎯 使用场景

### 场景 1：工作项管理

**查看我的工作项：**
```
@azure-devops 显示分配给我的所有工作项
```

**创建新任务：**
```
@azure-devops 创建一个新任务：实现用户登录功能，优先级为高
```

**更新工作项状态：**
```
@azure-devops 将工作项 #12345 的状态更新为"已完成"
```

### 场景 2：Pull Request 管理

**查看 PR：**
```
@azure-devops 列出我创建的所有 Pull Request
```

**创建 PR：**
```
@azure-devops 创建一个 Pull Request，从 feature/login 合并到 main
```

**审查 PR：**
```
@azure-devops 显示 PR #678 的详细信息和评论
```

### 场景 3：构建和部署

**查看构建历史：**
```
@azure-devops 显示最近 10 次构建的状态
```

**触发构建：**
```
@azure-devops 触发 main 分支的构建
```

**查看发布：**
```
@azure-devops 显示最近的发布状态
```

---

## ⚙️ 高级配置

### 配置多个组织

如果您需要访问多个 Azure DevOps 组织，可以这样配置：

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

### 设置默认项目

您可以通过环境变量设置默认项目：

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

### 指定服务器版本

如果您想固定使用特定版本的 MCP 服务器：

```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp@1.0.0", "your-org"]
    }
  }
}
```

---

## 🔍 故障排除

### 问题 1：无法连接到 Azure DevOps

**症状：** 提示认证失败或无法访问组织

**解决方法：**
1. 确认已登录 Azure CLI：`az login`
2. 检查账户权限：`az account show`
3. 确认组织名称正确
4. 尝试手动访问：https://dev.azure.com/your-org

### 问题 2：MCP 服务器启动失败

**症状：** VS Code 显示 MCP 服务器错误

**解决方法：**
1. 检查 Node.js 版本：`node --version`（需要 18+）
2. 清除 npm 缓存：`npm cache clean --force`
3. 手动安装服务器：`npm install -g @azure-devops/mcp`
4. 查看 VS Code 输出面板的错误日志

### 问题 3：找不到工作项或项目

**症状：** Copilot 返回"找不到资源"

**解决方法：**
1. 确认您有相应项目的访问权限
2. 在配置中指定默认项目
3. 在查询时明确指定项目名称

### 问题 4：性能慢或超时

**症状：** 查询响应时间长或超时

**解决方法：**
1. 检查网络连接
2. 限制查询结果数量（如"最近 10 个工作项"）
3. 使用更具体的过滤条件

---

## 📚 可用命令和功能

Azure DevOps MCP Server 支持以下主要功能：

### 工作项（Work Items）
- ✅ 列出工作项
- ✅ 创建工作项
- ✅ 更新工作项
- ✅ 查询工作项详情
- ✅ 添加评论
- ✅ 更新状态

### Pull Requests
- ✅ 列出 PR
- ✅ 创建 PR
- ✅ 更新 PR
- ✅ 添加评审意见
- ✅ 合并 PR

### 构建（Builds）
- ✅ 查看构建历史
- ✅ 触发构建
- ✅ 查看构建详情
- ✅ 查看构建日志

### 发布（Releases）
- ✅ 查看发布历史
- ✅ 创建发布
- ✅ 部署发布

### 仓库（Repos）
- ✅ 浏览代码
- ✅ 搜索文件
- ✅ 查看提交历史

---

## 🔗 相关资源

- [Azure DevOps MCP 官方文档](https://learn.microsoft.com/en-us/azure/devops/mcp-server/mcp-server-overview)
- [Azure DevOps REST API 参考](https://learn.microsoft.com/en-us/rest/api/azure/devops/)
- [GitHub Copilot 文档](https://docs.github.com/copilot)
- [MCP 协议规范](https://modelcontextprotocol.io/)

---

## 💡 最佳实践

1. **使用特定查询**：明确指定项目、时间范围等，提高查询效率
2. **配置默认项目**：减少每次查询都要指定项目的麻烦
3. **定期更新**：使用 `npx -y` 确保使用最新版本
4. **保护凭证**：不要在 `mcp.json` 中硬编码敏感信息
5. **团队协作**：将 `.vscode/mcp.json` 加入版本控制，方便团队共享配置

---

## 🆘 获取帮助

遇到问题？

- 查看 [故障排除指南](./troubleshooting.md)
- 查看 [常见问题解答](./faq.md)
- 在项目中提交 [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- 查阅 [官方文档](https://learn.microsoft.com/en-us/azure/devops/mcp-server/)

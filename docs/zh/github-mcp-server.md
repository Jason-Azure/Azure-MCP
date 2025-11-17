# GitHub MCP Server 安装指南

> GitHub MCP Server 是官方提供的 MCP 服务器，让 GitHub Copilot 能够与 GitHub 仓库、Issue、Pull Request 等进行交互。

## 📖 概述

**GitHub MCP Server** 提供了 100+ 个工具，让 Copilot 能够：

- 🔍 搜索代码、仓库、Issue 和 PR
- 📋 管理 Issue（创建、更新、关闭）
- 🔀 管理 Pull Request（创建、审查、合并）
- 📊 查看仓库统计和分析
- 🏷️ 管理标签和里程碑
- 👥 管理协作者和权限
- 🔐 与 GitHub Actions 集成

**特点：**
- ✅ 官方支持，稳定可靠
- ✅ 功能全面，覆盖 GitHub API
- ✅ 支持多种认证方式
- ✅ 实时同步数据

---

## 🔧 前置要求

1. **Visual Studio Code**（最新版本）
2. **GitHub Copilot 扩展**
3. **Node.js 18+**
4. **GitHub 账户**和访问令牌

---

## 📥 安装步骤

### 步骤 1：创建 GitHub Personal Access Token

1. 登录 GitHub
2. 访问 [Settings > Developer settings > Personal access tokens > Tokens (classic)](https://github.com/settings/tokens)
3. 点击 "Generate new token" > "Generate new token (classic)"
4. 设置：
   - **Note**: `MCP Server for Copilot`
   - **Expiration**: 选择过期时间
   - **Scopes**: 勾选以下权限：
     - ✅ `repo`（完整仓库访问）
     - ✅ `read:org`（读取组织信息）
     - ✅ `read:user`（读取用户信息）
     - ✅ `workflow`（访问 GitHub Actions）
5. 点击 "Generate token"
6. **重要**：复制生成的令牌并保存到安全的地方

### 步骤 2：配置环境变量

**Windows：**
```powershell
# 临时设置（仅当前会话有效）
$env:GITHUB_TOKEN="your_token_here"

# 永久设置
[System.Environment]::SetEnvironmentVariable('GITHUB_TOKEN', 'your_token_here', 'User')
```

**macOS/Linux：**
```bash
# 添加到 ~/.bashrc 或 ~/.zshrc
echo 'export GITHUB_TOKEN="your_token_here"' >> ~/.bashrc
source ~/.bashrc

# 或添加到 ~/.zshrc
echo 'export GITHUB_TOKEN="your_token_here"' >> ~/.zshrc
source ~/.zshrc
```

### 步骤 3：创建 MCP 配置文件

在项目根目录创建 `.vscode/mcp.json`：

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

**配置说明：**
- `@modelcontextprotocol/server-github`：官方 GitHub MCP 服务器包
- `${env:GITHUB_TOKEN}`：从环境变量读取 GitHub 令牌

### 步骤 4：重启 VS Code

保存配置后，重启 VS Code 以加载 MCP 服务器。

---

## ✅ 验证安装

打开 Copilot 聊天，尝试以下命令：

**搜索仓库：**
```
@github 搜索包含 "machine learning" 的 Python 仓库
```

**查看 Issue：**
```
@github 列出我在 microsoft/vscode 项目中创建的 Issue
```

**创建 Issue：**
```
@github 在 owner/repo 中创建一个 Issue，标题是"添加新功能"
```

---

## 🎯 使用场景

### 场景 1：仓库管理

**搜索代码：**
```
@github 在 facebook/react 仓库中搜索包含 "useState" 的代码
```

**查看仓库信息：**
```
@github 显示 tensorflow/tensorflow 仓库的基本信息和统计数据
```

**列出分支：**
```
@github 列出 myorg/myrepo 的所有分支
```

### 场景 2：Issue 管理

**查询 Issue：**
```
@github 显示 vuejs/vue 仓库中所有标签为 "bug" 的开放 Issue
```

**创建 Issue：**
```
@github 在当前仓库创建一个 Issue：
标题：修复登录页面错误
描述：用户无法在 Chrome 浏览器登录
标签：bug, high-priority
```

**更新 Issue：**
```
@github 为 Issue #123 添加评论："已修复，请测试"
```

**关闭 Issue：**
```
@github 关闭 Issue #456
```

### 场景 3：Pull Request 管理

**查看 PR：**
```
@github 列出 microsoft/TypeScript 仓库中所有开放的 Pull Request
```

**创建 PR：**
```
@github 创建一个 Pull Request：
从：feature/new-ui
到：main
标题：添加新的用户界面
描述：实现了全新的响应式 UI 设计
```

**审查 PR：**
```
@github 显示 PR #789 的所有评论和审查意见
```

**合并 PR：**
```
@github 合并 PR #789 使用 squash 模式
```

### 场景 4：GitHub Actions

**查看工作流：**
```
@github 列出当前仓库的所有 GitHub Actions 工作流
```

**查看运行状态：**
```
@github 显示最近 10 次工作流运行的状态
```

**触发工作流：**
```
@github 触发 CI 工作流在 main 分支
```

---

## ⚙️ 高级配置

### 配置多个仓库访问

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

### 使用 GitHub Enterprise

如果您使用 GitHub Enterprise Server：

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

### 限制权限范围

对于只读访问，创建令牌时只勾选必要的权限：

- ✅ `public_repo`（访问公开仓库）
- ✅ `read:org`（读取组织信息）
- ✅ `read:user`（读取用户信息）

---

## 🔍 故障排除

### 问题 1：认证失败

**症状：** 提示 "Authentication failed" 或 "Bad credentials"

**解决方法：**
1. 检查环境变量是否正确设置：
   ```bash
   # Windows
   echo $env:GITHUB_TOKEN
   
   # macOS/Linux
   echo $GITHUB_TOKEN
   ```
2. 确认令牌权限足够
3. 检查令牌是否过期
4. 重新生成令牌

### 问题 2：无法访问私有仓库

**症状：** 提示 "Repository not found" 或 "404"

**解决方法：**
1. 确认令牌包含 `repo` 权限
2. 检查仓库名称是否正确（格式：`owner/repo`）
3. 确认您有访问该仓库的权限

### 问题 3：速率限制

**症状：** 提示 "API rate limit exceeded"

**解决方法：**
1. 使用认证令牌（提高速率限制到 5000 次/小时）
2. 减少查询频率
3. 等待速率限制重置（每小时重置一次）
4. 查看当前速率限制状态：
   ```bash
   curl -H "Authorization: token YOUR_TOKEN" \
     https://api.github.com/rate_limit
   ```

### 问题 4：MCP 服务器无响应

**症状：** 命令执行时没有反应

**解决方法：**
1. 检查 VS Code 输出面板的 MCP 日志
2. 重启 VS Code
3. 验证 Node.js 版本：`node --version`（需要 18+）
4. 手动测试服务器：
   ```bash
   npx @modelcontextprotocol/server-github
   ```

---

## 📚 可用功能列表

### 仓库操作
- ✅ 搜索仓库
- ✅ 获取仓库信息
- ✅ 列出分支
- ✅ 列出标签
- ✅ 查看提交历史
- ✅ 搜索代码
- ✅ 获取文件内容

### Issue 管理
- ✅ 列出 Issue
- ✅ 搜索 Issue
- ✅ 创建 Issue
- ✅ 更新 Issue
- ✅ 关闭 Issue
- ✅ 添加评论
- ✅ 管理标签
- ✅ 管理里程碑

### Pull Request 管理
- ✅ 列出 PR
- ✅ 搜索 PR
- ✅ 创建 PR
- ✅ 更新 PR
- ✅ 合并 PR
- ✅ 添加审查
- ✅ 请求审查
- ✅ 查看文件变更

### GitHub Actions
- ✅ 列出工作流
- ✅ 查看运行状态
- ✅ 触发工作流
- ✅ 查看日志
- ✅ 下载构件

### 组织和团队
- ✅ 列出组织
- ✅ 列出团队
- ✅ 管理成员
- ✅ 管理权限

---

## 🔐 安全最佳实践

1. **令牌管理**
   - 🔒 不要在代码中硬编码令牌
   - 🔒 使用环境变量存储令牌
   - 🔒 定期轮换令牌
   - 🔒 为不同用途创建不同令牌

2. **权限最小化**
   - ✅ 只授予必要的权限
   - ✅ 对于只读操作，使用只读令牌
   - ✅ 定期审查令牌权限

3. **令牌泄露应对**
   - ⚠️ 立即撤销泄露的令牌
   - ⚠️ 生成新令牌
   - ⚠️ 审查使用该令牌的历史记录

---

## 🔗 相关资源

- [GitHub REST API 文档](https://docs.github.com/en/rest)
- [GitHub GraphQL API 文档](https://docs.github.com/en/graphql)
- [Personal Access Token 指南](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
- [GitHub Copilot 文档](https://docs.github.com/copilot)
- [MCP 协议规范](https://modelcontextprotocol.io/)

---

## 💡 最佳实践

1. **使用别名**：在 Copilot 中使用 `@github` 明确指定使用 GitHub MCP
2. **具体查询**：提供具体的仓库名、Issue 编号等，提高查询准确性
3. **批量操作**：使用脚本处理大量 Issue 或 PR
4. **监控速率限制**：避免超过 API 速率限制
5. **版本控制配置**：将 `.vscode/mcp.json`（不含令牌）加入版本控制

---

## 🆘 获取帮助

- 查看 [故障排除指南](./troubleshooting.md)
- 查看 [常见问题解答](./faq.md)
- 提交 [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- 查阅 [GitHub 官方文档](https://docs.github.com/)

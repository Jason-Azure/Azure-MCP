# MCP 服务器快速参考

> 常用 MCP 服务器配置和命令的快速参考指南。

## 📋 常用配置模板

### 完整配置示例

```json
{
  "inputs": [
    {
      "id": "ado_org",
      "type": "promptString",
      "description": "Azure DevOps 组织名称"
    },
    {
      "id": "github_token",
      "type": "promptString",
      "description": "GitHub Personal Access Token",
      "password": true
    }
  ],
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "${input:ado_org}"]
    },
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${env:GITHUB_TOKEN}"
      }
    },
    "browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-playwright"]
    },
    "datadog": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@datadog/mcp-server"],
      "env": {
        "DATADOG_API_KEY": "${env:DATADOG_API_KEY}",
        "DATADOG_APP_KEY": "${env:DATADOG_APP_KEY}"
      }
    }
  }
}
```

---

## 🔧 单个服务器配置

### Azure DevOps

**最简配置：**
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

**带项目默认值：**
```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@azure-devops/mcp", "your-org-name"],
      "env": {
        "ADO_PROJECT": "your-project-name"
      }
    }
  }
}
```

### GitHub

**基础配置：**
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

**GitHub Enterprise：**
```json
{
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "${env:GITHUB_TOKEN}",
        "GITHUB_API_URL": "https://github.company.com/api/v3"
      }
    }
  }
}
```

### Browser (Playwright)

**基础配置：**
```json
{
  "servers": {
    "browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-playwright"]
    }
  }
}
```

**自定义浏览器：**
```json
{
  "servers": {
    "browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-playwright"],
      "env": {
        "PLAYWRIGHT_BROWSER": "chromium",
        "PLAYWRIGHT_HEADLESS": "true"
      }
    }
  }
}
```

### Datadog

**基础配置：**
```json
{
  "servers": {
    "datadog": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@datadog/mcp-server"],
      "env": {
        "DATADOG_API_KEY": "${env:DATADOG_API_KEY}",
        "DATADOG_APP_KEY": "${env:DATADOG_APP_KEY}",
        "DATADOG_SITE": "datadoghq.com"
      }
    }
  }
}
```

---

## 💬 常用命令速查

### Azure DevOps 命令

| 功能 | 命令示例 |
|------|----------|
| 查看工作项 | `@azure-devops 显示我的工作项` |
| 创建任务 | `@azure-devops 创建任务：实现登录功能` |
| 创建 Bug | `@azure-devops 创建 Bug：页面加载错误` |
| 更新状态 | `@azure-devops 更新工作项 #123 状态为已完成` |
| 查看 PR | `@azure-devops 列出所有开放的 Pull Request` |
| 创建 PR | `@azure-devops 创建 PR 从 feature 到 main` |
| 查看构建 | `@azure-devops 显示最近的构建状态` |
| 触发构建 | `@azure-devops 触发 main 分支的构建` |

### GitHub 命令

| 功能 | 命令示例 |
|------|----------|
| 搜索仓库 | `@github 搜索 Python 机器学习仓库` |
| 查看 Issue | `@github 显示 microsoft/vscode 的开放 Issue` |
| 创建 Issue | `@github 创建 Issue：添加新功能` |
| 关闭 Issue | `@github 关闭 Issue #456` |
| 查看 PR | `@github 列出我创建的 Pull Request` |
| 创建 PR | `@github 创建 PR：添加用户认证` |
| 合并 PR | `@github 合并 PR #789` |
| 搜索代码 | `@github 在 react 仓库搜索 useState` |

### Browser 命令

| 功能 | 命令示例 |
|------|----------|
| 访问网页 | `@browser 访问 https://example.com` |
| 提取内容 | `@browser 提取页面所有链接` |
| 截图 | `@browser 截取整页截图` |
| 填写表单 | `@browser 在输入框填写：用户名` |
| 点击元素 | `@browser 点击登录按钮` |
| 检查元素 | `@browser 检查是否存在提交按钮` |
| 提取数据 | `@browser 提取表格中的所有数据` |

### Datadog 命令

| 功能 | 命令示例 |
|------|----------|
| 查询指标 | `@datadog 显示过去 1 小时的 CPU 使用率` |
| 搜索日志 | `@datadog 搜索包含 error 的日志` |
| 查看告警 | `@datadog 显示所有活跃告警` |
| 检查服务 | `@datadog 检查 api-service 的健康状态` |
| 追踪调用 | `@datadog 显示 trace ID abc123 的调用链` |
| 性能分析 | `@datadog 分析 web-server 的响应时间` |

---

## 🔑 环境变量设置

### Windows (PowerShell)

```powershell
# 临时设置（当前会话）
$env:GITHUB_TOKEN = "your_token"
$env:DATADOG_API_KEY = "your_key"

# 永久设置（用户级别）
[Environment]::SetEnvironmentVariable('GITHUB_TOKEN', 'your_token', 'User')
[Environment]::SetEnvironmentVariable('DATADOG_API_KEY', 'your_key', 'User')

# 查看环境变量
echo $env:GITHUB_TOKEN
```

### macOS/Linux (Bash)

```bash
# 添加到 ~/.bashrc 或 ~/.bash_profile
export GITHUB_TOKEN="your_token"
export DATADOG_API_KEY="your_key"
export DATADOG_APP_KEY="your_app_key"

# 重新加载配置
source ~/.bashrc

# 查看环境变量
echo $GITHUB_TOKEN
```

### macOS/Linux (Zsh)

```bash
# 添加到 ~/.zshrc
export GITHUB_TOKEN="your_token"
export DATADOG_API_KEY="your_key"

# 重新加载配置
source ~/.zshrc

# 查看环境变量
echo $GITHUB_TOKEN
```

---

## 🔧 常用 npm 命令

```bash
# 检查版本
node --version
npm --version

# 全局安装 MCP 服务器
npm install -g @azure-devops/mcp
npm install -g @modelcontextprotocol/server-github

# 更新 MCP 服务器
npm update -g @azure-devops/mcp

# 清理缓存
npm cache clean --force

# 查看全局安装的包
npm list -g --depth=0

# 卸载全局包
npm uninstall -g @azure-devops/mcp
```

---

## 🔍 故障排查速查

### 检查 Node.js 和 npm

```bash
# 检查版本
node --version  # 应该 >= 18.x.x
npm --version

# 检查 npm 配置
npm config list

# 验证 npx 可用
npx --version
```

### 检查 Azure CLI

```bash
# 检查版本
az --version

# 检查登录状态
az account show

# 重新登录
az login

# 列出订阅
az account list
```

### 检查环境变量

```bash
# Windows (PowerShell)
echo $env:GITHUB_TOKEN
echo $env:DATADOG_API_KEY

# macOS/Linux
echo $GITHUB_TOKEN
echo $DATADOG_API_KEY
```

### VS Code 日志查看

1. 打开输出面板：`Ctrl+Shift+U` / `Cmd+Shift+U`
2. 选择 "GitHub Copilot" 或 "Extension Host"
3. 查找错误或警告信息

---

## 📁 项目结构示例

```
my-project/
├── .vscode/
│   ├── mcp.json           # MCP 服务器配置
│   ├── settings.json      # VS Code 设置
│   └── extensions.json    # 推荐扩展
├── .env.example           # 环境变量模板
├── .gitignore            # Git 忽略文件
├── README.md             # 项目说明
└── src/                  # 源代码
```

**.gitignore 示例：**
```gitignore
# 环境变量
.env
.env.local
*.secret

# Node.js
node_modules/
npm-debug.log*

# VS Code
.vscode/*
!.vscode/mcp.json
!.vscode/settings.json
!.vscode/extensions.json
```

---

## 🔗 快速链接

### 官方文档
- [MCP 协议规范](https://modelcontextprotocol.io/)
- [Azure DevOps MCP](https://learn.microsoft.com/azure/devops/mcp-server/)
- [GitHub API 文档](https://docs.github.com/rest)
- [Playwright 文档](https://playwright.dev/)
- [Datadog API](https://docs.datadoghq.com/api/)

### 安装资源
- [Node.js 下载](https://nodejs.org/)
- [VS Code 下载](https://code.visualstudio.com/)
- [Azure CLI 安装](https://learn.microsoft.com/cli/azure/install-azure-cli)
- [GitHub Copilot](https://github.com/features/copilot)

### 社区支持
- [项目 GitHub](https://github.com/Jason-Azure/Azure-MCP)
- [提交 Issue](https://github.com/Jason-Azure/Azure-MCP/issues)

---

## 💡 提示

- 📌 将此页面添加到书签，方便快速查找
- 📌 根据您的需求自定义配置模板
- 📌 定期检查服务器更新
- 📌 保持环境变量的安全性

---

**需要更详细的说明？** 查看完整文档：
- [快速开始](./getting-started.md)
- [最佳实践](./best-practices.md)
- [故障排除](./troubleshooting.md)
- [常见问题](./faq.md)

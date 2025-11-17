# 最佳实践

> 本文档提供使用 MCP 服务器的最佳实践和优化建议。

## 📋 目录

- [配置管理](#配置管理)
- [安全实践](#安全实践)
- [性能优化](#性能优化)
- [团队协作](#团队协作)
- [开发工作流](#开发工作流)

---

## 配置管理

### 1. 组织配置文件

**推荐的项目结构：**
```
project/
├── .vscode/
│   ├── mcp.json          # MCP 服务器配置
│   ├── settings.json     # VS Code 设置
│   └── extensions.json   # 推荐的扩展
├── .env.example          # 环境变量模板（不含实际值）
└── README.md             # 包含 MCP 设置说明
```

**mcp.json 示例：**
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
    },
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

### 2. 使用环境变量

**优点：**
- 🔒 不会泄露敏感信息
- 🔄 易于在不同环境切换
- 👥 便于团队共享配置

**实现方式：**

**方法 1：系统环境变量**
```bash
# Mac/Linux - 添加到 ~/.bashrc 或 ~/.zshrc
export GITHUB_TOKEN="your_token_here"
export ADO_ORG="your_organization"

# Windows - PowerShell
[Environment]::SetEnvironmentVariable('GITHUB_TOKEN', 'your_token', 'User')
```

**方法 2：.env 文件（使用 dotenv）**
```bash
# .env 文件（不要提交到 Git）
GITHUB_TOKEN=your_token_here
ADO_ORG=your_organization

# .env.example 文件（可以提交到 Git）
GITHUB_TOKEN=your_github_token
ADO_ORG=your_azure_devops_org
```

### 3. 版本控制

**.gitignore 配置：**
```gitignore
# MCP 相关
.env
.env.local
*.secret

# 但保留模板
!.env.example
```

**提交配置文件：**
```bash
git add .vscode/mcp.json
git add .env.example
git commit -m "Add MCP server configuration"
```

---

## 安全实践

### 1. API 令牌管理

**最小权限原则：**
- ✅ 只授予必需的权限
- ✅ 为不同用途创建不同令牌
- ✅ 定期审查和轮换令牌

**GitHub 令牌作用域建议：**

| 用途 | 必需作用域 | 可选作用域 |
|------|-----------|-----------|
| 只读访问 | `public_repo`, `read:org` | - |
| 管理 Issue | `repo`, `read:org` | `write:discussion` |
| 管理 PR | `repo`, `workflow` | - |
| 全功能 | `repo`, `workflow`, `admin:org` | `delete_repo` |

**Azure DevOps 权限建议：**
- 开发环境：Reader
- 测试环境：Contributor
- 生产环境：Basic + 特定权限

### 2. 令牌存储

**推荐方式：**

**Windows - Credential Manager**
```powershell
# 存储
cmdkey /generic:GITHUB_TOKEN /user:token /pass:your_token_here

# 读取（在 PowerShell 脚本中）
$credential = Get-StoredCredential -Target "GITHUB_TOKEN"
```

**macOS - Keychain**
```bash
# 存储
security add-generic-password -a $USER -s GITHUB_TOKEN -w "your_token_here"

# 读取
security find-generic-password -a $USER -s GITHUB_TOKEN -w
```

**Linux - Secret Service**
```bash
# 使用 secret-tool
secret-tool store --label='GitHub Token' token github
secret-tool lookup token github
```

### 3. 审计和监控

**定期审查：**
- 📅 每月检查活跃的令牌
- 📅 每季度轮换敏感令牌
- 📅 审查 API 使用日志

**监控异常活动：**
- 🔍 检查 GitHub/Azure DevOps 的访问日志
- 🔍 设置异常登录告警
- 🔍 监控 API 速率使用

---

## 性能优化

### 1. 减少启动时间

**使用全局安装：**
```bash
# 一次性安装，避免每次下载
npm install -g @azure-devops/mcp
npm install -g @modelcontextprotocol/server-github
```

**配置使用全局包：**
```json
{
  "servers": {
    "azure-devops": {
      "type": "stdio",
      "command": "azure-devops-mcp",
      "args": ["your-org"]
    }
  }
}
```

### 2. 优化查询

**使用具体的查询：**
```
❌ 不好：显示所有工作项
✅ 好：显示我最近 7 天创建的工作项
```

**分页获取结果：**
```
❌ 不好：获取所有 PR
✅ 好：获取最近 10 个 PR
```

**使用过滤条件：**
```
❌ 不好：列出所有 Issue
✅ 好：列出标签为 "bug" 且状态为 "open" 的 Issue
```

### 3. 缓存策略

**启用缓存（如果支持）：**
```json
{
  "servers": {
    "github": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "CACHE_ENABLED": "true",
        "CACHE_TTL": "300"
      }
    }
  }
}
```

### 4. 资源管理

**只启用需要的服务器：**
```json
{
  "servers": {
    // 日常开发
    "github": { ... },
    
    // 仅在需要时启用
    // "browser": { ... },
    // "datadog": { ... }
  }
}
```

**定期清理：**
```bash
# 清理 npm 缓存
npm cache clean --force

# 清理 VS Code 缓存
rm -rf ~/.vscode/extensions/*
```

---

## 团队协作

### 1. 共享配置

**创建团队配置文件：**
```json
// .vscode/mcp.json
{
  "inputs": [
    {
      "id": "ado_org",
      "type": "promptString",
      "description": "Azure DevOps 组织（团队：myteam）",
      "default": "myteam"
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

**创建设置文档：**
```markdown
# 团队 MCP 设置

## 前置要求
- Node.js 18+
- Azure CLI
- VS Code 最新版

## 设置步骤
1. 克隆仓库
2. 运行 `az login`
3. 在 VS Code 中打开项目
4. 重启 VS Code

## 常用命令
- 查看工作项：`@azure-devops 显示我的工作项`
- 创建 Bug：`@azure-devops 创建 Bug：[描述]`
```

### 2. 统一开发环境

**推荐的扩展配置：**
```json
// .vscode/extensions.json
{
  "recommendations": [
    "github.copilot",
    "github.vscode-pull-request-github",
    "ms-vscode.azure-account"
  ]
}
```

**团队设置：**
```json
// .vscode/settings.json
{
  "github.copilot.enable": {
    "*": true
  },
  "files.exclude": {
    "**/.env": true
  }
}
```

### 3. 文档和培训

**创建团队文档：**
- 📖 快速开始指南
- 📖 常用命令速查表
- 📖 故障排除步骤
- 📖 最佳实践

**定期培训：**
- 🎓 新成员入职培训
- 🎓 月度技巧分享
- 🎓 案例研究和经验分享

---

## 开发工作流

### 1. 日常开发

**晨间检查：**
```
@azure-devops 显示我今天要处理的工作项
@github 显示我需要审查的 PR
```

**编码过程：**
```
@github 这个 Issue 的背景是什么？
@azure-devops 创建一个任务来实现这个功能
// 编写代码...
@github 为这个更改创建 PR
```

**代码审查：**
```
@github 显示 PR #123 的变更
@github 在 PR #123 上添加评论："建议使用缓存"
```

### 2. Bug 修复流程

**1. 识别问题**
```
@datadog 显示最近的错误日志
@azure-devops 搜索相关的 Bug 工作项
```

**2. 创建工作项**
```
@azure-devops 创建 Bug：
标题：用户登录失败
描述：用户在 Chrome 浏览器无法登录
优先级：高
```

**3. 修复和测试**
```
// 修复代码...
@browser 测试登录功能
```

**4. 提交和部署**
```
@github 创建 PR 修复登录 Bug
@azure-devops 更新 Bug 状态为"已解决"
```

### 3. 发布流程

**准备发布：**
```
@azure-devops 显示本次迭代完成的工作项
@github 生成 v2.0.0 的 CHANGELOG
```

**创建发布：**
```
@github 创建 release v2.0.0
@azure-devops 触发生产环境部署
```

**发布后：**
```
@datadog 监控生产环境错误率
@azure-devops 关闭本次迭代的所有工作项
```

---

## 💡 通用技巧

### 1. 使用别名

**在 Copilot 中：**
```
@github = GitHub MCP
@ado = Azure DevOps MCP
@web = Browser MCP
```

### 2. 组合使用

**跨服务工作流：**
```
1. @github 显示 Issue #123 的详情
2. @ado 创建对应的工作项
3. @github 在 Issue #123 上评论："已在 ADO 创建工作项"
```

### 3. 模板和快捷方式

**创建常用命令模板：**
```markdown
# 工作项模板
@ado 创建任务：
标题：[功能名称]
描述：
- 需求：
- 验收标准：
- 技术方案：
```

### 4. 自动化脚本

**使用 Copilot 生成脚本：**
```
请帮我写一个脚本：
每天早上 9 点，使用 MCP 查询我的工作项，
并发送邮件摘要
```

---

## 📊 度量和改进

### 跟踪指标

**效率指标：**
- ⏱️ 任务切换时间减少
- ⏱️ 信息查找时间
- ⏱️ 手动操作次数

**质量指标：**
- ✅ Bug 发现和修复时间
- ✅ 代码审查速度
- ✅ 文档完整性

**持续改进：**
1. 每月回顾工作流程
2. 收集团队反馈
3. 优化配置和流程
4. 分享最佳实践

---

## 🔗 相关资源

- [快速开始指南](./getting-started.md)
- [故障排除](./troubleshooting.md)
- [常见问题](./faq.md)
- [各服务器文档](../README.md)

---

**记住**：最佳实践会随着经验积累而演进。定期回顾和调整您的工作流程！

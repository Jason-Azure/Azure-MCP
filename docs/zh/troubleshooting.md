# 故障排除指南

> 本指南帮助您诊断和解决使用 MCP 服务器时遇到的常见问题。

## 📋 目录

- [诊断工具](#诊断工具)
- [常见错误](#常见错误)
- [特定服务器问题](#特定服务器问题)
- [获取帮助](#获取帮助)

---

## 诊断工具

在开始排查问题之前，先使用这些工具收集信息：

### 1. 查看 MCP 日志

**方法 1：输出面板**
1. 在 VS Code 中按 `Ctrl+Shift+U`（Windows/Linux）或 `Cmd+Shift+U`（Mac）
2. 从下拉菜单选择 "GitHub Copilot"
3. 查找包含错误或警告的消息

**方法 2：开发者工具**
1. 按 `Ctrl+Shift+P` / `Cmd+Shift+P` 打开命令面板
2. 输入 "Developer: Toggle Developer Tools"
3. 切换到 "Console" 标签页
4. 查找红色的错误消息

### 2. 检查系统环境

运行以下命令检查您的环境：

```bash
# 检查 Node.js 版本（需要 18+）
node --version

# 检查 npm 版本
npm --version

# 检查 VS Code 版本
code --version

# 检查环境变量（Windows）
echo %GITHUB_TOKEN%
echo %PATH%

# 检查环境变量（Mac/Linux）
echo $GITHUB_TOKEN
echo $PATH
```

### 3. 验证配置文件

检查 `.vscode/mcp.json` 的语法：

```bash
# 使用 jq 验证 JSON（需要先安装 jq）
cat .vscode/mcp.json | jq

# 或在 VS Code 中打开文件，查看是否有语法错误标记
```

---

## 常见错误

### 错误 1: "Cannot find module" 或 "command not found"

**症状：**
```
Error: Cannot find module '@azure-devops/mcp'
-或-
npx: command not found
```

**原因：**
- Node.js 或 npm 未正确安装
- PATH 环境变量配置不正确
- npm 缓存损坏

**解决方法：**

1. **重新安装 Node.js**：
   - 下载最新 LTS 版本：https://nodejs.org/
   - 安装后重启终端/命令提示符

2. **验证安装**：
   ```bash
   node --version  # 应显示 v18.x.x 或更高
   npm --version   # 应显示版本号
   ```

3. **修复 PATH**（如果需要）：
   
   **Windows：**
   - 右键"此电脑" > "属性"
   - 点击"高级系统设置"
   - 点击"环境变量"
   - 在"系统变量"中找到"Path"，确保包含 Node.js 路径

   **Mac/Linux：**
   ```bash
   # 添加到 ~/.bashrc 或 ~/.zshrc
   export PATH="$PATH:/usr/local/bin"
   ```

4. **清理 npm 缓存**：
   ```bash
   npm cache clean --force
   ```

---

### 错误 2: "Authentication failed" 或 "Unauthorized"

**症状：**
```
Error: Authentication failed
-或-
HTTP 401: Unauthorized
```

**原因：**
- API 令牌无效、过期或权限不足
- 环境变量未正确设置
- 令牌作用域不正确

**解决方法：**

1. **检查环境变量**：
   ```bash
   # Windows
   echo %GITHUB_TOKEN%
   
   # Mac/Linux
   echo $GITHUB_TOKEN
   ```
   
   如果为空，设置环境变量：
   ```bash
   # Windows (PowerShell)
   $env:GITHUB_TOKEN="your_token_here"
   
   # Mac/Linux
   export GITHUB_TOKEN="your_token_here"
   ```

2. **重新生成令牌**：
   - **GitHub**：Settings > Developer settings > Personal access tokens
   - **Azure DevOps**：运行 `az login`

3. **检查令牌权限**：
   - 确保令牌包含所需的作用域（如 `repo`、`read:org`）
   - 重新生成令牌时勾选所有必需权限

4. **重启 VS Code**：
   - 环境变量更改后需要重启 VS Code

---

### 错误 3: "Connection timeout" 或网络错误

**症状：**
```
Error: ETIMEDOUT
-或-
Error: connect ECONNREFUSED
```

**原因：**
- 网络连接问题
- 防火墙阻止连接
- 代理配置错误
- 服务暂时不可用

**解决方法：**

1. **检查网络连接**：
   ```bash
   # 测试网络连接
   ping github.com
   ping dev.azure.com
   ```

2. **配置代理**（如果使用代理）：
   ```bash
   # npm 代理设置
   npm config set proxy http://proxy.company.com:8080
   npm config set https-proxy http://proxy.company.com:8080
   
   # 或设置环境变量
   export HTTP_PROXY=http://proxy.company.com:8080
   export HTTPS_PROXY=http://proxy.company.com:8080
   ```

3. **检查防火墙**：
   - 确保允许 VS Code 和 Node.js 的网络访问
   - 临时禁用防火墙测试（然后重新启用）

4. **检查服务状态**：
   - GitHub Status: https://www.githubstatus.com/
   - Azure Status: https://status.azure.com/

---

### 错误 4: JSON 配置错误

**症状：**
```
Error: Unexpected token in JSON
-或-
配置文件无法加载
```

**原因：**
- JSON 语法错误（缺少引号、逗号、括号等）
- 文件编码问题
- 注释（JSON 不支持注释）

**解决方法：**

1. **使用 JSON 验证工具**：
   - 在 VS Code 中打开 `mcp.json`，查看语法高亮和错误标记
   - 或使用在线工具：https://jsonlint.com/

2. **常见 JSON 错误**：
   ```json
   // ❌ 错误：缺少逗号
   {
     "servers": {
       "github": {...}
       "azure": {...}
     }
   }
   
   // ✅ 正确
   {
     "servers": {
       "github": {...},
       "azure": {...}
     }
   }
   
   // ❌ 错误：多余的逗号
   {
     "servers": {
       "github": {...},
     }
   }
   
   // ✅ 正确
   {
     "servers": {
       "github": {...}
     }
   }
   ```

3. **删除注释**：
   - JSON 标准不支持注释
   - 使用 `description` 字段代替注释

---

### 错误 5: "Rate limit exceeded"

**症状：**
```
Error: API rate limit exceeded
-或-
HTTP 429: Too Many Requests
```

**原因：**
- 超过 API 调用限制
- 未使用认证（限制更低）

**解决方法：**

1. **使用认证令牌**：
   - 未认证：60 次/小时
   - 已认证：5,000 次/小时

2. **检查速率限制状态**：
   ```bash
   # GitHub
   curl -H "Authorization: token YOUR_TOKEN" \
     https://api.github.com/rate_limit
   ```

3. **等待限制重置**：
   - 速率限制每小时重置
   - 或等待 `X-RateLimit-Reset` 头指定的时间

4. **优化查询**：
   - 减少不必要的 API 调用
   - 使用更具体的查询条件
   - 启用缓存（如果支持）

---

## 特定服务器问题

### Azure DevOps MCP Server

**问题：找不到组织或项目**

**解决方法：**
1. 确认组织名称拼写正确
2. 使用 `az devops configure --list` 查看配置
3. 手动访问 https://dev.azure.com/your-org 验证访问权限

**问题：Azure CLI 登录失败**

**解决方法：**
```bash
# 清除缓存并重新登录
az account clear
az login

# 选择正确的订阅
az account list
az account set --subscription "Your Subscription"
```

### GitHub MCP Server

**问题：无法访问私有仓库**

**解决方法：**
1. 确认令牌包含 `repo` 作用域（不仅是 `public_repo`）
2. 检查组织的 OAuth 应用策略
3. 确认您是仓库的协作者

**问题：搜索结果不完整**

**解决方法：**
- GitHub 搜索有结果数量限制
- 使用更具体的搜索条件
- 分页获取结果

### Browser MCP

**问题：浏览器无法启动**

**解决方法：**
```bash
# 安装 Playwright 浏览器
npx playwright install
```

---

## 系统特定问题

### Windows

**问题：PowerShell 执行策略错误**

```powershell
# 临时允许脚本执行
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

**问题：路径包含空格**

```json
{
  "command": "C:\\Program Files\\nodejs\\node.exe",
  "args": ["C:\\path with spaces\\server.js"]
}
```

### macOS

**问题：权限被拒绝**

```bash
# 给予执行权限
chmod +x /path/to/script
```

**问题：证书验证错误**

```bash
# 更新证书
npm config set cafile /path/to/cert.pem
```

### Linux

**问题：缺少系统依赖**

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install build-essential

# CentOS/RHEL
sudo yum groupinstall "Development Tools"
```

---

## 收集诊断信息

如果以上方法都无法解决问题，请收集以下信息以便获取帮助：

### 1. 系统信息
```bash
# 操作系统版本
# Windows: winver
# Mac: sw_vers
# Linux: lsb_release -a

# VS Code 版本
code --version

# Node.js 和 npm 版本
node --version
npm --version
```

### 2. MCP 配置
- 您的 `mcp.json` 文件内容（移除敏感信息）

### 3. 错误日志
- VS Code 输出面板的完整错误信息
- 开发者工具控制台的错误堆栈

### 4. 重现步骤
- 详细描述如何触发错误
- 预期行为 vs 实际行为

---

## 获取帮助

### 自助资源

- 📖 [快速开始指南](./getting-started.md)
- ❓ [常见问题解答](./faq.md)
- 📚 [服务器文档列表](../README.md)

### 社区支持

- 💬 [提交 Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- 🌐 [GitHub Copilot 社区](https://github.com/orgs/community/discussions)
- 📧 联系 GitHub 或 Microsoft 支持（如有企业订阅）

### 报告 Bug

在提交 Issue 时，请包含：
1. 问题描述
2. 重现步骤
3. 预期行为
4. 实际行为
5. 系统信息和日志
6. 截图（如果有帮助）

---

## 💡 预防措施

避免常见问题的最佳实践：

1. ✅ **保持软件更新**
   - 定期更新 VS Code
   - 更新 Node.js 和 npm
   - 更新 Copilot 扩展

2. ✅ **使用版本控制**
   - 将 `mcp.json` 加入 Git
   - 记录配置更改

3. ✅ **测试新配置**
   - 在添加新服务器前备份工作配置
   - 一次添加一个服务器，逐个测试

4. ✅ **监控资源使用**
   - 定期检查内存和 CPU 使用
   - 关闭不需要的服务器

5. ✅ **安全第一**
   - 使用环境变量存储敏感信息
   - 定期轮换 API 令牌
   - 最小化权限范围

---

**仍然无法解决？** 不要犹豫，在项目中提交 Issue，我们会尽力帮助您！

# 常见问题解答（FAQ）

> 本文档整理了使用 MCP 服务器时的常见问题和解答。

## 📋 目录

- [基础问题](#基础问题)
- [安装和配置](#安装和配置)
- [认证和权限](#认证和权限)
- [性能和优化](#性能和优化)
- [故障排除](#故障排除)

---

## 基础问题

### Q1: 什么是 MCP 服务器？

**A:** MCP（Model Context Protocol）服务器是一种让 AI 助手（如 GitHub Copilot）与外部工具和服务交互的标准化接口。它允许 Copilot 访问实时数据、执行操作，而不仅仅是生成代码建议。

### Q2: MCP 服务器安全吗？

**A:** 是的，MCP 服务器非常安全：
- 🔒 服务器在本地运行，数据不会发送到云端
- 🔒 使用标准的认证机制（如 OAuth、API 令牌）
- 🔒 您完全控制服务器的权限和访问范围
- 🔒 可以随时停止或删除服务器

### Q3: 使用 MCP 服务器需要付费吗？

**A:** 大多数 MCP 服务器本身是免费的，但您需要：
- GitHub Copilot 订阅（个人版约 $10/月，企业版约 $19/用户/月）
- 某些服务的访问权限（如 Azure DevOps、Datadog 等）

### Q4: MCP 服务器和 Copilot 扩展有什么区别？

**A:** 
- **Copilot 扩展**：在云端运行，提供预定义的功能
- **MCP 服务器**：在本地运行，可以自定义，访问您自己的数据和服务

### Q5: 我可以同时使用多个 MCP 服务器吗？

**A:** 可以！您可以在 `mcp.json` 中配置多个服务器，Copilot 会根据您的请求自动选择合适的服务器。

---

## 安装和配置

### Q6: 为什么我的 mcp.json 文件不生效？

**A:** 检查以下几点：
1. 文件位置是否正确（`.vscode/mcp.json`）
2. JSON 语法是否正确（使用 VS Code 的 JSON 验证功能）
3. 是否重启了 VS Code
4. VS Code 是否已安装最新版本的 Copilot 扩展

### Q7: 如何检查 MCP 服务器是否正在运行？

**A:** 
1. 打开 VS Code 命令面板（`Ctrl+Shift+P` / `Cmd+Shift+P`）
2. 输入 "Developer: Show Logs"
3. 选择 "Extension Host"
4. 查找包含 "MCP" 的日志条目

或者：
1. 打开输出面板（`Ctrl+Shift+U` / `Cmd+Shift+U`）
2. 从下拉菜单选择 "GitHub Copilot"
3. 查看 MCP 相关日志

### Q8: 我需要为每个项目都配置 mcp.json 吗？

**A:** 不一定：
- **项目级配置**：`.vscode/mcp.json` - 仅对当前项目有效
- **用户级配置**：VS Code 设置中配置 - 对所有项目有效
- **建议**：团队共享的配置放在项目级，个人配置放在用户级

### Q9: 如何更新 MCP 服务器？

**A:** 如果使用 `npx -y` 方式：
- 服务器会自动更新到最新版本
- 如果想固定版本，指定版本号：`npx -y @azure-devops/mcp@1.0.0`

如果全局安装：
```bash
npm update -g @azure-devops/mcp
```

### Q10: 可以在团队中共享 MCP 配置吗？

**A:** 可以！将 `.vscode/mcp.json` 加入版本控制。注意：
- ✅ 共享：服务器配置、命令、参数
- ❌ 不要共享：API 令牌、密码等敏感信息
- 💡 使用环境变量替代硬编码的敏感信息

---

## 认证和权限

### Q11: 如何安全地存储 API 令牌？

**A:** 最佳实践：
1. **使用环境变量**：
   ```json
   {
     "env": {
       "API_TOKEN": "${env:MY_TOKEN}"
     }
   }
   ```

2. **使用密钥管理工具**：
   - Windows: Windows Credential Manager
   - macOS: Keychain
   - Linux: gnome-keyring 或 secret-service

3. **不要**：
   - ❌ 在 `mcp.json` 中硬编码令牌
   - ❌ 将包含令牌的配置文件提交到 Git

### Q12: 忘记了 API 令牌怎么办？

**A:** 
- **GitHub**：在 Settings > Developer settings > Personal access tokens 重新生成
- **Azure DevOps**：使用 Azure CLI 重新登录 (`az login`)
- **其他服务**：访问相应服务的设置页面重新生成

### Q13: MCP 服务器需要哪些权限？

**A:** 权限取决于您要执行的操作：

**只读操作**（推荐新手使用）：
- 查看仓库、Issue、PR
- 搜索代码和文档
- 读取工作项和构建状态

**读写操作**：
- 创建和更新 Issue、PR
- 修改工作项状态
- 触发构建和部署

**建议**：从只读权限开始，根据需要逐步增加权限。

### Q14: 如何撤销 MCP 服务器的访问权限？

**A:** 
1. **删除 API 令牌**：
   - GitHub: Settings > Developer settings > Tokens
   - Azure: 运行 `az logout`
   
2. **删除配置文件**：
   - 删除 `.vscode/mcp.json` 或移除特定服务器配置

3. **重启 VS Code** 使更改生效

---

## 性能和优化

### Q15: 为什么 MCP 服务器响应很慢？

**A:** 可能的原因和解决方法：

1. **网络问题**：
   - 检查互联网连接
   - 使用代理时确保配置正确

2. **查询范围太广**：
   - 使用更具体的查询条件
   - 限制结果数量（如"最近 10 个"而不是"所有"）

3. **服务器首次启动**：
   - 第一次运行时 `npx` 需要下载包，耐心等待
   - 后续运行会快得多

4. **资源限制**：
   - 关闭不必要的 VS Code 扩展
   - 增加 Node.js 内存限制

### Q16: MCP 服务器占用太多内存怎么办？

**A:** 
1. **只启用需要的服务器**：
   - 注释掉 `mcp.json` 中不使用的服务器配置

2. **限制缓存大小**：
   - 某些服务器支持配置缓存大小

3. **定期重启 VS Code**：
   - 清理累积的内存使用

### Q17: 可以离线使用 MCP 服务器吗？

**A:** 部分可以：
- ✅ **本地文件系统服务器**：完全离线可用
- ⚠️ **云服务 MCP**（如 GitHub、Azure DevOps）：需要网络连接
- 💡 某些服务器支持缓存，可以短期离线使用缓存数据

---

## 故障排除

### Q18: 为什么 Copilot 不使用我的 MCP 服务器？

**A:** 检查：
1. **Copilot 是否在代理模式**：
   - 按 `Ctrl+Shift+P` / `Cmd+Shift+P`
   - 搜索 "Copilot: Enable Agent Mode"

2. **MCP 服务器是否运行**：
   - 查看 VS Code 输出面板的日志

3. **查询是否明确**：
   - 使用 `@服务器名` 明确指定服务器
   - 例如：`@github 列出我的仓库`

### Q19: 出现 "command not found" 错误

**A:** 
1. **检查 Node.js 安装**：
   ```bash
   node --version
   npm --version
   ```

2. **检查 PATH 环境变量**：
   - 确保 npm 全局安装目录在 PATH 中

3. **使用完整路径**：
   ```json
   {
     "command": "/usr/local/bin/node",
     "args": ["/path/to/server.js"]
   }
   ```

### Q20: MCP 服务器频繁崩溃怎么办？

**A:** 
1. **查看崩溃日志**：
   - VS Code 输出面板
   - 系统日志

2. **更新依赖**：
   ```bash
   npm update
   ```

3. **重新安装**：
   ```bash
   npm cache clean --force
   npm install -g @package/name
   ```

4. **报告问题**：
   - 在服务器的 GitHub 仓库提交 Issue
   - 提供详细的错误日志和环境信息

---

## 💡 额外提示

### 有用的命令

**检查 Node.js 和 npm 版本**：
```bash
node --version
npm --version
```

**清理 npm 缓存**：
```bash
npm cache clean --force
```

**全局安装的包列表**：
```bash
npm list -g --depth=0
```

**测试 JSON 配置**：
```bash
# 使用 jq 验证 JSON 语法
cat .vscode/mcp.json | jq
```

### 获取更多帮助

- 📖 [完整文档目录](../README.md)
- 🔧 [故障排除指南](./troubleshooting.md)
- 🚀 [快速开始](./getting-started.md)
- 💬 [提交 Issue](https://github.com/Jason-Azure/Azure-MCP/issues)

---

**还有其他问题？** 欢迎在项目中提交 Issue 或 Pull Request！

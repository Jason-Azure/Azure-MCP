# Datadog MCP Server 安装指南

> Datadog MCP Server 让 GitHub Copilot 能够查询和分析 Datadog 中的监控数据、日志和指标。

## 📖 概述

**Datadog MCP Server** 提供实时的可观测性和分析能力，让 Copilot 能够：

- 📊 查询和分析监控指标
- 📝 搜索和过滤日志
- 🔍 追踪分布式系统的调用链
- 🚨 查看和管理告警
- 📈 生成性能报告
- 🔧 诊断系统问题

**特点：**
- ✅ 实时数据访问
- ✅ 强大的查询语言
- ✅ 完整的 Datadog API 支持
- ✅ 适合 DevOps 和 SRE 团队

---

## 🔧 前置要求

1. **Visual Studio Code**（最新版本）
2. **GitHub Copilot 扩展**
3. **Node.js 18+**
4. **Datadog 账户**和 API 密钥

---

## 📥 安装步骤

### 步骤 1：获取 Datadog API 密钥

1. 登录 [Datadog](https://app.datadoghq.com/)
2. 进入 **Organization Settings > API Keys**
3. 点击 **New Key** 创建新的 API 密钥
4. 输入名称（如 "MCP Server"）并保存
5. 复制生成的 API 密钥

### 步骤 2：获取 Application 密钥

1. 在 Datadog 中进入 **Organization Settings > Application Keys**
2. 点击 **New Key** 创建应用密钥
3. 输入名称并保存
4. 复制生成的应用密钥

### 步骤 3：设置环境变量

**Windows (PowerShell):**
```powershell
[Environment]::SetEnvironmentVariable('DATADOG_API_KEY', 'your_api_key', 'User')
[Environment]::SetEnvironmentVariable('DATADOG_APP_KEY', 'your_app_key', 'User')
```

**macOS/Linux:**
```bash
# 添加到 ~/.bashrc 或 ~/.zshrc
echo 'export DATADOG_API_KEY="your_api_key"' >> ~/.bashrc
echo 'export DATADOG_APP_KEY="your_app_key"' >> ~/.bashrc
source ~/.bashrc
```

### 步骤 4：创建 MCP 配置

在项目根目录创建 `.vscode/mcp.json`：

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

**注意：** 如果您使用欧洲或其他区域的 Datadog，请修改 `DATADOG_SITE`：
- US1: `datadoghq.com`（默认）
- US3: `us3.datadoghq.com`
- US5: `us5.datadoghq.com`
- EU: `datadoghq.eu`
- AP1: `ap1.datadoghq.com`

### 步骤 5：重启 VS Code

保存配置后重启 VS Code。

---

## ✅ 验证安装

打开 Copilot 聊天，测试以下命令：

**查询指标：**
```
@datadog 显示过去 1 小时的 CPU 使用率
```

**搜索日志：**
```
@datadog 搜索包含 "error" 的日志，最近 30 分钟
```

**查看告警：**
```
@datadog 显示当前所有活跃的告警
```

---

## 🎯 使用场景

### 场景 1：性能监控

**查询系统指标：**
```
@datadog 显示 web-server 的 CPU、内存和网络使用情况
```

**比较时间段：**
```
@datadog 比较今天和昨天同一时间的请求量
```

**查找异常：**
```
@datadog 找出过去 24 小时内响应时间超过 1 秒的服务
```

### 场景 2：日志分析

**搜索错误：**
```
@datadog 搜索过去 1 小时内 production 环境的所有错误日志
```

**按服务过滤：**
```
@datadog 显示 api-service 的最新 100 条日志
```

**分析日志模式：**
```
@datadog 统计过去 24 小时各类错误的数量
```

### 场景 3：故障排查

**检查服务健康：**
```
@datadog 检查所有服务的健康状态
```

**追踪调用链：**
```
@datadog 显示 request ID abc123 的完整调用链
```

**诊断问题：**
```
@datadog 为什么 api-service 在 10:00-10:30 之间响应变慢？
```

### 场景 4：告警管理

**查看告警：**
```
@datadog 列出所有高优先级的告警
```

**告警历史：**
```
@datadog 显示过去 7 天触发的告警
```

**静音告警：**
```
@datadog 静音 web-server-down 告警 2 小时
```

---

## ⚙️ 高级配置

### 配置默认查询参数

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
        "DATADOG_SITE": "datadoghq.com",
        "DEFAULT_TIME_RANGE": "1h",
        "DEFAULT_ENV": "production"
      }
    }
  }
}
```

### 配置多个环境

```json
{
  "servers": {
    "datadog-prod": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@datadog/mcp-server"],
      "env": {
        "DATADOG_API_KEY": "${env:DATADOG_PROD_API_KEY}",
        "DATADOG_APP_KEY": "${env:DATADOG_PROD_APP_KEY}",
        "DATADOG_ENV": "production"
      }
    },
    "datadog-staging": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@datadog/mcp-server"],
      "env": {
        "DATADOG_API_KEY": "${env:DATADOG_STAGING_API_KEY}",
        "DATADOG_APP_KEY": "${env:DATADOG_STAGING_APP_KEY}",
        "DATADOG_ENV": "staging"
      }
    }
  }
}
```

---

## 📚 常用查询模式

### 指标查询

**基础指标：**
```
@datadog system.cpu.user{host:web-01}
@datadog system.mem.used{*} by {host}
@datadog system.disk.used{*}
```

**聚合查询：**
```
@datadog avg:system.cpu.user{env:prod}
@datadog sum:http.requests{service:api}
@datadog max:system.load.1{*} by {host}
```

**时间范围：**
```
@datadog system.cpu.user{*} over last_1h
@datadog http.requests{*} from 2h ago to now
```

### 日志查询

**按级别：**
```
@datadog logs status:error
@datadog logs status:warn service:api
```

**按内容：**
```
@datadog logs "database connection failed"
@datadog logs service:web AND status:error
```

**时间过滤：**
```
@datadog logs status:error @timestamp:>now-1h
```

### APM 追踪

**查看服务：**
```
@datadog apm services
@datadog apm service:api operations
```

**性能分析：**
```
@datadog apm service:api operation:http.request latency
@datadog apm trace_id:abc123
```

---

## 🔍 故障排除

### 问题 1：认证失败

**症状：** "Invalid API key" 或 "Forbidden"

**解决方法：**
1. 验证 API 密钥是否正确
2. 确认应用密钥有足够权限
3. 检查 DATADOG_SITE 配置是否正确
4. 重新生成密钥

### 问题 2：找不到数据

**症状：** 查询返回空结果

**解决方法：**
1. 确认服务名称、主机名拼写正确
2. 检查时间范围是否有数据
3. 验证环境过滤器
4. 在 Datadog UI 中验证查询

### 问题 3：查询超时

**症状：** "Query timeout"

**解决方法：**
1. 缩小时间范围
2. 添加更具体的过滤条件
3. 使用聚合代替原始数据
4. 检查网络连接

---

## 💡 最佳实践

### 1. 查询优化

**使用具体的过滤器：**
```
❌ @datadog logs status:error
✅ @datadog logs status:error service:api env:prod @timestamp:>now-1h
```

**限制结果数量：**
```
❌ @datadog logs status:error
✅ @datadog logs status:error limit:100
```

### 2. 安全实践

**保护 API 密钥：**
- 🔒 使用环境变量，不要硬编码
- 🔒 定期轮换密钥
- 🔒 使用只读权限（如果只需查询）

**最小权限原则：**
- ✅ 只授予必要的权限
- ✅ 为不同环境使用不同密钥
- ✅ 监控 API 使用情况

### 3. 性能优化

**使用合适的时间范围：**
- 🕐 短期问题：1h
- 🕐 日常监控：4h-24h
- 🕐 趋势分析：7d-30d

**利用聚合：**
```
✅ avg:system.cpu.user{*} by {host}  # 快
❌ system.cpu.user{*}                 # 慢（大量数据点）
```

---

## 🔗 相关资源

- [Datadog API 文档](https://docs.datadoghq.com/api/)
- [Datadog 查询语言](https://docs.datadoghq.com/logs/explorer/search_syntax/)
- [Datadog APM 指南](https://docs.datadoghq.com/tracing/)
- [Datadog 指标类型](https://docs.datadoghq.com/metrics/types/)

---

## 🆘 获取帮助

- 查看 [故障排除指南](./troubleshooting.md)
- 查看 [常见问题解答](./faq.md)
- 提交 [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- 访问 [Datadog 支持](https://www.datadoghq.com/support/)

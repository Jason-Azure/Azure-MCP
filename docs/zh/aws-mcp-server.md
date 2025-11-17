# AWS Labs MCP Server 安装指南

> AWS Labs MCP Server 提供对 AWS 服务的访问，支持实时数据查询和资源管理。

## 📖 概述

**AWS Labs MCP Server** 让 GitHub Copilot 能够：

- ☁️ 访问 AWS 服务和资源
- 🔐 基于 IAM 的权限控制
- ⚡ 流式数据传输（实验性）
- 📊 实时数据分析

## 🔧 前置要求

1. Visual Studio Code（最新版本）
2. GitHub Copilot 扩展
3. Node.js 18+
4. AWS CLI 配置完成
5. 有效的 AWS 账户和凭证

## 📥 安装步骤

### 步骤 1：安装和配置 AWS CLI

```bash
# 安装 AWS CLI
# 参考: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html

# 配置凭证
aws configure
```

### 步骤 2：创建 MCP 配置

在 `.vscode/mcp.json` 中添加：

```json
{
  "servers": {
    "aws": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@aws-labs/mcp-server"],
      "env": {
        "AWS_REGION": "us-east-1"
      }
    }
  }
}
```

## 🎯 使用场景

- 查询 EC2 实例状态
- 访问 S3 存储桶
- 查看 CloudWatch 日志
- 管理 Lambda 函数

## 🔗 相关资源

- [AWS CLI 文档](https://docs.aws.amazon.com/cli/)
- [AWS SDK 文档](https://aws.amazon.com/sdk-for-javascript/)

---

**注意：** 本指南为简化版本。完整文档正在编写中。

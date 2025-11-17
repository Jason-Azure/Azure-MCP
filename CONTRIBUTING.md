# Contributing to Azure-MCP

> 感谢您有兴趣为 Azure-MCP 项目做出贡献！本指南将帮助您开始贡献。

Thank you for your interest in contributing to Azure-MCP! This guide will help you get started.

---

## 🌟 如何贡献 / How to Contribute

### 报告问题 / Report Issues

如果您发现了 bug 或有改进建议：
If you find a bug or have suggestions for improvement:

1. 在提交前搜索现有的 [Issues](https://github.com/Jason-Azure/Azure-MCP/issues)
   Search existing [Issues](https://github.com/Jason-Azure/Azure-MCP/issues) before submitting
2. 使用清晰描述性的标题
   Use a clear and descriptive title
3. 提供详细的重现步骤
   Provide detailed reproduction steps
4. 包含相关的日志、截图或错误信息
   Include relevant logs, screenshots, or error messages

### 添加新的 MCP 服务器 / Add New MCP Servers

如果您想添加新的 MCP 服务器文档：
If you want to add documentation for a new MCP server:

1. Fork 本仓库
   Fork this repository
2. 在 `docs/zh/` 和 `docs/en/` 中创建新的 Markdown 文件
   Create new Markdown files in `docs/zh/` and `docs/en/`
3. 遵循现有文档的格式和结构
   Follow the format and structure of existing documentation
4. 在主 README 中添加链接
   Add links in the main README
5. 提交 Pull Request
   Submit a Pull Request

### 改进现有文档 / Improve Existing Documentation

- 修正拼写或语法错误 / Fix spelling or grammar errors
- 添加更多示例或使用场景 / Add more examples or use cases
- 改进说明的清晰度 / Improve clarity of explanations
- 更新过时的信息 / Update outdated information

### 添加配置示例 / Add Configuration Examples

在 `examples/` 目录中添加新的配置示例：
Add new configuration examples in the `examples/` directory:

1. 创建 `.json` 配置文件
   Create a `.json` configuration file
2. 在 `examples/README.md` 中添加说明
   Add documentation in `examples/README.md`
3. 确保配置已经过测试
   Ensure the configuration has been tested

---

## 📝 文档规范 / Documentation Standards

### 文件命名 / File Naming

- 中文文档：`docs/zh/server-name-mcp-server.md`
- English docs: `docs/en/server-name-mcp-server.md`
- 使用小写字母和连字符 / Use lowercase letters and hyphens

### 文档结构 / Document Structure

每个 MCP 服务器文档应包含：
Each MCP server document should include:

1. **概述 / Overview**
   - 简短描述 / Brief description
   - 主要功能 / Key features
   - 特点 / Highlights

2. **前置要求 / Prerequisites**
   - 所需软件和工具 / Required software and tools
   - 账户要求 / Account requirements

3. **安装步骤 / Installation Steps**
   - 详细的分步说明 / Detailed step-by-step instructions
   - 配置示例 / Configuration examples

4. **验证安装 / Verify Installation**
   - 测试命令 / Test commands
   - 预期结果 / Expected results

5. **使用场景 / Use Cases**
   - 实际例子 / Practical examples
   - 常用命令 / Common commands

6. **故障排除 / Troubleshooting**
   - 常见问题和解决方案 / Common issues and solutions

7. **相关资源 / Related Resources**
   - 官方文档链接 / Official documentation links

### 代码示例 / Code Examples

使用代码块并标注语言：
Use code blocks with language tags:

```json
{
  "servers": {
    "example": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "example-package"]
    }
  }
}
```

### 截图 / Screenshots

- 使用清晰的截图
  Use clear screenshots
- 标注重要部分
  Highlight important parts
- 保存为 PNG 格式
  Save as PNG format
- 放在 `docs/images/` 目录
  Place in `docs/images/` directory

---

## 🔀 Pull Request 流程 / Pull Request Process

1. **Fork 仓库**
   Fork the repository

2. **创建分支**
   Create a branch
   ```bash
   git checkout -b feature/add-new-server
   ```

3. **进行更改**
   Make your changes
   - 编写清晰的提交消息
     Write clear commit messages
   - 遵循现有代码风格
     Follow existing code style

4. **测试更改**
   Test your changes
   - 验证所有链接有效
     Verify all links work
   - 检查 Markdown 格式
     Check Markdown formatting
   - 测试配置示例
     Test configuration examples

5. **提交 Pull Request**
   Submit a Pull Request
   - 使用描述性标题
     Use a descriptive title
   - 说明更改内容和原因
     Explain what and why changed
   - 引用相关 Issues
     Reference related Issues

---

## ✅ 检查清单 / Checklist

提交前请确认：
Before submitting, please ensure:

- [ ] 文档同时提供中文和英文版本
      Documentation is provided in both Chinese and English
- [ ] 所有链接都有效
      All links are working
- [ ] 代码示例已测试
      Code examples have been tested
- [ ] 遵循文档结构和格式
      Document structure and format are followed
- [ ] 拼写和语法检查完成
      Spelling and grammar checked
- [ ] 更新了相关索引和 README
      Updated relevant indexes and README

---

## 🎨 风格指南 / Style Guide

### Markdown

- 使用 ATX 风格标题（`#`）
  Use ATX-style headers (`#`)
- 列表项后空一行
  Leave blank line after list items
- 代码块使用三个反引号
  Use triple backticks for code blocks
- 链接使用描述性文本
  Use descriptive text for links

### 中英文混排 / Chinese-English Mixed Text

- 中英文之间加空格
  Add space between Chinese and English
- 数字和单位之间加空格
  Add space between numbers and units
- 示例：Node.js 18+ 版本
  Example: Node.js version 18+

---

## 💬 沟通 / Communication

- 提问使用 [GitHub Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions)
  Use [GitHub Discussions](https://github.com/Jason-Azure/Azure-MCP/discussions) for questions
- Bug 报告使用 [Issues](https://github.com/Jason-Azure/Azure-MCP/issues)
  Use [Issues](https://github.com/Jason-Azure/Azure-MCP/issues) for bug reports
- 保持友好和尊重
  Be friendly and respectful

---

## 📄 许可证 / License

贡献的内容将在 MIT 许可证下发布。
Contributions will be released under the MIT License.

查看 [LICENSE](../LICENSE) 文件了解详情。
See [LICENSE](../LICENSE) file for details.

---

## 🙏 感谢 / Thank You

感谢您的贡献！每一个贡献都让这个项目变得更好。
Thank you for your contribution! Every contribution makes this project better.

如有疑问，请随时提出 Issue 或联系维护者。
If you have any questions, feel free to open an Issue or contact the maintainers.

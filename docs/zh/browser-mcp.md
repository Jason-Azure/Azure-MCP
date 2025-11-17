# Browser MCP 安装指南

> Browser MCP 允许 GitHub Copilot 进行浏览器自动化，包括网页内容提取、表单填写、测试等。

## 📖 概述

**Browser MCP** 基于 Playwright 构建，让 Copilot 能够：

- 🌐 访问和浏览网页
- 📄 提取网页内容和数据
- 🖱️ 模拟用户交互（点击、输入、滚动）
- 📸 截取屏幕截图
- ✅ 进行网页测试和验证
- 🔍 执行 Web Scraping 任务

**特点：**
- ✅ 支持多种浏览器（Chrome、Firefox、Safari）
- ✅ 无头模式运行
- ✅ 强大的选择器支持
- ✅ 完整的浏览器 API

---

## 🔧 前置要求

1. **Visual Studio Code**（最新版本）
2. **GitHub Copilot 扩展**
3. **Node.js 18+**
4. **Playwright**（会自动安装）

---

## 📥 安装步骤

### 步骤 1：创建 MCP 配置

在项目根目录创建或编辑 `.vscode/mcp.json`：

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

### 步骤 2：安装 Playwright 浏览器

首次使用时，需要安装浏览器引擎：

```bash
npx playwright install
```

这将下载 Chromium、Firefox 和 WebKit。

**只安装特定浏览器：**
```bash
npx playwright install chromium
```

### 步骤 3：重启 VS Code

保存配置后重启 VS Code。

---

## ✅ 验证安装

打开 Copilot 聊天，测试以下命令：

**访问网页：**
```
@browser 访问 https://example.com 并告诉我页面标题
```

**提取内容：**
```
@browser 访问 GitHub 主页，提取所有导航链接
```

**截图：**
```
@browser 打开 https://github.com 并截图
```

---

## 🎯 使用场景

### 场景 1：数据提取

**提取表格数据：**
```
@browser 访问 https://example.com/table，提取表格中的所有数据
```

**提取特定元素：**
```
@browser 在 https://news.ycombinator.com 上提取所有标题和链接
```

### 场景 2：表单测试

**填写表单：**
```
@browser 访问 https://example.com/form
填写：
- 姓名：张三
- 邮箱：zhangsan@example.com
- 留言：测试消息
然后提交
```

**测试登录：**
```
@browser 测试 https://example.com/login 的登录功能
用户名：testuser
密码：testpass
```

### 场景 3：UI 测试

**检查元素存在：**
```
@browser 访问 https://myapp.com，检查是否存在"登录"按钮
```

**验证响应式设计：**
```
@browser 在移动设备视图下打开 https://myapp.com，截图
```

**测试导航：**
```
@browser 在 https://myapp.com 上：
1. 点击"产品"菜单
2. 点击第一个产品
3. 截图产品详情页
```

### 场景 4：监控和验证

**检查网站状态：**
```
@browser 访问 https://myapp.com，检查页面是否正常加载
```

**验证内容更新：**
```
@browser 检查 https://blog.example.com 上是否有新文章
```

---

## ⚙️ 高级配置

### 配置浏览器选项

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

**环境变量说明：**
- `PLAYWRIGHT_BROWSER`: 浏览器类型（`chromium`、`firefox`、`webkit`）
- `PLAYWRIGHT_HEADLESS`: 无头模式（`true` 或 `false`）

### 设置用户代理

```json
{
  "servers": {
    "browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-playwright"],
      "env": {
        "PLAYWRIGHT_USER_AGENT": "Mozilla/5.0 (Custom Agent)"
      }
    }
  }
}
```

### 配置代理

```json
{
  "servers": {
    "browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-playwright"],
      "env": {
        "PLAYWRIGHT_PROXY": "http://proxy.company.com:8080"
      }
    }
  }
}
```

---

## 🔍 常用命令模式

### 导航
```
@browser 访问 <URL>
@browser 前进/后退
@browser 刷新页面
```

### 内容提取
```
@browser 提取页面标题
@browser 获取所有链接
@browser 提取 CSS 选择器 <selector> 的内容
@browser 获取页面文本内容
```

### 交互
```
@browser 点击 <元素描述>
@browser 在 <输入框描述> 中输入 <文本>
@browser 滚动到 <元素描述>
@browser 选择下拉框选项 <选项>
```

### 截图和输出
```
@browser 截取整页截图
@browser 截取 <元素> 的截图
@browser 保存页面 HTML
```

### 等待和验证
```
@browser 等待 <元素> 出现
@browser 检查 <元素> 是否存在
@browser 验证页面标题包含 <文本>
```

---

## 🔍 故障排除

### 问题 1：浏览器无法启动

**症状：** "Failed to launch browser"

**解决方法：**
```bash
# 重新安装浏览器
npx playwright install --force

# 检查系统依赖（Linux）
npx playwright install-deps
```

### 问题 2：找不到元素

**症状：** "Element not found"

**解决方法：**
1. 增加等待时间
2. 使用更具体的选择器
3. 检查页面是否完全加载
4. 使用截图查看页面状态

### 问题 3：超时错误

**症状：** "Navigation timeout"

**解决方法：**
1. 增加超时时间
2. 检查网络连接
3. 验证 URL 是否正确
4. 检查网站是否可访问

### 问题 4：权限被拒绝

**症状：** "Permission denied"

**解决方法：**
```bash
# Linux/Mac
chmod +x ~/.cache/ms-playwright/*/chrome-linux/chrome

# 或使用 sudo 安装
sudo npx playwright install
```

---

## 📚 可用功能列表

### 页面操作
- ✅ 导航到 URL
- ✅ 前进/后退
- ✅ 刷新页面
- ✅ 关闭页面
- ✅ 打开新标签页

### 元素交互
- ✅ 点击元素
- ✅ 双击元素
- ✅ 右键点击
- ✅ 悬停
- ✅ 输入文本
- ✅ 选择选项
- ✅ 上传文件

### 内容获取
- ✅ 获取页面标题
- ✅ 获取 URL
- ✅ 获取文本内容
- ✅ 获取 HTML
- ✅ 获取属性
- ✅ 评估 JavaScript

### 截图和录制
- ✅ 截取页面截图
- ✅ 截取元素截图
- ✅ 全页截图
- ✅ PDF 导出

### 等待和断言
- ✅ 等待元素
- ✅ 等待导航
- ✅ 等待加载状态
- ✅ 检查元素存在
- ✅ 验证文本内容

---

## 🔐 安全注意事项

1. **不要自动化登录敏感网站**
   - 避免在脚本中硬编码密码
   - 使用测试账户

2. **遵守网站服务条款**
   - 检查 robots.txt
   - 不要进行 DOS 攻击
   - 尊重速率限制

3. **保护个人数据**
   - 不要在截图中包含敏感信息
   - 清理浏览器缓存和 Cookie

4. **网络安全**
   - 只访问可信网站
   - 注意 XSS 和注入风险
   - 验证 SSL 证书

---

## 💡 最佳实践

1. **使用显式等待**
   - 不要使用固定延迟
   - 等待特定元素或条件

2. **健壮的选择器**
   - 使用稳定的选择器（如 data-testid）
   - 避免依赖位置或索引

3. **错误处理**
   - 准备处理超时和失败
   - 使用截图辅助调试

4. **性能优化**
   - 使用无头模式
   - 禁用不必要的资源（图片、CSS）
   - 复用浏览器实例

5. **可维护性**
   - 将复杂流程分解为小步骤
   - 使用描述性的命令
   - 记录预期行为

---

## 🔗 相关资源

- [Playwright 官方文档](https://playwright.dev/)
- [Playwright API 参考](https://playwright.dev/docs/api/class-playwright)
- [CSS 选择器教程](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors)
- [XPath 教程](https://www.w3schools.com/xml/xpath_intro.asp)

---

## 🆘 获取帮助

- 查看 [故障排除指南](./troubleshooting.md)
- 查看 [常见问题解答](./faq.md)
- 提交 [Issue](https://github.com/Jason-Azure/Azure-MCP/issues)
- 访问 [Playwright Discord](https://discord.com/invite/playwright-807756831384403968)

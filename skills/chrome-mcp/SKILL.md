---
name: chrome
description: 用 Chrome MCP 读取浏览器页面内容（截图 + 快照），支持交互操作。需要先用 Chrome Dev Debug 启动浏览器。
user_invocable: true
---

# Chrome MCP 页面读取

通过 Chrome DevTools MCP 连接用户已打开的浏览器，读取页面内容、截图、交互。

## 前置条件

用户必须使用 **Chrome Dev Debug**（`/Applications/Chrome Dev Debug.app`）启动浏览器，才能让 MCP 连接。

## 使用流程

当用户提供 URL 时：

### 1. 检查浏览器连接

先用 `mcp__chrome-devtools-mcp__list_pages` 检查是否已连接到浏览器：
- 如果只显示 `about:blank` 或报错 → 提示用户用 Chrome Dev Debug 启动浏览器
- 如果能看到多个已打开的页面 → 连接正常

### 2. 导航到目标页面

用 `mcp__chrome-devtools-mcp__navigate_page` 打开 URL：
```
type: "url"
url: 用户提供的URL
timeout: 30000
```

### 3. 获取页面内容

同时执行两个操作：
- `mcp__chrome-devtools-mcp__take_screenshot` — 截图查看视觉样式
- `mcp__chrome-devtools-mcp__take_snapshot` — 获取文本快照查看结构内容

### 4. 输出结果

根据截图和快照内容，向用户总结页面信息。

## 注意事项

- 不要用 `mcp__chrome-devtools-mcp__new_page` 开新标签页，直接用 `navigate_page` 在当前页面导航
- 如果页面需要登录且未登录，提示用户先在浏览器中登录
- 截图是查看 UI 设计稿的最佳方式，快照是获取文本内容的最佳方式

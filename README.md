# Typora Claude-like Theme (Modified)

这是基于 [Muyiiiii/Typora_Claude-Like_Theme](https://github.com/Muyiiiii/Typora_Claude-Like_Theme) 修改的个人版 Typora 主题。

原仓库提供了模拟 Claude / Anthropic 官网视觉风格的 Typora 主题：暖棕米色调、衬线正文、赤陶橘强调色、药丸形行内代码、圆角引用块等。本仓库在原主题基础上做了导出样式、写作区布局和侧边栏交互三方面调整，并把主要修改同步到了浅色版和暗色版。为了让文件名更明确，原仓库中的浅色主题 `claude-like.css` 在这里重命名为 `claude-like-light.css`。

## 文件

| 文件 | 说明 |
|------|------|
| `claude-like-light.css` | 浅色主题，已包含本仓库的全部修改 |
| `claude-like-dark.css` | 暗色主题，已同步浅色主题的导出、布局和侧边栏修改 |

## 修改内容

### 1. 导出文件样式剥离

**问题**：原主题直接在 `html` / `body` 和正文元素上设置背景色、文字色、字体、代码块、引用块、表格等样式。导出为 HTML 或 PDF 时，这些主题样式也会被带出，导致导出文件仍然呈现 Claude-like 的暖色或暗色视觉，而不是干净的默认排版。

**修改**：

- 移除了 `@include-when-export url(...)`，导出时不再额外引入 Google Fonts。
- 新增 `.typora-export` 规则块，在导出上下文中重置主题颜色、背景、字体和边框。
- 覆盖范围包括正文容器、标题、加粗、引用块、表格、代码块、行内代码、分隔线、链接、目录、YAML 元信息块和标签等。

核心规则示例：

```css
.typora-export {
    background-color: transparent;
    color: revert;
    font-family: revert;
}

.typora-export #write {
    background-color: transparent;
    color: revert;
}
```

这样导出文件会回到更接近浏览器默认的白底黑字排版，不再携带编辑器主题的暖色调或暗色调。

### 2. 写作区布局对齐 GitHub 主题

**问题**：原 Claude-like 主题的写作区偏窄，默认最大宽度为 `780px`，大屏也只扩展到 `880px`。在较大的显示器上，正文区域利用率偏低，阅读行长和 Typora 内置 GitHub 主题差异明显。

**修改**：将 `#write` 的宽度和 padding 调整为与 Typora GitHub 主题一致。

| 属性 | 修改前 | 修改后 |
|------|--------|--------|
| 默认 `max-width` | `780px` | `860px` |
| 默认 `padding` | `32px 32px 104px` | `30px`，底部 `100px` |
| `>= 1400px` | `820px` | `1024px` |
| `>= 1800px` | `880px` | `1200px` |
| `<= 768px` 移动端规则 | 单独设置 `24px 20px 84px` | 移除，与 GitHub 主题保持一致 |

调整后，大屏下写作区能更充分地利用空间，浅色和暗色主题的正文布局保持一致。

### 3. 侧边栏 FILES / Outline 切换按钮样式

**问题**：Typora 在 Windows 和 macOS 上使用不同的侧边栏 DOM 结构。原先只针对 Windows 的 `#info-panel-tab-file` / `#info-panel-tab-outline` 写样式，在 macOS 上不会命中真正的按钮。

Typora 的两套结构大致如下：

| 平台 | 主要选择器 |
|------|------------|
| Windows | `#info-panel-tab-file`、`#info-panel-tab-outline`、`.info-panel-tab` |
| macOS | `#sidepanel-segmented-input-files`、`#sidepanel-segmented-input-outline`、`.sidebar-tab`、`.sidebar-tab-btn` |

**修改**：

- 为 macOS 的 `.sidebar-tab` 和 `.sidebar-tab-btn` 添加显式颜色、字体和激活态样式。
- 保留 Windows 的 `#info-panel-tab-file` / `#info-panel-tab-outline` 规则，兼容 Windows 结构。
- 修复 `.sidebar-tabs * { font-family: var(--font-ui) !important; }` 误伤图标字体的问题。
- 显式恢复 macOS 侧边栏按钮中 `.ty-icon` 的 `"typora-icon"` 字体，以及搜索图标 `ion-*` 的 `"Ionicons"` 字体。
- 左侧侧边栏切换按钮和右侧搜索按钮默认隐藏，仅在鼠标悬浮到侧边栏时显示。

关键行为：

```css
#typora-sidebar .sidebar-tab-btn {
    opacity: 0 !important;
    transition: opacity 0.16s ease;
}

#typora-sidebar:hover .sidebar-tab-btn,
#typora-sidebar:hover .sidebar-tab-btn > * {
    opacity: 1 !important;
}
```

这样在正文区写作时，侧边栏顶部按钮保持安静；鼠标移入侧边栏后，视图切换按钮和搜索按钮才出现。图标字体也不会再被 UI 字体覆盖而导致按钮不可见。

## 安装

将需要使用的 CSS 文件复制到 Typora 主题目录：

```bash
cp claude-like-light.css "$HOME/Library/Application Support/abnerworks.Typora/themes/"
cp claude-like-dark.css "$HOME/Library/Application Support/abnerworks.Typora/themes/"
```

重启 Typora，或者切换到其他主题后再切回对应主题，即可看到修改效果。

## 致谢

本仓库基于 [Muyiiiii/Typora_Claude-Like_Theme](https://github.com/Muyiiiii/Typora_Claude-Like_Theme) 修改而来，感谢原作者提供 Claude-like 主题基础。

## 许可证

原项目采用 MIT License。本仓库作为修改版继续使用 MIT License，并在 [LICENSE](LICENSE) 中保留原作者版权声明，同时标注本仓库修改部分的版权声明。

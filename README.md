# WebNav Hub

一站式个人网址导航，纯静态单文件实现。精选 AI 搜索、托管社交、实用工具、科技资讯、云存储与邮箱服务，共 180+ 站点即刻直达。

![WebNav Hub 界面预览](screenshot.png)

整个项目只有一个 `index.html`：无框架、无构建工具、无外部 JS 依赖，放到任何静态空间即可运行。

## 特性

### 搜索过滤

- 按站点**名称或网址**即时过滤，输入即出结果
- 命中分类自动隐藏无结果的分区，标题右侧显示 `命中数/总数`
- 顶部实时显示「找到 N 个站点」，支持一键清除
- 全部无结果时显示友好空状态提示
- 快捷键：`/` 聚焦搜索框、`Esc` 清除并退出

### 分类导航

- 吸顶毛玻璃导航栏（`backdrop-filter`），滚动时始终可见、居中排列
- **Scrollspy**：基于 `IntersectionObserver` 自动高亮当前浏览的分类
- 点击导航平滑滚动到对应分区（CSS `scroll-behavior` + `scroll-margin-top`）
- 移动端导航自动变为横向滑动，不换行

### 视觉与交互

- 「琥珀暗夜」主题：暖黑背景 + 琥珀渐变主色 + 网格纹理与顶部光晕氛围
- 卡片悬停：上浮 + 琥珀描边 + 聚光灯效果（径向渐变跟随光标，基于 CSS 变量 `--mx / --my`）
- 卡片进入视口时交错显现动画（`IntersectionObserver` + 批量延迟）
- 顶部阅读进度条（`scroll` 监听 + `requestAnimationFrame` 节流）
- 返回顶部悬浮按钮（滚动超过 480px 后出现）

### 无障碍与性能

- 搜索结果区域使用 `aria-live="polite"` 播报，卡片链接带 `aria-label`
- 完整键盘焦点环（`:focus-visible`）
- 尊重系统「减弱动态效果」（`prefers-reduced-motion` 时关闭所有动画）
- 仅动画 `transform / opacity` 等 GPU 友好属性；滚动监听经 rAF 节流；卡片容器启用 CSS `contain` 隔离重排

## 使用

无需构建，直接用浏览器打开 `index.html` 即可；或部署到任意静态托管（Vercel / Netlify / GitHub Pages / Cloudflare Pages 等，零配置上传即用）。

## 项目结构

```
.
├── index.html   # 页面结构 + 样式 + 数据 + 逻辑（单文件）
└── README.md
```

`index.html` 内部分为四块：

| 区块 | 说明 |
| ---- | ---- |
| `<style>` | 全部样式，主题变量集中在 `:root` |
| `<header>` | 页面标题与搜索框 |
| `<nav>` | 分类导航栏（由 JS 渲染） |
| `<script>` | `CATEGORIES` 数据数组 + 渲染 / 搜索 / Scrollspy / 进度条等逻辑 |

## 自定义指南

### 添加 / 修改站点

所有数据集中在脚本顶部的 `CATEGORIES` 数组。每个站点一行：

```js
["站点名称", "https://example.com/", "fa-solid fa-icon-name"]
```

| 字段 | 说明 |
| ---- | ---- |
| 第 1 项 | 站点显示名称（支持中文） |
| 第 2 项 | 跳转链接，建议以 `/` 结尾 |
| 第 3 项 | [FontAwesome](https://fontawesome.com/icons) 图标类名 |

新增分类时在数组中追加一个对象，包含 `id`（锚点）、`name`（标题）、`icon`（导航图标）、`desc`（描述）、`sites`（站点数组）。导航栏、页面区块和搜索会自动生效，无需改动其他代码。

### 新增分类

打开 `index.html`，找到脚本顶部的 `CATEGORIES` 数组，按以下格式追加一个对象：

```js
const CATEGORIES = [
  // ……已有的分类……
  {
    id: "dev",                          // 唯一 ID，用作页面锚点（#dev）
    name: "开发必备",                    // 分类标题
    icon: "fa-solid fa-code",           // 导航栏与标题处的图标
    desc: "常用开发者服务与平台",         // 标题下的简短描述
    sites: [                            // 该分类下的站点列表
      ["Stack Overflow", "https://stackoverflow.com/", "fa-brands fa-stack-overflow"],
      ["MDN", "https://developer.mozilla.org/", "fa-solid fa-book"]
    ]
  }
];
```

保存后刷新页面即可看到效果，无需修改任何其他代码——顶部导航、分类区块、Scrollspy 高亮和搜索过滤都会自动包含新分类。

> 注意：`id` 必须唯一且不含空格；同数组内已有分类的 `id` 不能重复。

### 修改主题配色

编辑 `<style>` 中 `:root` 的 CSS 变量即可全局生效：

```css
:root {
  --bg: #0a0a0c;              /* 页面背景 */
  --accent: #ffa028;          /* 主色 */
  --accent-strong: #ff8800;   /* 主色加深 */
  --text: #f4f3ef;            /* 正文颜色 */
  --muted: #83817a;           /* 次要文字 */
}
```

### 调整卡片密度

修改 `.link-grid` 的最小列宽控制每行卡片数量：

```css
.link-grid {
  grid-template-columns: repeat(auto-fill, minmax(158px, 1fr));
}
```

## 快捷键

| 按键 | 功能 |
| ---- | ---- |
| `/` | 聚焦搜索框 |
| `Esc` | 清除搜索内容并退出输入 |

## 技术栈

- 原生 HTML / CSS / JavaScript，零依赖、零构建
- [FontAwesome 7](https://fontawesome.com/) 图标（CDN 引入）
- Space Grotesk 字体（Google Fonts），中文回退系统字体

## 浏览器兼容性

支持所有现代浏览器（Chrome / Edge / Firefox / Safari 最新版）。使用了 `backdrop-filter`、CSS 变量、`IntersectionObserver` 等特性，不支持 IE。

## 许可

仅供个人学习与使用。

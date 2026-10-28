# Archify Embed 0.1.0

在 Markdown 文章内部直接显示 Archify 图表：一次外部脚本引入，加一个保存原始 JSON 的数据块。支持架构图、流程图、时序图、数据流图和生命周期图，同页可以展示多张图。

[参数预览与代码复制](index.html) · [版本清单](manifest.json) · [返回首页](../../index.html)

## 正文接入

将下面两个 HTML 块粘贴到文章正文，去掉代码示例的反引号围栏。替换其中的图表 JSON 即可：

```html
<script src="https://sonder9999.github.io/archify_pages/embed/0.1.0/archify-embed.js" defer></script>
<script type="application/json" data-archify data-options='{"colorTheme":"hotaru","height":560}'>
{
  "schema_version": 1,
  "diagram_type": "architecture",
  "meta": { "title": "应用架构", "locale": "zh-CN", "output": "app.html" },
  "components": [
    { "id": "web", "type": "frontend", "label": "网页", "pos": [40, 80], "size": [160, 80] },
    { "id": "api", "type": "backend", "label": "API", "pos": [300, 80], "size": [160, 80] }
  ],
  "connections": [
    { "id": "request", "from": "web", "to": "api", "label": "HTTPS" }
  ]
}
</script>
```

上面的 HTTPS 地址是本目录部署到 GitHub Pages 后的固定地址。本地预览使用同目录资源；准备了文件不代表远端已经部署。

这是两个正文块，JSON 可以有多行。第一个脚本自动加载同目录的 `archify-embed.css` 和 `archify-embed-fonts.css`，不用单独写 `<style>`、初始化脚本或图表容器。第二个块不执行 JavaScript，只保存完整的原始 Archify JSON；不需要 `runtimeVersion` / `diagram` 外壳。库自动创建正文图表、懒加载、适配与管理实例。

同一篇文章只需引入一次脚本，每张后续图表再放一个 `data-archify` JSON 块即可。博客不需要新增组件、插件或修改模板。文章平台需保留原始 HTML 并允许执行外部脚本；会过滤 `<script>` 的平台无法通过这两个正文块接入。网站已有 CSP 时，还需允许相应资源域名与字体的 `data:` 来源。

## 参数

参数写入 `data-options`，使用 JSON 布尔值控制入口，例如：

```html
<script type="application/json" data-archify data-options='{"theme":"auto","colorTheme":"hotaru","height":560,"showExport":true,"showSearch":true,"showFullscreen":false}'>
```

这行是 JSON 数据块的开始标签，后面仍然需要完整图表 JSON 和结束标签。展示页可生成完整可复制片段。

| 参数 | 正文数据块默认值 | 用途 |
| --- | --- | --- |
| `theme` | `"auto"` | 跟随宿主明暗；可选 `"light"`、`"dark"` |
| `height` | `420` | 画布高度，120–10000 的整数，单位 px |
| `appearance` | `"blog"` | 文章配色；`"default"` 使用原预设外观 |
| `preset` | `"classic"` | `classic`、`signal-flow`、`blueprint`、`editorial`；`null` 沿用作者预设 |
| `colorTheme` | 未指定 | `"hotaru"` 读取 Hotaru / Firefly 的现有颜色变量 |
| `colors` | 未指定 | 覆盖背景、正文、主色、次要文字和边框 |
| `motion` | `"auto"` | 开启图表动态并尊重减少动态效果；`"still"` 关闭动态 |
| `showMotion` | `false` | 显示动态切换按钮，独立于是否启用动态 |
| `showExport` | `false` | 显示导出按钮与菜单，支持 SVG / PNG |
| `showSearch` | `false` | 显示节点搜索按钮与面板 |
| `showFullscreen` | `true` | 显示全屏按钮；由库管理全屏画布尺寸与退出恢复 |
| `showTitle` | `false` | 显示大标题和副标题 |
| `showTheme` / `showPreset` | `false` | 显示主题 / 皮肤切换按钮 |
| `showNotes` / `showIndex` | `false` | 显示说明卡片 / 节点索引 |
| `controls` | `"compact"` | `compact`、`full`、`none`；单独的显示参数优先 |
| `display` | `"viewer"` | `"diagram"` 只展示图表，隐藏画布导航和额外浮层 |
| `initialView` | `"fit"` | 完整适配；`"reading"` 使用阅读倍率 |
| `interaction` | `"cooperative"` | 兼顾文章滚动；`"full"` 完整交互；`"none"` 禁用图表交互 |
| `loading` | `"lazy"` | 接近视口时加载；`"eager"` 立即加载 |
| `locale` | 使用图表语言 | 可显式传入 `"zh-CN"` 或 `"en"` |

地图按钮和地图面板不提供；不存在 `showMap` 参数。动态、导出、搜索按钮默认隐藏，动态本身默认开启；没有动态图层的图仍显示为静态。正文参数也可以写成 `show-export="true"` 等 HTML 属性，同名属性优先于 `data-options`。

`display: "diagram"` 或 `controls: "none"` 会改变未显式指定的入口默认值。上表描述未改变这两个布局参数时的正文默认行为；直接使用 SDK / 自定义标签有独立的默认值。

## 颜色与主题

`theme: "auto"` 跟随宿主明暗信息，没有宿主信息时跟随系统。颜色适配是单独的配置：

- Hotaru / Firefly：传入 `colorTheme: "hotaru"`，读取 `--card-bg`、`--tw-prose-body`、`--primary`、`--tw-prose-captions`、`--line-divider`；缺失变量时使用库内置配色。
- 其他博客：默认使用通用 `blog` 配色；可以传 `colors` 绑定博客自己的 CSS 变量或直接使用固定颜色。
- `colors` 中的对应项会覆盖命名主题颜色，支持 CSS 颜色与 `var(...)`。

```json
{
  "theme": "auto",
  "height": 560,
  "colors": {
    "surface": "var(--my-card-bg, #142329)",
    "text": "var(--my-text, #e9f0f1)",
    "accent": "var(--my-accent, #7ac7bf)",
    "muted": "var(--my-muted, #9badb2)",
    "border": "var(--my-border, #354950)"
  },
  "showExport": true
}
```

不用修改博客定义变量，也可以直接传十六进制颜色。库使用 Shadow DOM 隔离图表样式，同时继承宿主 CSS 变量；主题改变后会更新配色。

## JSON 与 schema 支持

| `diagram_type` | 支持的 `schema_version` |
| --- | --- |
| `architecture` | 1 |
| `workflow` | 1、2 |
| `sequence` | 1 |
| `dataflow` | 1 |
| `lifecycle` | 1、2 |

Embed 版本与 JSON schema 版本分别管理。不同类型使用自己的原始数据结构；不要将生命周期的 `states` 改写成架构图的 `components`。展示页提供五种已有公开示例，允许粘贴并验证 JSON；输入错误会保留上一张有效图。

生成 HTML 数据块时，将 JSON 文本里的 `<` 写为 `\u003c`，尤其是原文包含 `</script>` 时，避免提前结束标签。参数属性中的引号需按 HTML 属性规则转义；展示页复制功能会自动处理这两类转义。

## 发布内容与资源地址

```text
embed/0.1.0/
├── archify-embed.js             # 推荐：正文引入的浏览器库
├── archify-embed.css            # 自动加载的图表样式
├── archify-embed-fonts.css      # 自动加载的字体，包含字体数据
├── archify-embed.mjs            # 可选：ES Module / SDK
├── manifest.json               # Embed 版本、类型与 schema 清单
├── LICENSE                     # 代码 MIT 许可
├── JetBrainsMono-OFL.txt        # 字体许可
├── index.html                  # 五种图表预览、参数与正文复制
└── README.md                   # 本版本说明
```

浏览器正文使用前三个文件，约 1.65 MiB，未压缩。ES Module、清单、说明和展示页用于开发、发现与许可分发；无需额外的 Runtime 文件。保留同目录布局，JS / MJS 服务类型为 `text/javascript`，CSS 为 `text/css`，JSON 为 `application/json`。

固定入口：

- 展示页：`https://sonder9999.github.io/archify_pages/embed/0.1.0/index.html`
- 正文脚本：`https://sonder9999.github.io/archify_pages/embed/0.1.0/archify-embed.js`
- 样式：`https://sonder9999.github.io/archify_pages/embed/0.1.0/archify-embed.css`
- 字体：`https://sonder9999.github.io/archify_pages/embed/0.1.0/archify-embed-fonts.css`

本仓库 Pages 由 `main` 分支根目录发布。提交并推送这些资源后，等待 Pages 部署完成再使用远端地址；不需要额外的 GitHub Release。若需要标签或下载归档，可用 `embed-v0.1.0` 明确区分旧 Runtime。

## 0.1.0 发布说明

首次提供文章内嵌资源：原始 JSON 数据块自动挂载、同页多图、独立样式、主题与颜色参数、默认隐藏的动态 / 导出 / 搜索入口、全屏及 SVG / PNG 导出。展示页覆盖五种图表，提供参数预览和安全的正文代码复制。

资源由 Archify General 的 `general/build.mjs` 构建，Embed 入口是 `general/embed/index.mjs`；schema 清单从编译器的各类型 schema 文件生成。该构建复用上游 3.0.1 编译器与本地 Embed 修改。发布前需要保存最终源码与验证记录；此目录的版本号不表示开发仓库中的未提交源码已经归档。

已有 `runtime/0.1.0/` 独立页面资源保持原样。新 Embed 首次发布也使用 0.1.0；两者命名空间不同，不能混用 JS / CSS。发布后冻结本版本资源，更新时新建 `embed/<新版本>/`，让旧文章继续引用旧版。

代码适用 [MIT](LICENSE)，JetBrains Mono 适用 [SIL OFL 1.1](JetBrainsMono-OFL.txt)。图标与其他第三方资源说明见仓库根目录的 [LICENSE](../../LICENSE)。

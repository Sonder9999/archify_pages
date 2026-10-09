# Archify Runtime

Archify 图表查看器的静态资源包。HTML 保存图表 JSON，公共 JS 负责解析与交互，公共 CSS 提供统一样式。支持架构图、流程图、时序图、数据流图和生命周期图。

[演示与使用](https://sonder9999.github.io/archify_pages/) · [GitHub 仓库](https://github.com/Sonder9999/archify_pages)

## 目录

```text
├── index.html                 # 演示与使用入口
├── README.md
├── LICENSE                    # 代码、字体许可与第三方说明
└── runtime/
    └── 0.1.0/
        ├── archify.js          # 图表解析与查看器
        ├── archify.css         # 查看器样式
        ├── archify-overview.architecture.html
        ├── archify-authoring.workflow.html
        ├── archify-loading.sequence.html
        ├── archify-transform.dataflow.html
        └── archify-release.lifecycle.html
```

每个版本使用独立目录。页面只需引用同一版本的 `archify.js` 和 `archify.css`。字体与内置图标已包含在资源包中。

打开 [首页](index.html) 浏览五种演示，并复制资源地址、完整 HTML 页面或 iframe 嵌入代码。线上首页自动使用当前站点的根地址；本地打开时，填写实际资源地址后再复制用于分享的代码。

## 图表演示

以下示例介绍 Archify 的组成、使用方式和版本管理。每个 HTML 内嵌自己的 JSON，并引用同目录的 JS/CSS；下载完整版本目录后，可以直接用浏览器打开。

| 类型 | 示例 | 内容 |
| --- | --- | --- |
| 架构图 | [Archify 的组成](runtime/0.1.0/archify-overview.architecture.html) | HTML、公共 JS/CSS、SVG 与交互查看器 |
| 流程图 | [制作并分享一张图](runtime/0.1.0/archify-authoring.workflow.html) | 定义内容、生成页面、检查、分享与嵌入 |
| 时序图 | [浏览器加载过程](runtime/0.1.0/archify-loading.sequence.html) | 读取 HTML、加载资源、解析数据和显示图表 |
| 数据流图 | [从数据到页面](runtime/0.1.0/archify-transform.dataflow.html) | JSON 校验、布局编译、SVG 输出与交互展示 |
| 生命周期图 | [资源版本维护](runtime/0.1.0/archify-release.lifecycle.html) | 准备、验证、发布、保留旧版与升级 |

示例页面可作为模板：修改 `archify-data` 中的图表数据即可。要单独分发一个 HTML，将其中的 `./archify.js` 和 `./archify.css` 改为同版本资源的完整 HTTPS 地址。

## HTML 使用方式

将资源包放到支持 HTTPS 的静态服务器上，保留 `runtime/<版本>/` 目录结构。以下使用 `https://static.example.com/archify` 作为示例根地址，使用时替换为实际资源地址。

```html
<!doctype html>
<html lang="zh-CN" data-theme="dark" data-unified="true" data-motion="still">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>应用架构</title>
  <link rel="stylesheet"
        href="https://static.example.com/archify/runtime/0.1.0/archify.css">
</head>
<body>
  <main id="archify-root" data-archify-root aria-busy="true">
    <p id="archify-status" role="status">正在加载图表…</p>
  </main>
  <script id="archify-data" type="application/json">
  {
    "runtimeVersion": "0.1.0",
    "diagram": {
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
  }
  </script>
  <script src="https://static.example.com/archify/runtime/0.1.0/archify.js" defer></script>
</body>
</html>
```

保留 `archify-root` 和 `archify-data` 的 ID。查看器加载后会读取 JSON，生成图表与控制界面，无需额外调用初始化函数。

JSON 外层的 `runtimeVersion` 声明查看器版本，`diagram` 保存图表数据。`diagram_type` 可取 `architecture`、`workflow`、`sequence`、`dataflow` 或 `lifecycle`；不同类型使用各自的数据结构，上面的 `components` 和 `connections` 示例适用于架构图。

动态生成 JSON 数据块时，应将文本中的 `<` 转义为 `\u003c`，避免包含 `</script>` 的内容提前结束标签。

使用完整 HTTPS 资源地址的 HTML 可以移动或单独分发，接收者用浏览器打开后会自动加载 JS 和 CSS。加载需要网络可访问资源地址；离线使用需另行生成包含资源的单文件 HTML。邮件、聊天工具或博客的预览界面可能不执行脚本，应在浏览器中打开文件。

## 网页与博客嵌入

将图表 HTML 托管为独立页面，然后通过 iframe 嵌入：

```html
<iframe
  src="https://www.example.com/diagrams/app.html"
  title="应用架构图"
  width="100%"
  height="720"
  style="border: 0"
  allowfullscreen>
</iframe>
```

替换 `src` 为实际图表页面地址，并按版面调整高度。每个 iframe 承载一张图，页面样式与博客样式隔离。当前资源按独立页面设计，在同一网页展示多张图时使用多个 iframe。

## 静态托管

保留目录结构上传 `runtime/`，并随资源分发根目录的 `LICENSE`。资源服务应返回正确的文件内容与响应类型：JS 为 `text/javascript`，CSS 为 `text/css`。引用地址应指向实际静态文件，不能使用仓库的文件预览页地址。

HTML 中使用绝对资源地址，文件位置就不会影响 JS/CSS 的加载。若网页设置了 Content Security Policy，需要允许该资源域名；iframe 所在页面也需要允许嵌入图表地址。

## 版本与更新

- `runtime/0.1.0/` 表示查看器版本，HTML 中的 `runtimeVersion`、JS 和 CSS 必须一致。
- 发布后的版本目录保持不变。更新资源时增加新版本目录，保留旧版供已有 HTML 使用。
- 升级图表页面时同时更新 JS/CSS 地址和 `runtimeVersion`，并按目标版本的数据格式检查图表内容。
- `diagram.schema_version` 表示图表数据格式版本，与查看器版本分别管理。
- 使用明确版本地址，避免自动指向最新版本的地址使已有图表行为发生变化。

## 许可证

根目录的 [LICENSE](LICENSE) 集中保存代码的 MIT 许可证、JetBrains Mono 的完整 SIL Open Font License 1.1，以及图标和其他第三方资源说明。第三方资源仍适用各自的许可条款。

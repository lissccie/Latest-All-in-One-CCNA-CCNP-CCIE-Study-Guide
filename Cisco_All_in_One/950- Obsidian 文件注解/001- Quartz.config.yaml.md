

# Quartz 5 配置文件 (`quartz.config.yaml`) 详解

这份配置文件是 **Quartz 5**（将 Obsidian 笔记转换为静态网页的工具）的核心设置。它主要由 **基础全局配置 (Configuration)**、**插件系统 (Plugins)** 以及 **布局系统 (Layout)** 三大板块组成。

---

## 一、 基础全局配置 (configuration)
这部分定义了网站的全局属性，如网站标题、字体、颜色主题、搜索引擎优化（SEO）等。

| 配置项 | 作用说明 |
| :--- | :--- |
| `pageTitle: Quartz 5` | **网站主标题**：显示在浏览器标签页和左侧栏。 |
| `pageTitleSuffix: ""` | **页面标题后缀**：留空表示不追加额外文本（例如不追加 ` \| My Blog`）。 |
| `enableSPA: true` | **开启单页应用模式**：切换页面时无缝刷新，极大地提升网站浏览速度。 |
| `enablePopovers: true` | **开启鼠标悬停预览**：鼠标移到链接上时，会弹窗显示该笔记的预览内容。 |
| `analytics.provider: plausible` | **网站访问量统计**：使用 Plausible 服务来分析网站流量。 |
| `locale: en-US` | **语言区域设置**：使用美式英语（会影响日期、时间等格式的渲染）。 |
| `baseUrl: ciscoccie.pages.dev` | **网站根域名**：部署后的域名，用于给搜索引擎生成正确的 RSS 和站点地图。 |
| `ignorePatterns` | **忽略列表**：处于该列表下的文件夹或文件不会被解析和发布（如 `private`, `templates`, `.obsidian`）。 |

### 1. 字体配置 (typography)
* **`header` (Schibsted Grotesk)**：文章标题（H1 - H6）所使用的字体。
* **`body` (Source Sans Pro)**：正文文本所使用的字体。
* **`code` (IBM Plex Mono)**：代码块、内联代码所使用的等宽字体。

### 2. 颜色配置 (colors)
定义了**浅色模式 (`lightMode`)** 和 **深色模式 (`darkMode`)** 的具体配色：
* `light`: 页面背景色。
* `lightgray`: 边框、分割线、以及未激活状态的轻量级灰色。
* `gray`: 辅助文本、小图标的颜色。
* `darkgray`: 正文文本颜色。
* `dark`: 标题以及加粗文本的颜色。
* `secondary`: 核心交互颜色（如：链接颜色、左侧栏选中状态，浅色下为深蓝，深色下为浅蓝）。
* `tertiary`: 鼠标悬停链接时、或者部分标签的互动颜色。
* `highlight`: 内部链接高亮背景色。
* `textHighlight`: 文本标记高亮色（即 Obsidian 中 `==高亮==` 语法的背景色）。

---

## 二、 插件系统 (plugins)
Quartz 通过插件来扩展核心功能。每个插件包含 `source`（插件来源）、`enabled`（是否启用），以及可选的 `options`（参数控制）和 `layout`（布局位置）。

### 1. 核心解析与转换插件
这类插件负责将你的 Markdown 语法的笔记“翻译”成标准的 HTML 网页。

* **页面时间获取 (`created-modified-date`)**
  * **作用**：自动获取并解析页面的创建或修改时间。
  * **配置**：`defaultDateType: modified`（默认显示修改时间）；`priority` 定义了获取时间的优先级（先读笔记属性 frontmatter $\rightarrow$ 再读 Git 提交记录 $\rightarrow$ 最后读文件系统）。
* **代码高亮 (`syntax-highlighting`)**
  * **作用**：为代码块提供高亮。
  * **配置**：浅色模式使用 `github-light` 主题，深色模式使用 `github-dark` 主题。
* **Obsidian 语法兼容 (`obsidian-flavored-markdown`)** > [!IMPORTANT]
  > 这是最核心的插件，用于完美兼容 Obsidian 的特有语法。
  > * `comments`: 支持 `%%双百分号注释%%`
  > * `highlight`: 支持 `==高亮==`
  > * `wikilinks`: 支持 `[[双链]]` 内部链接
  > * `callouts`: 支持 `> [!NOTE]` 呼出块
  > * `mermaid`: 支持 Mermaid 流程图与图表
  > * `parseTags`: 支持文本中直接写 `#标签`
  > * `parseArrows`: 支持快捷箭头转换（如 `->` 自动变 `→`）
  > * `parseBlockReferences`: 支持块引用（`^blockId`）
  > * `enableYouTubeEmbed` / `enableVideoEmbed`: 支持视频嵌入
* **GitHub 风格 Markdown (`github-flavored-markdown`)**
  * **作用**：支持标准的 GFM 语法（如删除线 `~~删除~~`、标准 Markdown 表格等）。
* **链接关系爬取 (`crawl-links`)**
  * **作用**：解析并追踪笔记之间的所有链接关系。
  * **配置**：`markdownLinkResolution: shortest`（采用最短路径匹配双链，与 Obsidian 默认逻辑一致）。
* **自动描述生成 (`description`)**
  * **作用**：自动截取文章前几句话作为网页的 Meta Description，有利于搜索引擎优化（SEO）。
* **LaTeX 数学公式 (`latex`)**
  * **作用**：在网页端完美渲染数学公式。
  * **配置**：使用性能极佳的 `katex` 渲染引擎。
* **硬换行支持 (`hard-line-breaks`)**
  * **作用**：允许在 Markdown 中直接回车换行，不需要在行尾额外敲两个空格。
* **草稿过滤 (`remove-draft`)**
  * **作用**：如果笔记的 YAML 属性中包含 `draft: true`，构建时会自动过滤，不会将其发布。
* **隐藏特定页面 (`unlisted-pages`)**
  * **作用**：允许通过属性将某些页面隐藏（不会出现在页面列表、搜索结果或站点地图中）。
* **页面加密 (`encrypted-pages`)**
  * **作用**：支持对特定笔记设置密码。
  * **配置**：通过 Frontmatter 中的 `password` 字段识别密码；`iterations: 600000` 保证了极高的加密哈希安全度。
* **别名重定向 (`alias-redirects`)**
  * **作用**：完美兼容 Obsidian 的 `aliases` 属性。访问别名路径时会自动重定向到原笔记。
* **站点索引与订阅 (`content-index`)**
  * **作用**：自动生成 `sitemap.xml`（供搜索引擎抓取）以及 `RSS` 订阅源。
* **基础页面生成器群组**
  * `favicon`: 启用网站图标支持。
  * `og-image`: 自动生成社交媒体分享时的预览图（Open Graph Image）。
  * `cname`: 允许配置自定义域名的 CNAME 文件。
  * `canvas-page`: 支持直接在网页上渲染 Obsidian 的 **Canvas (白板)** 文件。
  * `content-page`: 负责渲染标准的笔记正文页面。
  * `folder-page`: 负责渲染文件夹的聚合列表页。
  * `tag-page`: 负责渲染标签的聚合列表页。

### 2. 界面组件与功能布局插件
这类插件会在网页上生成可见的交互组件，并通过 `layout` 参数控制它们出现在页面的什么位置（左侧栏、右侧栏、正文上方或正文下方）。

* **文章目录 (`table-of-contents`)**
  * **位置**：`right`（右侧栏），优先级为 `30`。
* **文件资源管理器 (`explorer`)**
  * **位置**：`left`（左侧栏），用于展现文件夹折叠树，优先级 `50`。
* **局部关系图谱 (`graph`)**
  * **位置**：`right`（右侧栏），展示当前笔记的双链图谱，优先级 `10`。
* **全局搜索框 (`search`)**
  * **位置**：`left`（左侧栏），划归在 `toolbar`（工具栏）组内，且 `grow: true` 代表自动拉伸填满工具栏空间。
* **反向链接 (`backlinks`)**
  * **位置**：`right`（右侧栏），列出有哪些其他笔记引用了当前页面，优先级 `50`。
* **文章标题 (`article-title`)**
  * **位置**：`beforeBody`（正文内容最上方）。
* **文章元数据 (`content-meta`)**
  * **位置**：`beforeBody`（标题下方），展示字数、预计阅读时间、修改日期等。
* **站点标题 (`page-title`)**
  * **位置**：`left`（左侧栏最顶部），用于点击回到首页。
* **深色模式切换 (`darkmode`)**
  * **位置**：`left`（左侧栏），划归在 `toolbar` 组内。
* **读者模式 (`reader-mode`)**
  * **位置**：`left`（左侧栏），提供专注阅读的开关，划归在 `toolbar` 组内。
* **面包屑导航 (`breadcrumbs`)**
  * **位置**：`beforeBody`（正文上方）；`condition: not-index` 限制了**只有非首页**才会显示导航路径。
* **页脚 (`footer`)**
  * **位置**：页面最底部，配置了指向 Quartz 的 GitHub 和 Discord 的友情链接。
* **移动端间距占位符 (`spacer`)**
  * **位置**：`left`（左侧栏）；`display: mobile-only` 代表它只在手机端屏幕生效，用来微调排版间距。
* **知识库基础页 (`bases-page`)**
  * **作用**：渲染网站的基础承载页。
* **笔记属性面板 (`note-properties`)**
  * **位置**：`beforeBody`（正文上方）。
  * **配置**：像 Obsidian 一样在网页端展示 YAML 属性。这里限制了只展示 `description`, `tags`, `aliases` 三个特定属性，其余隐藏。
* **第三方主题增强 (`quartz-themes`)**
  * **作用**：引入了来自 `saberzero1/quartz-themes` 的外部样式扩展。
* **手绘图渲染 (`obsidian-plugin-excalidraw`)**
  * **作用**：完美兼容 Obsidian 中的 **Excalidraw** 手绘图。
  * **配置**：`enableInteraction: true`（允许用户在网页端对复杂手绘图进行放大和拖拽交互）；`darkMode: auto`（自动跟随网站切换深浅色主题）。

### 3. 当前未启用的插件 (`enabled: false`)
> 如果日后需要这些功能，直接在文件中将对应插件的 `enabled` 改为 `true` 即可。
* `citations`: 文献引用与学术学术规范插件。
* `ox-hugo`: Emacs Org-mode 导出格式兼容。
* `roam`: Roam Research 语法风格兼容。
* `explicit-publish`: 显式发布控制（开启后只有明确标记 `publish: true` 的笔记才会被转为网页）。
* `stacked-pages`: 开启类似 Andy Matuschak 的叠层多卡片滑动堆叠效果。
* `tag-list`: 在正文顶部渲染文章包含的所有标签列表。
* `recent-notes`: 在侧边栏或主页展示最近更新或创建的笔记列表。
* `comments`: 基于 Giscus / Github Discussions 的评论系统。

---

## 三、 布局全局控制 (layout)
这部分用于对前面插件划分的区域以及具体的特殊页面类型（如 404 页面、文件夹页面）进行宏观的组件拦截调配。

```yaml
layout:
  groups:                         # 组件组定义
    toolbar:                      # 统一控制前面被归类为 "toolbar" 的组件（搜索、深色切换、读者模式）
      priority: 35
      direction: row              # 内部组件横向一字排开
      gap: 0.5rem                 # 组件之间的间距为 0.5rem
  byPageType:                     # 针对不同的页面类型，对组件进行精准拦截
    "404":                        # 1. 404 错误页面
      positions:
        beforeBody: []            # 清空上方组件（不显示面包屑、文章标题等）
        left: []                  # 清空左侧组件（不显示资源管理器、搜索框等）
        right: []                 # 清空右侧组件
    content: {}                   # 2. 标准正文页：默认应用所有插件的原本布局位置
    folder:                       # 3. 文件夹聚合列表页
      exclude:
        - reader-mode             # 排除“读者模式”开关（列表页不需要专注阅读）
      positions:
        right: []                 # 清空右侧组件（列表页右侧不显示目录和图谱）
    tag:                          # 4. 标签聚合列表页
      exclude:
        - reader-mode             # 排除“读者模式”开关
      positions:
        right: []                 # 清空右侧组件（标签页右侧不显示目录和图谱）
    canvas: {}                    # 5. Canvas 白板页：保持默认布局
    bases: {}                     # 6. 知识库基础页：保持默认布局
# CFBLOG-Plus 前台模板开发指南

本指南将教你如何为 CFBLOG-Plus 编写自定义的前台 HTML 模板。

---

## 1. 基础语法 (Mustache)

系统内置了一个轻量级的 Mustache 引擎，支持以下语法：

-   **变量渲染 (转义)**：`{{变量名}}`
    -   例如：`{{OPT.siteName}}` 会渲染为网站名称。
-   **原始 HTML 渲染 (不转义)**：`{{{变量名}}}` 或 `{{&变量名}}`
    -   用于渲染文章内容或带有 HTML 标签的标题。
-   **循环 / 存在判断**：`{{#列表名}} ... {{/列表名}}`
    -   如果后面是一个列表，则循环遍历；如果是一个对象/布尔值，则判断是否存在。
-   **反向判断**：`{{^列表名}} ... {{/列表名}}`
    -   当列表为空或变量不存在时显示其中的内容。

---

## 2. 全局配置变量 (`OPT`)

几乎在所有页面都可以通过 `{{OPT.xxx}}` 访问以下配置：

| 变量名 | 说明 |
| :--- | :--- |
| `OPT.siteName` | 网站名称 |
| `OPT.siteDescription` | 网站描述 |
| `OPT.keyWords` | 网站关键词 |
| `OPT.logo` | Logo 图片地址 |
| `OPT.top_flag` | 置顶标签内容 (通常是 HTML 代码) |

---

## 3. 首页与列表页 (`index.html`)

适用于：首页、分页页、分类页、标签页。

### 数据对象

| 变量名 | 类型 | 说明 |
| :--- | :--- | :--- |
| `title` | String | 页面标题 (包含页码) |
| `articleList` | Array | 当前页的文章列表 |
| `pageNewer` | Array | 上一页链接 (数组形式，方便 `{{#pageNewer}}` 判断) |
| `pageOlder` | Array | 下一页链接 |

### `articleList` 中的文章属性
在 `{{#articleList}}` 循环内部使用：
- `{{title}}`: 文章标题 (可能包含置顶图标)
- `{{url}}`: 文章详情页地址
- `{{createDate10}}`: 日期 (格式如 2023-10-24)
- `{{#category}} {{.}} {{/category}}`: 循环渲染分类名称

---

## 4. 文章详情页 (`article.html`)

### 数据对象

| 变量名 | 类型 | 说明 |
| :--- | :--- | :--- |
| `title` | String | 文章标题 - 网站名 |
| `articleSingle` | Object | 当前文章的完整详情 |

### `articleSingle` 中的属性
- `{{articleSingle.title}}`: 文章标题
- `{{{articleSingle.content}}}`: **文章正文 (必须用三个大括号)**
- `{{articleSingle.createDate10}}`: 发布日期
- `{{#articleSingle.category}} {{.}} {{/articleSingle.category}}`: 分类循环

---

## 5. 公用小部件 (Widgets)

无论在列表页还是详情页，你都可以访问这些侧边栏数据：

-   **菜单列表**: `{{#widgetMenuList}}` -> 属性：`{{name}}`, `{{url}}`
-   **分类列表**: `{{#widgetCategoryList}}` -> 内容即分类名
-   **标签列表**: `{{#widgetTagsList}}` -> 内容即标签名
-   **最新文章**: `{{#widgetRecentlyList}}` -> 属性同文章列表
-   **友情链接**: `{{#widgetLinkList}}` -> 属性：`{{name}}`, `{{url}}`

---

## 6. 示例代码片段

### 文章列表循环
```html
{{#articleList}}
<article>
  <h2><a href="{{url}}">{{{title}}}</a></h2>
  <span class="date">{{createDate10}}</span>
</article>
{{/articleList}}
```

### 分页控制
```html
{{#pageNewer}}
  <a href="{{url}}">上一页</a>
{{/pageNewer}}

{{#pageOlder}}
  <a href="{{url}}">下一页</a>
{{/pageOlder}}
```

---

## 7. 调试说明

如果你修改了模板但前台没变化，请注意：
1. **Cloudflare 缓存**: `worker.js` 设置了 `cacheTtl: 600` (10分钟缓存)，调试时可以暂时减小该值。
2. **CDN 引用**: 如果你使用 GitHub 存储模板，建议配合使用 `jsDelivr` 等 CDN 以确保 Worker 抓取顺利。

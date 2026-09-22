先定义一个常量

```rust
const MAIN_CSS: Asset = asset!("/assets/main.css");
```

然后在 App 的根组件中使用它

```rust
#[component]
fn App() -> Element {
    rsx! {
        document::Link { rel: "stylesheet", href: MAIN_CSS }
        // 其它代码
    }
}
```

其中的 `document::Link {...}` 也可以使用 `link {...}` 替换，最终效果一样。注意：这两种写法在底层逻辑、路径处理和构建优化上并不等价。

| **特性**     | **link { ... } (传统写法)**             | **document::Link { ... } (推荐写法)**        |
| ------------ | --------------------------------------- | -------------------------------------------- |
| **本质**     | 标准 HTML `link` 标签。                 | Dioxus 官方提供的**组件**（用于操作 Head）。 |
| **渲染位置** | 你把它写在 RSX 的哪里，它就渲染在哪里。 | **自动移动**到 HTML 的 `<head>` 标签中。     |
| **路径处理** | 只是简单的字符串，不校验文件是否存在。  | 配合 `asset!` 宏，提供**编译期路径校验**。   |
| **热重载**   | 依赖浏览器对 CSS 链接的刷新。           | 深度集成 Dioxus 资产流水线，热重载更稳定。   |

### 简写形式

`document::Link { rel: "stylesheet", href: MAIN_CSS }` 有点长，可以改用以下等价写法简化代码

```rust
document::Stylesheet { href: MAIN_CSS } // ⭐️
```

### Document 家族

与 Stylesheet 类似，以下都遵循同样的“传送”逻辑（自动置于 `<head>`）：

- **`document::Title { "页面标题" }`**: 设置网页标签页标题。
- **`document::Meta { name: "description", content: "..." }`**: 设置 SEO 信息。
- **`document::Script { src: asset!("/assets/script.js") }`**: 引入脚本。

### document::Stylesheet

如果不考虑 CLI 的额外处理，仅看生成的 HTML 节点，下面两行 Rust 代码会渲染出完全相同的 HTML DOM 结构：

```rust
// 使用 Stylesheet ⭐️
document::Stylesheet { href: asset!("/assets/style.css") }

// 使用 Link 显式指定 rel
document::Link { rel: "stylesheet", href: asset!("/assets/style.css") }
```

它们在 DOM 中都会输出：

```html
<link rel="stylesheet" href="/assets/style.css" />
```

可见在生成的 HTML 结构上，这两种写法是等价的；但在 Dioxus CLI 的构建与开发体验（如 CSS 预处理和 CSS 热重载）上，它们并不等价。Dioxus CLI (`dx`) 会针对 `document::Stylesheet` 做专门的工具链优化和生命周期拦截，而 `document::Link` 仅仅被视作一个通用的 HTML 标签属性映射。

**使用建议**：引入 CSS 样式表时**始终使用 `document::Stylesheet`**，这样可以最大化享受 `dx` CLI 提供的 CSS 热重载和资源构建优化；只有在处理 Favicon、Font Preload、Canonical URL 等非 CSS 链入时，才使用 `document::Link`。

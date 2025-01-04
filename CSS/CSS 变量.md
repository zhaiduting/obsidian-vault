```css
@import url("@styles/other.css")

:root {
	--main-color: blue;
}

.root {
	color: var(--main-color);
}
```

注意
1. `@import` 是 CSS 中的标准语法，类似于在 JavaScript 中使用 `import` 来引入模块。
2. CSS 变量必须以 `--` 开头，这是为了与普通属性区分，去掉 `--` 会导致错误。
3. `:root` 是定义全局 CSS 变量的地方，通常用于全局范围内的样式设置。

### 变量的作用域

`:root` 是一个伪类选择器，表示文档的根元素，通常在 HTML 中是 `<html>` 标签。它的作用通常是在文档的根级别定义全局的 CSS 变量，这样可以使这些变量在整个文档中都可用。

不使用 `:root`，而将 CSS 变量定义在其他选择器中，是可以的，但作用域会有很大的不同。例如以下文件中，`h2` 的背景色**无效**，而 `.foo p` 的背景色**可能有效，但取决于 HTML 结构**。

```css
p {
  --main-color: blue;
}

h2 {
  /* 以下无效 */
  background-color: var(--main-color);
}

.foo p {
  /* 对 foo 类下的 p 元素有效 */
  background-color: var(--main-color);
}
```

以上代码中 --main-color 的作用域限定于 p 元素，对于 h2 元素不生效。对于以下 p 元素并没有使用 --main-color 变量
```html
<p>This is a paragraph.</p>
<div class="foo"></div>
```

对于以下 p 元素仍然无效
```html
<div class="foo">
  <div><p>This is a paragraph</p></div>
</div>
```

只有类似下面这样 `.foo p` 选择器才会正确匹配到 p 元素，样式生效
```css
<div class="foo">
  <p>This is a paragraph.</p>
</div>
```
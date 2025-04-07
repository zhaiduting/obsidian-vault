|          | `document.compatMode`（文档模式）            | `box-sizing`（CSS 盒模型）                |
| -------- | -------------------------------------- | ------------------------------------ |
| 影响范围     | 整个文档                                   | 单个 HTML 元素                           |
| 作用       | 确定 HTML 解析模式（标准模式 vs 怪异模式）             | 控制元素的盒模型计算方式                         |
| 可能的值     | `"CSS1Compat"` / `"BackCompat"`        | `"content-box"` / `"border-box"`     |
| 影响       | 影响 `box-sizing` 默认值、`offsetWidth` 计算方式 | 影响 `width` 是否包含 `border` 和 `padding` |
| 受 CSS 影响 | ❌（仅由 `<!DOCTYPE>` 影响）                  | ✅（可由 CSS `box-sizing` 规则修改）          |

### 测试代码

```html
<!DOCTYPE html>
<!-- 删除首行可切换到怪异模式 -->
<html lang="en">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<style>
			body {
			    margin: 20px;
			    padding: 10px;
			    border: 5px solid red;
			    background-color: lightyellow;
			}
			#testBox {
			    width: 200px;
			    padding: 20px;
			    border: 10px solid blue;
			    background-color: lightgreen;
			}
			table {
			    width: auto;
			    border: 2px solid black;
			}
			pre {
			    background: #f4f4f4;
			    padding: 10px;
			    border: 1px solid #ddd;
			    white-space: pre-wrap;
			}
		</style>
	</head>
	<body>
		<div id="testBox">测试盒模型</div>
		<table>
			<tr>
				<td>表格测试</td>
			</tr>
		</table>
		<pre id="output"></pre>

		<script>
			const div = document.getElementById("testBox");
			const output = document.getElementById("output");

			function logInfo(label) {
				try {
					const value = eval(label);
					output.textContent += `${label}: ${value}\n`;
				} catch (e) {
					output.textContent += `${label}: [计算错误]\n`;
				}
			}

			logInfo("document.compatMode");
			logInfo("getComputedStyle(div).boxSizing");
			logInfo("div.offsetWidth");
			logInfo("div.clientWidth");
			logInfo("div.scrollWidth");
			logInfo("document.body.offsetTop");

			logInfo("document.body.clientWidth");
			logInfo("document.body.clientHeight");
			logInfo("document.documentElement.clientWidth");
			logInfo('document.querySelector("table").offsetWidth');
		</script>
	</body>
</html>
```

### 显示结果

div、table 和 pre 元素外观大小完全一样，没有区别。但是 body 高度不同。
![文档模式（怪异）](https://lib.zhaiduting.work.gd/uPic/文档模式（怪异）.png)
![文档模式（标准）](https://lib.zhaiduting.work.gd/uPic/文档模式（标准）.png)

### 结论

**标准模式** 和 **怪异模式** 的区别主要体现在 **`document.body.clientWidth` 和 `clientHeight`**。

`div` 没有区别的原因是 **盒模型的计算在 `content-box` 下没有区别**。

在两份文件的 style 标签内都加上如下代码以改变盒模型的计算方式，结果显示两份文件的 div 元素的宽度都缩小了（但是缩小后的两个 div 仍然一样大）。

```css
*, *::before, *::after {
    box-sizing: border-box;
}
```

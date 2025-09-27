---
author:
  - Gemini
created: 2025-09-28
source: https://g.co/gemini/share/66ec28cc4aa2
---

[作者：Franco Victorio](https://medium.com/@fvictorio?source=post_page---byline--a0d98c845e70---------------------------------------)
[翻译：Gemini](https://g.co/gemini/share/66ec28cc4aa2)

在这篇文章中，我将向您展示如何为 [Prettier](https://prettier.io/) 构建一个非常简单的插件。您将学习如何组织、测试以及当然是编写插件。我远非这方面的专家，但在为 [Solidity 插件](https://github.com/prettier-solidity/prettier-plugin-solidity/)做贡献的过程中学到了很多，并且注意到除了官方文档之外，关于这个主题的资料似乎并不多。

我们将使用 **[TOML](https://github.com/toml-lang/toml)** 作为示例语言。我选择 TOML 是因为它语法简单，而且据我所知，目前还没有 Prettier 插件支持它。正如您将看到的，最终结果不会是一个可用的插件，但如果运气好的话，您将学到足够多的知识来开发一个合适的插件。

您为什么要学习这个呢？嗯，有两个强有力的理由。首先，您将能够为任何尚未支持的语言（例如 TOML 和 Dockerfile，但可能还有很多其他语言）创建插件。其次，您将能够为 [现有插件](https://prettier.io/docs/en/plugins.html#official-plugins) 中的一个做出贡献。此外，由于 Prettier 核心本身就是使用插件 API 编写的，如果您愿意，甚至可以为主要的仓库做出贡献。

---

## Prettier 是如何工作的？

本质上，Prettier 所做的事情非常简单：它接收一些代码（一个字符串），将其转换为 **AST**（抽象语法树，即代码的一种表示），然后**仅**使用 AST 来打印代码。这意味着原始代码的样式被（几乎）完全忽略。您可以在 [最初的博客文章](https://jlongster.com/A-Prettier-Formatter) 中了解更多信息。

就我们的目的而言，重要的一点是我们**需要一个将代码转换为 AST 的解析器**，以及一个**接收 AST 并将其美化打印的函数**。我们的初始设置中已经配置了一个解析器，它使用 [toml-node](https://github.com/BinaryMuse/toml-node)，所以我们只需要关注**打印函数**。

---

## 设置

首先，我们将克隆 [这个仓库](https://github.com/fvictorio/prettier-plugin-toml)，它包含了您开始所需的所有**样板代码**。我稍后会解释它的内容。克隆后，进入顶级目录并运行 `npm install` 来安装依赖项。现在您应该可以使用以下命令在示例文件 (`example.toml`) 上运行该插件：

```sh
./node_modules/.bin/prettier --plugin . example.toml
```

还有一个 npm 脚本可以完成此操作，因此运行 `npm run example` 应该也行，但这展示了如何在任何您想要的文件中运行插件。

运行该命令后，您将看不到任何输出，这是正常的。目前，我们的插件不会输出任何东西：当打印函数接收到 AST 时，它只返回一个空字符串。

还有一个初始测试，您可以使用 `npm test` 运行。我们的测试将用 **jest** 编写，使用**[快照](https://jestjs.io/docs/en/snapshot-testing)**，但由于设置已经就绪，您所要做的就是添加新的**夹具**（fixtures）。这个初始测试将格式化 `tests/StringAssignements/example.toml` 的内容，并将结果与​​快照中预期的输出进行比较。我们所有的测试都将像这样：一个 TOML 文件和一个包含正确格式的快照。当然，这个测试会失败，但我们的首要目标是让它通过。

我们将编写的所有代码都将在 `src/index.js` 文件中。事实上，所有内容都将位于一个函数中：`printToml`。您可以查看文件的其余部分，但不必担心具体细节。如果您好奇，所有内容都**[在此处](https://github.com/prettier/prettier/blob/master/docs/plugins.md#developing-plugins)**进行了解释。

如果您想阅读**已完成的代码**而不是自己编写，只需检出 `finished` 分支即可。

---

## 打印函数（The printer function）

`printToml` 函数非常简单。它接受三个参数：

- `path`，它代表 AST 中的一个**节点**
- `options`，它代表提供给 prettier 的**配置**（`.prettierrc` 与提供给命令的标志等的组合）
- `print`，这是我们**递归调用打印函数**的方式

请注意，我说过 `path` 是 AST 中的**某个**节点，而不是根节点。这是因为该函数是**递归调用**的。例如，如果我有一个函数体，我可能希望单独美化打印每个语句，然后对结果进行一些处理。随着我们的继续，这一点会变得更加清晰。

这是我们函数的样板代码：

```js
function printToml(path, options, print) {
  const node = path.getValue();

  if (Array.isArray(node)) {
    return concat(path.map(print));
  }

  switch (node.type) {
    default:
      return "";
  }
}
```

第一行只是从 `path` 中提取 AST 节点。这是因为 `path` 包含一些与 AST 节点相关的额外信息和逻辑。

然后我们有一个奇怪的代码块，它检查节点是否是**数组**。这仅在**初始调用**中是必需的，因为我们使用的解析器将代码表示为**节点列表**，而不是节点树。不用担心这一点，但请记住它，因为稍后这将对我们的插件施加一些严重的限制。

最后，我们有 `switch` 语句。这是我们将花费大部分时间的地方。我们的逻辑非常简单：我们检查 AST 节点的类型并相应地进行操作。让我们开始填充它。

---

## 一个简单的赋值

如果您查看我们的测试，您会看到它包含两个键/值对。代表第一对的节点如下所示：

```js
{
	type: 'Assign',
	value: {
		type: 'String',
		value: 'TOML Example',
		line: 1,
		column: 9
	},
	line: 1,
	column: 1,
	key: 'title'
}
```

（我们怎么知道这个？有很多方法可以获得它：一个老式的 `console.log`，在 Node REPL 中使用解析器，或者使用 **[ndb](https://github.com/GoogleChromeLabs/ndb)** 运行插件并检查该值。）

这里有两件有趣的事情。首先是 `type` 属性，这是我们在 `switch` 中使用的。第二个是，虽然我们键值对的 `key` 是一个简单的字符串，但**我们的 `value` 是另一个 AST 节点**，其类型是 `String`。

所以我们要做的第一件事是为 `Assign` 节点添加一个子句：

```js
case 'Assign':
  return concat([node.key, ' = ', path.call(print, 'value'), hardline])
```

这里有很多信息需要解读，但主要思想很容易掌握：我们告诉 prettier，一个赋值是通过连接四部分来打印的：

- **键（Key）**。请记住，这只是一个纯字符串。
- 一个字面量等号，用空格填充。
- 美化打印**赋值的值**的结果，无论它是什么。
- 一个 **`hardline`**。

`concat` 和 `hardline` 是什么？它们被称为**构建器**（builders），是 Prettier 暴露的函数和值，我们用它们来构建我们想要的结果。我们已经导入了 `concat`，但我们需要将 `hardline` 添加到我们正在使用的构建器列表中：

```js
const {
  doc: {
    builders: { concat, hardline },
  },
} = require("prettier");
```

`concat` 构建器很容易理解：它告诉 prettier **只连接**它给定的部分列表。而 `hardline` 只是意味着“**放置一个换行符**”，无论如何。您可以在**[此处](https://github.com/prettier/prettier/blob/master/commands.md)**查看完整的构建器列表。

那么 `path.call(print, 'value')` 部分呢？这是一个 prettier 的**惯用表达**，它只是意味着“**递归调用打印函数，使用位于 `'value'` 键中的节点**”。为什么我们不能直接使用 `print(node.value)` 呢？嗯，请记住，打印函数期望的是一个 _path_，即一个**包装过的节点**，而不是一个节点。所以你必须像这样进行操作。

如果只添加这个并运行我们的测试，它会失败。差异告诉我们，键和等号被打印出来了，但值没有。这是有道理的，因为值是 `String` 类型的节点，而我们还没有相应的子句。幸运的是，该子句非常简单。再次查看 AST 子节点，看看你是否能猜到它。

是的，就是这么简单：

```js
case 'String':
  return concat(['"', node.value, '"'])
```

您可能猜到了 `return node.value`，但这会是错误的，因为在这种情况下，我们将只打印字符串的**内容**，而不是**完整的字符串**。例如，`foo = "bar"` 将被打印为 `foo = bar`。

如果我们再次运行测试，它现在应该会通过。

---

## 添加对其他值的支持

TOML 支持字符串以外的其他数据类型，我们也应该支持它们。如果您查看根目录中的示例，您会看到它有数字、布尔值、日期和列表。

数字和布尔值很容易：

```js
case 'Integer':
  return node.value.toString()
case 'Boolean':
  return node.value.toString()
```

我们必须将它们转换为字符串，因为这是 prettier 所期望的，仅此而已。

日期稍微棘手一些，在这里我们将遇到我们正在使用的解析器的第一个限制。以下是日期赋值的 AST 表示：

```js
{
	type: 'Assign',
	value: {
		type: 'Date',
		value: 1979-05-27T15:32:00.000Z,
		line: 5,
		column: 7
	},
	line: 5,
	column: 1,
	key: 'dob'
}
```

看看日期的值。这是一个 `Date` 对象，是日期的唯一表示。但是，如果您查看 **[TOML 规范](https://github.com/toml-lang/toml)**，您会看到您可以用许多不同的格式指定日期。在解析过程中，这些格式对我们来说是**丢失**的，因此我们将始终以相同的表示形式打印日期。

```js
case 'Date':
  return node.value.toISOString()
```

这可一点都不好！但要正确地进行操作，我们应该知道日期的**原始表示形式**。我们可以使用节点的位置和原始文本（我们在 `options.originalText` 中接收到）来获取它，但拥有一个在 AST 中保留原始值的解析器会更好。由于我们的解析器没有这样做，我们不得不满足于此。

---

## 表格 (Tables)

在 TOML 中，我们可以使用规范中称为“**Tables**”的东西来分隔不同的部分，但我们的解析器将其分配的类型是 `ObjectPath`。一个 AST 节点看起来像这样：

```js
{
	type: 'ObjectPath',
	value: [ 'owner' ],
	line: 3,
	column: 1
}
```

如您所见，节点的值不是一个字符串而是一个**数组**。这是因为我们可以有像 `[servers.alpha]` 这样的**嵌套部分**。我们使用以下子句来打印它：

```js
case 'ObjectPath':
  return concat(['[', node.value.join('.'), ']', hardline])
```

这里没有什么新的东西。我们用一个点连接 `value` 的每个部分，并用方括号将所有内容包围起来。

---

## 数组 (Arrays)

到目前为止，我们所做的一切都非常简单明了。**数组**稍微复杂一些，我们将不得不做出一些决定。数组可以以几种方式打印，例如：

```
arr1 = [1, 2, 3]
arr2 = [ 1, 2, 3 ]
arr3 = [1,2,3]
arr4 = [
	1,
	2,
	3
]
```

在类似这样的情况下，prettier 通常会这样做：如果数组**适合放在一行中**，则将其打印在一行中。否则，将所有内容打印在**自己的行**上。因此，当数组适合时，我们将采用 `arr1` 的方式；当不适合时，我们将打印类似 `arr4` 的方式。

这看起来很难，不是吗？但 prettier 可以帮助我们。这是实现我们想要的子句：

```js
case 'Array':
  return group(
    concat([
      '[',
      indent(
        concat([
          softline,
          join(concat([',', line]), path.map(print, 'value'))
        ])
      ),
      softline,
      ']'
    ])
  )
```

我知道这与我们目前所做的事情有很大的跳跃。坏消息是，很多插件代码看起来都有些像这样。好消息是你会很快习惯它。

让我们从表达式**最内部**的部分开始，然后逐步向外扩展。

首先，我们有一个 `path.map(print, 'value')` 表达式。这类似于我们之前讨论的 `path.call` 惯用语，但在这里我们是说“在这个节点的 `value` 键中，我有一个子节点数组；对每个子节点调用打印函数，并给我一个包含结果的数组”。换句话说，它就像执行 `node.value.map(print)`，但请记住我们不能这样做。

因此，我们有一个包含美化打印列表中每个元素结果的数组。下一步是添加我们的**逗号**。我们为此使用 `join` 构建器。它的签名是 `join(separator, list)`，它只是用给定的分隔符连接零件列表。例如，`concat(["1", ",", "2", ",", "3"])` 等价于 `join(",", ["1", "2", "3"])`。我们可以在这里这样做，对吗？只需 `join(",", path.map(print, 'value'))`。但是我们希望在列表适合放在一行时，**逗号后有一个空格**，而在我们拆分列表时，**用换行符**替换空格。这是使用 `line` 构建器完成的，这也是我们通过 `concat([",", line])` 连接的原因。文档很明确：

> 指定一个换行符。如果一个表达式适合放在一行中，换行符将被替换为一个**空格**。换行符总是用当前的缩进级别缩进下一行。

因此，如果列表适合放在一行中，我们用逗号和空格分隔打印每个值；如果不适合，我们将空格替换为换行符。我们应该准备好了，对吗？只需添加开口和闭合的方括号即可完成。嗯，不是。因为我们希望在拆分列表时**缩进**列表的每个元素。

我们通过用 `indent(concat([softline, ...]))` 包围我们到目前为止所做的一切来做到这一点。这里发生了什么？首先我们在列表的开头放置一个 **`softline`**。`softline` 与 `line` 非常相似，但区别在于，如果所有内容都适合放在一行中，`softline` 将被替换为**空字符串**。我们还使用了 **`indent`** 构建器，它只是增加缩进。当所有内容都适合放在一行中时，我们将没有换行符，因此 `indent` 不会执行任何操作。

快完成了！在那之后，我们用 `concat('[', ..., softline, ']')` 包围所有内容。我们只是添加方括号。我们还在闭合方括号前添加了一个 `softline`，由于它在 `indent` 构建器之外，因此 `]` 将具有我们开始时的**相同缩进**。否则我们的列表看起来会像这样：

```
arr = [TOML spec
	1,
	2
	]
```

**最后**，我们用对 **`group`** 的调用包围所有内容。这是一个构建器，它会尝试将它内部的所有内容放在**一行**中。如果不能，它将开始用换行符替换 `line` 和 `softline`。它实际上要复杂一些，但这个解释暂时就够了。查看文档以了解其细微差别。

再说一次，这看起来很难，但是当你开始使用 prettier 时，你会很快掌握它。所有这些也展示了 prettier 的强大之处，如果你仔细想想的话。我们只使用了几个构建块来美化打印任何列表。事实上，即使是**嵌套列表**，无论它们有多深，这都会起作用！

---

## 旁注：如何进行实验

除了阅读文档和用一些示例运行完整的插件之外，您如何检查构建器（builders）是如何相互作用的呢？原来您可以使用 **Node REPL** 来与 Prettier 交互。首先启动 REPL 并导入一些内容：

```sh
> const prettier = require('prettier')
> const print = prettier.doc.printer.printDocToString
> const { concat, group, join, line, softline } = prettier.doc.builders
```

然后您就可以用构建器进行实验了：

```
> print(concat(['foo', 'bar', 'baz']), {})
{ formatted: 'foobarbaz' }
> print(join('|', ['foo', 'bar', 'baz']), {})
{ formatted: 'foo|bar|baz' }
```

要测试像 `group` 这样的东西，您需要指定一个 `printWidth`：

```sh
> print(group(join(line, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 20 })
{ formatted: 'foo bar baz qux' }
> print(group(join(line, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 10 })
{ formatted: 'foo\nbar\nbaz\nqux' }
> print(group(join(softline, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 20 })
{ formatted: 'foobarbazqux' }
> print(group(join(softline, ['foo', 'bar', 'baz', 'qux'])), { printWidth: 10 })
{ formatted: 'foo\nbar\nbaz\nqux' }
```

您可以通过这种方式学习。我知道这不是一个很好的用户体验，如果能有更好的东西会很不错（也许是一个网络操场，您可以在其中运行像这样的表达式并查看不同输入的结果？），但我不知道有任何更好的选择。

---

## 待办事项

如果我们再次运行示例，我们会看到我们打印出了一个等效的 TOML，正如我们指定的那样：

```sh
> prettier-plugin-toml@0.0.1 example /home/fvictorio/repos/prettier-plugin-toml
> prettier --plugin . example.toml

title = "TOML Example"
[owner]
name = "Tom Preston-Werner"
dob = 1979-05-27T15:32:00.000Z
[database]
server = "192.168.1.1"
ports = [8001, 8001, 8002]
connection_max = 5000
enabled = true
[servers]
[servers.alpha]
ip = "10.0.0.1"
dc = "eqdc10"
[servers.beta]
ip = "10.0.0.2"
dc = "eqdc10"
[clients]
data = [["gamma", "delta"], [1, 2]]
hosts = ["alpha", "omega"]
```

但很难说这更**美观**（_prettier_）。我们没有做两件非常重要的事情，而且由于我们正在使用的解析器，我们无法轻松做到：

- **我们没有保留空行**。Prettier 的理念是保留空行（尽管如果有两个或多个空行连在一起，它们会被一个空行替换）。这是可以做到的，但要做到这一点，我们需要一种简单的方法来获取节点的起始和结束索引。正如您在节点示例中看到的，我们只有起始行和列。
- **我们没有缩进表格**。如果 AST 的表示是一个**合适的树**，这将相对容易，但请记住，我们拥有的却是**每行节点的一个列表**。如果我们在表格对象下有一个，比如说，“children”键，我们可以做类似 `path.map(print, 'children')` 的操作，用 `hardlines` 将它们连接起来并缩进它们，例如。

---

## 接下来是什么？

希望您学到了足够的知识来开始您自己的插件或为现有的插件做出贡献。看看[插件列表](https://prettier.io/docs/en/plugins.html#official-plugins)：如果您想美化打印的语言不在其中，您可以创建自己的插件！如果它在列表中，您可以加入并做出贡献。

关于 prettier 插件的一个好方面是，使用它们进行 **TDD**（测试驱动开发）非常容易。如果您想为一个插件做出贡献，只需添加一个**夹具**，其中包含一个不工作的示例，然后尝试让所有测试通过。如果您正在创建一个新插件，您可以从小处着手：使用语法的子集添加一些简单示例的测试，并使它们更美观！

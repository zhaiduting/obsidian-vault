---
source: https://gemini.google.com/app/5d5f08cb6082ba32
---

这种语法现象在英语语法中被称为 **"Than" as a Relative Pronoun（than 作为关系代词）**，有时也被归类为 **Comparative Clauses with Subject Ellipsis（带主语省略的比较从句）**。

简单来说，当 `than` 后面直接跟着谓语动词（如 `is`, `was`, `are`）时，`than` 就不再仅仅是一个连词，而是兼任了从句中的**主语**。

## 核心定义

在比较句式中，当从句的主语就是主句所比较的对象时，为了语言的精炼，`than` 可以直接充当从句的主语。

- **公式：** `[名词/形容词比较级] + than + [谓语动词] + [补充成分]`
- **功能：** `than` 在这里相当于 `than what` 或 `than the thing that`。

## 常见使用场景

### A. 描述“预期”或“要求”

常与 `expected`, `predicted`, `necessary`, `required` 等词连用。

- **例句：** The task was more difficult **than was expected**. 这项任务**比预想**的要难
- **解析：** 这里的 `than` 指代“这件事的难度”，它做了 `was expected` 的主语。

### B. 描述“能力”或“可能性”

常与 `achievable`, `possible`, `feasible` 等词连用（如你例子中的用法）。

- **例句：** The engine produces more power **than is possible** with older designs. 该发动机输出**的动力远超**旧款设计。
- **解析：** `than` 代指生成的“动力”。

再看一个编程方面的例句

- Reactivity systems enable larger, more modular, complex GUIs than is typically achievable with other approaches.
- 响应式系统能够构建出比其他方法通常所能实现的更大、更模块化、更复杂的图形用户界面。

### C. 描述“数量”或“限度”

- **例句：** Do not use more force **than is necessary**.
- **解析：** 这里的 `than` 相当于 `the amount of force which`。

## 常见的固定结构

在阅读技术文档（如 Dioxus 或 Rust 相关资料）时，你会经常遇到这些几乎可以当成“固定词组”的用法：

| **结构**                           | **含义**          |
| ---------------------------------- | ----------------- |
| **...than is usual**               | 比通常情况...     |
| **...than is necessary**           | 超过必要的...     |
| **...than is required**            | 比要求的更...     |
| **...than is achievable**          | 比能实现的更...   |
| **...than was previously thought** | 比之前认为的更... |

## 翻译技巧

当你遇到这种结构时，不需要在中文里死磕“它”或者“那个”，通常有三种处理方式：

1. **“所……”：** 比通常**所**能实现的（achievable）更大。
2. **“更……”：** 难度比预期的**更**大（expected）。
3. **直接忽略：** 投入了超过必要（necessary）的力量。

---

## 主语省略的标准

判断“什么时候该省略”还是“必须保留”主语，最核心的标准只有一条：**从句的主语是否就是主句中那个“被比较的对象”本身。**

如果 `than` 后面的动作或状态直接描述的就是主句的名词，那么 `than` 就充当了关系代词（主语），此时**必须省略**多余的代词（如 it/they）；如果从句有自己独立的主语，则**不能省**。

以下是针对各种情况的系统化造句对比：

### 情况 1：描述属性、状态或预期（最常省略）

**标准：** 从句是在描述主句名词的“性质”（如：必要的、预期的、可能的）。

- **省略（正确）：**
  - _The refactoring took more time **than was expected**._
  - （重构花费的时间比预期的\[时间]要多。）
  - _Do not allocate more memory **than is required**._
  - （不要分配超过要求的\[内存]。）
- **不省略（错误/啰嗦）：**
  - _...than **it** was expected._ （这里的 `it` 属于赘余，因为 `than` 已经承载了主语功能。）

### 情况 2：从句有独立的主语（必须保留）

**标准：** 比较的是两个不同的主体（人、物、时间点）。

- **必须保留（正确）：**
  - _The M2 chip performs better **than the M1 chip did**._
  - （M2 芯片的表现比 M1 芯片\[当年的表现]更好。）
  - _I write more Rust code **than my colleague does**._
  - （我写的 Rust 代码比我同事\[写的]多。）
- **对比：**
  - 如果你说 _...than is my colleague_，意思就变成了“我写的代码比我同事这个人还多”，逻辑就断了。

### 情况 3：代词指代“整个情况”而非“特定名词”（保留）

**标准：** 比较的不是主句里的某个东西，而是前后的两个事实。

- **必须保留（正确）：**
  - _It is much easier to use Dioxus **than it is** to learn raw Win32 API._
  - （使用 Dioxus 比学习原始 Win32 API 要容易得多。）
- **解析：** 这里的第一个 `It` 是形式主语，指代“用 Dioxus”这件事；第二个 `it` 指代“学 Win32”这件事。两个 `it` 各司其职，不能省。

### 情况 4：涉及到“数量”的习惯用法（省略）

**标准：** 当 `than` 后面紧跟数量词或比较级，形成“多于/少于”的逻辑。

- **省略（正确）：**
  - _There were more bugs **than could be fixed** in one day._
  - （Bug 的数量多到了在一天内无法修复的程度。）
- **不省略（错误）：**
  - _...than **they** could be fixed..._ （虽然语法上勉强通，但在地道的技术写作中，直接用 `than could be` 显得极其干练。）

### 总结表：一眼看穿

| **场景**                     | **than 后面接什么** | **是否省略主语** | **示例**                         |
| ---------------------------- | ------------------- | ---------------- | -------------------------------- |
| **描述同一事物的潜能/预期**  | 谓语 (is/was/could) | **是 (省略)**    | _...more **than is** necessary._ |
| **比较两个不同的实体**       | 名词/代词 + 谓语    | **否 (保留)**    | _...more **than I** expected._   |
| **主从句都是 It is... 结构** | It + is             | **否 (保留)**    | _It's harder **than it** looks._ |

### 💡 给 Rustacean 的直观理解（伪代码）：

- **省略模式：** `let result = compare(item_a, expected_state_of_a);` (描述 a 的状态)
- **保留模式：** `let result = compare(item_a, item_b);` (两个独立的变量比较)

在 [Dioxus](https://dioxuslabs.com/learn/0.7/essentials/basics/reactivity) 文档那句 `than is typically achievable` 中，比较的对象就是 **GUIs** 的复杂度。既然 `achievable` 描述的就是这些 GUIs，那么直接用 `than is` 就像是在 Rust 里使用了引用一样，简洁且精准。

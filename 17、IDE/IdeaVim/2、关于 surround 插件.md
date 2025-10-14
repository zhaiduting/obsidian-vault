![IdeaVim](https://lib.zhaiduting.work.gd/uPic/IdeaVim.png)

**tpope/vim-surround** 是一个非常流行的 Vim 插件，它的作用是让你可以轻松地添加、删除或修改配对的**环绕字符**，比如引号、括号、XML 标签等。

### 为什么它很受欢迎？

- **Vim 风格:** 它完美地融入了 Vim 的操作哲学，使用了与 Vim 内置命令相似的助记符（`y`ank, `d`elete, `c`hange, `s`urround）。
- **高效:** 极大地提高了编辑带环绕字符（尤其是代码和标记语言）的效率，省去了多次移动光标和输入配对符号的麻烦。

总而言之，`vim-surround` 是一个**不可或缺**的插件，如果你经常在 Vim 里处理需要配对符号（如 `()`, `{}`, `[]`, `""`, `''`, `<>`）的内容，它能让你事半功倍。

### 安装

点击 IDE 右下角的 V 字图标可弹出 IdeaVim 菜单，再点击 `Open ~/.ideavimrc` 菜单项，然后添加以下代码

```
Plug 'tpope/vim-surround'
```

重启 IDE 后插件生效。

### 功能介绍

`vim-surround` 的设计理念是把配对的环绕字符（surroundings）当作 Vim 的一个特殊**文本对象**来处理。这让你可以用一种非常 **Vimmatic** 的方式来操作它们，主要有三种核心操作：

1. **添加环绕 (Add):**
   - 在 **普通模式 (Normal Mode)** 下使用 `ys` (you surround) + **动作 (motion)** 或 **文本对象 (text object)** + **环绕字符 (surrounding character)**。
     - _示例：_ 在单词 `text` 上，输入 `ysiw)` 会将其变为 `(text)`。
   - 在 **可视模式 (Visual Mode)** 下选中内容后，输入 `S` (大写) + **环绕字符**。
     - _示例：_ 选中 `text`，然后输入 `S"` 会将其变为 `"text"`。

2. **删除环绕 (Delete):**
   - 在 **普通模式 (Normal Mode)** 下使用 `ds` (delete surrounding) + **环绕字符**。
     - _示例：_ 在 `(text)` 上，输入 `ds)` 会将其变回 `text`。

3. **替换环绕 (Change/Replace):**
   - 在 **普通模式 (Normal Mode)** 下使用 `cs` (change surrounding) + **旧的环绕字符** + **新的环绕字符**。
     - _示例：_ 在 `(text)` 上，输入 `cs)"` 会将其变为 `"text"`。
     - _示例：_ 在 `"text"` 上，输入 `cs"<tag>` 会将其变为 `<tag>text</tag>`。

### 用法举例

如果选中了一段文本，输入大写 S 后再输入 `<p>` 就可以将此文本放入 `<p>...</p>` 标签里面。

如果没有选中任何文本，仅仅将光标放在某个单词上，输入 `ysiw` 后再输入 `<p>` 就可以将这个单词放进 `<p>...</p>` 标签里。

也就是说：

- 普通模式用 `ysiw`
- 可视模式用 `S`
- 替换用 `cs`
- 删除用 `ds`

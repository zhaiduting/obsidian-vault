`git log <file>` 查询文件的修改历史
`git restore <file>` 将文件恢复到上次提交的状态
`git restore <file> --source=<commit-hash>` 恢复文件到指定的历史版本
`git restore <file> -s <commit-hash>` 作用同上🌟
`git restore --source=<commit-hash> <file>` 作用同上

### 举例
将文件 `~/obsidian-vault/JS/'slice()、toSpliced()和splice().md'` 恢复到某个历史版本，终端截图如下。
![](/assets/git%20restore.png)
### 注意
输入命令`git log <file>` 后，终端会分页显示，可以使用以下键盘快捷键浏览信息或退出日志：

- **`空格键`**：向下滚动一页。
- **`Enter`**：向下滚动一行。
- **`b`**：向上滚动一页。
- **`k`** 或 **`Up Arrow`**：向上滚动一行。
- **`q`**：退出查看日志并返回到命令行。🌟


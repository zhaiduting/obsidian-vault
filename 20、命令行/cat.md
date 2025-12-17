💡 **`cat`** 命令是 Linux/Unix 系统中最常用的命令之一，它的名字来源于 **concatenate**（连接）的缩写。

```sh
# 显示文件1的内容
test_cat > cat file1.txt
文件1的内容

# 多文件合并
test_cat > cat file1.txt file1.txt > double_file1.txt
test_cat > cat double_file1.txt
文件1的内容
文件1的内容

# 使用大于号，直接从终端修改或创建文件3
test_cat > cat > file3.txt
文件3的第一行
文件3的第二行
(按 Ctrl+D 结束输入)
test_cat >
test_cat > cat file3.txt
文件3的第一行
文件3的第二行
(按 Ctrl+D 结束输入)

# 将文件1追加到文件3的末尾
test_cat > cat file1.txt >> file3.txt
test_cat >
test_cat > cat file3.txt
文件3的第一行
文件3的第二行
(按 Ctrl+D 结束输入)
文件1的内容

# 展示 Here Document 的写法
test_cat > cat <<EOF > here_document.txt
heredoc> 此处文档第1行
heredoc> 此处文档第2行
heredoc> （另起一行输入 EOF 结束输入）
heredoc> EOF
test_cat >
test_cat > cat here_document.txt
此处文档第1行
此处文档第2行
（另起一行输入 EOF 结束输入）
test_cat >
```

### Here Document（此处文档）

#### 位置互换

`<<EOF` 和 `> here_document.txt` 的**位置互换不影响最终结果**，因此以下两种写法效果相同

1. `cat <<EOF > here_document.txt`
2. `cat > here_document.txt <<EOF`

#### 使用 `<<-EOF` 去除制表符

执行以下命令，生成的 here_document.txt 文件的每行开头都没有制表符

```sh
cat > here_document.txt <<-EOF
	第一行开头有个制表符
	第二行也是
	EOF
```

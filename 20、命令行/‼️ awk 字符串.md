```bash
awk -v i=8 'BEGIN{
  output_line1 = "<mark name=\x27timepoint_" (++i) "\x27/>"
  output_line2 = "<mark name='timepoint_" (++i) "'/>"
  print output_line1
  print output_line2
}'

```

输出结果如下

```shell
<mark name='timepoint_9'/>
<mark name=timepoint_ (++i) />
```

在 `awk` 中，**唯一合法的字符串字面量的表示方式就是使用双引号（`"`）**。

```shell
# 双引号定义字符串变量 ✅
awk 'BEGIN{
  punct = "abc"
}'

# 单引号定义字符串 ❌
awk 'BEGIN{
  punct = \'abc\'
}'
```

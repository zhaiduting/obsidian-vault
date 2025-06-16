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

为便于学习和测试，先封装一个打印助手函数。返回值为字符串，各参数说明如下：

- doc 是 Prettier 内部的「排版指令树」类型的对象
- printWidth 控制每行最多显示的字符个数
- log 为假时，控制台不会打印格式化结果（默认会打印）

```js
import prettier from "prettier";

export function ps(doc, printWidth = 80, log = true) {
  const { formatted } = prettier.doc.printer.printDocToString(doc, {
    printWidth,
    tabWidth: 4,
  });

  if (log) console.log(formatted);

  return formatted;
}
```

⚠️ tabWidth: 4 不可缺少，否则会导致文本没有缩进效果

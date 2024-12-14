**`slice()`** 方法返回一个新的数组对象，这一对象是一个由 `start` 和 `end` 决定的原数组的[浅拷贝](https://developer.mozilla.org/zh-CN/docs/Glossary/Shallow_copy)（包括 `start`，不包括 `end`），其中 `start` 和 `end` 代表了数组元素的索引。原始数组不会被改变。语法如下
```js
// start 与 end 皆为元素索引
slice()
slice(start)
slice(start, end)
```

toSpliced() 返回一个新数组，并在给定的索引处删除和/或替换了一些元素。
```js
// start 为元素索引，deleteCount 为删除元素的个数
// item1 到 itemN 皆为插入的新元素
toSpliced(start)
toSpliced(start, deleteCount)
toSpliced(start, deleteCount, item1)
toSpliced(start, deleteCount, item1, item2, /* …, */ itemN)
```

splice() 语法与 toSpliced() 完全一样，但是不会生成新数组，而是直接修改了原数组！
```js
splice(start)
splice(start, deleteCount)
splice(start, deleteCount, item1)
splice(start, deleteCount, item1, item2)
splice(start, deleteCount, item1, item2, /* …, */ itemN)
```


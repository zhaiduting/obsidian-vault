**`slice()`** 方法返回一个新的数组对象，这一对象是一个由 `start` 和 `end` 决定的原数组的[浅拷贝](https://developer.mozilla.org/zh-CN/docs/Glossary/Shallow_copy)（包括 `start`，不包括 `end`），其中 `start` 和 `end` 代表了数组元素的索引。原始数组不会被改变。语法如下
```js
// start 与 end 皆为元素索引
slice()
slice(start)
slice(start, end)
```

toSpliced() 返回一个新数组，并在给定的索引处删除和/或替换了一些元素。原数组不变！注意：这个是2023年7月份引入的新方法，旧浏览器不支持。
```js
// start 为元素索引，deleteCount 为删除元素的个数
// item1 到 itemN 皆为插入的新元素
toSpliced(start)
toSpliced(start, deleteCount)
toSpliced(start, deleteCount, item1)
toSpliced(start, deleteCount, item1, item2, /* …, */ itemN)
```

splice() 语法与 toSpliced() 一样，但是返回值是被删除的元素组成的数组，而是直接修改了原数组！
```js
splice(start)
splice(start, deleteCount)
splice(start, deleteCount, item1)
splice(start, deleteCount, item1, item2)
splice(start, deleteCount, item1, item2, /* …, */ itemN)
```

## 测试

```js
let arr = [0, 1, 2, 3, 4];
console.log(arr.splice(1, 1))		// [1]
console.log(arr)					// [0, 2, 3, 4]

arr = [0, 1, 2, 3, 4];
console.log(arr.toSpliced(1, 1))	// [0, 2, 3, 4]
console.log(arr)					// [0, 1, 2, 3, 4]
```

## 代码优化示例

以下JS函数好像写得很繁琐，能否简化？
```js
function removeItemAtIndex(arr, index) {  
  return [...arr.slice(0, index), ...arr.slice(index + 1)];  
}
```

改用 splice 的写法如下
```js
function removeItemAtIndex(arr, index) {
  arr.splice(index, 1);
  return arr;
}
```

改用 toSpliced 似乎更简洁，如下
```js
function removeItemAtIndex(arr, index) {
  return [...arr.toSpliced(index, 1)]; //此处 toSpliced 改成 splice 是错误写法
}
```

还可以采用 [filter](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 改写
```js
const removeItemAtIndex = (arr, index) => arr.filter((_, i) => i !== index);
```
假设有个 api.js 模块，需要对外导出3个函数`getSeller()、getGoods()和getRatings()`，导出方法采用的是`export {}`写法如下
```js
export {
    getSeller, getGoods, getRatings
}
```
在 App.vue 中，与此对应的调用代码如下
```js
import { getSeller, getGoods, getRatings } from 'api'
getSeller()	//拿到模块方法后使用之
getGoods()
getRatings()
```

如果 api.js 模块是用 export default 方式导出的
```js
export default {
    getSeller, getGoods, getRatings
}
```
那么在 App.vue 中，导入及调用代码就必须做修改（不能再用 import {..} 的写法）
```js
import api from 'api' 
api.getSeller() //函数名之前要加入模块名称
api.getGoods()  
api.getRatings()
```

而今，我要达到一个目的：对于 api.js 模块，用户既可以使用 import {..} 进行导入，也可以使用 import api from 'api' 进行导入。为此，需要修改模块的导出方式，比如改成如下方式（两次导出法）
```js
export {
    getSeller, getGoods, getRatings
}
export default {
    getSeller, getGoods, getRatings
}
```
如果函数很多（假设有几百个），那上面的写法将会变得臃肿。
为此，我找到了一个怪异的写法。代码如下
```js
let exp={
    getSeller, getGoods, getRatings
}
exp.default= exp;
Object.assign(arguments[0].exports, exp)
```
其中的赋值语句`exp.default= exp`即可达到代码瘦身的目的，而`Object.assign(arguments[0].exports, exp)`正是此法的怪异之处。这条，很不容易想到。

### 不这么写会怎么样？

比如尝试如下写法（可惜，都行不通的）
```js
//* 以下7种尝试简化的写法都行不通
let exp={
    getSeller, getGoods, getRatings
}
exp.default= exp;
export exp;                          // Failed to compile
// export {exp};                        // BAD
// export {Object.assign({}, exp)};     // Failed to compile
// export Object.assign({}, exp);       // Failed to compile
// module.exports=exp;          // Error: Cannot assign to read only property 'exports'
// exports=exp;                 // Error: exports is not defined
// Object.assign(this, exp);    // Error: Cannot convert undefined or null to object
//*/
```

### 本项目下载链接 

`git@gitee.com:zhaiduting/vue-sell-cube.git` 或者 https://gitee.com/zhaiduting/vue-sell-cube.git （npm i 之后 npm run serve）。

```js
Promise.resolve(1).then(
  x=>{
   console.log('then1 get '+x)
   return 2;
  }
)
.catch()
.then().then()
.then(x=>console.log('then4 get '+x))
```
第1个then的回调函数有返回值，后跟一个啥也不干的catch()，再跟两个啥也不干的then()，最后再跟第4个then()
第4个then有输出，可见第1个then的返回值跳过了中间的catch及其后的两个空then，直接传给了第4个then
```
> "then1 get 1"
> "then4 get 2"
```
如果删除第4行的`return 2`，导致第一个then没有返回值，得到的结果会是下面这样
```
> "then1 get 1"
> "then4 get undefined"
```

![测试 ES6 Promise 对象的链式传值](https://cdn.learnku.com/uploads/images/202004/16/24833/cKxAw0ayUk.png!large)
## 断链
截图使用 .reject() 产生一个错误。从截图的中间一例可以看出，空的 .catch() 函数实际上不会捕获异常，等于不存在。如果有异常，却不能被捕获，那 promise 链便会就此断开，后续的 then() 统统不执行。
## 续链
截图最后一例是一个续链的例子。错误抛出后被捕获，promise 链继续向后传播，因此可以看到 then4 get undefined 了。如果捕获异常，并且有返回值，那么 then4 就会打印这个返回值。
```js
Promise.reject(1).then(
  x=>{
   console.log('then1 get '+x)
   return 2;
  }
)
.catch(err=>{console.log('捕获', err);return '救星'})
.then().then()
.then(x=>console.log('then4 get '+x))

//结果如下
//  捕获 1
//  then4 get 救星
```
## 拒绝续链
以下截图对比了续链与拒绝续链的区别。两段代码的区别仅仅在于 catch() 函数的返回值是不同的。
续链：`return '救星'`
断链：`return Promise.reject(err)`
续链后 then4 继续执行；拒绝续链后 then4 没机会执行。
![测试 ES6 Promise 对象的链式传值](https://cdn.learnku.com/uploads/images/202004/16/24833/StQAPcSX2X.png!large)
## 拒绝续链的应用场景
以 promise-gif 为例，此插件的用法一行代码概括如下：
`Promise.resolve().gif().then(...).catch(...)`
以上Promise链里面实际上有两个catch，其中gif()函数内部也有一个catch。
通过gif()打开无限风火轮动画，当业务代码执行成功后，关闭风火轮是显而易见的。当业务代码执行失败后，风火轮也必须关闭，因此 promise-gif 必须捕获异常。但是业务代码可能会有自己的异常捕获逻辑，因此 promise-gif 在捕获异常后必须做两件事：1、关闭风火轮；2、拒绝续链并继续报错。
这便是拒绝续链的应用场景。由此可见，在一条 promise 链里面添加多个 catch() 函数的做法很有意义。每个 catch() 函数只处理特定的逻辑，完毕后继续报错，这样就可以使得整条 promise 链里面的其他 catch() 函数仍有执行的机会。
## promise链的promise返回与不返回的区别
第2个then函数里面新建一个promise，一秒之后将其resolve掉。加不加return决定了后续then的回调函数是否异步执行
![加了return语句](https://cdn.learnku.com/uploads/images/202203/24/24833/q1cvkRdgre.jpg!large "加了return语句")
加了return则then4延迟执行

![不加return](https://cdn.learnku.com/uploads/images/202203/24/24833/cIguEmK5Ft.jpg!large "不加return")
不加return则then4提前执行，它不会等到then2执行之后才执行
```js
Promise.reject(1).then(
    x => {
        console.log('then1 参数为 ' + x)
        return 2;
    }
)
    .catch(err => {
        console.log('捕获', err);
        return '继续'
    })
    .then(
        x => {
            return new Promise(function (resolve, reject) {
                setTimeout(
                    (y) => {
                        console.log('then2 参数为 %s, setTimeout() 参数为 %O', x, y)
                        resolve('久等了')
                    }, 1000, [2, 4, 6, 8]
                )
            })
        }
    )
    .then()
    .then(x => console.log('then4 参数为 ' + x))

```
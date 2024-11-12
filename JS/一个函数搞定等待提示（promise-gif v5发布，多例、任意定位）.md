![Laravel](https://cdn.learnku.com/uploads/images/202009/28/24833/lGLWUgzOFT.gif)
在插件column-relation和form-choice中（详情请看本篇的系列文章3和4），多次使用了一个前端组件。这东西小巧玲珑，用法极其方便，特此发文推荐。
## 效果如图所示，用法只要一个函数
例如使用axios发送get请求后想要显示一个转圈圈的动画，可以这么写：
```js
axios.get('http://www.xxx.tt')
     .gif()		//就是这个函数 gif()
	 .then(res => {
	     //成功的处理
	 })
	 .catch(err => {
	     //失败的处理
	 })
```
也就是在promise对象后面链式调用一个 gif() 函数就完事了。就是这么简单，一个函数搞定。

## 如果用户瞎胡闹，故意在多个分页按钮之间依次、快速、反复的点击会怎么样？
只要axios发送了请求，就会正常显示gif动画；无论发送了多少个请求，同一时间只会显示一个gif动画（不会出现多图叠加的现象）。当所有的请求都有了结果（或成功或失败）的时候，gif动画才会最终消失。一句话总结就是：`该等的时候就转圈，都完事了就隐藏。`

## 不喜欢这动画，可否替换？
当然可以，给gif()传递参数就可以了。例如想用一个wait.gif的图片替换，并修改最终显示的宽、高，可以像下面这样写
```js
//图片src也可以直接传递base64编码
promiseInstance.gif({width: '3rem', height: '3rem', src: 'wait.gif'})
```
为了调试方便，想在控制台里查一下get请求传回的结果，可以这么写
```js
//请求成功会显示 Resolved：数据
//失败会显示 Rejected：错误信息
promiseInstance.gif({log: true})
```

## 业务逻辑分多步，可否多次调用gif动画？
可以的，在一条链上可以无限次调用，例如
```js
axios.get('http://www.xxx.tt')
     .gif()		//首次调用
	 .then(res1 => {
	     //处理成功的请求，
		 return axios.post();
	 })
	 .gif()		//再次调用
	 .then(res2 => {
	     //接着处理
	 })
	 
```
总之一句话：`只要是个promise对象，就可以调用gif()开启动画；无论成败，只要有结果就关闭动画`。

## 安装或卸载
可以通过npm进行安装或卸载
```js
//安装
npm install zhaiduting/promise-gif
//或者这样安装
npm install github:zhaiduting/promise-gif

//在app.js文件里面加载这个函数（挂载到全局Promise对象的原型链上）
Promise.prototype.gif= require('@zhaiduting/promise-gif').default;

//加载之后就可以在vue组件内部直接调用了
promiseInstance.gif().then().catch()

//卸载方法如下（有个@符号）
npm remove @zhaiduting/promise-gif

```
-- -
## promise-gif v5 的新功能
前几个版本的gif图片都是单例图片，全局唯一、中心固定定位。在v5版本中，加了新功能（效果如下图所示）
![一个函数搞定等待提示（promise-gif v3发布）](https://cdn.learnku.com/uploads/images/202010/05/24833/M2A7995tIg.gif)
### 1、可以把 gif 装进任何一个盒子里面
只要传递 box 的选择器就ok了，例如装进一个 id='sample2-box' 的 div 里面可以像下面这样写（如果不传递 box 值就只能固定显示在浏览器的正中心，而不是盒子的正中心）
```js
promiseInstance.gif({
    box: "#sample2-box"
})
```
### 2、可以任意对齐
只要传递水平偏移量x，或者垂直偏移量y，就可以让gif相对于中心点偏移之后再对齐（可以混合使使用长度单位及百分比，也可以使用加减乘除运算）。
```js
//在盒内靠右对齐
//中心点横坐标+50%就是右边框，再-50px刚好右对齐（图片宽度100px）
promiseInstance.gif({
    width: "100px",
    height: "100px",
    x: "+50%-50px",
    box: "#sample2-box"
})
```
### 3、多图并存
之前的版本都是单一gif图片，v5版本里面只需要传递一个 id 即可得到一个新图片
```js
//添加一个id=promise-gif0的图片，盒内居中显示
promiseInstance.gif({
    box: "#sample2-box",
    id: "promise-gif0"
})

//再添加一个id=promise-gif3的图片，盒内左对齐
//不指定图片大小时，默认长、宽都是30px
promiseInstance.gif({
    x: "-50%+15px",
    box: "#sample2-box",
    id: "promise-gif3"
})
```
不同的id对应不同的promise-gif图片，这些图片是可以同时显示的。


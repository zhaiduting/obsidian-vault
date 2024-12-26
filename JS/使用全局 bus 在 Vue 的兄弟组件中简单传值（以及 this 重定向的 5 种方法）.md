总线传值有两种写法：原型bus写法`window.Vue.prototype.bus = new Vue()`、全局bus写法` window.bus=new Vue()`。
如果使用了原型写法，那在组件里面必须使用 `this.bus.$emit()`发送消息，以及使用 `this.bus.$on()`接收消息。它们全部要用 this 开头。如果使用了全局写法，则可以简化为`bus.$emit()`及`bus.$on()`了。

在确保全局bus不会发生变量名称冲突的前提下，使用全局bus的写法要更简单一些。首先，修改 `resources\assets\js\app.js`文件引入全局bus 
```js
require('./bootstrap');
window.Vue = require('vue');
window.bus=new Vue();   //全局bus
// window.Vue.prototype.bus = new Vue();   //原型bus
import ...
const app = new Vue({
    el: '#app',
    components:{
        ...
    },
});
```

这里需要注意的是`window.bus=new Vue()`这行代码的位置有讲究，不能把它放在`const app=new Vue({...});`之后。

在blade模板中，以下两个Vue组件是同级关系
```html
<cart-items :items="{{$cartItems}}" ></cart-items>
<cart-selected></cart-selected>
```
![file](https://cdn.learnku.com/uploads/images/201901/01/24833/l56bKNw1bs.gif!large)
**组件1的作用：**
1、接收数据库查询到的复杂对象 $cartItems
2、根据用户的勾选或删除等操作进行加工处理，得到特定数据data
3、加工完毕，发送一个消息 msg_SelectedItemChange 并对外传递数据data
组件1发送数据的代码很简单，只需一行
`bus.$emit('msg_SelectedItemChange', data)`

**组件2的作用：**
1、接收组件1加工后的数据data
2、将此数据刷新到浏览器里（或者等待用户点击提交按钮发送ajax请求等等）
组件2接收数据稍微麻烦一点，因为需要使用一个回调函数，大致如下
`bus.$on('msg_SelectedItemChange', 回调函数的名称)`

**难点在于回调函数内部的this指向可能有问题**，比如下面的写法就不能在浏览器里面显示组件2获取的数据
```vue
<template>
    <div>组件2：{{cartItems}}</div>
</template>
<script>
    export default {
        name: "CartSelected",
        data(){
            return{
                cartItems:[],
            }
        },
        mounted(){
            bus.$on('msg_SelectedItemChange', function(data){
                this.cartItems=data;    //无效，因为this指向的已经不是本组件了！
            })
        },
    }
</script>
```

尝试使用call()重定向回调函数内部的this指向也是无效的
```js
        mounted(){
            bus.$on.call(this, 'msg_SelectedItemChange', function(data){
                this.cartItems=data;    //也无效
            })
        },
```

改用that重定向，有效！
```js
        mounted(){
            let that=this;
            bus.$on('msg_SelectedItemChange', function(data){
                that.cartItems=data;    //有效，匿名函数使用that重定向到本组件
            })
        },
```

改用立即执行函数并返回一个**闭包函数**的写法也是有效的，可惜晦涩得很，写起来也麻烦。闭包函数既能访问形参it又能访问that变量，使用任何一个都可以完成任务，唯独不能使用this
```js
        mounted(){
            let that=this;
            bus.$on('msg_SelectedItemChange', function(it){
                return function(data){
                    that.cartItems=data;    // OK
                    it.cartItems=data;      // OK too
                    this.cartItems=data;    // Bad
                }
            }(this))
        },
```

**使用bind重定向（很简洁的写法）**
虽然that重定向的写法也很简洁，但是函数体里面的this必须全部改用that替换。使用bind则无需替换，刚好解决了这一缺陷。
```js
        mounted(){
            bus.$on('msg_SelectedItemChange', function(data){
                this.cartItems=data;
            }.bind(this))
        },
```
		
也可以采用一种很**标准的写法**（往methods对象里面插入一个新方法）。此法不够简洁，但是比较灵活，可以随时调用refresh()方法。
```js
        methods:{
            refresh(data){
                this.cartItems=data;
            }
        },
        mounted(){
            bus.$on('msg_SelectedItemChange', this.refresh)
        },
```
		
下面是第5种，使用**箭头函数**的写法（比bind还要简洁）
```js
        mounted(){
            bus.$on('msg_SelectedItemChange', data=>
                this.cartItems=data
            )
        },
```

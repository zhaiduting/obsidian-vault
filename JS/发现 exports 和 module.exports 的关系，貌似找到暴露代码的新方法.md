**这两东西都是指向 this 对象的！**
  `exports===this`
而且
  `module.exports===this`
  
对外暴露接口的新方法：**直接绑定到 this 对象上即可？**
  
这是我今天鼓捣 webpack 的时候发现的，特此记录。
本项目链接 git@gitee.com:zhaiduting/webpk.git
或者用 https://gitee.com/zhaiduting/webpk.git
项目下载后
1、安装依赖包 npm install
2、编译项目 npx webpack
3、在浏览器中打开 dist/index.html
4、按下功能键 F12 即可看到如下截图
![file](https://cdn.learnku.com/uploads/images/201903/18/24833/D1714abixz.png!large)
其中主js文件为 main.js 代码如下
```
var thisModule=require('./thisModule')
console.log('in main.js:')
console.log(thisModule);
console.log('thisModule.af(7,8):'+thisModule.af(7,8));
console.log('thisModule.f(9):'+thisModule.f(9));
```
其中引入的模块为 thisModule.js 代码如下
```js
var a = 200;

module.exports.f = [1,2,3];                 // module.exports 指向不变
exports.f = a;                              // exports 指向不变，覆盖了同名属性！

console.log('module.exports:');
console.log(module.exports)                 // 200
console.log('\n')

console.log('exports:');
console.log(exports)                        // 200
console.log('\n')

console.log('module.exports===exports:')
console.log(module.exports===exports)       // true
console.log('\n')

console.log('module.exports===this')
console.log(module.exports===this)          // true
console.log('\n')

console.log('exports===this')
console.log(exports===this)                 // true
console.log('\n')

this.arr=[2019, 3, 18]                      // Export Array OK
this.obj={email: 'zhaiduting@163.com'}      // Export Object OK
this.f=function(f){return f}                // Export Function OK
this.af=(a, f)=>a+f                         // Export Arrow Function OK
```
各版本号如下
![file](https://cdn.learnku.com/uploads/images/201903/18/24833/EJXtwCJC6C.png!large)
虽然构造函数并没有显式返回this，但是new运算的结果会自动拿到this
```js
function D1(){}
var d1= new D1

function D2(){
	return this
}
var d2= new D2
```
上面这段代码中构造函数D2的`return this`看起来有点多余了。在new运算中的确是多余的，但是在call函数中就未必了。

## 便捷的链式调用
```js
function C1(){
	this.log= function(){
		console.log(`C1 ${this.name}`)
	}
}
function C2(){
	this.log= function(){
		console.log(`C2 ${this.name}`)
	}
	return this			//显式返回this
}
function C3(){
	this.log= function(){
		console.log(`C3 ${this.name}`)
	}
	this.name= 'Tom'	   //再加个属性
	return this			//显式返回this
}

var obj

obj= {name: 'Jack'}
C3.call(obj).log()		//链式输出Tom（属性覆写）

obj= {name: 'Jack'}
C2.call(obj).log()		//链式输出Jack

obj= {name: 'Jack'}
C1.call(obj).log()		//报错，call的结果是undefined
		
```
上面这段代码的最后一行会报错，这是因为构造函数C1并没有返回this，因此`C1.call(obj)`执行后call()的返回值为undefined，显然`undefined.log()`会报错。

为了避免报错，可以象下面这样采用分步走的写法
```js
obj= {name: 'Jack'}
C1.call(obj)
obj.log()				//两步输出Jack
```
显然，分步走的写法没有一步到位来得痛快。为了在**call()方法调用之后继续进行链式调用**，就有必要在构造函数的末尾显式声明`return this`了。可见在构造函数的末尾显式返回this对象并不是画蛇添足的写法。

## call()函数的副作用

上面的例子已经暴露了call()的副作用，以下是从中抽离的相关代码
```js
function C3(){
    this.log= function(){
        console.log(`C3 ${this.name}`)
    }
    this.name= 'Tom'       //这个属性会带来副作用
    return this            //显式返回this实现链式调用
}

var obj= {name: 'Jack'}
C3.call(obj).log()		 //Tom， not Jack
```
程序的本意是想要借用构造函数C3的log方法打印对象属性obj.name，预期输出Jack。但是“借用”的结果却带来了副作用，不仅仅借来了log方法，甚至连不想要的name属性也被“捆绑”了，最终导致输出结果为Tom而非预期的Jack

## 消除副作用

为消除副作用，可能会想到抽离所有的成员方法，然后全部挂载到函数原型上。写法如下
```js
function C3(){
    this.name= 'Tom'
	return this		//这行失去意义，该删除了
}
C3.prototype.log= function(){
	console.log(`C3 ${this.name}`)
}

var obj= {name: 'Jack'}
C3.prototype.log.call(obj)	//正确输出Jack
```
虽然构造函数依旧返回了this对象，但是C3的成员方法this.log已经不存在了，此时继续使用`C3.call(obj).log()`会报错。由于log方法已经挂载到C3原型上了，因此“借用”log的写法也得改成`C3.prototype.log.call(obj)`

## 总结

讲了这么多，一段代码总结就是下面这样的
```js
function C3(){
    this.log= function(){
        console.log(`C3 ${this.name}`)
    }
    this.name= 'Tom'
    return this
}
C3.prototype.log= function(){
    console.log(`C3.prototype ${this.name}`)
}
C3.prototype.name= 'Rose'

var obj= {name: 'Jack'}
C3.call(obj).log()            //C3 Tom（覆盖了Jack）

var obj= {name: 'Jack'}
C3.prototype.log.call(obj)    //C3.prototype Jack（不会被Tom或Rose覆盖）
```
**直接在构造函数上调用call()方法可能会带来副作用**，因此应当避免使用诸如`C3.call(obj)`这样的写法。在一个普通函数上调用call()方法无此顾虑，例如本例的`log.call(obj)`这样的写法。
在编写构造函数的时候，如果期望某个成员方法可供外部代码安全借用，就应当将此方法挂载到原型上，例如本例的`C3.prototype.log= function(){...}`
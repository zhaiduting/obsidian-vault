这两个东西有啥区别？通过 atom() 创建的是响应式的状态对象，而  selector() 创建的是派生状态。把这个派生状态看成 Vue 中的计算属性就容易理解了。
### 定义状态

定义状态的写法如下
```js
const aaa = atom({
	key:
	default:
})

const sss = selector({
	key:
	get:
	set?:
})
```

### 使用状态

使用atom状态时，可以直接拿到数组并解构出get与set，也可以只拿一个get或者set
```js
const [get, set] = useRecoilState(aaa); // [get, set]
const get = useRecoilValue(aaa); // 只拿 get
const set = useSetRecoilState(aaa); // 只拿 set
```

使用无 set 属性的 selector 状态，只能使用 useRecoilValue 拿到 get
对于有 set 属性的 selector 状态，可以将其看成 atom 状态来使用
```js
const [get, set] = useRecoilState(sss); // 既要读又要写时用这种写法
const get = useRecoilValue(sss); // 只想读取内容
const set = useSetRecoilState(sss); // 只想写入而不用读取
```

### 小结

useRecoilState 返回数组，可以解构出 set 与 get 
useRecoilValue 只返回 get 没有 set
useSetRecoilState 只能拿到 set

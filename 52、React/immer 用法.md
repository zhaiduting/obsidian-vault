### 对于原始数据类型

如果 atomWithImmer(x) 中的 x 是数字、字符串、布尔值等原始数据类型，那么下面的写法不会生效

```js
import { useAtom } from "jotai";
import { atomWithImmer } from "jotai-immer";

const counterAtom = atomWithImmer(0);

export function MyCounter() {
	const [counter, setCounter] = useAtom(counterAtom);
	const clickHandler = () => {
		setCounter((draft) => {
			draft += 1; // ❌ 不生效
		});
	};
	return <button onClick={clickHandler}>{counter}</button>;
}
```

将其中的 setCounter(...) 改成下面的写法简单、有效

```js
setCounter(counter + 1); // 这需要先读取 atom 值
```

或者下面的写法也行

```js
// ✅ 正确写法，优点：无需读取 atom 值
setCounter((draft) => draft + 1);
```

注意，上面的箭头函数有返回值。而下面写法没有返回值，这种写法原始数据无效

```js
setCounter((draft) => {
	// ❌ 箭头函数无返回值，无效！
	draft + 1;
});
```

### 对于数组或对象

如果 atomWithImmer(x) 中的 x 是数组或者对象，无返回值的箭头函数的写法将会变得有效。将前例中的数字 0 改用对象 {c: 0} 替换，得到如下对比示例

```js
const counterAtom = atomWithImmer({ c: 0 });

export function MyCounter() {
	const [counter, setCounter] = useAtom(counterAtom);
	const clickHandler = () => {
		setCounter((draft) => {
			// ✅ 箭头函数无返回值，有效
			draft.c++;
		});
	};
	return <button onClick={clickHandler}>{counter.c}</button>;
}
```

注意，下面的写法仍然是错误的

```js
setCounter((draft) => {
	// ❌ 无效
	{
		draft = { c: draft.c + 1 };
	}
});
```

### 总结

错误写法的共同点都是尝试直接修改 draft 值，而这种修改仅仅在箭头函数内部有效，对于外部是无效的（除非明确 `return draft` 才会保持修改）。

immer 的主要用途是用来直接修改数组状态的某个元素或者对象状态的某个属性。对于原始数据类型的状态，使用 `atom(x)` 就可以了，无需使用 `atomWithImmer(x)`

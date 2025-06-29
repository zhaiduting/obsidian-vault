## 🧪 对比一览：atom

| 功能      | Recoil                    | Jotai                             |
| ------- | ------------------------- | --------------------------------- |
| 定义 atom | `atom({ key, default })`  | `atom(initialValue)`              |
| 使用 atom | `useRecoilState(atom)`    | `useAtom(atom)`                   |
| 只读值     | `useRecoilValue(atom)`    | `const val= useAtomValue(atom)`   |
| 更新值     | `useSetRecoilState(atom)` | `const setVal = useSetAtom(atom)` |

## ⚙️ 对比二：派生状态（selector）

| 功能         | Recoil `selector`        | Jotai `derived atom` |
| ------------ | ------------------------ | -------------------- |
| 创建方式     | `selector({ get, set })` | `atom(get => ...)`   |
| 是否必须命名 | 需要 `key`               | 不需要 key           |
| 是否支持异步 | 支持                     | 支持（更原生）       |
| 支持写入     | `set()` 可选             | 可定义读写 atom      |

## Recoil 示例

组件 RecoilCounter 必须放置在 RecoilRoot 标签内部，状态必须使用 key，不方便！

```js
import { atom, selector, useRecoilState, useRecoilValue } from "recoil";

const countState = atom({
	key: "countState",
	default: 7,
});
const doubleState = selector({
	key: "doubleState",
	get: ({ get }) => get(countState) * 2,
});

export function RecoilCounter() {
	const [count, setCount] = useRecoilState(countState);
	const double = useRecoilValue(doubleState);
	return (
		<>
			<h2>Recoil</h2>
			<p>Count: {count}</p>
			<p>Double: {double}</p>
			<button onClick={() => setCount(count + 1)}>Recoil +1</button>
		</>
	);
}
```

## Jotai 示例

相对于 Recoil 而言，Jotai 要方便得多

```js
import { atom, useAtom } from "jotai";

const countAtom = atom(8);
const doubleAtom = atom((get) => get(countAtom) * 2);

export function JotaiCounter() {
	const [count, setCount] = useAtom(countAtom);
	const [double] = useAtom(doubleAtom);
	return (
		<div>
			<h2>Jotai</h2>
			<p>Count: {count}</p>
			<p>Double: {double}</p>
			<button onClick={() => setCount(count + 1)}>Jotai +1</button>
		</div>
	);
}
```

## 结论

Jotai 无需设置 key， 无需使用 RecoilRoot 包裹状态，派生状态的 get 函数无需解构即可获取，使用状态时只需一个 useAtom 即可搞定。总之，**Jotai 要比 Recoil 更简洁、更优雅**。

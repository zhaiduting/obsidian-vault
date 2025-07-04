用 `atomWithStorage(key, value)` 定义一个本地存储的状态 baseAtom 并将其作为参数传递给 `withImmer(baseAtom)`，如下

```js
import { atomWithStorage } from "jotai/utils";
import { withImmer } from "jotai-immer";
import { useAtom } from "jotai";

const baseAtom = atomWithStorage("key", { count: 0 });
const immerAtom = withImmer(baseAtom);
```

在组件内，读取及修改这个组合状态值的方法非常简单，仍然使用 `useAtom()` 钩子。这与读、写一个普通状态值的方式没有区别。

```js
export const AtomWithStorageImmer = () => {
	const [state, setState] = useAtom(immerAtom);
	return (
		<>
			<h3>Using atomWithStorage with Immer</h3>
			<button
				onClick={() =>
					setState((draft) => {
						draft.count++;
					})
				}
			>
				{state.count}
			</button>
		</>
	);
};
```

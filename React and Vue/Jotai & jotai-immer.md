使用 Jotai 管理状态，并使用 immer 直接修改这个状态（无需显式创建状态的新副本）。

### 安装 3 个依赖包

```shell
npm install jotai immer jotai-immer
```

### 示例代码

定义一个状态管理文件 todoListState.js 并附带两条测试数据，注意：使用的是 atomWithImmer() 而非 atom()

```js
import { atomWithImmer } from "jotai-immer";

export const todoListState = atomWithImmer([
	{ id: 1, text: "default1", isComplete: false },
	{ id: 2, text: "default2", isComplete: true },
]);
```

可以类似如下这样直接将鼠标点中的方框取反

```js
import { todoListState } from "./todoListState.js";
import { useAtom } from "jotai";

export function TodoItem({ item }) {
	const [todoList, setTodoList] = useAtom(todoListState);

	return (
		<p>
			<input
				type="checkbox"
				checked={item.isComplete}
				onChange={toggleItemCompletion}
			/>
			{item.text}
		</p>
	);

	function toggleItemCompletion() {
		setTodoList((draft) => {
			const target = draft.find((t) => t.id === item.id);
			target.isComplete = !target.isComplete;
		});
	}
}
```

注意，如果不使用 immer 的话，toggleItemCompletion()中的代码应该写成下面这样

```js
setTodoList((prev) =>
	prev.map((t) =>
		t.id === item.id ? { ...t, isComplete: !t.isComplete } : t
	)
);
```

### 总结

prev.map(...) 生成了一个完整的新数组，然后将新数组作为参数传递给 setTodoList() 以更改状态。而使用 immer 的做法更加直接：找到需要更改状态的 target 后直接修改发生变化的属性

使用浏览器存储状态的方法很简单，只需调用 `atomWithStorage(key, value)` 就可以将 value 存储在浏览器的 key 键下。例如以下代码在浏览器的 `应用 » 本地存储 » localhost:5173` 下创建了一个名为 darkMode 的键，并存入一个布尔值。

```js
import { atomWithStorage } from "jotai/utils";
const darkModeAtom = atomWithStorage("darkMode", false);
```

![localStorage](https://lib.zhaiduting.work.gd/uPic/localStorage.png)
在组件内，读取及修改本地存储的状态值的方法非常简单，仍然使用 `useAtom()` 钩子。这与读、写一个普通状态值的方式没有区别。

```js
export const AtomWithStorage = () => {
	// Consume persisted state like any other atom
	const [darkMode, setDarkMode] = useAtom(darkModeAtom);
	const toggleDarkMode = () => setDarkMode(!darkMode);
	return (
		<>
			<h1>Welcome to {darkMode ? "dark" : "light"} mode!</h1>
			<button onClick={toggleDarkMode}>toggle theme</button>
		</>
	);
};
```

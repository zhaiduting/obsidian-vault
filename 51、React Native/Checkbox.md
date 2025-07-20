对于 Expo Managed Workflow 项目，推荐使用如下命令

```shell
npm install react-native-paper
```

不推荐 `@react-native-community/checkbox`，因为它是 **需要原生模块支持的库**，如果 `expo-dev-client` 未安装则会报错。

```js
import { useState } from "react";
import { Checkbox } from "react-native-paper";

export const MyCheckbox = () => {
	const [checked, setChecked] = useState(false);

	return (
		<Checkbox
			status={checked ? "checked" : "unchecked"}
			onPress={() => {
				setChecked(!checked);
			}}
		/>
	);
};
```

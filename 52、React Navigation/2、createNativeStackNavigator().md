此函数接受一个包含屏幕 (screens) 和自定义选项的**配置对象**。屏幕就是渲染导航器显示的内容的 React 组件，导航器的导航功能其实就是在多个屏幕之间进行跳转。

将此函数的返回值传递给 createStaticNavigation() 便可创建静态导航，类似于下面这样

```jsx
function HomeScreen() {
	return (...);
}

const RootStack = createNativeStackNavigator({
	screens: {
		Home: HomeScreen,
	},
});

const Navigation = createStaticNavigation(RootStack);

export default function App() {
	return <Navigation />;
}
```

既然使用导航，那肯定不止一个屏幕，假设还要处理 DetailsScreen 详情屏幕，修改后代码如下

```jsx
const RootStack = createNativeStackNavigator({
	screens: {
		Home: HomeScreen,
		Details: DetailsScreen,
	},
	screenOptions: {
		headerShown: false,
	},
	initialRouteName: "Home",
});
```

其中的 initialRouteName 自定义选项指定 HomeScreen 组件为 App 启动后默认显示的屏幕。而 screenOptions 选项又是一个对象，属性 headerShown 值为 false 则不显示导航器的标题栏，这会导致返回按钮不可见。通常对于 tab 导航器不需要返回按钮，隐藏标题栏没问题。对于内嵌的导航器，为防出现多个标题栏，应当隐藏内嵌的标题栏。

### 指定选项

为了指定选项，需要将以上屏幕组件包装到一个 {screen, options} 对象里面，使用 screen 属性指定屏幕组件，并使用 options 对象指定各种选项。例如

```jsx
const RootStack = createNativeStackNavigator({
	screens: {
		Home: HomeScreen,
		Details: {
			screen: DetailsScreen,
			options: {
				title: "详情", // 或 headerTitle
			},
		},
	},
});
```

查看运行效果可以点击 [Try on Snack](https://snack.expo.dev/@zhaiduting/passing-params)

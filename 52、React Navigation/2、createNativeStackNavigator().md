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
	initialRouteName: "Home",
	screens: {
		Home: HomeScreen,
		Details: DetailsScreen,
	},
});
```

其中的 initialRouteName 自定义选项指定 HomeScreen 组件为 App 启动后默认显示的屏幕。

### 指定选项

为了指定选项，需要将以上屏幕组件包装到一个 {screen, options} 对象里面，使用 screen 属性指定屏幕组件，并使用 options 对象指定各种选项。例如

```jsx
const RootStack = createNativeStackNavigator({
	initialRouteName: "Home",
	screens: {
		Home: {
			screen: HomeScreen,
			options: {
				title: "Overview",
			},
		},
		Details: DetailsScreen,
	},
});
```

查看运行效果可以点击 [Try on Snack](https://snack.expo.dev/?name=Options+for+Screen&code=import+*+as+React+from+%27react%27%3B%0Aimport+%7B+View%2C+Text+%7D+from+%27react-native%27%3B%0Aimport+%7B+createStaticNavigation+%7D+from+%27%40react-navigation%2Fnative%27%3B%0Aimport+%7B+createNativeStackNavigator+%7D+from+%27%40react-navigation%2Fnative-stack%27%3B%0A%0Afunction+HomeScreen%28%29+%7B%0A++return+%28%0A++++%3CView+style%3D%7B%7B+flex%3A+1%2C+alignItems%3A+%27center%27%2C+justifyContent%3A+%27center%27+%7D%7D%3E%0A++++++%3CText%3EHome+Screen%3C%2FText%3E%0A++++%3C%2FView%3E%0A++%29%3B%0A%7D%0A%0Afunction+DetailsScreen%28%29+%7B%0A++return+%28%0A++++%3CView+style%3D%7B%7B+flex%3A+1%2C+alignItems%3A+%27center%27%2C+justifyContent%3A+%27center%27+%7D%7D%3E%0A++++++%3CText%3EDetails+Screen%3C%2FText%3E%0A++++%3C%2FView%3E%0A++%29%3B%0A%7D%0A%0Aconst+RootStack+%3D+createNativeStackNavigator%28%7B%0A++initialRouteName%3A+%27Home%27%2C%0A++screens%3A+%7B%0A++++Home%3A+%7B%0A++++++screen%3A+HomeScreen%2C%0A++++++options%3A+%7B%0A++++++++title%3A+%27Overview%27%2C%0A++++++%7D%2C%0A++++%7D%2C%0A++++Details%3A+DetailsScreen%2C%0A++%7D%2C%0A%7D%29%3B%0A%0Aconst+Navigation+%3D+createStaticNavigation%28RootStack%29%3B%0A%0Aexport+default+function+App%28%29+%7B%0A++return+%3CNavigation+%2F%3E%3B%0A%7D%0A&dependencies=%40react-navigation%2Fnative-stack%407.3.23%2C%40react-navigation%2Fnative%407.1.16%2Creact%40*%2Creact-native%40*%2Creact-native-safe-area-context%40*%2Creact-native-screens%40*&platform=web&supportedPlatforms=ios%2Candroid%2Cweb&preview=true&hideQueryParams=true)

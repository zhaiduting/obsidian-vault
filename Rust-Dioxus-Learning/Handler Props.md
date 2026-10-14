定义一个组件，它的点击事件的处理器将在组件调用时传入

```rust
#[component]
fn FancyButton(onclick: EventHandler<MouseEvent>) -> Element {
    rsx! {
        button { class: "fancy-button",
        onclick: move |evt| onclick.call(evt),
        "Click me!" }
    }
}
```

调用这个组件：

```rust
FancyButton { onclick: move |_| info!("FancyButton clicked!") }
```

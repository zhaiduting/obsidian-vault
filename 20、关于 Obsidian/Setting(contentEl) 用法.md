Obsidian 插件的 Modal 的 `onOpen` 方法代码如下

```js
const {contentEl} = this;
contentEl.setText('普通文字');
contentEl.createEl('h2', {text: '标题文字'});
new Setting(contentEl).setName('感觉怎样？');
new Setting(contentEl).setName('感觉如何？').addText(text => text.setValue('还行'));
new Setting(contentEl).addText((text) =>
    text.setValue('无提示的输入框的文字').onChange(value => {
       console.log(value);
    }),
);
new Setting(contentEl).addButton((btn) =>
    btn.setCta().setButtonText('关闭').onClick(() => {
       this.close(); // 不是 onClose()    })
);
```

效果图如下
![Setting(contentEl)](<https://lib.zhaiduting.work.gd/uPic/Setting(contentEl).png>)
其中`setCta()` 的作用是将按钮设置为“主要行动按钮”（Call To Action），这会给按钮添加特定的样式，使其在视觉上更加突出。

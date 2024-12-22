这个插件是为 obsidian 插件的开发者设计的，如果只是使用 obsidian 但并不打算自己开发插件，那就不需要安装这个东西。

这个插件的作用是“热重载”，避免了每次更改插件源码后都需要手动加载一遍才能使更改生效的问题。安装命令如下
```sh
cd ~/obsidian-vault/.obsidian/plugins
git clone git@github.com:pjeby/hot-reload.git
```

尽管 obsidian 的插件市场搜索不到这款插件，但它确实有效。
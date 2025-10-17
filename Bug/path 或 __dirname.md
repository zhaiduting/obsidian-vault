无法直接使用 `__dirname`

```
未解析的变量或类型 __dirname
```

或者 `import path from "path"` 报错

```
TS2307: Cannot find module path or its corresponding type declarations.
```

### 解决方式

```sh
npm i -D @types/node
```

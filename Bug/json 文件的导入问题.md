`tsconfig.json` 文件需要开启 `resolveJsonModule`

```json
{
    "compilerOptions": {
       "resolveJsonModule": true,
       ...
    },
    "include": [
       "**/*.ts"
    ]
}
```

导入 json 文件时需要使用 `with{..}`

```js
import data from "./data.json" with { type: "json" };
```

---

`"resolveJsonModule": true` 和 `with { type: 'json' }` 看起来做的是一件事，但**它们其实属于两个完全不同的机制**——一个是 **TypeScript 编译期行为**，另一个是 **JavaScript 运行时行为**。

| 功能   | `"resolveJsonModule": true` | `with { type: 'json' }`             |
| ---- | --------------------------- | ----------------------------------- |
| 属于谁  | TypeScript 编译器              | ECMAScript (运行时)                    |
| 作用时机 | 编译时（类型检查 & 转译）              | 运行时（Node.js/浏览器真正加载模块时）             |
| 作用内容 | 允许导入 `.json` 并生成类型          | 告诉运行时这不是 JS 模块，而是 JSON 数据           |
| 是否必要 | 仅 TypeScript 环境时足够          | 若目标是 ES 模块 + 原生运行时（Node ≥20、浏览器）则必须 |

```js
import prettier from "prettier";
import { ps } from "./ps.mjs";

const { group, concat, join, line } = prettier.doc.builders;
const arr = ["1", "2", "3"];

// -------------------------
// 1. JS join
// -------------------------
const jsResult = arr.join(", ");
console.log("=== JS join ===");
console.log(jsResult);

// -------------------------
// 2. Prettier join
// -------------------------
const doc = group(join(concat([",", line]), arr));
console.log("=== Prettier join (printWidth=6) ===");
ps(doc, 6);

console.log("=== Prettier join ===");
ps(doc);
```

运行结果如下

```
=== JS join ===
1, 2, 3
=== Prettier join (printWidth=6) ===
1,
2,
3
=== Prettier join ===
1, 2, 3
```

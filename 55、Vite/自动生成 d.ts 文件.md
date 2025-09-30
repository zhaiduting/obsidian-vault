配置 vite.config.js 文件，使用 vite-plugin-dts 自动生成 d.ts

```js
import { defineConfig } from "vite";
import dts from "vite-plugin-dts";

export default defineConfig({
  build: {
    // ...
  },
  plugins: [
    dts({
      insertTypesEntry: true,
      include: ["src/index.ts"],
    }),
  ],
});
```

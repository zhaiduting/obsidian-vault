```sh
kubectl get node
kubectl describe node

kubectl explain node
kubectl explain node.spec
```

### 核心区别对比表

| **特性**       | **kubectl get**        | **kubectl describe**         | **kubectl explain**       |
| -------------- | ---------------------- | ---------------------------- | ------------------------- |
| **侧重点**     | 状态摘要 (List)        | 运行详情 (Detail/Status)     | 结构定义 (Definition/API) |
| **数据来源**   | 资源的当前关键指标     | 资源详情 + 相关事件 (Events) | 集群 API 文档             |
| **解决的问题** | “节点在线吗？”         | “节点为什么报错？”           | “这个字段怎么写？”        |
| **输出格式**   | 表格（可转 JSON/YAML） | 易读的文本段落               | 文本说明                  |

### 使用场景

如果你只是想确认节点好不好，用 `get`；如果节点出问题了需要找原因，用 `describe`；如果你在写 YAML 配置但不知道某个属性怎么填，用 `explain`。

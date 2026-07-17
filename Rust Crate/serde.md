**Serde** 是 Rust 生态里最著名的序列化（Serializer）和反序列化（Deserializer）框架。它的名字就是 **Ser**ialization 和 **De**serialization 的缩写。只要涉及到网络传输（Web API、微服务）、配置文件读取、数据库存储或者持久化缓存，几乎 100% 都会用到 Serde。

### 序列化（Serialize）

以下展示将结构体序列化为 yaml 或 json 的写法

```rust
use serde::{Serialize};

#[derive(Serialize)]
struct User {
    name: String,
    age: u32,
}

fn main() {
    let user = User {
        name: "Tom".into(),
        age: 18,
    };

    let yaml = serde_yaml::to_string(&user).unwrap();
    println!("{yaml}");

    let json = serde_json::to_string(&user).unwrap();
    println!("{json}");
}
```

输出结果如下

```sh
name: Tom
age: 18

{"name":"Tom","age":18}
```

### 反序列化（Deserialize）

将上例代码修改一下得到对应的反序列化示例

```rust
// 1. 引入 Deserialize 宏
use serde::{Deserialize};

// 2. 派生 Deserialize  trait
#[derive(Deserialize, Debug)] // 加上 Debug 方便打印查看结果
struct User {
    name: String,
    age: u32,
}

fn main() {
    // 准备一段 YAML 文本
    let yaml_data = r#"
name: Tom
age: 18
"#;

    // 准备一段 JSON 文本
    let json_data = r#"{"name":"Tom","age":18}"#;

    // 3. 反序列化 YAML
    // 我们需要显式指定变量类型为 : User，让 Serde 知道要解析成什么结构
    let user_from_yaml: User = serde_yaml::from_str(yaml_data).unwrap();
    println!("From YAML: {:?}", user_from_yaml);

    // 4. 反序列化 JSON
    let user_from_json: User = serde_json::from_str(json_data).unwrap();
    println!("From JSON: {:?}", user_from_json);
}
```

输出如下

```sh
From YAML: User { name: "Tom", age: 18 }
From JSON: User { name: "Tom", age: 18 }
```

### 对比

对于序列化示例中的代码 `let json = serde_json::to_string(&user).unwrap()` 无需显式注解 json 的数据类型。

然而反序列化时，对于 `let user_from_json = serde_json::from_str(json_data).unwrap()` 这样的无类型注解的写法则会报错。必须显式指定 `user_from_json` 的数据类型为 `User` 或者改用如下所示的涡轮鱼写法
`let user_from_json = serde_json::from_str::<User>(json_data).unwrap()`
很显然，涡轮鱼的写法又丑又长。

> [!TIP]
> 如果解析出来的变量马上就要传给另一个函数（比如作为 Web 框架的响应返回，或者传给业务函数），**只要接收函数声明了类型，`let` 这一步就完全不需要任何注解。**

反序列化时无需涡轮鱼，无需类型注解的示例如下

```rust
fn process_user(user: User) {
    println!("Processing {}...", user.name);
}

fn main() {
    // 编译器会顺着 process_user 的参数类型，反向推导出这里应该解析为 User
    // 这一行代码里没有任何长类型注解或涡轮鱼！
    let user = serde_json::from_str(json_data).unwrap();

    process_user(user);
}
```

命令为

```sh
cargo new <项目名称>
```

在项目根目录里，敲入以下命令直接编译并运行

```sh
cargo run
```

终端记录如下

```sh
Last login: Tue Dec  2 15:08:56 on ttys000
~ > cd Code
Code > cargo new hello-rust
    Creating binary (application) `hello-rust` package
note: see more `Cargo.toml` keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
Code > cd hello-rust
hello-rust > tree
.
├── Cargo.toml
└── src
    └── main.rs

2 directories, 2 files
hello-rust > cat src/main.rs
fn main() {
    println!("Hello, world!");
}
hello-rust > cargo run
   Compiling hello-rust v0.1.0 (/Users/zdt/Code/hello-rust)
    Finished `dev` profile [unoptimized + debuginfo] target(s) in 9.98s
     Running `target/debug/hello-rust`
Hello, world!
hello-rust >
```

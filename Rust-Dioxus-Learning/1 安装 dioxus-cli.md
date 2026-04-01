```sh
cargo install cargo-binstall
cargo binstall dioxus-cli --force
```

### 检查开发环境

```sh
~ > dx --version
dioxus 0.7.3 (22b06ba)
~ >
~ > dx doctor
Setup
 Web: wasm-bindgen, wasm-opt, and TailwindCSS are downloaded automatically
 iOS: Install iOS SDK and developer tools and through XCode
 Android: Install Android Studio, NDK, and then set ANDROID_HOME and ANDROID_NDK_HOME
 macOS: all tools should be installed by default
 Windows: install the webview2 binary
 Linux: Install libwebkit2gtk-4.1-dev libgtk-3-dev libasound2-dev libudev-dev libayatana-appindicator3-dev libxdo-dev libglib2.0-dev
 nix: Make sure all tools are in your path (codesign, ld, etc.)

Rust
 Rustc version: rustc 1.91.1 (ed61e7d7e 2025-11-07)

 Rustc sysroot: /Users/zdt/.rustup/toolchains/stable-aarch64-apple-darwin
 Rustc path: /Users/zdt/.cargo/bin/rustc
 Cargo path: /Users/zdt/.cargo/bin/cargo
 cc path: /usr/bin/cc

Devtools
 VSCode Extension: not found
 VSCode-Insiders Extension: not found
 Cursor Extension: not found
 TailwindCSS: /Users/zdt/.dx/tailwind/tailwindcss-v4.1.5

Web
 wasm-opt: not installed
 wasm-bindgen: automatically managed
 wasm-bindgen version: automatically managed

iOS/macOS
 XCode: /Applications/Xcode.app/Contents/Developer
 Simulator: /Applications/Xcode.app/Contents/Developer/Applications/Simulator.app
 Security CLI: /usr/bin/security
 Codesign CII: /usr/bin/codesign
 xcode-select: /usr/bin/xcode-select
 xcrun: /usr/bin/xcrun
 ranlib: /usr/bin/ranlib

Android
 sdk: /Users/zdt/Library/Android/sdk
 ndk: /Users/zdt/Library/Android/sdk/ndk/27.1.12297006
 adb: /Users/zdt/Library/Android/sdk/platform-tools/adb
 emulator: /Users/zdt/Library/Android/sdk/emulator/emulator
 java_home: /Applications/Android Studio.app/Contents/jbr/Contents/Home

Toolchains
 ❌ wasm32-unknown-unknown (web)
 ❌ aarch64-linux-android (android)
 ❌ i686-linux-android (android)
 ❌ armv7-linux-androideabi (android)
 ❌ x86_64-linux-android (android)
 ❌ x86_64-apple-ios (iOS)
 ❌ aarch64-apple-ios (iOS)
 ❌ aarch64-apple-ios-sim (iOS)
 ✅ aarch64-apple-darwin (iOS)

Get help: https://discord.gg/XgGxMSkvUM
More info: https://dioxuslabs.com/learn/0.7/
```

### 创建首个项目

```sh
~ > cd Code
Code > dx new hot_dog
warning: Waiting for https://github.com/dioxuslabs...
warning: (1/5) Taking a while, maybe your internet is down?
✔ 🤷   Which sub-template should be expanded? · Bare-Bones
✔ 🤷   Do you want to use Dioxus Fullstack? · false
✔ 🤷   Do you want to use Dioxus Router? · false
✔ 🤷   Do you want to use Tailwind CSS? · false
✔ 🤷   Do you want to include prompts for LLMs? · false
✔ 🤷   Which platform do you want DX to serve by default? · Web
# 此处省略部分内容

`cd` to your project and run `dx serve` to start developing.
More information is available in the generated `README.md`.

Build cool things! ✌️
Code >
```

### 启动项目

```sh
cd hot_dog
dx serve
```

之后会显示一个链接，点击查看效果

### 执行 `npm run ios` 命令报错

```shell
my-navi > npm run ios

> my-navi@1.0.0 ios
> expo run:ios

› Planning build

› 0 error(s), and 0 warning(s)

CommandError: Failed to build iOS project. "xcodebuild" exited with error code 70.
To view more error logs, try building the app with Xcode directly, by opening /Users/zdt/Code/my-navi/ios/mynavi.xcworkspace.

Command line invocation:
    /Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild -workspace /Users/zdt/Code/my-navi/ios/mynavi.xcworkspace -configuration Debug -scheme mynavi -destination id=E861FAF7-36E7-4CF9-8494-35CBE253493F



2025-07-29 12:16:09.733 xcodebuild[23266:436611] Writing error result bundle to /var/folders/wl/4qgscgcn0xbcs2p4yjt9l48m0000gn/T/ResultBundle_2025-29-07_12-16-0009.xcresult
xcodebuild: error: Unable to find a destination matching the provided destination specifier:
                { id:E861FAF7-36E7-4CF9-8494-35CBE253493F }

        Ineligible destinations for the "mynavi" scheme:
                { platform:iOS, id:dvtdevice-DVTiPhonePlaceholder-iphoneos:placeholder, name:Any iOS Device, error:iOS 18.5 is not installed. To use with Xcode, first download and install the platform }
Build logs written to /Users/zdt/Code/my-navi/.expo/xcodebuild.log
```

其中的关键信息是 error:iOS 18.5 is not installed.
解决办法很简单，安装一个：`Xcode » 设置 » Components » iOS 18.5 » Get`
![安装iOS模拟器](https://lib.zhaiduting.work.gd/uPic/%E5%AE%89%E8%A3%85iOS%E6%A8%A1%E6%8B%9F%E5%99%A8.png)

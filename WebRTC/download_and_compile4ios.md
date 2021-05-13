
*NOTE* : 
- 编译环境最好有配置好的开发者账号并配好证书签名等信息，否则Demo可能无法正常编译
- 尽量保持编译webrtc使用的编译器与上层应用使用的编译器版本保持一致，并保持-std=c++ xx一致，防止ABI兼容问题。
- [webrtc版本，目前使用的4280](https://chromiumdash.appspot.com/branches)
  
# 准备工作

编译对应平台的webrtc，最好用对应平台的机器去下载webrtc源码，之前使用Ubuntu下载webrtc ios源码，编译时发现third party下的llvm clang架构不对，下载的clang是gnu Linux的，不是Mac下的Mach-o架构的.

# 需要工具

1. 稳定的科学上网工具（我自己mac用了ShadowsocksX-NG-R8,支持SSR协议）
2. git
3. 安装depot_tools并配置环境变量（具体配置可以谷歌）   
    git clone https://chromium.googlesource.com/chromium/tools/depot_tools

# 配置环境

## git代理配置

```sh
vim ~/.gitconfig 文件添加如下，后面端口配置成自己工具端口即可，ShadowsocksX-NG-R8可查看具体代理端口
[https]
    proxy = https://127.0.0.1:1087
[http]
    proxy = http://127.0.0.1:1087
# socks5
[socks]
    proxy = http://127.0.0.1:1086
```

## depot_tools配置

```sh
# 在depot_tools目录下面建立http_proxy.boto文件
# 内容如下
[Boto]
    proxy=127.0.0.1
    proxy_port=1087
# 保存并退出
```
  
## 最终终端配置  

```sh
export http_proxy=127.0.0.1:1087
export https_proxy=127.0.0.1:1087
export PATH=$PATH:/Users/admin/code/depot_tools
export DEPOT_TOOLS_UPDATE=0  # 不更新depot_tools
export NO_AUTH_BOTO_CONFIG=/Users/admin/code/depot_tools/http_proxy.boto
```
到这里基本上准备工作都已经做完了。可以开始下载代码了。

# 源码下载

1. 下载代码
```sh
mkdir webrtc-ios && cd webrtc-ios
fetch --nohooks webrtc_ios
gclient sync # 同步代码，如果出错继续执行这行代码即可 
# 下载指定分支代码，例如我下载branch-4280 就是 
cd src
git checkout -b branch-4280 branch-heads/4280 #（远端分支名git branch -r查看）
gclient sync # 切换分支 也需要同步代码
cd ..
gclient sync --with_branch_heads # 不执行可能会出现未知错误
```
2. 接下来就是漫长等待，等待所有的都同步完成即可。
3. 编译和生成指定工程
4. 生成arm64平台ninja工程
```sh
 # release build for 64-bit iOS
 gn gen out/ios_64 --args='target_os="ios" rtc_enable_protobuf=false is_debug=false  target_cpu="arm64"'
 # 设置rtc_enable_protobuf 为false，开发中如果其他模块用到Google protocol buf会有崩溃，因此设置为false
 # 如果编译机没有开发者账号，可添加 `ios_enable_code_signing=false`,但编译出的APP Demo可能无法在真机运行，静态库不确认是否有影响.
 # 若有min_sdk 问题,可修改find sdk脚本。
```
5. 生成模拟器的（可选）
```sh
 # debug build for simulator
 gn gen out/ios_sim --args='target_os="ios" target_cpu="x64"'
```
    
6.  编译app
```sh
 ninja -C out/ios_64 AppRTCMobile
```  
7. 同样可以生成xcode工程用xcode编译
```sh
gn gen out/ios --args='target_os="ios" target_cpu="arm64"' --ide=xcode
open -a Xcode.app out/ios/all.xcworkspace
```
    
8. 编译出wenbrtc库文件
```sh
# 编译各个单独模块的静态库
ninja -C out/ios framework_objc
# 也可以调用脚本编译
python build_ios_libs.py --bitcode 生成库文件在out_ios_lib下面
# 编译libwebrtc.a 该库包含所有依赖，具体可查看out/ios_xx/obj/build.ninja文件
ninja -C out/ios webrtc
```
9. 结束

    正常使用直接编arm(armv7)和arm64架构的libwebrtc.a 库，然后用lipo合并成一个库给上层使用即可.


# 参考链接
- [webrtc ios 代码下载编译终极版](https://www.jianshu.com/p/f8ddf30845f9)
- [源码master文档](https://webrtc.googlesource.com/src/+/refs/heads/master/docs/native-code/ios/index.md)
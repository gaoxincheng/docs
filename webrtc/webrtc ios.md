
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
vim ~/.gitconfig 文件添加如下，后面端口配置成自己工具端口即可
[https]
    proxy = https://127.0.0.1:1087
[http]
    proxy = http://127.0.0.1:1087
# socks5
[socks]
    proxy = http://127.0.0.1:1086
```
## 终端配置

```sh
# Mac和Linux
export http_proxy=127.0.0.1:1087
export https_proxy=127.0.0.1:1087
# Windows
set http_proxy=127.0.0.1:1087
set https_proxy=127.0.0.1:1087
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
mkdir webrtc-checout-ios && cd webrtc-checout-ios
fetch --nohooks webrtc_ios
gclient sync # 同步代码，如果出错继续执行这行代码即可 
git new-branch your-branch-name 
# 下载指定分支代码，例如我下载branch-68 就是 
cd src
git checkout -b branch-68 remote-branch68 #（远端68具体地址）
gclient sync # 切换分支 也需要同步代码
cd ..
gclient sync --with_branch_heads # 不执行可能会出现未知错误
```
2. 接下来就是漫长等待，等待所有的都同步完成即可。
3. 编译和生成指定工程
4. 生成arm64平台ninja工程
```sh
 # debug build for 64-bit iOS
 gn gen out/ios_64 --args='target_os="ios" target_cpu="arm64"'
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
ninja -C out/ios framework_objc
# 也可以调用脚本编译
python build_ios_libs.py --bitcode 生成库文件在out_ios_lib下面
```
9. 结束
到这里基本上库和app都已经编译出来了。有问题可以在下面留言！

*NOTE* : 编译环境最好有配置好的开发者账号并配好证书签名等信息，否则Demo可能无法正常编译

[参考链接-webrtc ios 代码下载编译终极版](https://www.jianshu.com/p/f8ddf30845f9)

### 源码下载
```sh
   git clone --recursive https://github.com/obsproject/obs-studio.git
   git checkout 29.1.3
   git submodule init
   git submodule update
```
### 管理员权限启动powershell
```sh
	 Set-ExecutionPolicy Unrestricted
	 $env:HTTP_PROXY="http://127.0.0.1:7890"
	 $env:HTTPS_PROXY="http://127.0.0.1:7890"
	 $env:Path += ';D:\Program Files\7-Zip'
```

### 执行下载依赖
```sh
   CI/windows/01_install_dependencies.ps1
```

### CMAKE配置生成vs2022工程
- 创建构建目录`build-vs2022`
- CMake-gui 配置工程，增加配置项`DepsPath = E:\code\github\obs-build-dependencies\windows-deps-2023-04-12-x64`
- `CEF_ROOT_DIR=E:\work\molixiu_pro\sdk\obs-dependencies\cef_binary_5060_windows_x64`
- `ENABLE_BROWSER=true`
- 配置参数参考链接(OBS WIKI)[https://github.com/obsproject/obs-studio/wiki/Build-Instructions-For-Windows]

### OBS-DEP编译
- PowerShell 需要版本7以上
- Qt OpenSSL支持，需安装OpenSSL 1.1.1u，cmake会自动查询系统安装的OpenSSL，并在`qt6.ps1`中添加如下编译选项‘-ssl -openssl -openssl-runtime’
```sh
   $Env:PATH += ";D:\Program Files\CMake\bin"
   $env:Path += ';D:\Program Files\7-Zip'
    .\Build-Dependencies.ps1 -Configuration "Debug" -Dependencies "qt6"   # 构建qt6 debug
```

### OBS虚拟摄像头
- `ENABLE_VIRTUALCAM` 勾选
- 设置 `VIRTUALCAM_GUID` = "F5B693A0-517D-C3EB-14B5-3E55889C28FF" # 随机UUID

### 参考链接

- [Window下 VS2019+QT6下编译osb-studio](https://www.cnblogs.com/chaichengxun/p/16592116.html)
- [OBS WIKI](https://github.com/obsproject/obs-studio/wiki/Build-Instructions-For-Windows)
- [obs-vs2022编译](https://blog.csdn.net/hclbeloved/article/details/129485863)

- [bili 插件](https://github.com/bilibili/biliobs/blob/master/plugins/obs-filters/beauty-filter.c)

- [基于OBS开源直播软件](https://github.com/stream-labs/desktop)
- [阿里OBS输出插件](https://help.aliyun.com/zh/live/user-guide/download-sdks?spm=a2c4g.11186623.0.0.52942733C4SHvD#concept-2164399)

- [Qt6 编译选项](https://doc.qt.io/qt-6/configure-options.html)
- [Qt6 编译](https://www.cnblogs.com/eslzzyl/p/17270948.html)

- [进程通信ecal](https://github.com/eclipse-ecal/ecal)
- [chromium 进程通信框架Mojo](https://chromium.googlesource.com/chromium/src/+/master/mojo/README.md)

- [OBS人脸识别插件](https://github.com/norihiro/obs-face-tracker)


- 'state activate --default gaoxincheng/Perl-5.36.0-Windows'


### 投屏调研
- [乐播](https://cloud.lebo.cn/product/receive)
- [必捷](https://www.bijienetworks.com/sdk)
- [Android 手机投屏](https://github.com/Genymobile/scrcpy)
- [基于Scrcpy安卓投屏](https://github.com/barry-ran/QtScrcpy/blob/dev/README_zh.md)
- [都玩投屏-抖音、快手使用的方案](https://douwan.tv/index_ch.html)
- [OBS airplay](https://github.com/mika314/obs-airplay)
- [投屏调研](https://www.cnblogs.com/doudouyoutang/p/7892030.html)
- [usbmuxd通信机制](https://zyqhi.github.io/2019/08/20/usbmuxd-protocol.html)


### OBS 属性和设置的理解
- `obs_data_t` 用于设置一个源，和json配置相关
- `obs_properties_t` 源的配置项，用于UI展示设置，最终通过`obs_data_t`更新源

### OBS播放 MP4礼物特效
- 制作绿幕素材MP4特效文件，再使用`色度键`滤镜进行绿幕扣除
- 研究`图像蒙版/混合`滤镜，对MP4礼物文件进行特殊处理

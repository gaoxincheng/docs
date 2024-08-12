### 注意事项

- 最好参照[BranchesAndBuilding](https://bitbucket.org/chromiumembedded/cef/wiki/BranchesAndBuilding.md) 所列编译环境进行配置，所需`Windows SDK`要下载对应版本
- 使用`depot_tools`最新版本编译老的分支CEF(例如分支5414)，可能会报Python相关错误，简单错误网上基本可以搜索到解决方法;
- 不同分支的CEF最好使用不同路径的 `depot_tools`工具，防止版本不同造成`depot_tools`目录污染;
- CEF 104版本之后默认支持H265硬件解码，软件解码需修改文件，可禁用硬件加速测试软件解码是否可用
- 使用VS2022 可以正常编译CEF 109版本(最后一个支持win7的版本)
- CEF 109版本可以使用`--single-process`启动参数设置单进程运行，`CefSettings.cache_path`路径使用同一个目录也能正常运行，CEF 126版本测试下来却不行

### 编译环境

-	Win 10+ deployment, Win 10+ build system w/ VS2022 17.3.3, Win 10.0.22621 SDK
-   管理员权限执行`cmd`
-   源码版本信息 ：Chromium(126) + CEF(126) + Release Branch(6478)

### 下载源码

- 创建目录如下
```sh
	E:\code\cef_code\automate
	E:\code\cef_code\chromium_git
```
- 从官网文档中下载`depot_tools.zip`并解压到`E:\code\cef_code\depot_tools`,添加系统环境变量
- 运行 "update_depot_tools.bat" 安装 Python 、 Git.
```sh
	cd E:\code\cef_code\depot_tools
	update_depot_tools.bat
```
- 从官网文档中下载`automate-git.py`脚本到 `E:\code\cef_code\automate\automate-git.py`.
- 创建`E:\code\cef_code\chromium_git\BOTO_CONFIG.boto`并添加以下内容
```sh
	[Boto]
	proxy = 127.0.0.1
	proxy_port = 7890
```
- 创建 `E:\code\cef_code\chromium_git\update.bat` 脚本并添加以下内容.
```sh
	::设置代理地址
	set http_proxy=http://127.0.0.1:7890
	set https_proxy=http://127.0.0.1:7890
	::不要更新 DEPOT_TOOLS 工具
	set DEPOT_TOOLS_WIN_TOOLCHAIN=0
	set DEPOT_TOOLS_UPDATE=0
	set NO_AUTH_BOTO_CONFIG=E:\code\cef_code\chromium_git\BOTO_CONFIG.boto
	 ::编译环境使用 vs2022
	set GYP_MSVS_VERSION=2022
	set GYP_MSVS_OVERRIDE_PATH=C:\Program Files\Microsoft Visual Studio\2022\Community
	set GN_DEFINES=ffmpeg_branding=Chrome proprietary_codecs=true is_official_build=true chrome_pgo_phase=0 is_component_build=false use_thin_lto=false
	set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/*
	::`.git`目录占用空间比较大，是否可添加`--depth 1` 选项，暂未做尝试
	:: --chromium-url=https://github.com/chromium/chromium.git 指定从github上下载chromium镜像，未尝试;
	python3 ..\automate\automate-git.py --download-dir=E:\code\cef_code\chromium_git --depot-tools-dir=E:\code\cef_code\depot_tools --branch=6478 --no-distrib --no-build
```
- 配置git代理
```sh
	git config --global http.proxy http://127.0.0.1:7890
	git config --global https.proxy http://127.0.0.1:7890
```
- 执行`E:\code\cef_code\chromium_git\update.bat`脚本，耐心等待源码下载完成


### 编译

- 创建 `E:\code\cef_code\chromium_git\chromium\src\cef\create.bat` 脚本并添加以下内容.
```sh
	set CEF_USE_GN=1
	set GYP_GENERATORS=msvs-ninja,ninja
	::告诉depot_tools使用我们本机的VS进行编译
	set DEPOT_TOOLS_WIN_TOOLCHAIN=0
	set DEPOT_TOOLS_UPDATE=0
	set GYP_MSVS_VERSION=2022
	set GYP_MSVS_OVERRIDE_PATH=C:\Program Files\Microsoft Visual Studio\2022\Community
	set CEF_VCVARS=C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars32.bat
	set GN_DEFINES=ffmpeg_branding=Chrome proprietary_codecs=true is_official_build=true chrome_pgo_phase=0 is_component_build=false use_thin_lto=false
	set GN_ARGUMENTS=--ide=vs2022 --sln=cef --filters=//cef/*

	call cef_create_projects.bat
```

- 编译参数说明

```sh
	::ffmpeg_branding 和 proprietary_codecs 表示开启部分多媒体编解码支持(这段是开启H264的关键)
	::is_official_build=true创建release版本，is_component_build=true创建debug版本
	::use_sysroot=true 是否提供sysroot 映像，以实现跨 Linux 发行版的一致构建
	::symbol_level=1 将为堆栈跟踪生成足够的信息，但不会逐行调试。设置symbol_level=0将不包括调试符号
	::is_cfi=false 是否使用控制流完整性进行编译，以保护虚拟调用和强制转换。
	::use_thin_lto=false说是可以解决 LLVM ERROR: out of memory 的错误
	::use_vaapi=false 是否启用Intel的 vaapi 视频加速技术
	::chrome_pgo_phase=0 说是需要禁用PGO，可以解决部分报错...
```

- 执行`E:\code\cef_code\chromium_git\chromium\src\cef\create.bat`，等待执行完成

- 编译指定版本
```sh
	cd E:\code\cef_code\chromium_git\chromium\src
	ninja -C out/Release_GN_x86 cef
	ninja -C out/Debug_GN_x86 cef
	ninja -C out/Release_GN_x86_sandbox cef_sandbox
	ninja -C out/Debug_GN_x86_sandbox cef_sandbox
```

### 打包

- 执行以下命令进行打包
```sh
	cd E:\code\cef_code\chromium_git\chromium\src\cef\tools
	::-–ninja-build：使用ninja构建
	::-–no-docs：不创建文档
	::-–x64-build：64位
	::-不加--x64-build默认为x86版本
	make_distrib.bat --ninja-build --no-docs
```
### `libcef_dll_wrapper.lib` 编译

- 直接使用源码目录编译的`libcef_dll_wrapper.lib`会有链接问题
- 使用`chromium_git\chromium\src\cef\binary_distrib\cef_binary_109.1.18+gf1c41e4+chromium-109.0.5414.120_windows32`的CMake文件配置工程，重新编译生成`libcef_dll_wrapper.lib`

### 异常处理

- 打包失败，设置以下变量，64位修改为'vcvars64.bat'
```sh
	set CEF_VCVARS=C:\Program Files\Microsoft Visual Studio\2022\Community\VC\Auxiliary\Build\vcvars32.bat
```
- `sandbox` 不编译会打包失败，建议四个版本都编译完再打包

- chromium源码clone地址访问连接不稳定，导致失败后又得重新下载
```sh
	automate-git.py 增加：--chromium-url=https://github.com/chromium/chromium.git 指定从github上下载chromium镜像
```

### 参考链接

- [官方WIKI](https://bitbucket.org/chromiumembedded/cef/wiki/Home)

- [官网编译文档](https://bitbucket.org/chromiumembedded/cef/wiki/MasterBuildQuickStart.md)

- [官网构建好的库](https://cef-builds.spotifycdn.com/index.html)

- [Windows11环境下编译CEF添加H264支持](https://www.cnblogs.com/ysdyoOo/articles/17896451.html)

- [构建参数说明](https://www.chromium.org/developers/gn-build-configuration/)

- [CEF DEMO分析](https://cloud.tencent.com/developer/article/2344496)

- [CEF107版本编译](https://www.cnblogs.com/river12/p/17023144.html)

- [CEF109 H265软解支持修改](https://blog.csdn.net/CHNIM/article/details/137969065)

- [CEF 支持H264、H265](https://blog.csdn.net/qq_42144475/article/details/125017053)

- H265软解支持，需拷贝添加两个文件,libavcodec/hevcdec.c => libavcodec/autorename_libavcodec_hevcdec.c libavformat/hevc.c => libavformat/autorename_libavformat_hevc.c

### 测试

- 缓存目录删除是否正常

- 禁用硬件加速: --disable-gpu --disable-gpu-compositing

### CEF设置参数说明
```c++
	CefSettings //cef_settings_t
	{
		//这里只列出部分重要的参数，完整参数请查看源码
		int no_sandbox;                                 //设置1，不启动沙箱。沙箱可以保护用户不受不信任且可能存在恶意的Web内容。
		cef_string_t browser_subprocess_path;           //子进程可执行文件的路径（若为空则主进程会被启动），也可以使用命令行"browser-subprocess-path"代替
		int multi_threaded_message_loop;                //设置1，则启动browser进程消息循环在另一个线程，若为0，则必须调用CefDoMessageLoopWork去更新循环
		int windowless_rendering_enabled;               //设置1，则启动离屏渲染
		int command_line_args_disabled;                 //设置1，则禁止进程启动时的命令行参数
		cef_string_t cache_path;                        //全局浏览器缓存数据存储路径。若为空，则将以“隐名模式”创建，使用内存缓存，不会持久化到磁盘中。若不为空，则必须是root_cache_path或者其子目录
		cef_string_t root_cache_path;                   //缓存根目录
		cef_string_t user_data_path;                    //用户数据路径。Widevine CDM模块和拼写等用户数据所在的位置。若为空，则默认路径被使用：AppData\Local\CEF\User Data。
		int persist_session_cookies;                    //设置1，则持久化会话cookies（未过期的）。|cache_path|在此时必须要指定，也可以用使用命令行"persist-session-cookies"
		int persist_user_preferences;                   //设置1，则持久化用户参数为json文件。|cache_path|在此时必须要指定，也可以用使用命令行"persist-user-preferences"
		cef_string_t locale;                            //本地化字符。若为空，则使用默认“en-US”，也可以使用命令行"lang"
		cef_string_t log_file;                          //日志文件名。若为空，则默认为产生debug.log在主可执行文件目录，可使用命令行"log-file"
		cef_log_severity_t log_severity;                //日志等级。"verbose", "info","warning", "error", "fatal" or "disable"。 可使用命令行"log-severity"
		cef_string_t resources_dir_path;                //资源目录，若为空，则必须保持当前模块路径。可使用命令行"resources-dir-path"
		cef_string_t locales_dir_path;                  //本地化目录，若为空，则必须保持当前模块路径。可使用命令行"locales-dir-path"
		cef_color_t background_color;                   //背景颜色。在网页加载前或者未指定网页颜色时使用。
		cef_string_t accept_language_list;              //可接受的语言列表。逗号分隔不带空格，使用在"Accept-Language" HTTP header中。
	}
	CefBrowserSettins //_cef_browser_settings_t
	{
		//这里只列出部分重要的参数，完整参数请查看源码
		int windowless_frame_rate;                      //离屏渲染最大帧率，实际帧率可能比设置的小，范围（1-60）,默认30.可动作修改CefBrowserHost::SetWindowlessFrameRate
		cef_string_t default_encoding;                  //默认编码。为空则使用"ISO-8859-1"。可使用命令行"default-encoding"
		cef_state_t image_loading;                      //控制是否从网络加载图像URL
		cef_state_t image_shrink_standalone_to_fit;     //控制是否缩小独立的图像以适应页面，可使用命令行"image-shrink-standalone-to-fit"
		cef_state_t text_area_resize;                   //控制文字区域是否可以重定义大小，命令行"disable-text-area-resize"
		cef_state_t tab_to_links;                       //控制是否tab键可以移动链接焦点。命令行"disable-tab-to-links"
		cef_state_t local_storage;                      //控制是否开启本地存储。命令行"disable-local-storage"
		cef_state_t databases;                          //控制数据库是否开启。命令行"disable-databases"
		cef_state_t webgl;                              //控制webgl是否开启。命令行"disable-webgl"
		cef_color_t background_color;                   //背景颜色。同上面初始化设置中
		ef_string_t accept_language_list;               //同上面初始化设置中
	}

```
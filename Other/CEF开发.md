### 概念
```
	CEF（Chromium Embedded Framework）采用了多进程架构，主要包括浏览器进程（Browser Process）和渲染进程（Render Process）。
	这种架构类似于 Chrome 浏览器，通过将不同的功能分配到不同的进程中，提高了应用程序的稳定性、安全性和性能。
	浏览器进程是整个应用程序的核心进程，它负责管理窗口、标签页、导航、网络请求等功能。它还负责创建和管理渲染进程，并与操作系统和其他系统组件进行交互。
	渲染进程则专注于网页内容的渲染，包括 HTML 解析、CSS 样式应用、JavaScript 执行以及将这些内容转换为可视化的图像显示在屏幕上。
```

- CefApp

```
	CefApp是 CEF（Chromium Embedded Framework）应用程序的基础接口类。
	它代表整个 CEF 应用程序，用于处理应用程序级别的初始化和清理等任务。在 CEF 应用启动时，会创建一个CefApp的实例，这个实例在整个应用的生命周期中起到全局管理的作用。
	每个进程都对应一个CefApp实例，不同进程使用不同功能;
```

- CefClient

```
	CefClient是与浏览器实例（CefBrowser）紧密相关的接口类，用于处理浏览器相关的各种事件和操作。每个CefBrowser实例通常都有一个对应的CefClient实例，用于在浏览器的生命周期内提供各种回调和处理功能。
```

- 浏览进程

```
	CefBrowserHost::CreateBrowserSync 调用进程
```

- 渲染进程

```
	渲染进程是 JavaScript 代码的主要执行环境
```

- CefBrowser

```
	CefBrowser是 CEF（Chromium Embedded Framework）中用于管理浏览器实例的类，它处于浏览器进程和渲染进程交互的核心位置。
	浏览器进程主要负责创建、管理和协调CefBrowser实例的生命周期，包括窗口管理、导航控制等功能；
	而渲染进程则负责为CefBrowser加载和渲染具体的网页内容。
```

- CefV8Context
	- 定义与作用：
```
	在 CEF（Chromium Embedded Framework）中，CefV8Context是一个关键的类，用于表示 V8 引擎的执行上下文。简单来说，它定义了一个 JavaScript 代码执行的环境。这个环境包括了当前可访问的变量、函数、对象以及它们的作用域等信息。例如，在一个网页中，每个 JavaScript 函数都有自己的执行上下文，当函数被调用时，会在对应的CefV8Context中执行。
```
	- 与 JavaScript 执行环境的关系：
```
	CefV8Context紧密关联着 JavaScript 的执行环境。它类似于一个容器，里面存放着 JavaScript 代码执行时所需的所有信息。在浏览器中，不同的页面或者页面中的不同框架（如iframe）可能会有不同的CefV8Context。当在 C++ 代码中想要与 JavaScript 进行交互（如执行 JavaScript 代码、访问 JavaScript 对象等）时，通常需要先获取正确的CefV8Context。
```

- 进程间通信（IPC）机制

```
	CefProcessMessage：
	这是一种常用的进程间消息传递方式。在浏览器进程和渲染进程中，可以通过创建和发送CefProcessMessage来传递信息。例如，渲染进程中的 JavaScript 可以通过CefProcessMessage向浏览器进程发送请求，如请求打开一个新的窗口或者获取某些应用程序级别的数据。
	消息可以包含各种类型的数据，如字符串、整数、布尔值以及更复杂的结构化数据（通过CefListValue）。接收方通过实现CefClient::OnProcessMessageReceived函数来处理接收到的消息。
	V8 上下文绑定：
	通过在浏览器进程和渲染进程之间绑定 V8 上下文，可以实现更复杂的交互。例如，在浏览器进程中，可以将一个 C++ 对象或者函数通过 V8 绑定的方式暴露给渲染进程中的 JavaScript。这样，JavaScript 就可以直接调用这些 C++ 功能，实现更自然的跨进程交互。这种方式常用于实现一些高级的应用程序功能，如将本地系统功能（如文件系统访问、硬件设备控制等）集成到网页应用中

```

### CefSettings.browser_subprocess_path
- 进程分离功能：
```
	browser_subprocess_path是 CEF（Chromium Embedded Framework）中的一个重要配置选项。
	它用于指定浏览器子进程可执行文件的路径。在 CEF 的多进程架构中，浏览器主进程会创建子进程来处理一些特定的任务，如渲染网页、处理插件等。
	通过设置browser_subprocess_path，可以将这些子进程的执行文件路径明确指定，实现进程分离。
```

- 资源隔离与安全优势：
```
	这种进程分离带来了资源隔离和安全方面的好处。
	例如，当一个子进程因为插件崩溃或者遇到恶意脚本攻击而出现问题时，由于与主进程和其他子进程相对隔离，不会轻易导致整个浏览器应用程序崩溃。
	同时，在安全敏感的环境中，通过控制子进程的可执行文件路径，可以更好地进行权限管理和安全审计
```

- 性能优化场景：
```
	如果主应用程序可执行文件较大且加载时间长，采用分离的子进程可执行文件能显著提高子进程的启动速度，减少整体启动时间，提升用户体验。比如一些大型的企业级应用，集成了 CEF 浏览器功能，使用分离的子进程可执行文件，可以让浏览器相关的功能更快地响应用户操作.
```
- 安全与定制化场景：
```
	在对安全性要求较高的环境中，将子进程可执行文件放在特定的安全目录下，并通过配置browser_subprocess_path指定该路径，可以确保子进程的启动来源可靠。
	同时，若需要对子进程进行特殊的定制，如添加特定的安全模块或修改默认的启动行为，也可以通过这种方式实现.
```

### 其他

- wmic process where processid=37552 get commandline

- YY 进程参数
	```
	"F:\Program Files\yy\9.38.0.1\yyexternal.exe" --type=renderer --log-severity=warning --user-agent="Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/109.0.5414.120 Safari/537.36 YYCefChannel/131345 UID/2615093586" --uncaught-exception-stack-size=10 --user-data-dir="C:\Users\Administrator\AppData\Local\CEF\User Data" --no-sandbox --autoplay-policy=no-user-gesture-required --disable-background-timer-throttling --enable-experimental-web-platform-features --log-file="C:\Users\Administrator\AppData\Roaming\duowan\yy\cache\.cache2/2569901389/orgin/yycef.log" --remote-debugging-port=34724 --lang=zh-CN --device-scale-factor=1 --num-raster-threads=4 --enable-main-frame-before-activation --renderer-client-id=8 --time-ticks-at-unix-epoch=-1735180272350376 --launch-time-ticks=113344510456 --mojo-platform-channel-handle=8568 --field-trial-handle=6396,i,17668095637246602956,8320293609495337073,131072 --enable-features=BlockInsecurePrivateNetworkRequests,BlockInsecurePrivateNetworkRequestsForNavigations,BlockInsecurePrivateNetworkRequestsFromPrivate,BlockInsecurePrivateNetworkRequestsFromUnknown,ClientHintThirdPartyDelegation,ClientHintsMetaEquivDelegateCH,ClientHintsMetaHTTPEquivAcceptCH,ClipboardCustomFormats,CookieSameSiteConsidersRedirectChain,CriticalClientHint,DocumentPolicyNegotiation,DocumentReporting,EditContext,EnableCanvas2DLayers,ExperimentalContentSecurityPolicyFeatures,OriginIsolationHeader,PendingBeaconAPI,PrefersColorSchemeClientHintHeader,PrefersReducedMotionClientHintHeader,PrivateNetworkAccessForWorkers,PrivateNetworkAccessRespectPreflightResults,SchemefulSameSite,UserAgentClientHint --disable-features=AllowAggressiveThrottlingWithWebSocket,IntensiveWakeUpThrottling --cefpid=16416 -myimid 2569901389 -extl "C:\Users\Administrator\AppData\Roaming\duowan\yy\yycomstore\2052\com.yy.cefdev2\131345\yycefchannel.dll" -component "" --cefchannel_node=false --yy-disable-font-proxy -myuid 2615093586 -browserpid 16416 --js-flags=--no-lazy,--no-lazy-eval,--no-lazy-streaming,--no-flush-bytecode " watchdog=16416-860902 " /prefetch:1
	```
	
- cocos2d页面打开失败
	- 错误日志

	```
		[1231/093726.538:ERROR:gpu_process_host.cc(991)] GPU process exited unexpectedly: exit_code=-1073741819
		[1231/093726.538:WARNING:gpu_process_host.cc(1338)] The GPU process has crashed 3 time(s)
		[1231/093726.539:ERROR:command_buffer_proxy_impl.cc(128)] ContextResult::kTransientFailure: Failed to send GpuControl.CreateCommandBuffer.
		[1231/093726.539:ERROR:command_buffer_proxy_impl.cc(325)] GPU state invalid after WaitForGetOffsetInRange.
		[1231/093726.631:WARNING:gpu_process_host.cc(1014)] Reinitialized the GPU process after a crash. The reported initialization time was 72 ms
		....
		[1230/132510.390:INFO:CONSOLE(1)] "This device does not support webgl", source: http://test.puzzle.ispeak.cn/ship/cocos2d-js-min.1d5de.js (1)
		[1230/132510.390:INFO:CONSOLE(1)] "TypeError: Cannot read properties of null (reading 'getExtension')", source: http://test.puzzle.ispeak.cn/ship/cocos2d-js-min.1d5de.js (1)
	```
	- 可能原因一：没有配置'manifest'文件，参考链接: [https://blog.csdn.net/qq_33437675/article/details/138568988](https://blog.csdn.net/qq_33437675/article/details/138568988)
	- 可能原因二：‘http command’命令导致的页面取消
	- 可能原因三：线程调度
	- NOTE:: 观察发现是原因一导致
	
- **部分电脑启动崩溃（GPU进程启动失败）**
	- 系统、硬件信息收集
	- 跑一份日志
	- 显卡驱动更新
	- CEF 测试程序能否运行（APPDATA 目录测试，确定是否目录权限有关）
	- OBS 能否打开浏览器源
	- VC 运行库添加尝试，参照YY目录的依赖库
	- 代码判断是否有显卡可用，没有则使用旧内核
	- !!! 排查发现相关exe被修改，文件大小不一样，病毒篡改导致 ！！！！
	- 添加对'is_navigate_browser.exe' MD5校验检查，’cef_browser.dll‘暂不校验，因为该dll被修改会导致动态加载失败，不会造成崩溃
	-[GPU进程强制被杀多次、系统重启等场景会导致崩溃](https://magpcss.org/ceforum/viewtopic.php?t=19335)
	
### MD5 计算

```sh
	certutil.exe -hashfile .\cef_base.zip MD5
```
# 参考链接
- [Windows下编译WebRTC](https://segmentfault.com/a/1190000041104283)
- [fdk-aac github](https://github.com/mstorsjo/fdk-aac)
- [WebRTC音频引擎实现分析](https://www.jianshu.com/p/5a8a91cd84ef)

gn gen out-x86\Debug-vs2017 --ide=vs2017 --args="is_debug=true target_os=\"win\" target_cpu=\"x86\" is_component_build=false is_clang=false use_lld=false treat_warnings_as_errors=false use_rtti=true rtc_include_tests=false enable_libaom=false"

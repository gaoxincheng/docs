## 参考链接
- [WebRTC 编译指南](https://mthli.xyz/webrtc-compilation/)
- [官方文档](https://webrtc.github.io/webrtc-org/native-code/android/)

## 参考编译参数

```sh
gn gen out64/release --args='target_os="android" target_cpu="arm64" is_debug=false rtc_enable_protobuf=false use_rtti=true use_custom_libcxx=false'
```

## 编译问题
- 之前编译静态库时，没有加`use_custom_libcxx = false`选项，导致上层链接`libwebrtc.a`时，会报链接`c++`库错误。`webrtc` 编译默认会使用源码自带的`libc++(LLVM)`,使用`use_custom_libcxx = false`选项后，会优先使用系统的`c++`库。
  
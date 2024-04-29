### Qt6 编译
- 安装OpenSSL 1.1.1u，cmake会自动查询系统安装的OpenSSL
- 启动vs2022 命令行工具
- 启动Perl运行环境 'state activate --default gaoxincheng/Perl-5.36.0-Windows'
- 创建构建目录'build'，安装目录‘Qt6.4.3’
- 编译release pdb则使用"-release -force-debug-info"选项
```sh
  cd build
  ..\qt-everywhere-src-6.4.3\configure -opensource -confirm-license -debug-and-release -ssl -openssl -openssl-runtime -qt-doubleconversion -qt-pcre -qt-zlib -qt-libjpeg -qt-libpng -qt-libmd4c -qt-tiff -qt-webp -prefix E:\code\Qt\Qt6.4.3
  cmake --build . --parallel
  cmake --install . # 默认只安装release，可使用命令安装debug，‘cmake --install . --config Debug’
```
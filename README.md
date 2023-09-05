# nginx-http-flv-win64
nginx-http-flv-module Windows builds. 

下载地址：https://github.com/aushy/nginx-http-flv-win32/releases

nginx源码：http://hg.nginx.org/nginx

/nginx/objs/lib 下放置以下项目：
* openssl：https://www.openssl.org/source/ ，openssl-1.1.1v 编译时出错，openssl-1.1.1u可以。
* pcre：https://www.pcre.org/ ，可用PCRE2
* zlib：http://www.zlib.net/
* nginx-http-flv-module：https://github.com/winshining/nginx-http-flv-module


使用方法可参考 
* nginx http://nginx.org/
* nginx-rtmp-module https://github.com/arut/nginx-rtmp-module

# 软件准备 #
1.安装 Visual Studio 2022

包含单个组件：
* .NET Framework 4.6.1 目标包
* .NET Framework 4.6.1 SDK
* Windows 通用 C 运行时
* Windows 通用 CRT SDK
* MSVC v142 - VS 2019 C++ x64/x86 生成工具(v14.26)
* 对 v142 生成工具(14.21)的 C++/CLI 支持
* Clang compile for Windows
* Windows 10 SDK (10.0.16299.0)

2.安装 msys2

3.安装 ActivePerl

4.安装 NASM

# 编译准备 #
1.下载 [Nginx](http://hg.nginx.org/nginx/) 、[openssl](https://www.openssl.org/source/)、[pcre](https://ftp.pcre.org/pub/pcre/)、[zlib](http://www.zlib.net/)、[nginx-http-flv-module](https://github.com/winshining/nginx-http-flv-module) 的源码。

2.解压 Nginx 源码到 nginx 文件夹；建立 nginx/objs/lib/ 文件夹，将其他源码解压在此处。

3.修改 /nginx/auto/cc/msvc 文件

因为 MSVC 2022 对应 19.32，在
`echo " + cl version: $NGX_MSVC_VER"`
上一行加入一句
`NGX_MSVC_VER=19.32`

4.64位编译修改（32位编译忽略）

打开.\auto\lib\openssl\makefile.msvc文件。
找到`perl Configure $(OPENSSL_TARGET) no-shared no-threads`替换为`perl Configure VC-WIN64A no-shared`；
`if exist ms\do_ms.bat`替换为`if exist ms\do_win64a.bat`；
`ms\do_ms`替换为`ms\do_win64a`。

# 编译 #
1.在 nginx 文件夹打开命令行，执行
`"C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Auxiliary/Build/vcvarsall.bat" x64`
命令，然后执行
`"D:/exe/msys64/msys2_shell.cmd" -mingw64 -use-full-path`
命令，在打开的新窗口内执行
`PATH=/c/Users/NINGMEI/AppData/Local/bin/NASM:/c/Perl64/bin:"/c/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.32.31326/bin/Hostx64/x64":$PATH`命令。

2.执行
`cd /d/VisualStudio/code/nginx/`切换到 nginx 文件夹；执行
```
./auto/configure \
--with-cc=cl --builddir=objs.msvc8 --with-debug --prefix= \
--conf-path=conf/nginx.conf --pid-path=logs/nginx.pid \
--http-log-path=logs/access.log --error-log-path=logs/error.log \
--sbin-path=nginx.exe --http-client-body-temp-path=temp/client_body_temp \
--http-proxy-temp-path=temp/proxy_temp \
--http-fastcgi-temp-path=temp/fastcgi_temp \
--http-scgi-temp-path=temp/scgi_temp \
--http-uwsgi-temp-path=temp/uwsgi_temp \
--with-cc-opt=-DFD_SETSIZE=1024 \
--with-http_v2_module --with-http_realip_module \
--with-http_addition_module --with-http_sub_module \
--with-http_dav_module --with-http_stub_status_module \
--with-http_flv_module --with-http_mp4_module \
--with-http_gunzip_module --with-http_gzip_static_module \
--with-http_auth_request_module --with-http_random_index_module \
--with-http_secure_link_module --with-http_slice_module --with-mail \
--with-stream --with-stream_realip_module --with-stream_ssl_preread_module \
--with-openssl-opt='no-asm no-tests -D_WIN32_WINNT=0x0501' \
--with-http_ssl_module --with-mail_ssl_module --with-stream_ssl_module \
--with-pcre=objs.msvc8/lib/pcre2-10.42 \
--with-zlib=objs.msvc8/lib/zlib-1.3 \
--with-openssl=objs.msvc8/lib/openssl-3.0.10 \
--add-module=objs.msvc8/lib/nginx-http-flv-module
```
进行编译前的配置；执行
`nmake -f Makefile`
开始编译。

3.编译结束后，会在 nginx/objs/ 文件夹内出现 nginx.exe

# nginx-http-flv-win32
nginx-http-flv-module Windows builds. 

下载地址：https://github.com/aushy/nginx-http-flv-win32/releases

nginx源码：http://hg.nginx.org/nginx

/nginx/objs/lib 下放置以下项目：
* openssl：https://www.openssl.org/source/
* pcre：https://ftp.pcre.org/pub/pcre/
* zlib：http://www.zlib.net/
* nginx-http-flv-module：https://github.com/winshining/nginx-http-flv-module


使用方法可参考 
* nginx http://nginx.org/
* nginx-rtmp-module https://github.com/arut/nginx-rtmp-module

# 软件准备 #
1.安装 Visual Studio 2019

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

# 编译准备 #
1.下载 [Nginx](http://hg.nginx.org/nginx/) 、[openssl](https://www.openssl.org/source/)、[pcre](https://ftp.pcre.org/pub/pcre/)、[zlib](http://www.zlib.net/)、[nginx-http-flv-module](https://github.com/winshining/nginx-http-flv-module) 的源码。

2.解压 Nginx 源码到 nginx 文件夹；建立 nginx/objs/lib/ 文件夹，将其他源码解压在此处。

3.修改 /nginx/auto/cc/msvc 文件

因为 MSVC 2019 对应 19.26，在
`echo " + cl version: $NGX_MSVC_VER"`
上一行加入一句
`NGX_MSVC_VER=19.26`

4.64位编译修改（32位编译忽略）

打开.\auto\lib\openssl\makefile.msvc文件。
找到`VC-WIN32`替换为`VC-WIN64A`；
`if exist ms\do_ms.bat`替换为`if exist ms\do_win64a.bat`；
`ms\do_ms`替换为`ms\do_win64a`。

# 编译 #
1.在 nginx 文件夹打开命令行，执行
`"D:/VisualStudio/2019/Community/VC/Auxiliary/Build/vcvarsall.bat" x64`
命令，然后执行
`"D:/VisualStudio/MSYS2/msys2_shell.cmd" -mingw64 -use-full-path`
命令，在打开的新窗口内执行
`PATH=/c/Perl64/bin:/d/VisualStudio/2019/Community/VC/Tools/MSVC/14.26.28801/bin/Hostx64/x64:$PATH`命令。

2.执行
`cd /d/VisualStudio/code/nginx/`切换到 nginx 文件夹；执行
```
./auto/configure \
--with-cc=cl --builddir=objs --prefix= \
--conf-path=conf/nginx.conf --pid-path=logs/nginx.pid \
--http-log-path=logs/access.log --error-log-path=logs/error.log \
--sbin-path=nginx.exe --http-client-body-temp-path=temp/client_body_temp \
--http-proxy-temp-path=temp/proxy_temp \
--http-fastcgi-temp-path=temp/fastcgi_temp \
--http-scgi-temp-path=temp/scgi_temp \
--http-uwsgi-temp-path=temp/uwsgi_temp \
--with-cc-opt=-DFD_SETSIZE=1024 \
--with-select_module --with-http_ssl_module \
--with-pcre=objs/lib/pcre-8.44 \
--with-zlib=objs/lib/zlib-1.2.11 \
--with-openssl=objs/lib/openssl-1.1.1g \
--add-module=objs/lib/nginx-http-flv-module
```
进行编译前的配置；执行
`nmake -f Makefile`
开始编译。

3.编译结束后，会在 nginx/objs/ 文件夹内出现 nginx.exe

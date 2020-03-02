## 搭建Pelican博客系统

### 1.介绍

Pelican是一套开源的使用Python编写的博客静态生成, 可以添加文章和和创建页面, 可以使用MarkDown reStructuredText 和 AsiiDoc 的格式来抒写, 同时使用 Disqus评论系统, 支持 RSS和Atom输出, 插件, 主题, 代码高亮等功能, 采用Jajin2模板引擎, 可以很容易的更改模板。

### 2.安装前准备

- Mingw：生成静态页面时需要使用到Make， Windows环境需要安装Mingw创建包含GCC和Make的环境
- Python3：Pelican是Python平台的静态页面生成器，需要安装Python3
- Pelican：
- Markdown：Pelican支持Markdown

### 3.安装

#### Mingw安装

https://www.cnblogs.com/herelsp/p/8679200.html

https://www.cnblogs.com/lidabo/p/8990348.html

找到mingw安装目录下mingw32-make.exe拷贝一份并重命名为make.exe

mingw32-base

mingw32-gcc

mingw32-gcc-g++

mingw32-libgcc

mingw32-make

执行`gcc -v` 和`make -v`回显如下，安装成功

```
C:\Users\zora>gcc -v
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=d:/software/mingw/bin/../libexec/gcc/mingw32/6.3.0/lto-wrapp
er.exe
Target: mingw32
Configured with: ../src/gcc-6.3.0/configure --build=x86_64-pc-linux-gnu --host=m
ingw32 --target=mingw32 --with-gmp=/mingw --with-mpfr --with-mpc=/mingw --with-i
sl=/mingw --prefix=/mingw --disable-win32-registry --with-arch=i586 --with-tune=
generic --enable-languages=c,c++,objc,obj-c++,fortran,ada --with-pkgversion='Min
GW.org GCC-6.3.0-1' --enable-static --enable-shared --enable-threads --with-dwar
f2 --disable-sjlj-exceptions --enable-version-specific-runtime-libs --with-libic
onv-prefix=/mingw --with-libintl-prefix=/mingw --enable-libstdcxx-debug --enable
-libgomp --disable-libvtv --enable-nls
Thread model: win32
gcc version 6.3.0 (MinGW.org GCC-6.3.0-1)

C:\Users\zora>make -v
GNU Make 3.81
Copyright (C) 2006  Free Software Foundation, Inc.
This is free software; see the source for copying conditions.
There is NO warranty; not even for MERCHANTABILITY or FITNESS FOR A
PARTICULAR PURPOSE.

This program built for i386-pc-mingw32
```

#### Python3安装

#### 配置Virtualenv

#### Pelican安装

#### Markdown安装

### 4. 开始创建博客系统Step by step

#### quickstart

#### 写一篇文章

#### 生成静态页面

#### 本地预览
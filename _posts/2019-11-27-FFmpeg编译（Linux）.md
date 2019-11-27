# FFmpeg编译（Linux）

FFmpeg作为开源视频处理的集大成者，对于各个平台都是兼容的，由于最近需要在Ubuntu下开发推流程序，而商用的库根本玩不起，所以学习FFmpeg，第一步，安装编译（

[参考文章]: https://blog.csdn.net/xIsidro9293/article/details/53097949

：）。

## 一、下载

获取FFmpeg源码，可以到官网上直接下载，也可以使用Git命令从Git仓库下载。
下载：
       地址： http://ffmpeg.org/download.html   下载最新版本
       解压缩：tar -zxvf ffmp..

克隆：

​      在使用克隆之前你应该做到如何使用Git工具，起码需要知道怎么安装它，可以使用下面命令。

    $ sudo apt install git
    $ sudo git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
注：Git下载后在你 /home/xianyu 目录下的ffmpeg文件夹中。

## 二、编译

##### 1、配置  configure 

使用cd命令进入ffmpeg文件夹中，对configure文件进行配置。

```sh
$ cd ffmpeg
$ ./configure --enable-shared --disable-yasm --prefix=/usr/local/ffmpeg
```

对其中参数进行解释为：

--enable-shared     >>  编译共享库
--disable-yasm       >> 不使用yasm ，默认使用yasm配置会很麻烦,需要下载yasm

【该命令可以考虑不使用，使用apt安装yasm相对来说比较方便，对应代码为：】

```sh
$ sudo apt install yasm
```

--prefix=/usr/local/ffmpeg     >> 编译完成后输出我们要用的文件路径

这种为最简化的配置方式，编译出来的库也最大，推荐新手使用，可以确保你在使用过程中不会莫名其妙缺失某个库。

当然，你也可以参照ffmpeg功能清单去掉不必要的功能减少库的体积，定制自己的ffmpeg，这样可以减少内存的占用。

##### 2、编译

```sh
$ make
```

##### 3、编译安装

```sh
$ make install
```

值得注意的是，在编译和安装的过程中可能会出现源文件编译出错的情况，忽略它们，你将会看到你的ffmpeg仍可以正常使用。

你在安装完成后能在/usr/local/ffmpeg/路径下找到 include里的头文件 和 lib里的.so文件  

## 三、使用FFMPEG

 上面我们编译完了FFMPEG之后可以去运行以下bin目录下生成的可执行文件 

```sh
~***/bin$ ./ffmpeg 
./ffmpeg: error while loading shared libraries: libavdevice.so.57: cannot open shared object file: No such file or directory
```

 发现系统提示找不到动态库，可以用来查看运行当前可执行文件需要哪些动态库 

```sh
$ ldd ffmpeg
```

```sh
~***/bin$ ldd ffmpeg 
    linux-vdso.so.1 =>  (0x00007fffcfeaf000)
    libavdevice.so.57 => not found
    libavfilter.so.6 => not found
    libavformat.so.57 => not found
    libavcodec.so.57 => not found
    libswresample.so.2 => not found
    libswscale.so.4 => not found
    libavutil.so.55 => not found
    libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f41d6d9e000)
    libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f41d6b7f000)
    libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f41d67b9000)
    /lib64/ld-linux-x86-64.so.2 (0x00007f41d70c7000)
```

应该有很多人和我一样，不想编译FFMPEG之后还要动自己系统的环境，这时有一个简单的方法可以解决这个问题。就是在当前终端export一个环境变量。

```sh
~/usr/local/ffmpeg/bin$ export LD_LIBRARY_PATH=../lib/
~/usr/local/ffmpeg/bin$ ldd ffmpeg 
linux-vdso.so.1 =>  (0x00007ffeeb6fc000)
	libavdevice.so.58 => ../lib/libavdevice.so.58 (0x00007f4cf7d7f000)
	libavfilter.so.7 => ../lib/libavfilter.so.7 (0x00007f4cf789a000)
	libavformat.so.58 => ../lib/libavformat.so.58 (0x00007f4cf7437000)
	libavcodec.so.58 => ../lib/libavcodec.so.58 (0x00007f4cf5ec2000)
	libswresample.so.3 => ../lib/libswresample.so.3 (0x00007f4cf5cac000)
	libswscale.so.5 => ../lib/libswscale.so.5 (0x00007f4cf5a2b000)
	libavutil.so.56 => ../lib/libavutil.so.56 (0x00007f4cf560d000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f4cf5304000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f4cf50e7000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f4cf4d1d000)
	libxcb.so.1 => /usr/lib/x86_64-linux-gnu/libxcb.so.1 (0x00007f4cf4afb000)
	libxcb-shape.so.0 => /usr/lib/x86_64-linux-gnu/libxcb-shape.so.0 (0x00007f4cf48f7000)
	libxcb-xfixes.so.0 => /usr/lib/x86_64-linux-gnu/libxcb-xfixes.so.0 (0x00007f4cf46ef000)
	libz.so.1 => /lib/x86_64-linux-gnu/libz.so.1 (0x00007f4cf44d5000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f4cf7f8f000)
	libXau.so.6 => /usr/lib/x86_64-linux-gnu/libXau.so.6 (0x00007f4cf42d1000)
	libXdmcp.so.6 => /usr/lib/x86_64-linux-gnu/libXdmcp.so.6 (0x00007f4cf40cb000)

```

当然，应该注意的是，这种添加环境变量的方法只针对于当前终端，终端关闭后或者另一个终端都是无效的。
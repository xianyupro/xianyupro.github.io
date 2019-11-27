# FFmpeg编译（Linux）

FFmpeg作为开源视频处理的集大成者，对于各个平台都是兼容的，由于最近需要在Ubuntu下开发推流程序，而商用的库根本玩不起，所以学习FFmpeg，第一步，安装编译（

[参考文章]: https://blog.csdn.net/xIsidro9293/article/details/53097949

：）。

## 一、下载

获取FFmpeg源码，可以到官网上直接下载，也可以使用Git命令从Git仓库下载。
**下载：**
       地址： http://ffmpeg.org/download.html   下载最新版本
       解压缩：tar -zxvf ffmp..

**克隆：**

​      在使用克隆之前你应该做到如何使用Git工具，起码需要知道怎么安装它，可以使用下面命令。

```sh
$ sudo apt install git
$ sudo git clone https://git.ffmpeg.org/ffmpeg.git ffmpeg
```
注：Git下载后在你 /home/xianyu 目录下的ffmpeg文件夹中。

## 二、编译

##### 1、配置  configure 

使用cd命令进入ffmpeg文件夹中，对configure文件进行配置。

```sh
$ cd ffmpeg
$ ./configure --enable-shared --disable-yasm --prefix=/usr/local/ffmpeg
```

对其中参数进行解释为：

--enable-shared                          编译共享库
--disable-yasm                            不使用yasm ，默认使用yasm配置会很麻烦,需要下载yasm

该命令可以考虑不使用，使用apt安装yasm相对来说比较方便，对应代码为：

```sh
$ sudo apt install yasm
```

--prefix=/usr/local/ffmpeg        编译完成后输出我们要用的文件路径

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

## 三、检查FFmpeg完整性

出于未知原因，使用上述方法安装的FFmpeg包并不完整，在/usr/local/ffmpeg/bin目录下缺失ffplay的文件。如果需要ffplay文件，还需要执行一下操作。**（不需要请忽略这一步）**

#### 1、编译SDL2

Simple DirectMedia Layer Version 2（简称SDL2）是一个跨平台的库，旨在简化编写多媒体软件（例如游戏和模拟器）的过程。已知此软件包可使用LFS-9.0平台构建并正常工作。

**1）** 安装 libasound2-dev 

```sh
$ sudo apt-get install libasound2-dev
```

**2）**下载并安装SDL2

```sh
$ wget http://www.libsdl.org/release/SDL2-2.0.5.zip
$ unzip SDL2-2.0.5.zip
$ cd SDL2-2.0.5/
$ ./configure --prefix=/usr/local/
$ make
$ sudo make install
```

#### 2、重新编译FFmpeg

```sh
$ cd /home/xianyu/ffmpeg
$ ./configure --prefix=host --enable-shared --disable-static --disable-doc --enable-ffplay
$ make
$ make install
```

 这样就会在/home/xianyu/ffmpeg/host/bin目录下生成ffplay了 

## 四、使用FFMPEG

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

应该注意的是，这种添加环境变量的方法只针对于当前终端，终端关闭后或者另一个终端都是无效的。

经常需要使用的话可以将FFmpeg加入环境变量：

```sh
$ sudo vi /home/xianyu/.bash.profile
```

 加入以下内容: 

```sh
export PATH=/usr/local/ffmpeg/bin
```

运行生效：

```sh
$ source /home/xianyu/.bash.profile
```

当然，你也可以通过更改系统配置设置永久生效的环境变量。这里整理一下Linux环境变量相关知识。

### Linux环境变量

#### 1、Linux的变量种类

 按变量的生存周期来划分，Linux变量可分为两类： 
**1）**永久的：需要修改配置文件，变量永久生效。 
**2）**临时的：使用export命令声明即可，变量在关闭shell时失效。 

上述添加的临时环境变量就是第二种。

#### 2、设置变量的三种方法

**1）** 在/etc/profile文件中添加变量【对所有用户生效(永久的)】 
用VI在文件/etc/profile文件中增加变量，该变量将会对Linux下所有用户有效，并且是“永久的”。 
例如：编辑/etc/profile文件，添加LD_LIBRARY_PATH变量 

```sh
$ sudo vi /etc/profile 
```

在profile文件的最后添加下列代码，并保存退出

```sh
export LD_LIBRARY_PATH=/usr/local/ffmpeg/lib
```

如果添加多个，中间分号隔开，如：

```sh
export LD_LIBRARY_PATH=/usr/local/ffmpeg/lib;/usr/local/ffmpeg/include
```

修改文件后运行下句使修改生效。

```sh
$ source /etc/profile
```

**2）** 在用户目录下的.bash.profile文件中增加变量【对单一用户生效(永久的)】 
用VI在用户目录下的.bash.profile文件中增加变量，改变量仅会对当前用户有效，并且是“永久的”。 
例如：编辑xianyu用户目录(/home/xianyu)下的.bash.profile 

```sh
$ sudo vi /home/xianyu/.bash.profile
```

在profile文件的最后添加下列代码，并保存退出

```
export LD_LIBRARY_PATH=/usr/local/ffmpeg/lib
```

修改文件后运行下句使修改生效。

```sh
$ source /home/xianyu/.bash.profile
```

**3）** 直接运行export命令定义变量【**只对当前shell(BASH)有效（临时的）**】 
在shell的命令行下直接使用：

```sh
//[export 变量名=变量值] 定义变量
$ export LD_LIBRARY_PATH=/usr/local/ffmpeg/lib
```

该变量只在当前的shell(BASH)或其子shell(BASH)下是有效的，shell关闭了，变量也就失效了，再打开新shell时就没有这个变量，需要使用的话还需要重新定义。

#### 3、环境变量的查看

**1）** 使用echo命令查看单个环境变量。例如： 

```sh
$ echo $PATH 
```

**2）** 使用env查看所有环境变量。例如： 

```sh
$ env 
```

**3）** 使用set查看所有本地定义的环境变量。 

#### 4、使用unset删除指定的环境变量

set可以设置某个环境变量的值。清除环境变量的值用unset命令。如果未指定值，则该变量值将被设为NULL。示例如下： 

```sh
export TEST="Test..." #增加一个环境变量TESTexport TEST="Test..." #增加一个环境变量TEST env|grep TEST #此命令有输入，证明环境变量TEST已经存在了 
TEST=Test... 
unset TEST #删除环境变量TEST 
$ env|grep TEST #此命令没有输出，证明环境变量TEST已经删除
```

#### 5、常用的环境变量

PATH                           决定了shell将到哪些目录中寻找命令或程序 
HOME                         当前用户主目录 
HISTSIZE                 　历史记录数 
LOGNAME                  当前用户的登录名 
HOSTNAME            　指主机的名称 
SHELL                          当前用户Shell类型 
LANGUGE 　              语言相关的环境变量，多语言可以修改此环境变量 
MAIL                        　当前用户的邮件存放目录 
PS1　                          基本提示符，对于root用户是#，对于普通用户是$
LD_LIBRARY_PATH    主要用于指定查找共享库（动态链接库）时除了默认路径之外的其他路径。 
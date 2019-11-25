# JETSON TX2 配置QT开发环境

​        重新对TX2的板子进行刷机后，下载QT包，发现无论是64位还是32位的软件安装包都没能正常安装，在测试多个版本多个系统无果后，找到官网给出的安装教程。

## 一、安装QT开发界面

从官方给出的镜像源中找到并安装对应的QT安装包，命令函执行：

```
$ sudo apt-get install qt5-default qtcreator -y 
```

官方镜像源的速度倒也还不错，大概一分钟，软件安装好，你可以打开菜单来搜索QT看到软件。

![](\img\1.png)

## 二、配置QT编译环境

按照官方给出的解释，QT无法配置正确的处理器体系结构，必须进行手动配置，手动配置的流程为：

```
Qt Creator -> Tools-> Options-> Build & Run-> Compilers 
```

##### 1、点击Add，选择GCC编译器

![](\img\2.png)

##### 2、修改默认路径

![](\img\3.png)

选择默认编译路径，在路径处填写 /usr/bin/gcc

##### 3、修改ABI

修改GCC编译器对话框的ABI部分。将设置更改为：

```
$ custom – arm – linux – generic – elf – 64 bit 
```

![](\img\4.png)

##### 4、修改Kit，更改默认编译器

将系统默认的编译器删除后，点击Add，手动添加新编译器，在Compiler处，点击Manager，配置编译器。

![](\img\5.png)

## 三、导入QT默认案例（example）

为了方便调试，QT增加了帮助文档和一些使用案例，同样可以通过命令行进行下载与安装。

```
 sudo apt-get install qt5-doc qt5-doc-html qtbase5-doc-html qtbase5-examples -y 
```

## 四、总结

在Jetson TX2上启动Qt Creator并使其运行需要一些技巧，但是幸运的是我们能够弄清楚它们。注意：如果在24.2.1之前运行L4T 24.X版本，则可能会遇到与Mesa OpenGL驱动程序的软链接问题相关的错误。在24.2.1中，这些已解决。对于以前的版本，需要执行：

```
$ cd /usr/lib/aarch64-linux-gnu
$ sudo rm libGL.so
$ sudo ln -s /usr/lib/aarch64-linux-gnu/tegra/libGL.so libGL.so 
```

## 五、额外一些坑

##### 1、编译过程中出现错误

```
Project ERROR: Unknown module(s) in QT: qml quick
```

检查项目文件中肯定是否加添加quick qml

```
 QT += quick qml 
```

如果并未解决则缺失库文件，命令行安装：

```
$ sudo apt-get install qtdeclarative5-dev 
```


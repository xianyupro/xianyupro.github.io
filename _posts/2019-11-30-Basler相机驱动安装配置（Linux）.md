# Basler相机驱动代码安装

早先配置在Linux系统下配置basler相机驱动又要下安装包又要配置环境变量看起来还是挺麻烦的，为了简化操作，这里将在命令行内全部用代码实现，和手动下载区别不大，但相对来说要方便一点。

```sh
#使用wget在官网地址下载包（不同的系统可能对应的版本不一样，需要注意区分）
wget  https://www.baslerweb.com/fp-1551786516/media/downloads/software/pylon_software/pylon-5.2.0.13457-x86_64.tar.gz
#解压到/opt目录下
sudo tar -C /opt -xzf pylon-5.2.0.13457-x86_64.tar.gz 
#cd到该目录下以便于解压源文件
cd /opt/pylon-5.2.0.13457-x86_64
#解压源文件
sudo tar -C /opt -xzf pylonSDK-5.2.0.13457-x86_64.tar.gz
#删除之前解压的第一个目录（是没有用的）
sudo rm -r ../pylon-5.2.0.13457-x86_64/
#配置ld.so.conf
cd /etc/ld.so.conf.d/
touch pylon.conf
sudo vim pylon.conf
#文件最后加入 
/opt/pylon5/lib64
#esc —> : -> wq -> 回车
sudo ldconfig -v
```

执行完以上操作即安装完成

在目录

```
/opt/pylon5/bin
```

中即可打开软件 PylonViewerApp 查看相机状态。
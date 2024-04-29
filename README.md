# 超详细编译iOS环境下的FFmpeg库

网络上可以搜索很多类似的iOS FFmpeg编译的教程，但是大都时间都比较早了，FFmpeg的版本也迭代了很多个了，也有就是没有说详细，有的只是码字，没有图文教程，看的也是一头雾水。
刚好最新项目有个需求，要在iOS项目中集成使用FFmpeg库，今天有上手实操一编，顺便做个记录。


# 目录

1、FFmpeg简介

2、FFmpeg的编译

3、iOS集成使用FFmpeg

### 一、FFmpeg的简介

1.FFmpeg是什么？

你可以把 FFmpeg 理解成一套音视频解决方案，使用 C语言 开发的开源程序，并且免费、开源、跨平台，它提供了录制、转换以及流化音视频，编码，特效，视音频操作等功能，包含了非常先进的音频/视频编解码库。

2.FFmpeg能做什么？

可以实现播放歌曲、视频，甚至通过命令实现对 视频文件的转码、混合、剪辑，采集等各种复杂处理。

3.哪些地方用到了FFmpeg？

使 FFmpeg内核视频播放 : Mplayer，射手播放器 ，暴风影音 ，KMPlayer...
使 FFmpeg作为内核的Direct show Filter 解码 : ffdshow，lav filters...
使 FFmpeg作为内核的转码工具: ffmpeg，格式工厂...



### 二、FFmpeg的编译

1、介绍

FFmpeg是一个多平台多媒体处理工具，所以也可以在Mac下运行，先说一下Mac下如何安装FFmpeg。

2、相关地址

 - FFmpeg官网：[http://ffmpeg.org/download.html](http://ffmpeg.org/download.html)
 - FFmpeg源码：[https://github.com/FFmpeg/FFmpeg](https://github.com/FFmpeg/FFmpeg)
 - FFmpeg doc :[http://www.ffmpeg.org/documentation.html](http://www.ffmpeg.org/documentation.html)
 - FFmpeg wiki :[https://trac.ffmpeg.org/wiki](https://trac.ffmpeg.org/wiki)
 - Homebrew 安装网站 :[http://brew.sh/index_zh-cn.html](http://brew.sh/index_zh-cn.html)
 - FFmpeg官方安装教程 :[https://trac.ffmpeg.org/wiki/CompilationGuide/MacOSX](https://trac.ffmpeg.org/wiki/CompilationGuide/MacOSX)

3、编译Mac下可用的FFmpeg库
		3.1、Homebrew安装
		打开终端执行：
		

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)”
    
通过Homebrew 安装 FFmpeg
终端执行

    brew install ffmpeg

等待完成即可。

4、编译iOS环境下的FFmpeg库

在进行编译之前，需要先做以下准备工作：

4.1、安装 gas-preprocessor

安装步骤，在终端中依次执行

    sudo git clone https://github.com/bigsen/gas-preprocessor.git /usr/local/bin/gas
    sudo cp /usr/local/bin/gas/gas-preprocessor.pl /usr/local/bin/gas-preprocessor.pl
    sudo chmod 777 /usr/local/bin/gas-preprocessor.pl
    sudo rm -rf /usr/local/bin/gas/

4.2、安装yams

安装步骤，在终端中依次执行

    curl http://www.tortall.net/projects/yasm/releases/yasm-1.2.0.tar.gz -o yasm-1.2.0.gz
    tar -zxvf yasm-1.2.0.gz
    cd yasm-1.2.0
    ./configure && make -j 4 && sudo make install

4.3、配置FFmpeg编译脚本

编译FFmpeg可使用一个脚本：FFmpeg-iOS-build-script。
FFmpeg-iOS-build-script 是一个外国人写的自动编译的脚本，脚本则会自动从github中把ffmpeg源码下到本地并开始编译出iOS可用的库，支持各种架构。

脚本下载地址：

    git clone https://github.com/kewlbear/FFmpeg-iOS-build-script.git


配置FFmpeg的版本

下载完成后我们可指定编译的FFmpeg版本，修改 FF_VERSION 后面的参数就行了，本篇文章使用7.0最新版本。
![version](https://raw.githubusercontent.com/MrHT/FFmpeg-iOS/main/version.png)

到此其实就基本上配置完成了，其他的都是可选项就不在这里说了。

运行脚本生成FFmpeg库：

配置好选项参数后就可以运行脚本了，首先终端cd到FFmpeg-iOS-build-script所在的目录，执行build-ffmpeg.sh脚本。

    ./build-ffmpeg.sh

此过程可能时间会比较长，等运行完毕，在同级目录会生成库文件夹：
![enter image description here](https://github.com/MrHT/FFmpeg-iOS/blob/main/WechatIMG79.jpg?raw=true)

常见问题报错：

    tar: Error opening archive: Unrecognized archive format

根据字面意思是下载解压失败了，网络和路径都没有问题，此问题就很奇怪。
既然脚本自动解压不行，那就手动下载吧。
下载地址：

    https://ffmpeg.org/releases/

![enter image description here](https://github.com/MrHT/FFmpeg-iOS/blob/main/1956429384520.png?raw=true)

下载自己同版本号的库源码包，我下载的是ffmpeg-7.0.tag.bz2，然后解压到FFmpeg-iOS-build-script文件夹下就可以继续再次执行了。
![enter image description here](https://github.com/MrHT/FFmpeg-iOS/blob/main/WechatIMG79.jpg?raw=true)

5、iOS项目下集成FFmpeg库

生成完FFmpeg库与代码后，我们就可以集成到iOS项目中进行使用

5.1、在Xcode项目的Link Binary With Libraries 里添加

 - libz.tbd
 - libbz2.tbd
 - libiconv.tbd
 - CoreMedia.framework
 - VideoToolbox.framework
 - AVFoundation.framework

![enter image description here](https://github.com/MrHT/FFmpeg-iOS/blob/main/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy83OTA4OTAtYjZlNjMwODY2MTk3MWQ2Zi5wbmc.png?raw=true)

5.2、导入库文件

将目录相爱的include和lib文件夹拖到项目中
设置Header Search Paths 路径，指向项目中的include目录。

    $(SRCROOT)/FFmpeg_iOS/FFmpeg/include

![enter image description here](https://github.com/MrHT/FFmpeg-iOS/blob/main/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy83OTA4OTAtYTU0YWFkMzNiZDQ2Y2UzYS5wbmc.png?raw=true)

到这一步FFmpeg库就已经成功导入项目中的，就可以开始使用了。

由于脚本执行时间比较长，也有可能部分电脑的环境问题，执行失败，为江湖救急，此处把我已经编译好的FFmpeg-4.3.1的库压缩上传到网盘了，有需要的可以先拿去用。

网盘地址：https://pan.quark.cn/s/1890e22fae84


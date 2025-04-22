#  jellyfin 搭建影视库

### 什么是Jellyfin

Jellyfin是一个开源的媒体系统，是Emby 和 Plex的替代方案，后两者功能类似但都要收费。你可以将所有的电影、电视剧、动漫、漫画、书籍、音乐等放进去，然后在所有的平台上免费观看，进度是同步的。你还可以设置「刮削器」，把影片信息从网上下载整理好放到旁边，整个过程是自动的。如果你在通勤的路上想要看电影，Jellyfin也可以硬件加速转码，帮助你提高视频流畅度，同时节省流量。

## 安装 Jellyfin

这里直接在 Dockge 管理页面直接通过 docker compose 的方式安装

docker 版本推荐 nyan­misaka/jel­lyfin 这是个针对 In­tel 的「QuickSync QSV」硬件加速进行过本地优化的版本。

```C++
version: "3.9"
services:
  jellyfin:
    image: nyanmisaka/jellyfin:latest
    container_name: jellyfin
    # network_mode: host
    ports:
      - 8086:8096
      # - 8920:8920
      # - 1900:1900/udp
      # - 7359:7359/udp
    environment:
      - PUID=1026
      - PGID=100
      - TZ=Asia/Shanghai
      - HTTP_PROXY=http://192.168.31.123:7890
      - HTTPS_PROXY=http://192.168.31.123:7890
    devices:
      - /dev/dri:/dev/dri
    volumes:
      - ./jellyfin/config:/config
      - ./jellyfin/cache:/cache
      - /volume1/media:/media
    restart: unless-stopped
```

## 设置 Jellyfin

### 初始化

初始设置很简单。

先选择**语言**。nyan­misaka 的版本一律将「中文」显示成了「汉语（简化字）」。

[![img](https://static.himiku.com/2022/05/03/6270067509b95.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/6270067509b95.webp#vwid=2560&vhei=1373)

**用户名**默认是 root，可以修改成自己想要的。密码也可以留空，毕竟是自用

[![img](https://static.himiku.com/2022/05/03/627006fb19d84.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/627006fb19d84.webp#vwid=2560&vhei=1373)

**媒体库**可以稍后设置，这里点击下一步。

[![img](https://static.himiku.com/2022/05/03/6270074e40155.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/6270074e40155.webp#vwid=2560&vhei=1373)

**元数据语言**按图中所示选择。

[![img](https://static.himiku.com/2022/05/03/627007a91f7bc.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/627007a91f7bc.webp#vwid=2560&vhei=1373)

**远程访问**这一页保持默认，不勾选「开启自动端口映射」也能用。

[![img](https://static.himiku.com/2022/05/03/62700794de881.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/62700794de881.webp#vwid=2560&vhei=1373)

这样初始化完成

[![img](https://static.himiku.com/2022/05/03/62700819bf5ab.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/62700819bf5ab.webp#vwid=2560&vhei=1373)

[![img](https://static.himiku.com/2022/05/03/6270081f405a6.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/6270081f405a6.webp#vwid=2560&vhei=1373)

### 转码设置

在始使用前，先不要着急添加媒体库。把转码功能开了。「硬件转码」是流媒体中很重要的一部分，不管是 PLEX 亦或是 Emby，硬解功能都是收费的。而选择「nyan­misaka/jel­lyfin」这个映像，为的就是能简单快捷地用上「In­tel QuickSync (QSV)」硬解。

所以只要进入「控制台」-「播放」，选择「In­tel QuickSync (QSV)」，把能勾选的视频编码格式全勾上，其他选项根据自己的理解勾选。

[![img](https://static.himiku.com/2022/05/03/62713a7da975a.webp#vwid=2114&vhei=3324)](https://static.himiku.com/2022/05/03/62713a7da975a.webp#vwid=2114&vhei=3324)

### 关于「元数据」

在安装插件之前，来介绍什么是「元数据metadata」。简单地说，封面、标题 、简介、发行时间、制作公司、演员表等等一切和该影片相关的信息，都是元数据。元数据越完善，流媒体程序展现出来的界面就越美观。这些信息自然不会在下载资源的时候附带，因此就需要使用 Jel­lyfin 中的「元数据插件」，将这些信息从网上「刮削」下来。「刮削」这个词，大概是源自英语「Scraper」的直译。例如「Meta Data Scrap­ers」即为「元数据刮削」。按理说译成「问数据采集」会更合适些，但现在全中文站点都将这种行为称为「刮削」，也就这么用着吧！

### 安装 插件

[![img](https://static.himiku.com/2022/05/03/6270e4c54a0ec.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/6270e4c54a0ec.webp#vwid=2560&vhei=1373)

「**The Movie Database**」，简称「TMDb」，是目前接触到的元数据网站中唯一一个提供中文界面的网站，并且支持采集电视剧 、电影和人物信息。

### 添加媒体库

元数据插件准备完毕，就可以添加媒体库了。

[![img](https://static.himiku.com/2022/05/03/62710316ebbb5.webp#vwid=2560&vhei=1373)](https://static.himiku.com/2022/05/03/62710316ebbb5.webp#vwid=2560&vhei=1373)

内容类型选择「节目」，显示名称就随意了，我这里用的是「动画」。

点击文件夹旁的按钮添加媒体库文件夹，也就是一开始安装 Jel­lyfin 映射到容器中的那个。勾选所有的元数据插件，勾选「优先使用内置的剧集信息而不是文件名」、「媒体资料储存方式︰Nfo」、「将媒体图像保存到媒体所在文件夹」

[![点击展开超长长图](https://static.himiku.com/2022/05/03/627111b0ed2f7.png#vwid=1473&vhei=800)](https://static.himiku.com/2022/05/03/627111b0ed2f7.png#vwid=1473&vhei=800)


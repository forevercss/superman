#   NAS 搭建和使用

##  背景

## **1. NAS 是什么？**

**NAS（Network Attached Storage，网络附加存储）** 是一种专门用于存储和共享数据的设备，通过局域网（LAN）或互联网提供文件访问服务。

- **本质**：一台专门用于存储的计算机，通常运行轻量级操作系统（如 FreeNAS、OpenMediaVault 或商业系统如 Synology DSM 群辉）。
- **核心功能**：集中存储、备份、共享文件，并提供远程访问能力。
- **对比其他存储方案**：
  - **DAS（直连存储，如移动硬盘）**：只能单设备访问，无法共享。
  - **SAN（存储区域网络）**：企业级高性能存储，成本高，不适合个人。
  - **云存储（如百度网盘、Google Drive）**：依赖网络，隐私性差，长期订阅费用高。

------

## **2. 为什么需要 NAS？**

### **（1）数据集中管理，告别碎片化存储**

- 手机、电脑、平板的文件可以统一存储到 NAS，避免数据分散在不同设备上。
- 支持 **自动备份**（如 Time Machine、Windows 备份）。

### **（2）私有云，替代网盘**

- 完全掌控数据，不受云服务商限制（如百度网盘限速、iCloud 容量不足）。
- 支持 **远程访问**，在外也能像本地一样读写文件。

### **（3）家庭媒体中心**

- 存储电影、音乐、照片，并通过 **Plex/Jellyfin/Emby** 实现智能影音库（自动匹配海报、字幕）。
- 支持 **多设备同时播放**（如电视、手机、平板）。

### **（4）数据安全 & 备份**

- **RAID 冗余**（如 RAID 1/5/6）防止硬盘损坏导致数据丢失。
- **自动快照**（ZFS/Btrfs）可恢复误删文件。
- **异地备份**（如同步到另一台 NAS 或加密上传至云存储）。

### **（5）智能家居 & 开发环境**

- 作为 **智能家居中枢**（如 Home Assistant 的数据存储）。
- 搭建 **私有 Git 仓库、Docker 服务、虚拟机** 等。

------

## **3. NAS 常用功能**

| **功能**          | **典型应用**      | **推荐软件**                 |
| :---------------- | :---------------- | :--------------------------- |
| **文件共享**      | 家庭/团队文件共享 | Samba/NFS/AFP                |
| **自动备份**      | 电脑/手机照片备份 | Nextcloud/Syncthing          |
| **影音库**        | 电影/音乐管理     | Plex/Jellyfin/Emby           |
| **下载机**        | 24小时挂机下载    | qBittorrent/Transmission     |
| **私有云**        | 替代百度网盘      | Nextcloud/Seafile            |
| **虚拟机/Docker** | 搭建开发环境      | Proxmox/Portainer            |
| **监控存储**      | 保存摄像头录像    | Surveillance Station/Shinobi |

------

## **4. 现有 NAS 方案对比**

### **（1）品牌 NAS（开箱即用）**

| **品牌**                  | **特点**                  | **适合人群**        |
| :------------------------ | :------------------------ | ------------------- |
| **Synology（群晖）**      | 软件生态强（DSM系统易用） | 不想折腾的小白/企业 |
| **QNAP（威联通）**        | 硬件性价比高，扩展性强    | 进阶用户/影音爱好者 |
| **Terramaster（铁威马）** | 价格低，适合入门          | 预算有限的用户      |

### **（2）自建 NAS（灵活低成本）**

| **方案**                       | **优点**             | **缺点**               |
| :----------------------------- | :------------------- | :--------------------- |
| **旧电脑改造**                 | 零成本，性能强       | 功耗高，体积大         |
| **树莓派 + 硬盘盒**            | 超低功耗，便携       | 性能弱，仅适合轻量使用 |
| **迷你主机（如 J4125/N5105）** | 平衡性能与功耗       | 需自行安装系统         |
| **服务器级（如 TrueNAS）**     | 高扩展性，企业级功能 | 成本高，维护复杂       |

### **（3）软件方案**

| **系统**                            | **特点**                                                     | **适合场景**            |
| :---------------------------------- | :----------------------------------------------------------- | :---------------------- |
| **TrueNAS Core（基于 FreeBSD）**    | ZFS 文件系统，数据安全                                       | 企业/高级用户           |
| **OpenMediaVault（基于 Debian）**   | 轻量易用，插件丰富                                           | 家庭/入门用户           |
| **UnRAID（付费）**                  | 支持混合硬盘，Docker 友好                                    | 影音库/虚拟机用户       |
| **Windows Server + Storage Spaces** | 兼容性好，适合企业                                           | 需要 Windows 生态的用户 |
| **飞牛fnOS**                        | 国产新秀，支持Docker、影视库自动刮削、相册管理，界面类似群晖DSM，内置内网穿透服务 | 免费且兼容旧硬件        |

------

## **5. 如何选择适合自己的 NAS？**

### **（1）普通家庭用户**

- **推荐**：Synology DS220+/DS920+（易用性最佳）

### **（2）技术爱好者/开发者**

- **推荐**：自建 TrueNAS/UnRAID + Docker,  目前我是基于 debian 系统搭建的NAS环境，后面有时间会研究下飞牛os

#### *二、自建NAS搭建流程*

##### **1. 硬件准备**

- **核心组件**：

  - **CPU**：低功耗处理器（如Intel J4125、N5105）或旧电脑改造（i5四代以上）。
  - **内存**：8GB起步（TrueNAS建议16GB以上）。
  - **存储**：NAS专用硬盘（如WD Red）、SSD缓存盘（可选）。
  - **网络**：千兆网卡（推荐2.5G/10G网卡提升传输速度）。

  ```C++
  CPU: 8100t
  主板：铭瑄老主板
  内存：8g * 2 ddr3 有点捞
  网络：千兆网卡
  硬盘： 16T
      
  进阶版配置单：价格偏贵 低价收购了上面的配置
  机箱： 乔思伯N2
  主板： 精粤 b760i
  CPU： i3 12300t
  电源： tt sfx 450w
  内存： 昂达 ddr4 3200 16g
  散热： 利民 tl-12c
      
  硬盘笼：闲鱼3D打印的4盘位
  ```

- **可选优化**：

  - **UPS电源**：防止断电导致数据损坏。

##### **2. 系统安装与配置**

以**Debian为例**：

1. **制作启动盘**：下载ISO镜像，使用Rufus写入U盘1。
2. **制作启动盘**：下载ISO镜像，使用 [Rufus](https://rufus.ie/zh) 写入U盘，[官网地址](https://www.debian.org/)
3. **安装系统**： 选择独立SSD作为系统盘
4. **存储池设置**：创建ZFS存储池，按需选择RAID类型并格式化。(RAID（独立磁盘冗余阵列）是一种将多个独立磁盘组合成一个大的存储系统，以提高存储性能和数据可靠性的技术。)
5. **共享服务**：启用SMB/NFS协议，设置用户权限和共享文件夹

***3.*软件安装**

### 3.1 基础组件安装

* 配置软件源
* curl git vim wget exim4 gnupg apt-transport-https ca-certificates smartmontools 基础组件
* docker 服务, 安装 docker-ce、ocker-compose-plugin等相关组件, 后续大部分应用都使用 docker 配置

###  3.2常用 docker 安装

- dockge：Compose文件集中管理
- nginx-ui：反向代理与SSL证书管理
- portainer：Docker可视化管理面板
- scrutiny: 硬盘健康监控（SMART数据）
- qbittorrent: BT/PT下载
- jellyfin：4K视频转码与播放
- photoprism：AI照片管理（人脸识别/场景分类）
- nas-tools：动态域名解析（支持阿里云/腾讯云）
- ddns-go：动态域名解析（支持阿里云/腾讯云）
- alist：聚合网盘管理 挂载阿里云盘
- filebrowser： 网页文件管理器
- homepage: 高度可定制的导航页面

典型Docker Compose配置示例

```C++
# Jellyfin媒体服务
version: '3'
services:
  jellyfin:
    image: jellyfin/jellyfin:latest
    volumes:
      - /nas/media:/media
      - /nas/config/jellyfin:/config
    devices:
      - /dev/dri:/dev/dri  # Intel核显硬件转码
    ports:
      - "8096:8096"
    restart: unless-stopped

# qBittorrent下载
  qbittorrent:
    image: ghcr.io/binhex/arch-qbittorrentvpn:latest
    volumes:
      - /nas/downloads:/data
    environment:
      - VPN_ENABLED=yes
      - VPN_TYPE=wireguard
      - VPN_CONFIG=/config/wireguard.conf
    cap_add:
      - NET_ADMIN
    restart: unless-stopped
```












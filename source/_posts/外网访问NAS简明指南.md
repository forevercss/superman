# 外网访问NAS简明指南

## 🌟 核心逻辑图解

```
graph TD
    A[公网IPv6地址] --> B(动态域名<br>demotest.com)
    C[光猫桥接模式] --> D(路由器拨号)
    D --> E[获取公网IP]
    B --> F[DDNS自动更新]
    E --> F
    F --> G{外网访问}
```

![mermaid_20250422_765248](D:\file\Code\blog\source\_posts\images\mermaid_20250422_765248.png)

------

## 📦 五步极简配置流程

### 第一步：网络基础检测

```
# 确认IPv6地址（关键！）
ip -6 addr show | grep 'inet6 24'  # 需看到2408/2409开头的公网地址

# 测试IPv6连通性
ping6 ipv6.baidu.com -c 3
```

### 第二步：光猫桥接设置

![光猫设置对比图](https://example.com/bridge-mode-comparison.png)
*左：路由模式（错误） / 右：桥接模式（正确）*

**操作路径：**

1. 访问 `http://192.168.1.1`
2. 网络设置 → 宽带设置 → 删除原有连接
3. 新建连接：
   ✅ 模式选择 **Bridge**
   ✅ VLAN ID填 **41**（电信）/ **101**（联通）
   ✅ 保存重启

### 第三步：路由器双栈配置

| 配置项     | IPv4设置               | IPv6设置                     |
| :--------- | :--------------------- | :--------------------------- |
| WAN口类型  | PPPoE拨号              | DHCPv6/SLAAC                 |
| 防火墙规则 | 端口转发               | 允许入站                     |
| 示例配置   | 转发5000→192.168.1.100 | 放行2408:8207::/64的5000端口 |

### 第四步：DDNS-Go容器部署

```
# docker-compose.yml 
services:
  ddns-go:
    image: jeessy/ddns-go
    network_mode: host
    volumes:
      - ./ddns-go:/root
    restart: always
```

**启动命令：**

```
docker compose up -d && docker logs ddns-go -f  # 实时查看日志
```

### 第五步：域名绑定配置

1. 访问管理界面：
   浏览器打开 `http://你的NAS_IP:9876`
2. **IPv6 配置：**
   - **启用 IPv6**：勾选 `IPv6` 开关。
   - **获取方式**：选择 `通过网卡获取`。
   - **域名**：填写需要更新的域名（如 `demotest.com`）。
3. **DNS 服务商配置：**
   - 选择你的域名提供商（如 `Cloudflare`、`阿里云` 等）。
   - 输入对应的 API 密钥或 Token（如 Cloudflare 的 Global API Key）。
4. **保存配置**：
   点击 `Save`，ddns-go 会自动将 NAS 的 IPv6 地址绑定到域名。

选择你域名所在的 DNS 服务商。DDNS-GO 中不同的 DNS 服务商获取 To­ken 或是 API key 都会有相应的提示。这里以 Cloud­flare 为例。



[![img](https://static.himiku.com/2023/10/29/653e04ea56a64.png#vwid=1736&vhei=770)](https://static.himiku.com/2023/10/29/653e04ea56a64.png#vwid=1736&vhei=770)

访问「[创建令牌](https://dash.cloudflare.com/profile/api-tokens)」页面，点击「创建令牌」；选择「编辑区域 DNS」，点击「使用模板」。在「区域资源」处选择「**所有区域**」，其余保持默认。

[![img](https://static.himiku.com/2023/10/29/653e069a8f4bd.png#vwid=1342&vhei=562)](https://static.himiku.com/2023/10/29/653e069a8f4bd.png#vwid=1342&vhei=562)

点击「继续以显示摘要」，完成创建后将秘钥粘贴至 DDNS GO 的 `token` 处即可。

接着，启用 IPv6 配置，选择「通过网卡获取」IP 地址，在下方 Do­mains 里填入你想要使用的域名即可。

[![img](https://static.himiku.com/2023/10/29/653e081597bc0.png#vwid=1286&vhei=719)](https://static.himiku.com/2023/10/29/653e081597bc0.png#vwid=1286&vhei=719)


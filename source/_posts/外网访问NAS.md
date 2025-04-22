#  外网访问NAS

### **核心原理**

- **动态域名**：通过一个固定域名（如 demotest.com`）绑定到你设备的动态公网 IP（IPv4/IPv6）。

- **DDNS 服务**：自动检测你的公网 IP 变化，并更新域名解析记录，确保域名始终指向最新 IP。

  

### **前期准备**

1. 确保你的 NAS 已分配 **公网 IPv6 地址

   ```
   ip -6 addr show | grep inet6
   ```

   如果看到类似 `inet6 2408:8207:xxx:xxx::xx/64` 的地址，则说明已启用 IPv6。

2. 拥有一个 **域名**（如阿里云、Cloudflare 等注册的域名）。



------

### 部署 ddns-go 容器

```
version: "3.9"
services:
  ddns-go:
    image: jeessy/ddns-go:latest
    container_name: ddns-go
    network_mode: host
    volumes:
      - ./ddns-go:/root
    environment:
      - TZ=Asia/Shanghai
    restart: unless-stopped
```

------

### **配置 ddns-go**

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

------

### **配置路由器/防火墙**

1. **允许 IPv6 入站连接**：
   在路由器防火墙中放行 NAS 服务端口（如 SMB 的 `445`、Web 服务的 `80/443` 等）。
   （不同路由器界面不同，通常位于 **安全设置 > IPv6 防火墙**）

2. **示例放行规则**：

   ```
   允许协议：TCP/UDP
   目标地址：NAS 的 IPv6 地址（如 2408:8207:xxx:xxx::xx）
   目标端口：需要访问的端口（如 5000）
   ```

------

### 外网访问测试**

使用域名访问服务：
在外网设备上通过 `http://demotest.com:端口`测试连接。


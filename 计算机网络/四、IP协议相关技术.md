## IP协议相关技术

### 1. DNS 域名系统协议

-   域名的构成

    ![域名分层结构](https://github.com/CyS2020/Notebook/raw/master/%E7%AC%94%E8%AE%B0%E5%9B%BE%E7%89%87/%E5%9F%9F%E5%90%8D%E7%9A%84%E5%88%86%E5%B1%82%E7%BB%93%E6%9E%84.jpg)

    主机名. 次级域名. 顶级域名. 根域名

      www.     baidu.       com.       root

-   域名服务器：根部所设置的DNS叫做根域名服务器，根域名服务器中注册着根（root）以下第一层域名服务器的IP地址（.com, .edu, .hk, .cn等)。下一层的域名服务器中注册着再往下一层的域名服务器的IP地址，如果没有往下的其他分层，就可以自由地指定主机名称或子网名称

-   域名解析器：进行DNS查询的主机和软件叫做DNS解析器

    ![DNS查询过程](https://github.com/CyS2020/Notebook/raw/master/%E7%AC%94%E8%AE%B0%E5%9B%BE%E7%89%87/DNS%E6%9F%A5%E8%AF%A2%E8%BF%87%E7%A8%8B.png)

### 2. ARP 地址解析协议

-   ARP概要：以目标IP地址为线索，来定位下一个应该接收数据分包的网络设备对应的MAC地址。如果目标主机不在同一个链路上，可以通过ARP查找下一条路由的的MAC地址。不过ARP只适用于IPv4

-   工作机制：起初要通过广播发送一个ARP请求包，这个包中包含了想要了解其MAC地址的主机IP地址，由于广播包会被这同一个链路上的所有的主机和路由器进行解析，如果ARP请求包中的目标IP地址与自己的IP地址一致，那么这个节点就将自己的MAC地址塞入ARP响应包返回给主机A。如果每发送一个IP数据报就进行一次ARP请求会造成不必要的浪费，通常做法把获取到的MAC地址缓存一段时间，定期更新

-   ARP包格式：

    ![ARP包格式](https://github.com/CyS2020/Notebook/raw/master/%E7%AC%94%E8%AE%B0%E5%9B%BE%E7%89%87/ARP%E5%8C%85%E6%A0%BC%E5%BC%8F.jpg)

-   RARP：**逆地址解析协议**，从MAC地址定位IP地址的一种协议。有些设备没有任何输入接口或无法通过DHCP自动分配获取IP地址的情况，就可以使用RARP，先架设一台RARP的服务器，从而在这个服务器上注册设备的MAC地址及IP地址，然后插电启动设备时，进行请求响应操作如图。

    ![RARP操作](https://github.com/CyS2020/Notebook/raw/master/%E7%AC%94%E8%AE%B0%E5%9B%BE%E7%89%87/RARP%E6%93%8D%E4%BD%9C.jpg)

### 3. ICMP 互联网控制消息协议


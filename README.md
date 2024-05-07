# tailscale
tailscale内网穿透常用命令

## 在软路由/iStoreOS上安装Tailscale实现内网穿透、异地组网、远程访问，点对点传输，更好的使用体验！轻松打通内外网！群晖、威联通NAS，替代zerotier
1、开源项目地址：[【点击进入】](https://github.com/adyanth/openwrt-tailscale-enabler/releases)

#可以根据实际情况替换最新的

```
wget https://github.com/adyanth/openwrt-tailscale-enabler/releases/download/v1.60.0-e428948-autoupdate/openwrt-tailscale-enabler-v1.60.0-e428948-autoupdate.tgz
```  

2、解压软件包

```
tar x -zvC / -f openwrt-tailscale-enabler-v1.60.0-e428948-autoupdate.tgz
```

3、安装依赖包

```
opkg update

opkg install libustream-openssl ca-bundle kmod-tun
```

4、设置开机自启并验证

```
/etc/init.d/tailscale enable

ls /etc/rc.d/S*tailscale*
```

5、启动Tailscale

```
/etc/init.d/tailscale start
```

6、获取登录链接，配置路由

```
tailscale up
```

7、Tailscale常用命令

退出Tailscale：```tailscale logout```

关闭Tailscale：```tailscale down```

检查Derp服务器：```tailscale netcheck```

检查Tailscale：```tailscale status```

8、开启子网网路由

在Tailscale的管理页面上，单击设备列表右侧的更多图标，禁用密钥过期，并打开子网路由。

```
tailscale up --accept-routes --advertise-routes=192.168.2.0/24 --accept-dns=false
```

![image](https://github.com/3981877/tailscale/assets/60610978/84773267-524b-4d4b-9eca-c063626c9f05)

如果是使用headscale搭建的，可以用下面的命令

```
tailscale up --login-server=http://服务器IP:headscale端口 --accept-routes=true --accept-dns=false --advertise-routes=192.168.2.0/24 --reset
```

9、添加接口

在OpenWrt上新建一个接口，协议选静态地址，设备选tailscale0，地址为Taliscale管理页面上分配的地址，掩码255.0.0.0。防火墙区域选lan区域。

![image](https://github.com/3981877/tailscale/assets/60610978/2688102e-95c5-4b28-8ebb-82e1e9b78f7c)

![image](https://github.com/3981877/tailscale/assets/60610978/91324aaa-9c3f-4e92-ba9e-333f8b51a582)

10、添加防火墙规则

网络——防火墙——自定义规则——把下面的规则添加进去——重启防火墙

```
iptables -I FORWARD -i tailscale0 -j ACCEPT
```
```
iptables -I FORWARD -o tailscale0 -j ACCEPT
```

```
iptables -t nat -I POSTROUTING -o tailscale0 -j MASQUERADE
```

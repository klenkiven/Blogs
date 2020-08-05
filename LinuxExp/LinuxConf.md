# 在LInux上面的一些配置记录

## Deepin QQ和TIM无法自动加载图片的问题

### 方法一：IPV6的关闭

1. 首先，执行命令 `ip addr` 找到相关的网卡名字，例如，wlp0s20f3，可以看到相关的信息.

   > 我已经关闭了IPV6了，不然应该会有 inet6 的字眼

   ```
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
       inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
       inet6 巴拉巴拉
   2: wlp0s20f3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
       link/ether 50:e0:85:c1:e0:b2 brd ff:ff:ff:ff:ff:ff
       inet 192.168.1.103/24 brd 192.168.1.255 scope global dynamic noprefixroute wlp0s20f3
          valid_lft 6604sec preferred_lft 6604sec
       inet6 巴拉巴拉
   6: enp0s20f0u3u3: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
       link/ether 00:e0:4c:36:00:25 brd ff:ff:ff:ff:ff:ff
   ```

2. 编辑配置文件 `sudo vim /etc/sysctl.conf`

   + 这里是禁止特定的网卡的IPV6的配置，比如禁止 lo, wlp0s20f3 网卡的IPV6功能 ( 推荐 )

   	``` 
   	net.ipv6.conf.lo.disable_ipv6 = 1
   	net.ipv6.conf.wlp0s20f3.disable_ipv6 = 1
   	```
	
   
   + 下面是全局禁用IPV6的方式
   
     ``` 
     net.ipv6.conf.all.disable_ipv6 = 1
     net.ipv6.conf.default.disable_ipv6 = 1
     ```
   
3. 执行配置文件 `sudo sysctl -p`

4. 然后执行 `ip addr` 就可以检查是否禁用成功了。

### 方法二：设置代理

在Manjaro下面deepin的QQ和TIM无法打开相关设置，我这里就不再过多探究这个问题了。

## KDE：Deepin QQ和TIM无法在KDE桌面启动的问题

1. 安装`gnome-setting-daemon`

   ```bash
   sudo pacman -S gnome-setting-deamon
   ```

2. 执行下面的命令

   ```bash
   cp /etc/xdg/autostart/org.gnome.SettingsDaemon.XSettings.desktop ~/.config/autostart
   ```

3. 设置KDE开机启动

   系统设置 => 开机和关机 => 自动启动

   然后，勾选GNOME Setting Deamon's xsetting plugin

4. 如果你是高分屏幕就需要设置dpi

   ```bash
   gsettings set org.gnome.desktop.interface text-scaling-factor 1.27
   ```

   > 我这里设置的是1.27，你也可以设置成更大或者更小的数，数字越大，放大倍数越大mari

## KDE：MySQL Workbench在archlinux中出现The name org.freedesktop.secrets was not provided by any .service files

MySQL Workbench在archlinux中出现 Could not store password: The name org.freedesktop.secrets was not provided by any .service files的错误

解决方案是安装 gnome-keyring 包。
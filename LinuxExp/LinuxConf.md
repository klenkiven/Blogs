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
   sudo pacman -S gnome-settings-deamon
   ```

2. 执行下面的命令

   ```bash
   cp /etc/xdg/autostart/org.gnome.SettingsDaemon.XSettings.desktop ~/.config/autostart/ 
   ```

3. 设置KDE开机启动

   系统设置 => 开机和关机 => 自动启动

   然后，勾选GNOME Setting Deamon's xsetting plugin

4. 如果你是高分屏幕就需要设置dpi

   ```bash
   gsettings set org.gnome.desktop.interface text-scaling-factor 1.31
   ```

   > 我这里设置的是1.27，你也可以设置成更大或者更小的数，数字越大，放大倍数越大mari

## KDE：MySQL Workbench在archlinux中出现The name org.freedesktop.secrets was not provided by any .service files

MySQL Workbench在archlinux中出现 Could not store password: The name org.freedesktop.secrets was not provided by any .service files的错误
解决方案是安装 gnome-keyring 包。

## Sogoupinyin: Error parsing key “overrides” in schema “org.gnome.settings-daemon.plugins.xsettings” as specified in override file “/usr/share/glib-2.0/schemas/50_sogoupinyin.gschema.override”: 1:expected value. Ignoring override for this key.

```bash
sudo nano /usr/share/glib-2.0/schemas/50_sogoupinyin.gschema.override
	# change the line of overrides：
	overrides=Gtk/IMModule:<fcitx>
	# change to:
	overrides=Gtk/IMModule:<fcitx>
sudo glib-compile-schemas /usr/share/glib-2.0/schemas/
reboot
```

# Archlinux 安装的一些东西

## Fcitx5 相当不错的一个输入法

### 安装及其配置

```bash
sudo pacman -S fcitx5-im				# Fcitx5的本体
sudo pacman -S fcitx5-chinese-addons	# Fcitx5的中文扩展支持
sudo pacman -S fcitx5-material-color	# Fcitx5的一个主题
```

完成安装步骤以后，对其进行相关的配置。

```bash
# 首先要配置的是自动启动
cp /usr/share/applications/fcitx5.desktop ~/.config/autostart-scripts/
# 接下來配置相关的环境变量配置
vim .xprofile
	# 文件添加下面的内容
	export GTK_IM_MODULE=fcitx5
	export QT_IM_MODULE=fcitx5
	export XMODIFIERS="@im=fcitx5
```

### 自己遇到的问题：候选框字体设置

KDE桌面（**安装了KCM**）的解决办法：

`设置=>区域设置=>输入法配置=>配置附加组件=>经典用户恶极面=>设置`

非KDE桌面：

右键系统托盘上面的输入法图标，选择`配置`，然后`配置附加组件=>经典用户恶极面=>设置`

# GRUB美化

### 下载主题文件

> 登入网站Gnome-Look
>
> https://www.gnome-look.org/

找到GRUB Theme标签，就可以找自己喜欢的主题啦！我喜欢的主题是Tela主题。

### 具体安装

```bash
# 解压文件
tar -Jxf Tela-1080p.tar.xz
# 将文件移动到指定位置
sudo mkdir /usr/share/grub/themes/Tela
sudo cp -rf ~/Downloads/Tela-1080p/*  /usr/share/grub/themes/Tela
# 

```

# modprobe: FATAL: Module vboxnetflt not found in directory /lib/modules/5.8.5-arch1-1

安装 `linux-header` , 然后执行  `sudo modprobe vboxdrv vboxnetflt vboxnetadp vboxpci ` 
---
layout: post
title: V2Ray Ubuntu Server and Windows Client
---

![Windows PowerShell SSH](/images/v2ray-windows-001.png)

To view a screencast of this post, visit [https://www.youtube.com/watch?v=qFh8tirbXA8](https://www.youtube.com/watch?v=qFh8tirbXA8).

Start by right-clicking on the Windows start button, and selecting **Windows PowerShell**. In PowerShell, invoke the SSH feature:

```
ssh linuxuserid@your.server.ip.address
```

where `linuxuserid` is replaced by your actual Ubuntu Linux user id, and `your.server.ip.address` is replaced by your actual server IP address or hostname.

Once logged on to the server, edit the system control configuration file:

```
sudo vi /etc/sysctl.conf
```

Add lines at the end to specify BBR congestion control:

```
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
```

Write the system control configuration file to disk. Activate these changes:

```
sudo sysctl -p
```

Download the V2Ray automated installation script:

```
wget https://install.direct/go.sh
```

Execute the V2Ray installation script:

```
sudo bash go.sh
```

Note the generated server port and universally unique id (UUID).

![V2Ray generated port and UUID](/images/v2ray-windows-002.png)

Open the server firewall for the generated port number. For example, if your port number is `31896` and you have already installed `iptables-persistent`, you would put:

```
sudo iptables -vnL
sudo iptables -A INPUT -p tcp --dport 31896 -j ACCEPT
sudo iptables -vnL
sudo dpkg-reconfigure iptables-persistent
```

Start V2Ray server:

```
sudo systemctl enable v2ray
sudo systemctl start v2ray
```

Check that V2Ray server is working correctly:

```
sudo systemctl status v2ray
sudo netstat -tulpn
```

Exit your Windows PowerShell SSH session with the V2Ray server:

```
exit
```

You can test the server by installing the V2RayN GUI client for V2Ray on a Windows PC. You will need to have made a note of the server address, server port, and universally unique id (UUID).

Open a browser and navigate to [https://github.com/2dust/v2rayN/releases](https://github.com/2dust/v2rayN/releases).

Download `v2rayN-Core.zip`, and extract the files from it.

Launch the application `v2rayN.exe`.

In the system tray, which is at the bottom right of your Windows desktop, open the V2RayN control panel. 

Select the menu bar item for Servers (**服务器**). Select the first option, which is the one for Add VMess server (**添加VMess服务器**). 

Enter the values for:

* Server address (**地址**)
* Server port (**端口**)
* UUID (**用户**)

Click the Open (**确定**) button.

![V2RayN Vmess V2Ray server configuration](/images/v2ray-windows-003.png)

In Google Chrome, go to **More Tools** > **Extensions** > **Open Chrome Web Store**. 

Search for SwitchyOmega by FelisCatus. Add this extension to Chrome. 

Set up the local proxy:

* The protocol should be **SOCKS5**
* The server should be `127.0.0.1`
* The port should be `10808`

![SwitchyOmega SOCKS5 proxy on localhost](/images/v2ray-windows-004.png)

In SwitchyOmega, turn on the proxy. 

Check that everything is working by visiting [https://www.iplocation.net](https://www.iplocation.net)

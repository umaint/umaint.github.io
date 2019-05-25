---
layout: post
title: V2Ray Client on Ubuntu Linux Desktop
---

Following are some screenshots of installing V2Ray as a client on an Ubuntu Linux Desktop PC.

Open a browser and visit [https://github.com/v2ray/v2ray-core](https://github.com/v2ray/v2ray-core).

![v2Ray Github repository](/images/ss354.png)

Click the [releases](https://github.com/v2ray/v2ray-core/releases) button.

![v2Ray releases](/images/ss355.png)

Assuming you have a 64-bit processor in your PC, look for `v2ray-linux-64.zip`.

![v2Ray for Linux zip file](/images/ss356.png)

From your browser, save the file `v2ray-linux-64.zip` in your `Downloads` directory.

![v2Ray save zip file](/images/ss357.png)

Extract the `zip` file.

![v2Ray extract zip file here](/images/ss358.png)

The contents of the `zip` file are extracted to a new directory named `v2ray-linux-64`.

![v2Ray linux directory in file manager](/images/ss359.png)

Open a terminal and list the contents of the  `v2ray-linux-64` directory.

![v2Ray linux directory in terminal](/images/ss360.png)

Edit the configuration file, which is named `config.json`.

![v2Ray edit configuration json file in vi editor](/images/ss361.png)

Your contents will look something like this. IP address, port, and user id must match your server.

```
{
  "inbounds": [{
    "port": 10808, 
    "listen": "127.0.0.1",
    "protocol": "socks",
    "settings": {
      "udp": true
    }
  }],
  "outbounds": [{
    "protocol": "vmess",
    "settings": {
      "vnext": [{
        "address": "YOUR.SERVER.IP.ADDRESS", 
        "port": YOURPORT,
        "users": [{ "id": "YOUR-UNIVERSALLY-UNIQUE-ID" }]
      }]
    }
  },{
    "protocol": "freedom",
    "tag": "direct",
    "settings": {}
  }],
  "routing": {
    "domainStrategy": "IPOnDemand",
    "rules": [{
      "type": "field",
      "ip": ["geoip:private"],
      "outboundTag": "direct"
    }]
  }
}
```

Execute the `v2ray` program with the `config.json` configuration file.

![v2Ray execution with json config file](/images/ss362.png)

You see a message to say that V2Ray has started.

![v2Ray started](/images/ss363.png)

Open Firefox. Select the option for Network Settings. Choose manual proxy configuration, SOCKS Host `127.0.0.1`, and SOCKS v5.

![Firefox manual proxy configuration SOCKS v5 on 127.0.0.1](/images/ss364.png)

Choose port `10808` for your local port to communicate with the V2Ray client. Choose to proxy DNS when using SOCKS v5. Click **OK** to save these settings.

![Firefox manual proxy configuration port 10808 proxy DNS when using SOCKS v5](/images/ss365.png)

In your Firefox browser, check that your IP location now matches your server by visiting [https://www.iplocation.net](https://www.iplocation.net).

![IP location when connected from V2Ray client to V2Ray server](/images/ss366.png)

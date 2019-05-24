---
layout: post
title: V2Ray Client on Ubuntu Linux Desktop
---

Installation of V2Ray client on Ubuntu Linux PC.

![v2Ray Github repository](/images/ss354.png)

![v2Ray releases](/images/ss355.png)

![v2Ray for Linux zip file](/images/ss356.png)

![v2Ray save zip file](/images/ss357.png)

![v2Ray extract zip file here](/images/ss358.png)

![v2Ray linux directory in file manager](/images/ss359.png)

![v2Ray linux directory in terminal](/images/ss360.png)

![v2Ray edit configuration json file in vi editor](/images/ss361.png)

```
{
  "log": {
    "loglevel": "warning"
  },
  "inbounds": [{
    "port": 10808,
    "listen": "127.0.0.1",
    "tag": "socks-inbound",
    "protocol": "socks",
    "settings": {
      "auth": "noauth",
      "udp": false,
      "ip": "127.0.0.1"
    },
    "sniffing": {
      "enabled": true,
      "destOverride": ["http", "tls"]
    }
  }],
  "outbounds": [{
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "YOUR.SERVER.IP.ADDRESS",
            "port": YOURPORT,
            "users": [
              {
                "id": "YOUR-UNIVERSALLY-UNIQUE-ID",
                "alterId": 233,
                "security": "auto"
              }
            ]
          }
        ]
      }
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],
  "routing": {
    "domainStrategy": "IPOnDemand",
    "rules":[
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      },
      {
        "type": "field",
        "domain": ["geosite:category-ads"],
        "outboundTag": "blocked"
      }
    ]
  },
  "dns": {
    "hosts": {
      "domain:v2ray.com": "www.vicemc.net",
      "domain:github.io": "pages.github.com",
      "domain:wikipedia.org": "www.wikimedia.org",
      "domain:shadowsocks.org": "electronicsrealm.com"
    },
    "servers": [
      "1.1.1.1",
      {
        "address": "114.114.114.114",
        "port": 53,
        "domains": [
          "geosite:cn"
        ]
      },
      "8.8.8.8",
      "localhost"
    ]
  },
  "policy": {
    "levels": {
      "0": {
        "uplinkOnly": 0,
        "downlinkOnly": 0
      }
    },
    "system": {
      "statsInboundUplink": false,
      "statsInboundDownlink": false
    }
  },
  "other": {}
}
```

![v2Ray execution with json config file](/images/ss362.png)

![v2Ray started](/images/ss363.png)

![Firefox manual proxy configuration SOCKS v5 on 127.0.0.1](/images/ss364.png)

![Firefox manual proxy configuration port 10808 proxy DNS when using SOCKS v5](/images/ss365.png)

![IP location when connected from V2Ray client to V2Ray server](/images/ss366.png)

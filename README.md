# calibre_online

## free domain
second-level domain + hostry.com name server

### eu.org
https://nic.eu.org/
un: KG384-FREE
ref: https://dallas.lu/zh-CN/eu-org-domain-register-usage/

### hostry
https://hostry.com/
un: sd*01

## cloudflare tunnel
Cloudflare Tunnel CDN
https://dash.cloudflare.com/login
un: sd*01
ref: https://bra.live/setup-home-server-with-cloudflare-tunnel/

### install
download
```
curl -L 'https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64' -o /usr/bin/cloudflared
chmod +x /usr/bin/cloudflared
```

login and auth
```
cloudflared tunnel login
```

create tunnel and output UUID
```
cloudflared tunnel create [tunnel_name]
```

domain route
```
cloudflared tunnel route dns [tunnel_name] [domain]

config
```
vim ~/.cloudflared/config.yml
```

example
```
tunnel: <隧道UUID>
credentials-file: /root/.cloudflared/<隧道UUID>.json
protocol: h2mux
ingress:
  # 第一个网站，连接到本地的80端口
  - hostname: <域名1.com>
    service: http://localhost:80
  # 第二个网站，https协议，连接到本地的443端口，禁用证书校验（用于自签名SSL证书）
  - hostname: <域名2.com>
    service: https://127.0.0.1:443
    originRequest:
      noTLSVerify: true
      originServerName: <域名2.com>
  # 第三个网站，8012端口，泛域名
  - hostname: <*.域名3.com>
    service: http://localhost:8012
  # 第四个，反代MySQL sock服务
  - hostname: <mysql.域名4.com>
    service: unix:/tmp/mysql.sock
  # 第五个，反代SSH服务
  - hostname: <ssh.域名5.com>
    service: ssh://localhost:22
  - service: http_status:404
```
ref: https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/configure-tunnels/local-management/configuration-file/#origin-configuration

test
```
cloudflared --loglevel debug --transport-loglevel warn --config ~/.cloudflared/config.yml tunnel run [tunnel_UUID]
```

create system service
```
cloudflared service install
systemctl start cloudflared
systemctl status cloudflared
```

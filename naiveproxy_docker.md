## Get this image
```
docker pull pocat/naiveproxy
docker pull pocat/naiveproxy:client
```
## create a directory
```
mkdir -p /etc/naiveproxy
```
## copy CA 
```
git clone https://github.com/kpzhao/fqqq.git
cp -r ~/fqqq/CA /etc/naiveproxy/CA
```
## Modify the configuration
```
$ cat > /etc/naiveproxy/Caddyfile <<EOF
{
  admin off
  log {
      output file /var/log/caddy/access.log
      level INFO
  }
  servers :443 {
      protocol {
          experimental_http3
      }
  }
}

:80 {
  redir https://{host}{uri} permanent
}

:443, kpzhao.xyz
tls /etc/naiveproxy/CA/kpzhao.xyz.pem /etc/naiveproxy/CA/kpzhao.xyz.key
route {
  forward_proxy {
      basic_auth kpzhao 123456
      hide_ip
      hide_via
      probe_resistance rP7uSWkJpZzfg5g2Qr.com  #Modify to a secret domain, like password
  }
  file_server {
      root /var/www/html
  }
}
EOF
```
```
cat > /etc/naiveproxy/config.json <<EOF
{
  "listen": "socks://127.0.0.1:1080",
  "proxy": "https://kpzhao:123456@kpzhao.xyz"
}
EOF
```
## Start the service
```
sudo docker run --network host --name naiveproxy -v /etc/naiveproxy:/etc/naiveproxy -v /var/www/html:/var/www/html -v /var/log/caddy:/var/log/caddy -e PATH=/etc/naiveproxy/config.json --restart=always -d pocat/naiveproxy
sudo docker run --network host --name naiveproxy-client -v /etc/naiveproxy:/etc/naiveproxy --restart=always -d pocat/naiveproxy:client
```
## client
```
{
  "listen": "socks://127.0.0.1:1080",
  "proxy": "quic://kpzhao:123456@kpzhao.xyz", # quic or http
  "log": ""
}
```
## bbr
### 修改系统变量
```
echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf && echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
```
### 保存生效
```
sysctl -p
```
### 查看内核是否已开启BBR
```
sysctl net.ipv4.tcp_available_congestion_control
```
### 查看BBR是否启动
```
lsmod | grep bbr
```
## 后台运行
在程序目录下创建 ss-background.vbs
```
Set ws = CreateObject("Wscript.Shell")   
ws.run "naive.exe", vbhide
```
创建快捷方式放到 系统的启动栏中
以Win10为例，放到：

C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp

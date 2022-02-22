## Get this image
```
docker pull pocat/naiveproxy
docker pull pocat/naiveproxy:client
```
## create a directory
```
mkdir -p /etc/naiveproxy
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
tls 970956535@qq.com
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
"listen": "socks://127.0.0.1:1080"
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

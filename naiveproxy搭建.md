## SSL 证书申请
### 安装acme.sh
```
curl  https://get.acme.sh | sh
alias acme.sh=~/.acme.sh/acme.sh
```
### 切换到let's encrypt
```
acme.sh --set-default-ca --server letsencrypt
0 0 * * * "/home/user/.acme.sh"/acme.sh --cron --home "/home/user/.acme.sh" > /dev/null ##定时任务
~/.acme.sh/acme.sh  --upgrade  --auto-upgrade ##自动升级
```
###证书申请
```
acme.sh --issue  -d kpzhao.xyz --standalone
```
## 下载 GO 最新版
wget "https://go.dev/dl/$(curl https://go.dev/VERSION?m=text).linux-amd64.tar.gz"

## 解压至/usr/local/
tar -xf go*.linux-amd64.tar.gz -C /usr/local/

## 在 /etc/profile 中添加 Go 环境变量:
export GOROOT=/usr/local/go
export PATH=$GOROOT/bin:$PATH

## 使变量立即生效
source /etc/profile
## 创建caddyfile
```
vim /etc/caddy/Caddyfile
```
```
:443, kpzhao.xyz
route {
  forward_proxy {
    basic_auth kpzhao 123456
    hide_ip
    hide_via
    probe_resistance
  }
  #file_server { root /usr/share/caddy }
}
reverse_proxy http://kpzhao.xyz {
        header_up Host kpzhao.xyz
    }
```
## naiveproxy
vim /etc/naiveproxy/config.json
```
{
    "listen": "http://127.0.0.1:1080",
    "padding": "true"
}
```
./caddy start -config /etc/caddy/Caddyfile
./native config.json
## 客户端
```
vim /etc/naiveproxy/config.json
```
```
{
    "listen": "socks://127.0.0.1:1080",
    "proxy": "https://kpzhao:666999@domain.example",
    "padding": "true"
}
```

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

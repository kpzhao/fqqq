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

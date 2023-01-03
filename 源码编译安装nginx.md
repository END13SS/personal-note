先前若有dpkg安装的nginx环境，则`dpkg -r nginx`进行删除

```bash
systemctl unmask nginx
```

安装基础环境依赖（若需要全离线安装则提前准备好离线包）

```bash
sudo apt-get install gcc
sudo apt-get install libpcre3 libpcre3-dev
sudo apt-get install zlib1g-dev
sudo apt-get install openssl libssl-dev
```

准备好源码包`nginx-1.23.2.tar.gz`

示例扔到/opt目录

```bash
cd /opt/
tar -zxvf nginx-1.23.2.tar.gz
cd nginx-1.23.2/
ls
mkdir /var/temp/nginx -p
```

加入依赖配置

```bash
./configure     --prefix=/usr/local/nginx     --pid-path=/var/run/nginx/nginx.pid     --lock-path=/var/lock/nginx.lock     --error-log-path=/var/log/nginx/error.log     --http-log-path=/var/log/nginx/access.log     --with-http_gzip_static_module     --http-client-body-temp-path=/var/temp/nginx/client     --http-proxy-temp-path=/var/temp/nginx/proxy     --http-fastcgi-temp-path=/var/temp/nginx/fastcgi     --http-uwsgi-temp-path=/var/temp/nginx/uwsgi     --http-scgi-temp-path=/var/temp/nginx/scgi  --with-http_stub_status_module --with-http_ssl_module
```

编译安装

```bash
make && make install
```

设置软链接

```bash
ln -s /usr/local/nginx/sbin/nginx /usr/local/bin/
```

查看版本

```bash
nginx -v
```

先不要急着启动nginx,若已启动则删除进程

```bash
ps -ef |grep nginx
pkill -9 nginx
```

将nginx加入systemctl管理

```bash
sudo vi /usr/lib/systemd/system/nginx.service
```

添加内容：

```bash
[Unit]
Description=nginx
After=network.target
  
[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true
  
[Install]
WantedBy=multi-user.target
```

启动服务

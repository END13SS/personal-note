**Install Zabbix repository**
```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu20.04_all.deb
dpkg -i zabbix-release_6.0-4+ubuntu20.04_all.deb
apt-get update
```

**安装Zabbix server，Web前端，agent**

```bash
apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```

**创建初始数据库**

在数据库主机上运行以下代码

```bash
mysql -uroot -p
password
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
```

导入初始架构和数据，系统将提示您输入新创建的密码。

```bash
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbixDisable log_bin_trust_function_creators option after importing database schema.
```

Disable log_bin_trust_function_creators option after importing database schema.

```bash
mysql -uroot -p
password
mysql> set global log_bin_trust_function_creators = 0;
mysql> quit;
```

**为Zabbix server配置数据库**

编辑配置文件 /etc/zabbix/zabbix_server.conf 

```bash
listen 8080;
server_name example.com;
```

**启动Zabbix server和agent进程**

启动Zabbix server和agent进程，并为它们设置开机自启：

```bash
systemctl restart zabbix-server zabbix-agent nginx php7.4-fpm
```

```bash
systemctl enable zabbix-server zabbix-agent nginx php7.4-fpm
```

前端web登录 http://server_ip:8080

```bash

账号：Admin

密码：zabbix
```

### 修改中文字体

```bash
cd /usr/share/zabbix/assets/fonts
```

下载字库文件

```bash
wget https://www.xxshell.com/download/sh/zabbix/ttf/msyh.ttf
```

替换文件内容

```bash
vim /usr/share/zabbix/include/defines.inc.php
```

```bash
define('ZBX_GRAPH_FONT_NAME',           'graphfont'); // font file name
define('ZBX_FONT_NAME', 'graphfont');
-----------------------------------------------------------------------------------
define('ZBX_GRAPH_FONT_NAME',           'msyh'); // font file name
define('ZBX_FONT_NAME', 'msyh');
```

```bash
sed -i "s/graphfont/[要替换的文件名]/" /usr/share/zabbix/include/defines.inc.php
sed -i "s/graphfont/msyh/" /usr/share/zabbix/include/defines.inc.php   
#命令示例，替换为msyh，注意这个地方的文件名是不加.ttf
#可以使用sed命令一键替换，替换完成刷新zabbix页面
```

## ubuntu agent2部署

[下载Zabbix](https://www.zabbix.com/cn/download?zabbix=6.0&os_distribution=ubuntu&os_version=20.04&components=agent_2&db=&ws=)

**Install Zabbix repository**

```bash
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu20.04_all.deb
dpkg -i zabbix-release_6.0-4+ubuntu20.04_all.deb
apt-get update
```

**Install Zabbix agent2**

```bash
apt install zabbix-agent2 zabbix-agent2-plugin-*
```

**Start Zabbix agent2 process**

Start Zabbix agent2 process and make it start at system boot.

```bash
systemctl restart zabbix-agent2
systemctl enable zabbix-agent2
```

## windows agent部署

[](https://cdn.zabbix.com/zabbix/binaries/stable/6.0/6.0.0/zabbix_agent-6.0.0-windows-amd64.zip)

[https://www.zabbix.com/cn/download_agents](https://www.zabbix.com/cn/download_agents)

修改配置文件后，关闭防火墙，允许防火墙通过zabbix_agentd.exe

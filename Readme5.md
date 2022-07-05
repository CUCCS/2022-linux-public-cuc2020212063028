## 实验环境
- 主机: Ubuntu 20.04 LTS
- 客机:windows10,Ubuntu 20.04.2 LTS

## 基本要求
- 在一台主机（虚拟机）上同时配置Nginx和VeryNginx
	- VeryNginx作为本次实验的Web App的反向代理服务器和WAF
	- PHP-FPM进程的反向代理配置在nginx服务器上，VeryNginx服务器不直接配置Web站点服务
- 使用Wordpress搭建的站点对外提供访问的地址为： http://wp.sec.cuc.edu.cn
- 使用Damn Vulnerable Web Application (DVWA)搭建的站点对外提供访问的地址为： http://dvwa.sec.cuc.edu.cn

## 安全加固要求
- 使用IP地址方式均无法访问上述任意站点，并向访客展示自定义的友好错误提示信息页面-1
- Damn Vulnerable Web Application (DVWA)只允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-2
- 在不升级Wordpress版本的情况下，通过定制VeryNginx的访问控制策略规则，热修复WordPress < 4.7.1 - Username Enumeration
- 通过配置VeryNginx的Filter规则实现对Damn Vulnerable Web Application (DVWA)的SQL注入实验在低安全等级条件下进行防护

## VeryNginx配置要求
- VeryNginx的Web管理页面仅允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-3
- 通过定制VeryNginx的访问控制策略规则实现：
- 限制DVWA站点的单IP访问速率为每秒请求数 < 50
- 限制Wordpress站点的单IP访问速率为每秒请求数 < 20
- 超过访问频率限制的请求直接返回自定义错误提示信息页面-4
- 禁止curl访问

## 安装配置步骤
更改客机上的Hosts文件
192.168.56.106 vn.sec.cuc.edu.cn  
192.168.56.106 dvwa.sec.cuc.edu.cn
192.168.56.106 wp.sec.cuc.edu.cn
## Nginx安装&配置
```
# 安装
sudo apt update
sudo apt -y install nginx
# Nginx默认为80端口，这里将Nginx的监听端口改为8080
sudo sed -i 's/80/8080/' /etc/nginx/sites-available/default
sudo systemtcl reload nginx
```
然后客机就可以访问192.168.56.106:8080了。



## VeryNginx 安装
```
# 安装依赖
sudo apt -y install gcc make python3 libssl-dev libssl1.1 libpcre3-dev unzip zlib1g-dev 
git clone https://github.com/alexazhou/VeryNginx.git
sudo python3 install.py install 
sudo adduser nginx
# 修改权限
chmod -R 777 /opt/verynginx/verynginx/configs
# 启动
sudo/opt/verynginx/openresty/nginx/sbin/nginx

```
然后就可以访问vn.sec.cuc.edu.cn/verynginx/index.html了

## wordpress 安装
```
sudo apt -y install mysql-server php-fpm php-mysql
# 安装扩展
sudo apt install php-curl php-gd php-intl php-mbstring php-soap php-xml php-xmlrpc php-zip
# 建立根目录
sudo mkdir /var/www/wp.sec.cuc.edu.cn
sudo chown -R www-data:www-data  /var/www/wp.sec.cuc.edu.cn
# 写入配置文件
sudo tee /etc/nginx/sites-available/wp.sec.cuc.edu.cn << EOF
server {
    listen 8081;
    server_name wp.sec.cuc.edu.cn.sec.cuc.edu.cn www.wp.sec.cuc.edu.cn.sec.cuc.edu.cn;
    root /var/www/wp.sec.cuc.edu.cn.sec.cuc.edu.cn;

    index index.html index.htm index.php;

    location / {
        #try_files $uri $uri/ =404;
       try_files $uri $uri/ /index.php$is_args$args;
    }

    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt { log_not_found off; access_log off; allow all; }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
EOF
# 创建软链接
sudo ln -s /etc/nginx/sites-available/wp.sec.cuc.edu.cn /etc/nginx/sites-enabled/ 
sudo unlink /etc/nginx/sites-enabled/default
# check
sudo nginx -t
# 下载安装包
sudo wget https://wordpress.org/wordpress-4.7.zip
unzip wordpress-4.7.zip
cp wordpress/wp-config-sample.php wordpress/wp-config.php
# 修改配置文件，写入数据库信息
sed -i 's/database_name_here/wp_db/' wp-config.php
sed -i 's/username_here/wordpress/' wp-config.php
sed -i 's/password_here/wordpress/' wp-config.php
# 追加
sed -i  "$ a define( 'FS_METHOD', 'direct' );" wordpress/wp-config.php
# 增加一行，禁止wordpress自动更新
sed -i "$ a define( 'WP_AUTO_UPDATE_CORE', false );" wordpress/wp-config.php
# 将其移动到目录下
sudo cp -a /tmp/wordpress/. /var/www/wp.sec.cuc.edu.cn 
sudo chown -R www-data:www-data /var/www/wp.sec.cuc.edu.cn 
# 创建wordpress 数据库
sudo mysql
CREATE DATABASE wp_db;
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'wordpress';
GRANT ALL ON wp_db.* TO 'wordpress'@'localhost';
exit
# check
sudo nginx -t
sudo systemctl restart php7.4-fpm
```

## DVWA 搭建
与Wordpress步骤类似
```
git clone https://github.com/ethicalhack3r/DVWA
cp DVWA/config/config.inc.php.dist DVWA/config/config.inc.php 
sudo cp -a DVWA/. /var/www/dvwa.sec.cuc.edu.cn
sudo chown www-data:www-data  /var/www/dvwa.sec.cuc.edu.cn
# 写入配置文件
sudo tee /etc/nginx/sites-available/dvwa.sec.cuc.edu.cn <<EOF
server {
    listen 8082;
    server_name dvwa.sec.cuc.edu.cn www.dvwa.sec.cuc.edu.cn;
    root /var/www/dvwa.sec.cuc.edu.cn;

    index index.html index.htm index.php;

    location / {
        #try_files $uri $uri/ =404;
       try_files $uri $uri/ /index.php$is_args$args;
    }

    location = /favicon.ico { log_not_found off; access_log off; }
    location = /robots.txt { log_not_found off; access_log off; allow all; }
    location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
        expires max;
        log_not_found off;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
EOF
# 创建数据库
sudo mysql
CREATE DATABASE dvwa DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'p@ssw0rd';
GRANT ALL ON dvwa.* TO 'dvwa'@'localhost';
exit
# 创建软链接
sudo ln -s /etc/nginx/sites-available/dvwa.sec.cuc.edu.cn /etc/nginx/sites-enabled/
# 检查
sudo nginx -t
sudo systemctl reload nginx
```
---
title: Docker下安装nginx和php
date: 2022-01-10 12:36:21
tags:
categories:
---
记录用docker安装nginx以及php
<!--more--> 
# nginx
```
docker run  --name nginx -d -p 80:80 -v /etc/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /etc/nginx/log:/var/log/nginx  -v /etc/nginx/html:/usr/share/nginx/html  nginx
```
可以把项目放在html文件夹内

# php
因为9000端口给了portainer，所以宿主机端口改成9002
```
docker run -d -p 9002:9000 --name php -v /etc/nginx/html:/usr/share/nginx/www php:fpm
```
另外，需要查看docker内运行的ip地址，把nginx中的default.conf的php部分改成下面格式，172.17.0.4是docker下的ip地址
```
    location ~ \.php$ {
    fastcgi_pass   172.17.0.4:9000; 
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/www$fastcgi_script_name;
    fastcgi_param  SCRIPT_NAME      $fastcgi_script_name;
    include        fastcgi_params;
    }

```
需要注意的是，docker内部的IP是可能变化的，如果发现你的php项目无法识别，请检查一下docker内IP，并重新编辑上面的文件
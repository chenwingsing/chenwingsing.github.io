---
title: Docker下安装nginx和php
date: 2022-01-10 12:36:21
tags:
categories:
---
记录用docker安装nginx以及php
<!--more--> 
# nginx 
首先要创建一个nginx，不挂载任何目录，主要目的是为了先拷贝里面生成的文件
```
docker run  --name nginx -d -p 80:80 nginx
```
然后把文件复制出来，注意这个bae只是一个例子而已，需要你docker ps查询你的id是什么，前面路径是docker下的，后面是你主机下的路径
```
docker cp bae:/etc/nginx/nginx.conf /etc/nginx/conf/
docker cp bae:/etc/nginx/conf.d /etc/nginx/conf/
docker cp bae:/usr/share/nginx/html/ /etc/nginx/html/
docker cp bae:/var/log/nginx/ /etc/nginx/log/
注：docker cp dbc 中的 "bae" 为容器ID前缀，只要唯一就好了，用docker ps查询
```
然后删除nginx镜像，重新创建一个
```
docker run  --name nginx -d -p 80:80 -v /etc/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /etc/nginx/conf/conf.d/default.conf:/etc/nginx/conf.d/default.conf  -v /etc/nginx/log:/var/log/nginx  -v /etc/nginx/html:/usr/share/nginx/html  nginx
```

可以把项目放在html文件夹内

# php
因为9000端口给了portainer，所以宿主机端口改成9002
```
docker run -d -p 9002:9000 --name php -v /etc/nginx/html:/usr/share/nginx/www php:fpm
```
另外，需要查看docker内运行的ip地址，把nginx中的default.conf的php部分改成下面格式，172.17.0.4是docker下的ip地址
```
vim /etc/nginx/conf.d/default.conf
```
```
location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm index.php;
}


location ~ \.php$ {
    fastcgi_pass   172.17.0.4:9000; 
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/www$fastcgi_script_name;
    fastcgi_param  SCRIPT_NAME      $fastcgi_script_name;
    include        fastcgi_params;
}
```
需要注意的是，docker内部的IP是可能变化的，如果发现你的php项目无法识别，请检查一下docker内IP，并重新编辑上面的文件


# 测试
写一个index.php文件放入html文件夹中
```php
<?php
echo phpinfo();
?>
```
---
title: 树莓派HomeAssistant系列（零）Docker安装
date: 2022-01-01 11:25:55
tags:
categories: "HomeAassistant"
---
HomeAassistant系列
<!--more-->
设备：树莓派4b 4G版本

系统：官方32位界面系统

HomeAssistant一句话介绍：all in one

写这个系列的原因：利他思维，踩坑分析，没人看就当自己做笔记。

废话少说，直接上代码！

# 1.安装Docker
```
sudo curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

# 2.安装portainer2.x版本(可选，可视化管理Docker镜像太方便了)
```
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock --restart=always --name portainer portainer/portainer-ce
然后打开http://localhost:9000/即可。
```

# 3.安装HomeAssistant
需要注意，我创建了一个文件夹homeassistant，路径是/home/pi/homeassistant，这里根据你的目录来修改即可。-v代表挂载文件，这是是为了方便修改镜像中的配置文件，然后我们就可以直接在宿主机上修改配置，不需要进入到容器里面。
```
docker run -d \
  --name homeassistant \
  -p 8123:8123 \
  --privileged \
  --restart=unless-stopped \
  -e TZ=Asia/Shanghai \
  -v /home/pi/homeassistant:/config \
  --network=host \
  ghcr.io/home-assistant/raspberrypi4-homeassistant:stable
```
然后打开http://localhost:8123/即可。

# 4.更新HomeAssistant
首先把最新的镜像下载完毕
```
docker pull ghcr.io/home-assistant/raspberrypi4-homeassistant:stable
```
然后删除之前的旧镜像和旧容器（在portainer操作即可），再重新继续执行本文的3.安装HomeAssistant中的命令即可。

# 5.Other
tips:这里还有个问题就是下载稍微比较慢，可以更新docker的下载源，我用的是网易的源，这里不再叙述，大家可以查资料怎么修改下载源。

附上HomeAssistant手机客户端下载官方链接：

[安卓APP下载地址](https://github.com/home-assistant/android/releases)

[IOS下载地址](https://apps.apple.com/cn/app/home-assistant/id1099568401)

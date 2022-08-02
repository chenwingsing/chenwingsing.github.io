---
title: 树莓派dashboard
date: 2020-03-20 20:22:08
tags: [dishboard]
categories: "捣鼓树莓派"
---
折腾了一下午，总算把nginx+php（dashboard要用到）装好了，推荐更换国内源，这样安装软件更加快速。远程桌面用window自带的挺方便，不用桌面的话先打开SSH然后用putty登录。  
Dashboard一个特别不错的开源项目，可以查看pi的温度，内存各种运行情况，强烈推荐使用。
把项目放在nginx的html目录下即可。
```
sudo apt-get install git
cd /etc/nginx/html
sudo git clone https://github.com/nxez/pi-dashboard.git
sudo chown -R  pi-dashboard
```
![dashboard](/images/pi/dashboard.png)
---
title: 树莓派HomeAssistant系列（四）NodeRed安装配置
date: 2022-04-01 09:06:07
tags:
categories: "HomeAassistant"
---
HomeAassistant系列
<!--more-->
Nodered是为了方便实现自动化的一个工具，所以用在HA上很有必要。
## 1.新建目录nodered（用于映射docker中的nodered）
```
mkdir /home/pi/nodered
```
## 2.docker安装nodered（注意里面的有刚才创建的路径/home/pi/nodered，如果你是其他路径，请修改）
```
sudo docker run -it -d -p 1880:1880 --name=nodered --restart=always --user=root --net=host -v /home/pi/nodered:/data -e TZ=Asia/Shanghai nodered/node-red
```
## 3.打开http://localhost:1880/进入nodered配置相关信息
![](/images/ha/4/1.png)
搜索node-red-contrib-home-assistant-websocket并安装
![](/images/ha/4/2.png)
此时左边栏会出现home-assistant相关的节点，随便拉一个出来先
![](/images/ha/4/3.png)
然后鼠标左键编辑这个节点，会出现这个界面（因为我已经配置好才出现HomeAssistant），选择添加新的server节点并编辑。
![](/images/ha/4/4.png)
然后编辑好红框的信息，BaseUrl就是你的HA地址。
![](/images/ha/4/5.png)
Access Token在HA的这里获取，点击创建令牌，然后复制令牌到Access Token，并点击更新。
![](/images/ha/4/6.png)
最后请部署！请部署！请部署！全面在工作区部署所有内容！！！！！
![](/images/ha/4/7.png)
然后我们可以找到HA中的实体。
![](/images/ha/4/8.png)
## 4.HACS搜索nodered并下载，注意下载后要重启HA，然后在集成中安装

## 5.HA侧边栏加入nodered
在configuration.yaml中填写以下信息
```
panel_iframe:
 nodered:
   title: 'Node-Red'
   icon: 'mdi:shuffle-variant'
   #填写node-red的地址
   url: 'http://192.168.31.134:1880/'
```
全部安装配置大功告成！
## 6.初体验nodered
现在我要创建一个自动化，当实时室外温度小于18°时，我的小爱音箱自动播报：天气冷了，记得加衣服。反之，如果大于等于18°，则播报：天气热了，记得脱衣服。
ps：需要安装好和风天气和小爱音箱
下面这是总览图
整体逻辑是首先检测到实体的状态，然后进行判断，最后做出相应动作
![](/images/ha/4/9.jpg)
室外温度状态设置，红框表示我设置的，其他地方是默认，因为图片太长，分两张上传
![](/images/ha/4/10.jpg)
![](/images/ha/4/11.jpg)
当前实体状态，可以用来作为判断条件，这里是写室外温度小于28度，注意这里还没进行判断
![](/images/ha/4/12.jpg)
那么是怎么判断的，我们看这个图，橙色小点写if state is true，我们上面那张图写的是温度小于28度，也就是当这个条件是成立的，那么就是true
![](/images/ha/4/13.jpg)
然后再看下面这个橙色点，写的是if state is false，对应成的话就是当温度不小于18度，这时候状态就是false，因为我们一开始设置的状态就是低于18度，所以你如果高于或者等于18度，这个状态就不成立了，不成立的意思就是false
![](/images/ha/4/14.jpg)
触发服务：这个是对应低于18度的服务，其中红框中的信息，你只要搜索相应字母就会显示出来（只要你配置好，他就会搜索到你HA中的信息），然后点击蓝色框框的load_example_data，绿色框框就会马上给你一个模板，然后输入对应信息即可，反之另外一个服务只需要复制一个，然后修改下对应数据即可
![](/images/ha/4/15.jpg)
最后把这几个服务拉起来连接，然后部署即可，这时候在HA中就会有这个实体！
![](/images/ha/4/16.jpg)
---
title: 树莓派HomeAssistant系列（三）天气，邮箱，RSS订阅，自动化(每日天气邮件提醒，RSS通知)
date: 2022-03-18 11:07:34
tags:
categories: "HomeAassistant"
---
HomeAassistant系列
<!--more-->

# 1.天气设置
提供两种天气设置，任君选择，也可以都要哦
## 1.1彩云天气设置
### 1.1.1集成中搜索colorfulcloud并安装。
### 1.1.2彩云天气申请api
https://dashboard.caiyunapp.com/v1/token/
### 1.1.3使用配套的样式来展示天气
在HACS的前端中搜索Colorfulclouds Weather Card下载。
### 1.1.4配置天气
输入好你的api，居住地方所在的经纬度即可。
当然，如果你在configuration.yaml中提前填入你的经纬度，彩云天气会自动获取。
```
homeassistant:
  name: Home
  latitude: xxxx
  longitude: xxx
  time_zone: "Asia/Shanghai"
```
## 1.2和风天气设置
首先非常感谢这个作者的开源，链接如下：
https://github.com/morestart/HeWeather
### 1.2.1和风天气申请api
### 1.2.2下载集成到HA中
打开你HA所在路径，然后在custom_components中创建文件夹HeWeather
```

mkdir -p custom_components/HeWeather
```
使用命令下载集成
```
cd custom_components/HeWeather/
curl -O https://raw.githubusercontent.com/morestart/HeWeather/More-than-0.63/sensor.py
curl -O https://raw.githubusercontent.com/morestart/HeWeather/More-than-0.63/manifest.json
```
配置天气，在configuration.yaml中填入
```
sensor:
  - platform: HeWeather
    city: auto_ip 或者 填写城市名称 eg（北京，beijing）
    appkey: 你的密钥（即API）
    options:
      - fl
      - tmp
      - cond_txt
      - wind_spd
      - hum
      - pcpn
      - pres
      - vis
      - wind_sc
      - aqi
      - main
      - qlty
      - pm10
      - pm25
      - comf
      - cw
      - drsg
      - flu
      - sport
      - trav
      - uv
      - wind_dir
      - tmp_max
      - tmp_min
      - pop
```
重启HA即可
# 2.邮箱设置
## 2.1在configuration.yaml设置如下信息，以QQ邮箱为例
```
notify:
  - name: "send_email"
    platform: smtp
    server: "smtp.qq.com"
    port: 465
    timeout: 15
    sender: "xx@qq.com"
    encryption: tls
    username: "xx@qq.com"
    password: xx
    recipient:
      - "xx@qq.com"
    sender_name: "智能家庭助手"
```
注意的点：
1.这是用SMTP来设置的邮箱提醒，需要确保你的邮箱开启SMTP服务
2.server，不同邮箱是不一样的，比如163邮箱是smtp.163.com
3.port，一般是465，采用是SMTP SSL
4.password，不是你的邮箱密码！是SMTP授权码！
5.username和recipient就是发送者和接受者的邮箱，自己发给自己就填一样的。
# 3.RSS订阅
在configuration.yaml设置如下信息
```
feedreader:
  urls:#订阅的网址，可以订阅多个RSS
    - xxxx.xml
    - xxxx.xml
  scan_interval:
    minutes: 1   #扫描时间间隔
  max_entries: 10
```
# 4.自动化（每日天气邮件提醒，RSS通知）
以下配置均在automations.yaml中填写(请大家注意，下面参数前面的空格不要丢掉，如-alias前面有空格)
## 4.1每天日出的时候发送天气到邮件
需要设置和风天气（1.2），以及设置你们家的经纬度信息来判断日出（1.1.4的configuration.yaml设置）
```
  - alias: 每日天气邮件提醒
    trigger:
      platform: sun
      event: sunrise
      offset: '0'
    action:
      service: notify.send_email
      data:
        message: 早上好鸭，小陈同学!现在外面的温度是{{states('sensor.shi_shi_shi_wai_wen_du')}}°C，今日最低温度{{states('sensor.jin_ri_zui_di_wen_du')}}°C，最高温度{{states('sensor.jin_ri_zui_gao_wen_du')}}°C。今天{{state_attr('sensor.chuan_yi_zhi_shu','生活建议') }}今天也要加油呀！！ 
```
上面有两点注意，和风天气的传感器有状态和属性，比如如果你要提取状态，就写如下信息，下面是实时室外温度
```
{{states('sensor.shi_shi_shi_wai_wen_du')}}
提取属性需要填写如下信息，一般来说，属性有很多参数，比如可能有生活建议，更新时间，friendly_name，填写你需要的属性即可提取
{{state_attr('sensor.chuan_yi_zhi_shu','生活建议') }}
```
## 4.2RSS订阅提醒
需要先提前设置好RSS订阅
```
  - alias: "RSS订阅更新"
    trigger:
      platform: event
      event_type: feedreader
    action:
      service: persistent_notification.create
      data:
        title: '{{trigger.event.data.link}}'
        message: "RSS更新啦 - {{ as_timestamp(now()) | timestamp_custom('%I:%M:%S %p %d%b%Y', true) }}"
        notification_id: "{{ trigger.event.data.title }}"
```
这里需要注意一点，就是上面的自动化信息我都是在automations.yaml，然后的话在配置-场景自动化中也能手动创建自动化，但是如果在这里创建了新的自动化，会显示不出来，可能与automations.yaml的冲突。
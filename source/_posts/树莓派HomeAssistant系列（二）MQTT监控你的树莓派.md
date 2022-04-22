---
title: 树莓派HomeAssistant系列（二）MQTT监控你的树莓派
date: 2022-03-05 11:01:56
tags: [MQTT]
categories: "HomeAassistant"
---
HomeAassistant系列
<!--more-->
# 1. 安装MQTT
## 1.1 使用Docker安装
首先我们要创建要3个文件夹，用来等下挂载容器用的。
```
sudo mkdir -p /mosquitto/config
sudo mkdir -p /mosquitto/data
sudo mkdir -p /mosquitto/log
```
## 1.2 创建好配置文件
```
sudo vim /mosquitto/config/mosquitto.conf
```
## 1.3 写入内容并保存
这里就是遇到终极大坑，很多博主都是写前三行，包括官方文档确实也是这样，但是！官方升级2.x版本后，如果你没有设置账号密码，是不允许匿名连接MQTT的，害我找了2天资料才发现，也就是allow_anonymous true这一行非常关键，因为当时急着用这个功能，就不创建账号密码，结果折腾死我。
```
persistence true
persistence_location /mosquitto/data/
log_dest file /mosquitto/log/mosquitto.log
allow_anonymous true
listener 1883
```
## 1.4 授权文件运行
```
chmod -R 755 /mosquitto
```
## 1.5 安装MQTT
还有需要说明一点，MQTT其实有好几家做的，然后eclipse-mosquitto是比较欢迎的。
```
docker run -it --name=mosquitto --privileged \
-p 1883:1883 -p 9001:9001 \
-v /mosquitto/config/mosquitto.conf:/mosquitto/config/mosquitto.conf \
-v /mosquitto/data:/mosquitto/data \
-v /mosquitto/log:/mosquitto/log \
eclipse-mosquitto
```
# 2. 验证MQTT是否启动成功
## 2.1 配置MQTT
在configuration.yaml中加入以下信息来添加MQTT：
```
mqtt:
  # MQTT Broker的IP地址或者域名
  broker: 192.168.1.104
  # MQTT Broker的端口号，缺省为1883
  port: 1883
  # 配置自动发现
  discovery: true
  # 自动发现使用的主题位置前缀，缺省为homeassistant
  discovery_prefix: homeassistant
```

# 3.监控树莓派
## 3.1 安装好一些必备包
```
sudo apt-get install git python3 python3-pip python3-tzlocal python3-sdnotify python3-colorama python3-unidecode python3-paho-mqtt
```
## 3.2 下载源文件
```
sudo git clone https://github.com/ironsheep/RPi-Reporter-MQTT2HA-Daemon.git /opt/RPi-Reporter-MQTT2HA-Daemon
cd /opt/RPi-Reporter-MQTT2HA-Daemon
sudo pip3 install -r requirements.txt
```
## 3.3 复制并修改配置文件
```
sudo cp /opt/RPi-Reporter-MQTT2HA-Daemon/config.{ini.dist,ini}
sudo vim /opt/RPi-Reporter-MQTT2HA-Daemon/config.ini
```
## 3.4 我的配置信息
```
# Configuration file for RPi-Reporter-MQTT2HA-Daemon
# Source: https://github.com/ironsheep/RPi-Reporter-MQTT2HA-Daemon
#
# Uncomment and adapt all settings as needed.
# Some settings can be configured by environment variables.
# If an env variable is set, it takes precedence over settings in this file

[Daemon]

# Enable or Disable an endless execution loop (Default: true)
enabled = true

# This script reports RPi values at a fixed interval in minutes [2-30], [Default: 5]
interval_in_minutes = 5

# default domain to use when hostname -f doesn't return a proper fqdn
#fallback_domain = home

[MQTT]

# The hostname or IP address of the MQTT broker to connect to (Default: localhost)
# Also read from the MQTT_HOSTNAME environment variable
hostname = localhost

# The TCP port the MQTT broker is listening on (Default: 1883)
# Also read from the MQTT_PORT environment variable
port = 1883

# Maximum period in seconds between ping messages to the broker. (Default: 60)
keepalive = 60

# by default Home Assistant listens to the /homeassistant but it can be changed for a given installation
#  likewise, by default this script advertises on the same default topic. If you use a different 
#  discovery prefix then specify yours here.  [default: homeassistant]
discovery_prefix = homeassistant

# NOTE: The MQTT topic used for this device is constructed as:
#  {base_topic}/{sensor_name}
#
# The MQTT base topic under which to publish the Raspberry Pi sensor data topics.
base_topic = home/nodes

# The MQTT name for this Raspberry Pi as a sensor
sensor_name = rpi-localhost


# The MQTT broker authentification credentials (Default: no authentication)
# Will also read from MQTT_USERNAME and MQTT_PASSWORD environment variables
#username = user
#password = pwd123

# Enable TLS/SSL on the connection
#tls = false

# Path to CA Certificate file to verify host
#tls_ca_cert =

# Path to TLS client auth key file
#tls_keyfile =

# Path to TLS client auth certificate file
#tls_certfile =
```
## 3.5 测试
```
python3 /opt/RPi-Reporter-MQTT2HA-Daemon/ISP-RPi-mqtt-daemon.py
```
## 3.6 设置守护程序帐户以允许访问温度值。
```
groups daemon
sudo usermod daemon -a -G video
groups daemon
```
## 3.7 设置程序开机后台运行，至此我们已经全部设置完毕了！
```
sudo ln -s /opt/RPi-Reporter-MQTT2HA-Daemon/isp-rpi-reporter.service /etc/systemd/system/isp-rpi-reporter.service
sudo systemctl daemon-reload
sudo systemctl enable isp-rpi-reporter.service
sudo systemctl start isp-rpi-reporter.service
sudo systemctl status isp-rpi-reporter.service
```
## 3.8 后期更新软件
```
cd /opt/RPi-Reporter-MQTT2HA-Daemon
sudo systemctl stop isp-rpi-reporter.service
sudo git pull
sudo systemctl daemon-reload
sudo systemctl start isp-rpi-reporter.service
systemctl status isp-rpi-reporter.service
```
# 4 高颜值外表卡片设置
## 4.1 下载地址
https://github.com/ironsheep/lovelace-rpi-monitor-card
## 4.2 卡片配置代码
```
type: entities
entities:
  - sensor.rpi_cpu_use_raspberrypi
  - sensor.rpi_monitor_raspberrypi
  - sensor.rpi_temp_raspberrypi
  - sensor.rpi_used_raspberrypi
title: RPi-raspberrypi
state_color: true
```
```
type: custom:rpi-monitor-card
entity: sensor.rpi_monitor_raspberrypi
card_style: full
temp_scale: C
fs_severity:
  - color: Green
    from: 0
    to: 25
  - color: Orange
    from: 26
    to: 50
  - color: Red
    from: 51
    to: 100
temp_severity:
  - color: Green
    from: 0
    to: 59
  - color: Orange
    from: 60
    to: 79
  - color: Red
    from: 80
    to: 100
```
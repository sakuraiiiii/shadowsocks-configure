# 搭建shadowsocks的相关总结（Centos7+及Ubuntu16+）
* 登录vps
* 系统：

	* 如果系统为Centos7+：
	
	1.安装epel扩展源：```yum -y install epel-release```
	
	2.安装```pip```：```yum -y install python-pip```
	
	3.清除```cache```：```yum clean all```
	
	4.```pip```安装```python```版本的```shadowsocks```：```pip install shadowsocks```
	
	5.配置文件：```sudo vim /etc/shadowsocks.json```
	
	```json
	{
  		"server": "0.0.0.0",
 		"server_port": 8623,
  		"local_address": "127.0.0.1",
  		"local_port": 1080,
  		"password": "your password",
  		"timeout": 300,
  		"method": "aes-256-cfb",
  		"fast_open": false
	}
	```
	
	6.开机自启动：```vim /etc/systemd/system/shadowsocks.service```:
	
	```
	[Unit]
	Description=Shadowsocks

	[Service]
	TimeoutStartSec=0
	ExecStart=/usr/bin/ssserver -c /etc/shadowsocks.json

	[Install]
	WantedBy=multi-user.target
	```
	
	7.启动```shadowsocks```服务：
	
	```
	systemctl enable shadowsocks
	systemctl start shadowsocks
	systemctl status shadowsocks -l  # 查看状态
	```
	
	8.防火墙设置：
	
	```
	firewall-cmd --query-port=80/tcp  # 查询端口
	firewall-cmd --zone=public --add-port=80/tcp --permanent  # 开启端口
	firewall-cmd --reload  # 重启防火墙
	systemctl status firewalld  # 查看firewalld状态，发现当前是dead状态，即防火墙未开启。
	systemctl start firewalld  # 开启防火墙，没有任何提示即开启成功。
	```
	
	* 如果系统为Ubuntu16+:
	
	1.更新软件源：```sudo apt-get update```
	
	2.安装```pip3```：```sudo apt-get install python3-pip```
	
	3.安装```shadowsocks```：```sudo pip3 install shadowsocks```
	
	4.编辑配置文件：```sudo vim /etc/shadowsocks.json```
	
	```json
	{
  		"server": "0.0.0.0",
 		"server_port": 8623,
  		"local_address": "127.0.0.1",
  		"local_port": 1080,
  		"password": "your password",
  		"timeout": 300,
  		"method": "aes-256-cfb",
  		"fast_open": false
	}
	```
	
	5.启动|关闭|重启 服务（以后台方式启动）：```sudo ssserver -c /etc/shadowsocks.json -d start|stop|restart```
	
	6.设置开机自启动：```sudo vi /etc/rc.local```，再```exit 0 ```之前添加```sudo ssserver -c /etc/shadowsocks/config.json -d start```
	
	7.设置防火墙：
	
	```
	sudo apt-get install ufw  # 安装ufw，可能系统已经安装
	```
	
	```
	# 开启了防火墙并随系统启动同时关闭所有外部对本机的访问（本机访问外部正常）。
 	sudo ufw enable
 	sudo ufw default deny
	```
	
	```
	sudo ufw status  # 查看防火墙状态
	```
	
	```
	sudo ufw disable  # 关闭防火墙
	```
	
	由于打开防火墙可能会ssh登陆不上vps，所以可以只打开设置的端口：
	
	```
	sudo ufw allow 80  # 允许外部访问80端口
	sudo ufw delete allow 80  # 禁止外部访问80端口
 	sudo ufw allow from 192.168.1.1  # 允许此IP访问所有的本机端口
 	sudo ufw deny proto tcp from 10.0.0.0/8 to 192.168.0.1 port 22  # 要拒绝所有的TCP流量从10.0.0.0/8 到192.168.0.1地址的22端口
	```

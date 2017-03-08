##monit
---
monit是轻量级的系统监控工具之一，它没有特别复杂的配置，分分钟即可完成安装使用。

####监控对象：

>系统资源状况 ( cpu,mem,diskspace......）

>文件系统 ( filestat,md5sum......)

>Network ( linkstat,bandwidth,ping test......)

>进程端口监控 ( process,port......) 

最后一项则是monit最强大的地方。


####功能

>邮件告警

>故障自愈

>页面展示

####安装

安装方法很简单，建议直接采用源安装，以centos为例：

`yum install monit`

####配置



<pre><code> 
set daemon 30

set mailserver smtp.qiye.163.com  ###设置邮箱

PORT 25

USERNAME "user@domain.com"

PASSWORD "password"

set mmonit http://monit:monit@192.168.1.90:8080/collector ###配置mmonit

set mail-format {  ###设置邮件格式

from: user@domain.com

subject: monit alert -- $EVENT $SERVICE

message: $EVENT Service $SERVICE

Date: $DATE

Action: $ACTION

Host: $HOST

Description: $DESCRIPTION

Your faithful employee,

Monit

}

set alert user@domain.com # receive all alerts ###设置告警邮箱

set httpd port 2812 and   ### 设置web接口

use address 192.168.1.90 

allow 192.168.100.0/24 

allow 192.168.1.90 

allow admin:monit

include /etc/monit.d/*

######## /etc/monit.d/mysqld.conf  
######## 一个监控实例
check process mysql with pidfile /usr/local/mysql/data/vm90.pid
    start program = "/etc/init.d/mysqld start"
    stop program = "/etc/init.d/mysqld stop"
    if failed
	unixsocket /tmp/mysql.sock
	protocol mysql username "monit" password "123456"
    then alert
</code></pre> 

此外还可以设置告警按条件发送，对web管理口启用https等。

####web管理接口

管理services，展示services状态信息

####mmoitor

可以通过一个http接口集中管理多个monit实例，但是需要付费购买lisence

####monit 命令行工具

集中管理services

`monit restart mysqld ###重启mysqld`















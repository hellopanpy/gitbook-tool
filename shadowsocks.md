##shadowsocks

shadowsocks可以说是目前最为强大的开源代理工具，支持各种平台：windows，linux(centos,ubuntu,openwrt)，MAC，Android......

下载地址：[shadowsocks](https://github.com/shadowsocks)

### 1.shadowsocks + SwitchyOmega

一般是在windows上配合chrome使用，SwitchyOmega是chrome插件，很方便管理代理设置，建议使用auto switch模式，能访问的网站就直接访问，打不开的插件会提示，直接点击添加至代理，简单粗暴。

####安装

下载最新版shadowsocks：[shadowsocks-windows](https://github.com/shadowsocks/shadowsocks-windows)

SwitchyOmega：chrome扩展程序添加

###2.shadowsocks + PAC

上述方法比较适合个人使用，如果在同一个局域网内共享使用，还是使用pac脚本代理方式，这种代理的基本原理是：每次http请求，都会有pac脚本判断，不需要代理的域名直接出去，需要代理的域名走代理。

<pre><code>
var direct ='DIRECT';
var proxy = 'PROXY 127.0.0.1:1080';
var domains = 'google.com';
if match(request_domain,domains) {
 return proxy;
}
else {
 return direct;
}
</code></pre>

pac文件可以放在本地，也可以放在webserver上，但是系统要识别pac文件才可以正常代理。

我是放在nginx上，conf/mime.types添加:

`application/x-ns-proxy-autoconfig   pac;`

指定pac文件资源类型

pac文件模板：[proxy.pac](https://github.com/hellopanpy/src/blob/master/proxy.pac)

可能要修改的地方：代理IP:port,代理域名，非代理域名

###3.shadowsocks + openwrt

对于移动网络做代理，我们可以使用openwrt的路由器配合shadowsocks。

####shadowsocks

`opkg update`

`opkg install shadowsocks`

`/usr/bin/ss-tunnel -s ${SERVER_IP} -p ${PORT} -l {LOACL_PORT} -m rc4-md5 -k ${PASS} -L 8.8.8.8:53 -f /tmp/run/ss-tunnel.pid`

`/usr/bin/ss-redir -s ${SERVER_IP} -p ${PORT} -b 0.0.0.0 -l {LOACL_PORT} -k ${PASS}  -m rc4-md5 -f /tmp/run/ss-redir.pid`

ss-tunnel转发dns解析请求到8.8.8.8，避免dns污染

ss-redir在本地启用透明代理

####dnsmasq

/etc/dnsmasq.d 添加如下配置文件

>1.China_domain.conf

`server=/baidu.com/114.114.114.114`

>2.Proxy.conf

`server=/#/127.0.0.1#5300`

####iptable

<pre><code>
##添加俩条自定义链
iptables -t nat -N SHADOWSOCKS
iptables -t nat -N SHADOWSOCKS_WHITELIST
##目标IP直接返回
iptables -t nat -A SHADOWSOCKS -d ${SERVER_IP} -j RETURN 
##局域网不走代理
iptables -t nat -A SHADOWSOCKS -d 192.168.0.0/16 -j RETURN
##国内不走代理
iptables -t nat -A SHADOWSOCKS_WHITELIST -d 1.0.1.0/24 -j MARK --set-mark 1
iptables -t nat -A SHADOWSOCKS -j SHADOWSOCKS_WHITELIST
iptables -t nat -A SHADOWSOCKS -m mark --mark 1 -j RETURN
##默认走代理
iptables -t nat -A SHADOWSOCKS -p tcp -j REDIRECT --to-ports {LOACL_PORT}
iptables -t nat -A PREROUTING -p tcp -j SHADOWSOCKS
</code></pre>

###文件

把上述过程写入启动脚本

dnsmasq：









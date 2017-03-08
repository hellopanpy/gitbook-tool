##zabbix 端口监控

####端口监控概述

主要通过本地端口探测来检测服务的运行状况，使用zabbix discovery功能来实现，检测的端口统一写在一个配置文件中，通过配置文件来检测监控的端口

####1.配置文件

<pre><code>
[hostname]
mysqld = 127.0.0.1:3306
php-fpm = 127.0.0.1:9000
nginx = 0.0.0.0:80

</code></pre>

 每个监控主机实例的格式：[tag] =  IP:port,tag为一个标识，监控中用不到

####2.监控脚本


<pre><code>
import commands
import socket
import json
import ConfigParser
from optparse import OptionParser

config_file = "/usr/local/zabbix/conf/monitor_port.conf"

class Monit_port:

    def __init__(self,config_file):

        self.config_file = config_file

    def get_hostname(self):

        return socket.gethostname()

    def get_sock_list(self):

        cp = ConfigParser.ConfigParser()
        try:
            cp.read(self.config_file)
        except IOError,e:
            print e
        port_list = [ item[1] for item in cp.items(self.get_hostname()) ]
        sock_list = [ port.split(':') for port in port_list ]
        return  sock_list

    def test_port(self,host,port,print_std=False):
        '''

        :return:
        '''
        command_str = 'nc -zv %s -w 3 %s' % (host,port)
        (exitstatus, outtext) = commands.getstatusoutput(command_str)
        if print_std:
            print outtext
        return exitstatus

if __name__ == "__main__":

    parser = OptionParser()
    parser.add_option("-p", "--port", type="string", help="monit port", dest="port", action="store")
    parser.add_option("-d", "--discovery", help="discovery json dump", dest="discovery", action="store_true")

    (options, args) = parser.parse_args()
    mp = Monit_port(config_file)

    if options.discovery and options.port:
        print "print only input one options!"
        exit(1)
    elif options.discovery:
        sock_list = mp.get_sock_list()
        port_list = [ sock[1] for sock in sock_list ]
        port_data = []
        for port in port_list:
            port_data += [{'{#PORT}':port}]
        print json.dumps({'data':port_data},sort_keys=True,indent=7,separators=(',',':'))
    elif options.port:
        sock_list = mp.get_sock_list()
        for sock in sock_list:
            if options.port in sock:
                host = sock[0]
                port = sock[1]
                print mp.test_port(host,port)
    else:
        print "invalid input!"
        exit(1)

</code></pre>

####3.zabbix_agentd 配置

`UserParameter=port.status.discovery,python /usr/local/zabbix/lib/monitor_port.py -d`
`UserParameter=port.status[*],python /usr/local/zabbix/lib/monitor_port.py -p $1`

####4.zabbix后台配置

1).添加一个template

>key：port.status.discovery

>Filters：{#PORT}


2).添加items：

>Name: port {#PORT} status 

>key: port.status[{#PORT}] 

3).添加trigger

>name: port {#PORT} status is not ok

>Expression: { Monitor Port Template:port.status[{#PORT}].last()}<>0

4).关联模板

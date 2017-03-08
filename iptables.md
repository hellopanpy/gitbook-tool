##iptable

Iptabels是与Linux内核集成的包过滤防火墙系统，几乎所有的linux发行版本都会包含Iptables的功能。如果 Linux 系统连接到因特网或 LAN、服务器或连接 LAN 和因特网的代理服务器， 则Iptables有利于在 Linux 系统上更好地控制 IP 信息包过滤和防火墙配置。
Iptables用于设置，维护和检查Linux内核中的IPv4包过滤规则的表。 可以定义几个不同的表。 每个表包含多个内置链，并且还可以包含用户定义的链。每个链是可以匹配一组分组的规则的列表。 每个规则指定如何处理匹配的数据包。 这被称为“目标”，其可以是跳到相同表中的用户定义的链。

####优点

1.iptables可以配置有状态的防火墙。有状态的防火墙能够指定并记住为发送或接收信息包所建立的连接的状态。

_防火墙可以从信息包的连接跟踪状态获得该信息。在决定新的信息包过滤时，防火墙所使用的这些状态信息可以增加其效率和速度。这里有四种有效状态，名称分别为 ESTABLISHED、INVALID、NEW和RELATED 。状态ESTABLISHED指出该信息包属于已建立的连接，该连接一直用于发送和接收信息包并且完全有效。NVALID状态指出该信息包与任何已知的流或连接都不相关联，它可能包含错误的数据或头。状态 NEW 意味着该信息包已经或将启动新的连接，或者它与尚未用于发送和接收信息包的连接相关联。最后， RELATED 表示该信息包正在启动新连接，以及它与已建立的连接相关联。_

2.配置功能强大。

3.iptables免费开源，对于大部分应用可以取代昂贵firewall解决方案。

####原理图


![数据包流向导图](/assets/20131023184402031.jpg)


>1.一个数据包进入网卡时，它首先进入PREROUTING链，内核根据数据包目的IP判断是否需要转发出去。

>2.如果数据包就是进入本机的，它就会沿着图向下移动，到达INPUT链。数据包到了INPUT链后，任何进程都会收到它。本机上运行的程序可以发送数据包，这些数据包会经 过OUTPUT链，然后到达POSTROUTING链输出。

>3.如果数据包是要转发出去的，且内核允许转发，数据包就会如图所示向右移动，经过 FORWARD链，然后到达POSTROUTING链输出。

####几个关键定义

1.表(table)：iptables内置了4个表，即raw表、filter表、nat表和mangle表 

>1) raw表
 只使用在PREROUTING链和OUTPUT链上,因为优先级最高，从而可以对收到的数据包在连接跟踪前进行处理。一但用户使用了RAW表,在某个链上,RAW表处理完后,将跳过NAT表和ip_conntrack处理,即不再做地址转换和数据包的链接跟踪处理了.

>2) filter表
 主要用于过滤数据包，该表根据系统管理员预定义的一组规则过滤符合条件的数据包。对于防火墙而言，主要利用在filter表中指定的规则来实现对数据包的过滤。Filter表是默认的表，如果没有指定哪个表，iptables就默认使用filter表来执行所有命令，filter表包含了INPUT链（处理进入的数据包），RORWARD链（处理转发的数据包），OUTPUT链（处理本地生成的数据包）在filter表中只能允许对数据包进行接受，丢弃的操作，而无法对数据包进行更改

>3) nat表
 主要用于网络地址转换NAT，该表可以实现一对一，一对多，多对多等NAT 工作，iptables就是使用该表实现共享上网的，NAT表包含了PREROUTING链（修改即将到来的数据包），POSTROUTING链（修改即将出去的数据包），OUTPUT链（修改路由之前本地生成的数据包）

>4) mangle表
 主要用于对指定数据包进行更改，在内核版本2.4.18 后的linux版本中该表包含的链为：INPUT链（处理进入的数据包），RORWARD链（处理转发的数据包），OUTPUT链（处理本地生成的数据包）POSTROUTING链（修改即将出去的数据包），PREROUTING链（修改即将到来的数据包）

优先顺序：Raw——mangle——nat——filter

2.链(chain)：五条链 PREROUNTING, INPUT, FORWARD, OUTPUT, POSTROUTING

>1） PREROUTING: 主机外报文进入位置，允许的表mangle, nat（目标地址转换，把本机地址转换为真正的目标机地址，通常指响应报文） 
>2） INPUT：报文进入本机用户空间位置，允许的表filter, mangle 
>3） OUTPUT：报文从本机用户空间出去的位置，允许filter, mangle, nat
>4） FOWARD：报文经过路由并且发觉不是本机决定转发但还不知道从哪个网卡出去，允许filter, mangle 
>5） POSTROUTING：报文经过路由被转发出去，允许mangle，nat（源地址转换，把原始地址转换为转发主机出口网卡地址）

3.规则(rules)

>规则（rules）其实就是网络管理员预定义的条件，规则一般的定义为“如果数据包头符合这样的条件，就这样处理这个数据包”。规则存储在内核空间的信息包过滤表中，这些规则分别指定了源地址、目的地址、传输协议（如TCP、UDP、ICMP）和服务类型（如HTTP、FTP和SMTP）等。当数据包与规则匹配时，iptables就根据规则所定义的方法来处理这些数据包，如放行（accept）、拒绝（reject）和丢弃（drop）等。配置防火墙的主要工作就是添加、修改和删除这些规则

####命令行使用

大致可归纳如下：

iptables 【-t table 】 【 rules动作 】 【 匹配 】 【 数据包动作 】

选项参数说明：

>table：

        应用的表raw，mangle，nat，filter

>rules动作:

        -P  --policy        <链名>  定义默认策略
        -L  --list          <链名>  查看iptables规则列表
        -A  --append        <链名>  在规则列表的最后增加1条规则
        -I  --insert        <链名>  在指定的位置插入1条规则
        -D  --delete        <链名>  从规则列表中删除1条规则
        -R  --replace       <链名>  替换规则列表中的某条规则
        -F  --flush         <链名>  删除表中所有规则
        -Z  --zero          <链名>  将表中数据包计数器和流量计数器归零
        -X  --delete-chain  <链名>  删除自定义链
        -v  --verbose       <链名>  与-L他命令一起使用显示更多更详细的信息

>匹配规则：

        -i --in-interface   <网络接口名>     指定数据包从哪个网络接口进入，
        -o --out-interface  <网络接口名>     指定数据包从哪个网络接口输出
        -p ---proto         <协议类型        指定数据包匹配的协议，如TCP、UDP和ICMP等
        -s --source         <源地址或子网>   指定数据包匹配的源地址
        -d --destination    <目的地址或子网> 指定数据包匹配的目的地址

        --sport             <源端口号>       指定数据包匹配的源端口号
        --dport             <目的端口号>     指定数据包匹配的目的端口号
        -m --match          <匹配的模块>      指定数据包规则所使用的过滤模块

常用的模块：state， limit ，multiport

>数据包动作

 -j 参数用来指定要进行的处理动作，常用的处理动作包括：ACCEPT、REJECT、DROP、REDIRECT、MASQUERADE、LOG、DNAT、SNAT、MIRROR、QUEUE、RETURN、MARK。

ACCEPT 将数据包放行，进行完此处理动作后，将不再比对其它规则，直接跳往下一个规则链（natostrouting）。

REJECT 拦阻该数据包，并传送数据包通知对方，可以传送的数据包有几个选择：ICMP port-unreachable、ICMP echo-reply 或是tcp-reset（这个数据包会要求对方关闭联机），进行完此处理动作后，将不再比对其它规则，直接 中断过滤程序。 范例如下：

`iptables -A FORWARD -p TCP --dport 22 -j REJECT --reject-with tcp-reset`

DROP 丢弃包不予处理，进行完此处理动作后，将不再比对其它规则，直接中断过滤程序。

REDIRECT 将包重新导向到另一个端口（PNAT），进行完此处理动作后，将会继续比对其它规则。 这个功能可以用来实作通透式porxy 或用来保护 web 服务器。例如：

`iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8080`

MASQUERADE 改写数据包来源 IP为防火墙 NIC IP，可以指定 port 对应的范围，进行完此处理动作后，直接跳往下一个规则（mangleostrouting）。这个功能与 SNAT 略有不同，当进行 IP 伪装时，不需指定要伪装成哪个 IP，IP 会从网卡直接读取，当使用拨号连接时，IP 通常是由 ISP 公司的 DHCP 服务器指派的，这个时候 MASQUERADE 特别有用。范例如下：linux基础

`iptables -t nat -A POSTROUTING -p TCP -j MASQUERADE --to-ports 1024-31000`

LOG 将封包相关讯息纪录在 /var/log 中，详细位置请查阅 /etc/syslog.conf 配置文件，进行完此处理动作后，将会继续比对其规则。例如：

`iptables -A INPUT -p tcp -j LOG --log-prefix "INPUT packets"`

SNAT 改写封包来源 IP 为某特定 IP 或 IP 范围，可以指定 port 对应的范围，进行完此处理动作后，将直接跳往下一个规则（mangleostrouting）。范例如下：

`iptables -t nat -A POSTROUTING -p tcp-o eth0 -j SNAT --to-source 194.236.50.155-194.236.50.160:1024-32000`

DNAT 改写封包目的地 IP 为某特定 IP 或 IP 范围，可以指定 port 对应的范围，进行完此处理动作后，将会直接跳往下一个规炼（filter:input 或 filter:forward）。范例如下：

`iptables -t nat -A PREROUTING -p tcp -d 15.45.23.67 --dport 80 -j DNAT --to-destination192.168.1.1-192.168.1.10:80-100`

MIRROR 镜像数据包，也就是将来源 IP 与目的地 IP 对调后，将数据包送回，进行完此处理动作后，将会中断过滤程序。

QUEUE 中断过滤程序，将数据包放入队列，交给其它程序处理。透过自行开发的处理程序，可以进行其它应用，例如：计算联机费.......等。

RETURN 结束在目前规则链中的过滤程序，返回主规则链继续过滤，如果把自订规则链看成是一个子程序，那么这个动作，就相当提早结束子程序并返回到主程序中。

MARK 将数据包标上某个代号，以便提供作为后续过滤的条件判断依据，进行完此处理动作后，将会继续比对其它规则。


####常用修改办法

`vim /etc/sysconfig/iptables`

`/etc/init.d/iptables restart`

或

`vim /etc/sysconfig/iptables`

`iptables -I `


####参考

[CSDN](http://blog.csdn.net/reyleon/article/details/12976341)

[Iptables](https://wiki.archlinux.org/index.php/Iptables)

[iptables-tutorial](https://www.frozentux.net/iptables-tutorial/cn/iptables-tutorial-cn-1.1.19.html)



















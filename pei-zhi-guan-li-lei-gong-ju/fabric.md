# Fabric

Fabric 是一个 Python \(2.5-2.7\) 的库和命令行工具，用来提高基于 SSH 的应用部署和系统管理效率。

更具体地说，Fabric 是：

* 一个让你通过**命令行**执行**无参数 Python 函数**的工具；

* 一个让通过 SSH 执行 Shell 命令更加**容易**、**更符合 Python 风格**的命令库（建立于一个更低层次的库）

1. start

```
$ cat fabfile.py
def hello():
    print("Hello world!")

$ fab hello
Hello world!

Done.
```

2.带参数的任务

```
$ cat fabfile.py

def hello(name="world"):
    print("Hello %s!" % name)

$ fab hello:name=Jeff
Hello Jeff!

Done.
```

3.执行本地命令

```
$ cat fabfile.py

from fabric.api import local

def run_local():
    local("echo 'aa'")
    local("hostname")
    local("ls")

$ fab run_local
[localhost] local: echo 'aa'
aa
[localhost] local: hostname
ops
[localhost] local: ls
fabfile.py  fabfile.pyc

Done.
```

4.执行远程命令

```
$ cat fabfile.py

from fabric.api import *

env.use_ssh_config = True
env.hosts = ['my_test']

def run_remote():
    print("Executing on %s as %s" % (env.host, env.user))
    run("hostname")
    print("Executing cmd %s" % env.command)
    run("w")

###### config ssh host name at ~/.ssh/config

$ fab run_remote
[my_test] Executing task 'run_remote'
Executing on 192.168.1.50 as root
[my_test] run: hostname
[my_test] out: smokeping
[my_test] out: 

Executing cmd run_remote
[my_test] run: w
[my_test] out:  17:24:30 up 36 days,  7:37,  2 users,  load average: 0.00, 0.00, 0.00
[my_test] out: USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
[my_test] out: root     pts/0    192.168.100.21   16:19    1:04m  0.00s  0.00s -bash
[my_test] out: root     pts/1    192.168.1.90     17:24    0.00s  0.00s  0.00s w
[my_test] out: 


Done.
Disconnecting from 192.168.1.50... done.
```

5.环境变量

| **env.all\_hosts** | ** 由 fab 设置的当前正在执行命令的主机列表** |
| :--- | :--- |
| **env.host** | **fab 执行的主机** |
| **env.user** | **fab 执行远程命令的用户** |
| **env.key** | **字符串类型或者字符串列表,指向用于连接的 SSH 密钥文件** |
| **env.ssh\_config\_path** | **用于设置SSH 配置文件路径** |
| **env.use\_ssh\_config** | **设置为True,Fabric将会导入本地 SSH 配置文件,Default:False** |
| **env.warn\_only** | **遇到错误时究竟是警告还是退出,Default:False** |

6.执行策略

```
cat fabfile.py

from fabric.api import run, env

env.hosts = ['host1', 'host2']

def taskA():
    run('ls')

def taskB():
    run('whoami')
    

$ fab taskA taskB

$ fab -H host1,host2 mytask

$ fab mytask:hosts="host1;host2"

```

```
$ cat fabfile.py

from fabric.api import *

env.use_ssh_config = True

@hosts('my_test', 'my_test1')
def run_remote():
    print("Executing on %s as %s" % (env.host, env.user))
    run("hostname")
    print("Executing cmd %s" % env.command)
    run("w")
$ fab run_remote
```

```

```



7.角色

一个符合条件的主机集合

```
from fabric.api import env

env.roledefs['webservers'] = ['www1', 'www2', 'www3']
```

```
from fabric.api import env

env.roledefs = {
    'web': {
        'hosts': ['www1', 'www2', 'www3'],
        'foo': 'bar'
    },
    'dns': {
        'hosts': ['ns1', 'ns2'],
        'foo': 'baz'
    }
}
```

```
cat fabfile.py
from fabric.api import *

env.use_ssh_config = True

env.roledefs = {'web': ['my_test', 'my_test1']}

@roles('web')
def run_remote():
    print("Executing on %s as %s" % (env.host, env.user))
    run("hostname")
    print("Executing cmd %s" % env.command)
    run("w")
    
$ fab run_remote
```

```
$ fab run_remote:roles=web,exclude_hosts="my_test1"
```

8.fab 命令行

```
### 执行shell命令

$ fab [options] -- [shell command] 

$ fab -H my_test,my_test1 -- hostname




```




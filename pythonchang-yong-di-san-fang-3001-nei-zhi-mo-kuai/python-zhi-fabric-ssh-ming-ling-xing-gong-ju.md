# python 之Fabric-SSH命令行工具

Fabric是基于python2.5及以上版本实现的SSH命令行工具，简化了SSH应用程序部署及系统管理任务，他提供了系统基础的操作组件，可以实现本地或远程shell命令，包括命令执行、文件上传、下载及完整执行日志输出等功能。Fabric在paramiko的基础上做更高一层的封装，操作起来更加简单。

官网地址：[http://www.fabfile.org/](http://www.fabfile.org/)

## fab的常用参数

```\`\`\`
Usage: fab [options] <command>[:arg1,arg2=val2,host=foo,hosts='h1;h2',...] ...
```

* -l,显示定义好的任务函数名；
* -f,指定fab入口文件，默认入口文件名为fabfile.py
* -g,指定网关（中转）设备，比如堡垒机环境，填写堡垒机ip即可
* -H,指定目标主机，多台主机用“，”号分隔；
* -P,以异步并行方式运行多主机任务，默认为串行运行；
* -R,指定role，以角色名区分不同业务组设备；
* -t，设置设备连接超时时间（秒）；
* -T,设置远程主机命令执行超时时间（秒）；
* -w,当命令执行失败，发出告警，而非默认终止任务。

有时候我们可已不需要写一行python代码也可以完成远程操作，直接使用命令的形式，例如：

```
fab -p 123456(密码) -H 192.168.1.21,192.168.1.22 -- 'uname -s'
```

## fabric的环境变量

fabric的环境变量有很多，存放在一个字典中，  
fabric.state.env，而它包含在fabric.api中。  
为了方便，我们一般使用env来指代环境变量。  
env环境变量可以控制很多fabric的行为，一般通过env.xxx可以进行设置。

fabric默认使用本地用户通过ssh进行连接远程机器，不过你可以通过env.user变量进行覆盖。  
当你进行ssh连接时，fabric会让你交互的让你输入远程机器密码，如果你设置了env.password变量，则就不需要交互的输入密码。  
下面介绍一些常用的环境变量：

* abort\_on\_prompts    设置是否运行在交互模式下，例如会提示输入密码之类，默认是false
* connection\_attempts    fabric尝试连接到新服务器的次数，默认1次
* cwd    目前的工作目录，一般用来确定cd命令的上下文环境
* disable\_known\_hosts    默认是false，如果是true，则会跳过用户知道的hosts文件
* exclude\_hosts    指定一个主机列表，在fab执行时，忽略列表中的机器
* fabfile    默认值是fabfile.py在fab命令执行时，会自动搜索这个文件执行。
* host\_string    当fabric连接远程机器执行run、put时，设置的user/host/port等
* hosts    一个全局的host列表
* keepalive    默认0 设置ssh的keepalive
* loacl\_user    一个只读的变量，包含了本地的系统用户，同user变量一样，但是user可以修改
* parallel    默认false，如果是true则会并行的执行所有的task
* pool\_size    默认0 在使用parallel执行任务时设置的进程数
* password    ssh远程连接时使用的密码，也可以是在使用sudo时使用的密码
* passwords    一个字典，可以为每一台机器设置一个密码，key是ip，value是密码
* path    在使用run/sudo/local执行命令时设置的$PATH环境变量
* port    设置主机的端口
* roledefs    一个字典，设置主机名到规则组的映射
* roles    一个全局的role列表
* shell    默认是/bin/bash -1 -c 在执行run命令时，默认的shell环境
* skip\_bad\_hosts    默认false，为ture时，会导致fab跳过无法连接的主机
* sudo\_prefix    默认值"sudo -S -p '%\(sudo\_prompt\)s' " % env 执行sudo命令时调用的sudo环境
* sudo\_prompt    默认值"sudo password:"
* timeout    默认10 网络连接的超时时间
* user   ssh使用哪个用户登录远程主机
* local  执行本地命令，如： local\('uname -s'\)
* lcd   切换本地目录，如： lcd\('/home'\)
* cd 切换远程目录，如： cd\('/data/logs'\)
* run 执行远程命令 如： run\('free -m'\)
* sudo sudo方式执行命令，如sudo\('/etc/init.d/httpd start'\)
* put 上传本地文件到远程主机，如put\('/home/user.info',/data/user.info''\)
* get 从远程主机下载文件到本地，如get\('/home/user.info',/data/user.info''\)
* prompt 获得用户输入信息，如： prompt\('please input user:'\)
* confirm 获得提示信息确认， 如： confirm\('Tests failed. Continue\[Y/N\]?'\)
* reboot 重启远程主机，如： reboot\(\);
* @task 函数修饰符，标识的函数为fab可调用的，非标记对fab不可见，纯业务逻辑。
* @runs\_once, 函数修饰符，标识的函数只会执行一次，不会受多台主机的影响。

示例： fab\_test.py

```
#!/usr/bin/env python
#-*- coding:utf-8 -*-

from fabric.api import *

env.user = 'root'
env.hosts = ['192.168.17.232','192.168.18.6']
env.password='123456'

@runs_once
def input_raw():
    return prompt("please input directory name:",default="/home")

def worktask(dirname):
        run("ls -l" + dirname)
@task #限定只有go函数对fab命令可见
def go():
    getdirname = raw_input()
    worktask(getdirname)
```

执行方式： fab -f  fab\_test.py go

示例：网关模式文件上传与执行，就是通过堡垒机执行远程主机操作

```
#!/usr/bin/env python
#-*- coding:utf-8 -*-

# from fabric.api import *
#
# env.user = 'root'
# env.hosts = ['192.168.17.232','192.168.18.6']
# env.password='123456'
#
# @runs_once
# def input_raw():
#     return prompt("please input directory name:",default="/home")
#
# def worktask(dirname):
#         run("ls -l" + dirname)
# @task() #限定只有go函数对fab命令可见
# def go():
#     getdirname = raw_input()
#     worktask(getdirname)

from fabric.api import *
from fabric.context_managers import *
from fabric.contrib.console import confirm

env.user='root'
env.gateway='192.168.1.23' #定义堡垒机ip，作为文件上传、执行的设备
env.hosts=['192.168.1.21','192.168.1.22']
#假如所有主机密码都不一样，可以通过env.passwords字典变量--指定
env.passwords = {
    'root@192.168.1.21:22': '123456',
    'root@192.168.1.22:22': '1123456',
    'root@192.168.1.23:22': 'test111'#堡垒机
}
lpackpath = "/home/install/test" #本地安装包路径
rpackpath = "/tmp/install" #远程安装包路径

@task
def put_task():
    run ("mkdir -p /tmp/install")

with settings(warn_only=True):
    result = put(lpackpath,rpackpath) #上传安装包
if result.failed and not confirm("put file failed,Continue[y/n]"):
    abort("Aborting file put task!")

@task
def run_task(): #执行远程命令，安装lnmp环境
    with cd("/tmp/install"):
        run("tar -zxvf test")
        with cd("test/"):
            run("./install")
@task
def go(): #上传、安装组合
    put_task()
    run_task()
```

实现不同业务环境，安装部署不同软件包

```
#!/usr/bin/env python
#-*- coding:utf-8 -*-


from fabric.api import *
from fabric.colors import *

env.user='root'
env.roledefs = {
    'webservers': ['192.168.1.21','192.168.1.22'],
    'dbservers': ['192.168.1.23']
}
env.passwords = {
    'root@192.168.1.21:22': '123456',
    'root@192.168.1.22:22': '1123456',
    'root@192.168.1.23:22': 'test111'
}

@roles('webservers')
def webtask(): #部署nginx php php-fpm等环境
    print yellow("Install nginx php php-fpm ...")
    with settings(warn_only=True):
        run("yum -y install nginx")
        run("yum -y install php-fpm php-mysql php-mbstring php-xml php-gd")
        run("chkconfig --levels 235 php-fpm on ")


@roles('dbservers') #dbtask 任务函数引用'dbservers'角色修饰符
def dbtask(): #部署mysql环境
    print yellow("Install Mysql...")
    with settings(warn_only=True):
        run("yum -y install mysql mysql-server")
        run("chkconfig --leverls 235 mysqld on")
@roles('webservers','dbservers')
def publictask():
    print yellow("Install epel ntp...")
    with settings(warn_only=True):
        run("rpm -Uvh  http://mirrors.aliyun.com/repo/epel-7.repo")
        run("yum -y install ntp")
def deploy():
    execute(publictask)
    execute(webtask)
    execute(dbtask)
```




# Debian 9

## 自学任务
> - 完成操作系统的安装
> - 熟悉Unix/Linux常用命令的使用
> - 掌握apt工具安装常用软件
> - 安装nginx，尝试建立一个web站点


## Debian 9
### Debian 系统介绍
Debian是由 *Debian计划* 所建立的一个操作系统，该系统的内核基于 Linux 或者 FreeBSD。在此之上，Debian开发者创建了大量来自于GNU工程的基础工具，因此 Debian 一般被称之为Debian GNU/Linux。Debian的包管理系统是其最出色的特性，深受其用户的喜爱和赞赏。

### Debian系统的安装
1. 下载安装 **VirtualBox / Vmware Workstation** 作为虚拟机管理软件;
2. 下载 *Debian 9* 系统镜像;
3. 从VirtualBox中导入镜像进行系统安装，可自行选择内存和硬盘大小
4. 安装时配置地区语言，账户密码，镜像网站，磁盘分区等内容 

## Linux常用命令
linux命令是让计算机执行任务的指令，命令的格式通常为```命令 [选项] [参数]```。

### 机器硬件相关
|命令                 |                     说明|
|:-------------------:|:----------------------:|
| cat /proc/cpuinfo   |               查看CPU信息 |
|cat  /proc/meminfo   |               查看内存信息 |
| lscpu               |               查看CPU信息 |
| lspci               |             查看PCI总线信息 |


### 文件相关命令
|命令                 |                     说明|
|:-------------------:|:----------------------:|
| ls                  |         查看目录下的文件 |
| ls                  |         查看目录下的文件 |
| ls                  |         查看目录下的文件 |
| ls                  |         查看目录下的文件 |
| find                  |         查看目录下的文件 |
| locate                  |         查看目录下的文件 |
| whereis                  |         查看目录下的文件 |

### 常见运维命令
|命令                 |                     说明|
|:-------------------:|:----------------------:|
| top                 |               进程监控  |
| ps                  |                |
|kill                 |显示内核的版本 |
|free                 |    查看内存使用情况 |
|vmstat  |  虚拟内存统计|
|mpstat  |  CPU统计信息|


## apt工具
先前提到debian系统的优异特性就是包管理系统，这个系统主要是dpkg和apt工具。

### dpkg
dpkg全称为package manager for Debian，主要用于对已下载到本地和已安装的软件包进行管理。但不解决包的依赖情况。

### apt
apt全称是Advanced Packaging Tool，能够自动管理关联文件和维护已有配置文件，是基于dpkg的前端工具。apt工具在获取deb安装包时，会分析deb包包头，生成deb索引清单，从而解决各软件包的依赖关系。

#### 常用命令
``` bash
apt-get install
apt-get remove
apt-get update
apt-get upgrade
apt-cache search
apt-cache show
```
## Nginx
Nginx是一个高性能的HTTP和反向代理服务，主要用于负载均衡和处理静态文件。Nginx的高性能来源于其事件驱动模式，采用epoll I/O多路复用来提高性能。

### Nginx安装
``` bash
sudo apt-get install nginx
sudo nginx -v  # 查看是否安装成功

sudo service nginx start # 开启nginx服务
sudo service nginx stop  # 停止nginx服务
```
访问[localhost](http://localhost)看到 **Welcome to nginx!** 即可验证Nginx服务正常开启。

### Nginx配置
nginx的配置文件位于``` /etc/nginx/nginx.conf```。

配置文件按层级组织，可以分为全局模块，events模块，http模块，server模块，location模块。我们主要关注的是http，server和location块。

http模块：可以嵌套多个server，配置代理，缓存，日志定义等绝大多数功能。

server模块：配置虚拟主机的相关参数，一个http中可以有多个server。

location块：配置请求的路由，以及页面的处理情况。

### Nginx + uWSGI

#### 安装uWSGI
``` bash
sudo pip install uwsgi 
uwsgi --version # 查看是否安装成功
``` 

#### 测试uWSGI服务
创建python文件```test.py```
``` python
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return "Hello World"
``` 
启动服务
``` bash
uwsgi --http :8001 --wsgi-file test.py
```
访问 ```http://localhost:8001```看到 ```hello world!```说明服务正常开启

#### 写uWSGI配置文件
创建```test.ini```文件
``` bash
[uwsgi]
socket=127.0.0.1:8001
wsgi-file=test.py
master=true
processes=2
```
启动uSWGI服务
``` bash
uwsgi test.ini
```
#### 修改Nginx配置文件
文件位置为```/etc/nginx/sites-enabled/default```，新添内容
``` bash
location / {
    include uwsgi_params;
    uwsgi_pass 127.0.0.1:8001;
}
```
Ngnix重新加载配置文件
``` bash
sudo service nginx reload

```
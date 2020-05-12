# docker



## 1,docker简介

### 1,什么是容器？

#### 一种虚拟化解决方案

#### 操作系统级别的虚拟化

#### 只能运行相同或者相似的内核的操作系统

#### 依赖于linux内核特性：Namespace 和 Cgroups 





### 2,什么是docker？

#### 将应用程序自动部署到容器的引擎

#### go编写，2013年初 dotcloud公司开发基于apache 2.0开源授权协议

#### 方便高效从 开发者环境-->测试环境-->生产环境





### 3,docker的特点

#### 3.1，提供简单轻量建模方式

容器秒速运行，同一个宿主机可以运行多个容器用来充分利用系统资源

#### 3.2，职责的逻辑分离

开发者关心容器中的应用程序，运维需要管理容器，目的就是开发环境和生产环境的一致性

#### 3.3，快速高效的开发生命周期

以容器的形式交互和分发，开发测试生产使用相同的环境，避免额外的调试和部署开销

#### 3.4，鼓励使用面向服务的架构

应用程序和服务可以表现为一堆内部互联的容器，高内聚低耦合单一任务





### 4,docker使用场景

#### 4.1，使用docker容器开发，测试，部署服务

  本地开发可以构建并运行，分享docker容器，容器可以在开发环境中创建，再提交到测试，最终进入生产环境

#### 4.2，创建隔离的运行环境

 docker可以使用不同的运行环境来满足不同的服务

#### 4.3，用来搭建测试环境

开发者利用docker在本地搭建开发测试环境，测试程序在不同系统下的兼容性

#### 4.4，构建多用户的平台即服务pass基础设施

#### 4.5，提供软件即服务saas应用程序

#### 4.6，高性能、超大规模的宿主机部署



<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200422141826575.png" alt="image-20200422141826575" style="zoom: 25%;" />







### 5,docker的基本组成

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200416112445927.png" alt="image-20200416112445927" style="zoom:33%;" />

----

#### 5.1，docker cli 客户端/docker daemon 守护进程(服务端)

C/S架构

1，客户端访问服务端

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200416113402587.png" alt="image-20200416113402587" style="zoom:33%;" />

2，Remote API (自己写程序调用api，自己的程序与docker服务端通信)

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200416113422681.png" alt="image-20200416113422681" style="zoom:33%;" />



本地/远程访问3种方式

	unix:///var/run/docker.sock
	tcp://host:port 
	fd://socketfd



----

#### 5.2，docker image 镜像

容器基于镜像启动

层叠的只读文件系统

联合加载技术(将各层只读文件系统叠加在一起)，最终文件系统包含所有底层文件和目录

以上条件的文件系统，称为镜像

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200416110941308.png" alt="image-20200416110941308" style="zoom:33%;" />



----

#### 5.3，docker container 容器

+ 通过docker镜像启动(镜像是只读的)

+ 启动和执行阶段(容器会在镜像只读层上面 增加一个可写层)

+ 写实复制：容器在启动时，会在镜像最顶层增加一个读写层文件系统(可写层)，docker中运行的程序 在此层执行，容器第一次启动，这个读写层是空的，当文件发生变化都会应用到这一层，比如要修改一个文件，会从可写层下面的只读层复制到该可写层，该文件只读版本依然存在只是被隐藏起来

  <img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200416111821716.png" alt="image-20200416111821716" style="zoom:33%;" />



#### 5.4，docker registry 仓库

公有仓库

私有

docker hub





### 6,依赖Linux内核2个特性Namespace 和 Cgroups





#### 6.1，Namespace 命名空间

实现容器之间资源的隔离(进程，网络，文件系统....)

Namespace实现5中隔离方式：

* PID 进程隔离
* NET 网络隔离
* IPC 跨进程通信隔离
* MNT 挂载点隔离
* UTS 隔离内核和版本标识

----

#### 6.2，Cgroups(control groups) 控制组

cgroups用来分配资源

linux kernel 2.6.24 内核开始支持 cgroups

cgroups 提供的功能：

+ 资源限制
  + 设置内存使用上限，当达到上限后会报out of memory的信息

+ 优先级设定
  + 设置哪些进程组使用更大的cpu，或者磁盘IO资源

+ 资源统计
  + 计算进程组使用了多少系统资源，在计费系统中尤为重要

+ 资源控制
  + 可以将进程组挂起/恢复

----

#### 6.3，Namespace 和 cgroup 能实现docker的功能

##### 文件系统的隔离：

每个容器都有自己的root文件系统

##### 进程隔离：

每个容器运行在自己的进程环境中

##### 网络隔离：

容器间的虚拟网络接口和IP地址都是分开的

##### 资源隔离和分组：

使用cgroups 将 cpu和内存等资源独立分配给每个docker容器

-------

## 2,  安装 docker

### 1) 安装 VirtualBox & vagrant

+ VirtualBox 会开放一个创建虚拟机的接口
+ vagrant 会利用这个接口创建虚拟机，并且通过Vagrant来管理，配置和自动安装虚拟机

### 2)  vagrant命令 创建虚拟机

+ cd到自定义目录下，这样vagrant启动的虚拟机就在当前目录下生成文件
+  vagrant init centos/7   初始化 
  + 初始化，并且在当前目录下生成一个vagrantfile的配置文件
  + 方便修改生成虚拟机需要的配置
+ vagrant up  根据Vagrantfile配置创建虚拟机 
  + 镜像加速  
    + 当启动的时候会download，通过打印日志获取下载链接手动下载自己需要的
    + 例如 https://cloud.centos.org/centos/7/vagrant/x86_64/images/CentOS-7-x86_64-Vagrant-1905_01.VirtualBox.box
    + 通过迅雷/wget下载
  + https://c4ys.com/archives/1230
+ vagrant box add 自定义镜像名 镜像文件路径  

```shell
# 将镜像起名并添加
vagrant box add centos7 ~/Downloads/CentOS-7-x86_64-Vagrant-1905_01.VirtualBox.box
```

+ vagrant ssh	登录新创建的虚拟服务器

```shell
# 设置root密码
[vagrant@localhost ~]$ sudo passwd root
```

+ exit    退出虚拟机
+ vagrant status     查看当前的虚拟机状态
  + vagrant global-status   查看全局下的运行的虚拟机
+ vagrant suspend  挂起虚拟机
+ vagrant resume   唤醒虚拟机
+ vagrant  halt         停掉虚拟机
+ vagrant destroy    删除虚拟机

vagrant-scp安装

+ `vagrant plugin install vagrant-scp`
+ 吧宿主机数据复制到虚拟机中
  + vagrant scp   当前目录/文件  目标虚拟机名id:/home/vagrant  
    + 注： 必须复制到虚拟机 /home/vagrant 下才有权限，其他目录没权限



##### 通过 vagrant上传文件到虚拟机

+ 注：必须复制到虚拟机下/home/vagrant 才有权限

```shell
vagrant scp ./xxx af9c151:/home/vagrant
```



### 3) 部署 docker

#### 通过 Vagrantfile 配置文件生成虚拟机

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 1.6.0"

boxes = [
    {
      	# 配置hostname
        :name => "docker-host",
      	# 配置虚拟机ip
        :eth1 => "192.168.50.111",
      	# 内存
        :mem => "1024",
      	# cpu核数
        :cpu => "1"
    }
]

Vagrant.configure(2) do |config|

  config.vm.box = "centos/7"
  boxes.each do |opts|
    config.vm.define opts[:name] do |config|
      config.vm.hostname = opts[:name]
      config.vm.provider "vmware_fusion" do |v|
        v.vmx["memsize"] = opts[:mem]
        v.vmx["numvcpus"] = opts[:cpu]
      end
      config.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", opts[:mem]]
        v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]
      end
      # public_network 配置指虚拟机为网络桥接模式
      config.vm.network :public_network, ip: opts[:eth1]
    end
  end
  # 复制文件到虚拟机
  # config.vm.synced_folder "./labs", "/home/vagrant/labs"
  # 生成虚拟机后内部运行脚本部署 与 Vagrantfile 同目录下编写 ./setup.sh 脚本
  # config.vm.provision "shell", privileged: true, path: "./setup.sh"
end
```





#### 安装 docker

```shell
## 先卸载旧版本
[root@localhost ~]# sudo yum remove docker docker-client  docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine

# centos 7.2 + 配置环境以及yum源
[root@localhost ~]# systemctl stop firewalld.service && systemctl disable firewalld.service && sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config && setenforce 0 && yum -y install wget curl && curl -o /etc/yum.repos.d/Centos-7.repo http://mirrors.aliyun.com/repo/Centos-7.repo && curl -o /etc/yum.repos.d/docker-ce.repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

## 安装docker依赖
[root@localhost ~]# sudo yum install -y yum-utils device-mapper-persistent-data lvm2

## 配置docker仓库
[root@localhost ~]# sudo yum-config-manager  --add-repo  https://download.docker.com/linux/centos/docker-ce.repo

## 查看源中可用版本
[root@localhost ~]# yum list docker-ce --showduplicates | sort -r
## 安装指定版本
##yum install -y docker-ce-18.09.9

## yum安装 docker-ce
[root@localhost ~]# sudo yum install -y docker-ce docker-ce-cli containerd.io

## 设置开机自启
[root@localhost ~]# systemctl enable docker

## 启动docker
[root@localhost ~]# systemctl daemon-reload && systemctl restart docker 

# 查看docker 版本
[root@localhost ~]# docker version

######### 选配 配置网卡转发,看值是否为1
[root@localhost ~]# sysctl -a |grep -w net.ipv4.ip_forward
net.ipv4.ip_forward = 1

######### 选配 若未配置网卡转发，需要执行如下 重启网卡
[root@localhost ~]# cat <<EOF >  /etc/sysctl.d/docker.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward=1
EOF
######### 选配
[root@localhost ~]# sysctl -p /etc/sysctl.d/docker.conf	&& systemctl restart network

## 配置源加速
[root@localhost ~]# mkdir -p /etc/docker
[root@localhost ~]# cat <<EOF >  /etc/docker/daemon.json
{
  "registry-mirrors" : [
    "https://8xpk5wnt.mirror.aliyuncs.com",
    "https://dockerhub.azk8s.cn",
    "https://registry.docker-cn.com",
    "https://ot2k4d59.mirror.aliyuncs.com/"
  ]
}
EOF

# 重启docker
[root@localhost ~]# systemctl daemon-reload && systemctl restart docker

## docker-client
[root@localhost ~]# which docker
## docker daemon
[root@localhost ~]# ps aux |grep docker
```



-------



## 3, Docker 容器





### 1，容器基本操作

----



#### 1.1,启动容器(之后自动停止)

`	docker run 镜像 [命令] [参数..]`

+ run 在新容器中执行命令

实例：

```shell
# 下载alpine镜像(docker最小的镜像，想要快速下载镜像需要配置镜像加速cdn)
wangjunxiang@My_MacBook_pro_2018  ~  docker pull alpine
# 执行单次命令的一个容器
wangjunxiang@My_MacBook_pro_2018  ~  docker run alpine echo 'hello world'
hello world
```





#### 1.2,启动交互式容器(之后自动停止)

`	docker run -i -t 镜像 /bin/bash`

+ 容器启动时运行bash命令
+ -i --interactive=true|false 默认false   为容器始终打开标准输入
+ -t --tty=true|false 默认false  为创建的容器分配一个tty终端

实例

```shell
# 启动并进入容器
wangjunxiang@My_MacBook_pro_2018  ~  docker run -it alpine /bin/sh
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # exit

# 启动并进入容器
wangjunxiang@My_MacBook_pro_2018  ~  docker run -it centos /bin/bash
[root@e09e1f3925a0 /]# exit
exit
```



#### 1.3,查看容器

方式1：

`docker ps [-a] [-l]`

+ -a 列出所有  -l 列出最新创建的容器

实例

```shell
# 查看 正在运行中 的容器
wangjunxiang@My_MacBook_pro_2018  ~  docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

# 查看所有容器(运行+未运行)
# CONTAINER ID  docker启动时分配的容器 唯一ID
# NAMES docker为容器自动分配的名字
wangjunxiang@My_MacBook_pro_2018  ~  docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                      PORTS                   NAMES
2cdc187c863c        alpine              "/bin/sh"              5 minutes ago       Exited (0) 5 minutes ago                            cocky_merkle
e09e1f3925a0        centos              "/bin/bash"            6 minutes ago       Exited (0) 5 minutes ago                            wizardly_aryabhata
4ec59f402689        alpine              "/bin/sh"              8 minutes ago       Exited (0) 8 minutes ago                            thirsty_lumiere
```



方式2：

`docker inspect 容器唯一ID/容器NAMES`

实例

```shell
# 查看所有容器
wangjunxiang@My_MacBook_pro_2018  ~  docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                      PORTS                   NAMES
2cdc187c863c        alpine              "/bin/sh"              10 minutes ago      Exited (0) 10 minutes ago                           cocky_merkle


# 查看容器详细配置信息，名称，命令，网络配置
wangjunxiang@My_MacBook_pro_2018  ~  docker inspect 2cdc187c863c
[
    {
        "Id": "2cdc187c863ce3fd49b570c4279798614222412cd6f2bd645e66fcf69ff6892b",
        "Created": "2020-04-16T05:59:41.171988545Z",
        "Path": "/bin/sh",
        "Args": [],
        "State": {
            "Status": "exited",
            "Running": false,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 0,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-04-16T05:59:41.825680064Z",
            "FinishedAt": "2020-04-16T05:59:52.282563931Z"
        },
        "Image": "sha256:a187dde48cd289ac374ad8539930628314bc581a481cdb41409c9289419ddb72",
        "ResolvConfPath": "/var/lib/docker/containers/2cdc187c863ce3fd49b570c4279798614222412cd6f2bd645e66fcf69ff6892b/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/2cdc187c863ce3fd49b570c4279798614222412cd6f2bd645e66fcf69ff6892b/hostname",
        "HostsPath": "/var/lib/docker/containers/2cdc187c863ce3fd49b570c4279798614222412cd6f2bd645e66fcf69ff6892b/hosts",
        "LogPath": "/var/lib/docker/containers/2cdc187c863ce3fd49b570c4279798614222412cd6f2bd645e66fcf69ff6892b/2cdc187c863ce3fd49b570c4279798614222412cd6f2bd645e66fcf69ff6892b-json.log",
        "Name": "/cocky_merkle",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/5ef6073b4db5ee384373209bb8d896ee17e95bba128a319959b7cdf32908176b-init/diff:/var/lib/docker/overlay2/3f4164908402169ddb92aa9a792d2c7256b1e8c3027abf60b78215ab00d97493/diff",
                "MergedDir": "/var/lib/docker/overlay2/5ef6073b4db5ee384373209bb8d896ee17e95bba128a319959b7cdf32908176b/merged",
                "UpperDir": "/var/lib/docker/overlay2/5ef6073b4db5ee384373209bb8d896ee17e95bba128a319959b7cdf32908176b/diff",
                "WorkDir": "/var/lib/docker/overlay2/5ef6073b4db5ee384373209bb8d896ee17e95bba128a319959b7cdf32908176b/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "2cdc187c863c",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh"
            ],
            "Image": "alpine",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "9b7068fdde7c6fbd45d6c0a26ddb1c3d8cae5b8158964f8d50ccccc3c4300f23",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/9b7068fdde7c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "",
            "Gateway": "",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "",
            "IPPrefixLen": 0,
            "IPv6Gateway": "",
            "MacAddress": "",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "db17253e7bd2df52e762042fad66e11707e050a8c5675c3e11e18989996ef9fa",
                    "EndpointID": "",
                    "Gateway": "",
                    "IPAddress": "",
                    "IPPrefixLen": 0,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "",
                    "DriverOpts": null
                }
            }
        }
    }
]

```



#### 1.4,自定义容器名

`docker run --name=自定义名 -it 镜像 /bin/bash`

实例

```shell
# 修改容器名
wangjunxiang@My_MacBook_pro_2018  ~  docker run --name=continer01 alpine /bin/sh

# 容器名NAMES字段已被修改
wangjunxiang@My_MacBook_pro_2018  ~  docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                      PORTS                   NAMES
6fffd384ec12        alpine              "/bin/sh"              16 seconds ago      Exited (0) 15 seconds ago                           continer01

```



#### 1.5,重新启动已经停止的容器

`docker start [-i] 容器名`

+ -i 以交互方式启动

实例

```shell
# 启动停止的容器
wangjunxiang@My_MacBook_pro_2018  ~  docker start -i container01
```



#### 1.6,删除停止的容器/所有停止的容器

`docker rm 容器名/容器唯一ID`

实例

```shell
# 删除停止的容器
wangjunxiang@My_MacBook_pro_2018  ~  docker rm b3b62001fee6
b3b62001fee6

# 删除所有停止的容器
wangjunxiang@My_MacBook_pro_2018  ~  docker ps $(docker ps -aq)
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```



#### 1.7,删除所有正在运行和已停止的容器

`docker stop $(docker ps -a -q)`

`docker rm $(docker ps -a -q)`





#### 1.8,删除所有容器，没有任何标准

`docker container rm $(docker container ps -aq)`





-----



### 2，守护式容器

+ 能够长期运行

+ 没有交互式会话

+ 适合运行应用程序







#### 2.1,启动守护式运行容器并进入

`docker run -it 镜像 /bin/bash`

+ <kbd>control</kbd> + <kbd>p</kbd>   之后在 <kbd>control</kbd> + <kbd>q</kbd> 组合快捷键退出容器

实例

```shell
# 运行守护式容器 
wangjunxiang@My_MacBook_pro_2018  ~  docker run -it centos /bin/bash
[root@c960988a2bb9 /]# cd
[root@c960988a2bb9 ~]# ps
  PID TTY          TIME CMD
    1 pts/0    00:00:00 bash
   15 pts/0    00:00:00 ps

```



#### 2.2,启动守护式容器(在后台运行)

`docker run -d 镜像名 [命令] [参数...]`

实例

```shell
# 启动守护式容器(启动后在后台运行while循环脚本)
wangjunxiang@My_MacBook_pro_2018  ~  docker run --name dc1 -d centos:latest /bin/sh -c "while true;do echo hello world;sleep 1;done"
2f1c276379772574f5695c0b121518a7e9a250c85e4e58d0c8f38c76cb61e483

# 查看后台的容器一直在无限循环执行命令
wangjunxiang@My_MacBook_pro_2018  ~  docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
2f1c27637977        centos:latest       "/bin/sh -c 'while t…"   45 seconds ago      Up 45 seconds                           dc1

```





#### 2.3,进入正在运行中的容器

`docker exec -it 容器名/容器唯一ID/bin/bash` 

`(已过时)docker attach 容器名/容器唯一ID`





#### 2.4,查看容器运行日志

查看容器内部运行命令的情况

`docker logs [-f] [-t] [--tail] 容器名`

+ -f --follows=true|false 默认为false	一直跟踪logs的变化并返回结果
+ -t --timestamps=true|false 默认为false  在返回结果的基础上加上时间戳
+ --tail=="all"    返回结尾处多少数量的日志，不指定 logs返回所有的日志

实例

```shell
# 显示dc1容器的全部日志(不常用)
wangjunxiang@My_MacBook_pro_2018  ~  docker logs dc1
hello world
hello world
...
..

# 一直跟踪日志并显示时间戳
wangjunxiang@My_MacBook_pro_2018  ~  docker logs dc1 -ft
2020-04-16T07:14:28.598256814Z hello world
2020-04-16T07:14:29.603245420Z hello world
2020-04-16T07:14:30.607829048Z hello world
...
..

# 显示最后5条日志 并显示时间戳 不跟踪日志更新
wangjunxiang@My_MacBook_pro_2018  ~  docker logs dc1 -t --tail 5
2020-04-16T07:24:43.045563087Z hello world
2020-04-16T07:24:44.049133761Z hello world
2020-04-16T07:24:45.052566596Z hello world
2020-04-16T07:24:46.055610383Z hello world
2020-04-16T07:24:47.060254411Z hello world

# 显示最后5条，跟踪日志更新并显示时间戳
 wangjunxiang@My_MacBook_pro_2018  ~  docker logs dc1 -ft --tail 5
2020-04-16T07:24:47.060254411Z hello world
2020-04-16T07:24:48.063485080Z hello world
2020-04-16T07:24:49.066471300Z hello world
2020-04-16T07:24:50.074909876Z hello world
2020-04-16T07:24:51.075706731Z hello world
2020-04-16T07:24:52.079037000Z hello world
...
..
```



#### 2.5,查看运行中的容器的进程

`docker top 容器名`

实例

```shell
# 查看运行着的容器内部进程情况
wangjunxiang@My_MacBook_pro_2018  ~  docker top dc1
PID                 USER                TIME                COMMAND
4610                root                0:00                /bin/sh -c while true;do echo hello world;sleep 1;done
5456                root                0:00                {sleep} /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
```





#### 2.6,在运行中的容器内启动新的进程

需要对运行中的容器进行维护，监控，执行管理任务等

`docker exec [-d] [-i] [-t] 容器名 [命令] [参数...]`

实例

```shell
# 先查看容器进程没有bash进程
wangjunxiang@My_MacBook_pro_2018  ~  docker top dc1
PID                 USER                TIME                COMMAND
4610                root                0:00                /bin/sh -c while true;do echo hello world;sleep 1;done
5456                root                0:00                {sleep} /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1

# 运行中的容器启动新的进程，然后control+p , control+q 退出
wangjunxiang@My_MacBook_pro_2018  ~  docker exec -it dc1 /bin/bash
[root@2f1c27637977 /]# 

# 再次查看容器中有bash进程
wangjunxiang@My_MacBook_pro_2018  ~  docker top dc1
PID                 USER                TIME                COMMAND
4610                root                0:00                /bin/sh -c while true;do echo hello world;sleep 1;done
5790                root                0:00                /bin/bash
5909                root                0:00                {sleep} /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1

```



#### 2.7,停止守护式容器



方式1`docker stop 容器名`

+ 发送信号给容器等待容器停止

方式2：`docker kill 容器名`



+ 直接停止容器

实例

```shell
#查看运行中的容器
wangjunxiang@My_MacBook_pro_2018  ~  docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS               NAMES
79265725af79        centos              "/bin/sh -c 'while t…"   3 seconds ago        Up 2 seconds                            dc2
2cca3016e05a        centos              "/bin/sh -c 'while t…"   About a minute ago   Up About a minute                       amazing_sanderson
2f1c27637977        centos:latest       "/bin/sh -c 'while t…"   29 minutes ago       Up 29 minutes                           dc1

# 速度慢，stop方式停止容器，成功后返回容器名
wangjunxiang@My_MacBook_pro_2018  ~  docker  stop dc2
dc2

# 速度快，kill方式停止容器
wangjunxiang@My_MacBook_pro_2018  ~  docker kill dc1
dc1
```



#### 2.8,其他命令帮助文件

`man docker-run`

`man docker-logs`

`man docker-top`

`man docker-exec`

...









-------



### 3，容器中通过nginx部署静态web



#### 3.1,设置容器的端口映射

`docker run [-P] [-p]`

+ -P,--publish-all=true|false 默认为false
  + `docker run -P -it centos /bin/bash`

+ -p,--publish=[]    指定映射那些端口
  + containerPort  只指定容器的端口，这种情况 宿主机端口是随机映射
    + `docker run -p 80 -it centos /bin/bash`
  + hostPort:containerPort  指定宿主机端口和容器端口，一一对应的端口映射
    + `docker run -p 8080:80 -it centos /bin/bash` 
  + ip :: containerPort  指定IP和容器端口
    + `docker run -p 0.0.0.0:80 -it centos /bin/bash`
  + IP : hostPort : containerPort
    + `docker run -p 0.0.0.0:8080:80 -it centos /bin/bash`





#### 3.2,Nginx 部署流程

##### 1，创建映射80端口的交互式容器，并修改源

```shell
# 下载ubuntu 16.04镜像
wangjunxiang@My_MacBook_pro_2018  ~  docker pull ubuntu:16.04

# 通过centos镜像 创建并进入容器 指定 容器端口80，宿主机端口随机映射
wangjunxiang@My_MacBook_pro_2018  ~  docker run --name web -p 80 -it ubuntu:16.04 /bin/bash
root@14eec3b759d0:/#

# 容器内更新 apt-get包
root@14eec3b759d0:/# apt-get update

# 安装 vim 编辑器
root@14eec3b759d0:/# apt-get install -y vim

# 容器内修改源为 阿里云
root@14eec3b759d0:/# cat <<EOF> /etc/apt/sources.list
deb http://mirrors.aliyun.com/ubuntu/ trusty main multiverse restricted universe
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main multiverse restricted universe
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main multiverse restricted universe
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main multiverse restricted universe
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main multiverse restricted universe
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main multiverse restricted universe
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main multiverse restricted universe
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main multiverse restricted universe
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main multiverse restricted universe
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main multiverse restricted universe
EOF

# 更新源
root@14eec3b759d0:/# apt-get clean && apt-get update
```

##### 2，安装Nginx

```shell
# 容器内 apt-get 安装nginx
root@14eec3b759d0:/# apt-get -y install nginx
```

##### 3，创建静态页面

```shell
# 新建放网站的目录/var/www/html
root@14eec3b759d0:/#  mkdir -p /var/www/html

# 新建index页面
root@14eec3b759d0:/# cd /var/www/html/ && vim index.html
        <html>
          <head>
            <title>Nginx in docker</title>
          </head>
            <h1>hello,Nginx in docker</h1>
        </html>
```



##### 4，修改Nginx配置文件

```shell
# 查找nginx位置
root@14eec3b759d0:~# whereis nginx
nginx: /usr/sbin/nginx /etc/nginx /usr/share/nginx /usr/share/man/man1/nginx.1.gz

# 修改配置文件根目录
root@14eec3b759d0:~# vim /etc/nginx/sites-enabled/default
        server {
                listen 80 default_server;
                listen [::]:80 default_server ipv6only=on;

                # 此处修改 根目录在/var/www/html
                root /var/www/html;
                index index.html index.htm;
                server_name localhost;

                location / {
                        try_files $uri $uri/ =404;
                }
        }


```



##### 5，运行Nginx

```shell
root@14eec3b759d0:~# nginx
root@14eec3b759d0:~# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 11:30 pts/0    00:00:00 /bin/bash
root      1139     1  0 11:50 ?        00:00:00 nginx: master process nginx
www-data  1140  1139  0 11:50 ?        00:00:00 nginx: worker process
www-data  1141  1139  0 11:50 ?        00:00:00 nginx: worker process
www-data  1142  1139  0 11:50 ?        00:00:00 nginx: worker process
www-data  1143  1139  0 11:50 ?        00:00:00 nginx: worker process
root      1144     1  0 11:50 pts/0    00:00:00 ps -ef
```

<kbd>control</kbd> + <kbd>p</kbd>  ,  <kbd>control</kbd> + <kbd>q</kbd> 退出容器

```shell
# 通过 docker port 容器名 查看容器的端口  
wangjunxiang@My_MacBook_pro_2018  ~  docker port web
80/tcp -> 0.0.0.0:32773	#容器80端口映射为宿主机随机的32773端口

# 查看容器中进程运行情况
wangjunxiang@My_MacBook_pro_2018  ~  docker top web
PID                 USER                TIME                COMMAND
19480               root                0:00                /bin/bash
20684               root                0:00                nginx: master process nginx
20685               xfs                 0:00                nginx: worker process
20686               xfs                 0:00                nginx: worker process
20687               xfs                 0:00                nginx: worker process
20688               xfs                 0:00                nginx: worker process
```



##### 6，验证访问

方式1：

```shell
# 宿主机通过curl 访问本机的 32773(映射在容器的80端口)
wangjunxiang@My_MacBook_pro_2018  ~  curl http://127.0.0.1:32773
<html>
<head>
	<title>Nginx in docker</title>
</head>
	<h1>hello,Nginx in docker</h1>
</html>

```

方式2:

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200416195829771.png" alt="image-20200416195829771" style="zoom: 50%;" />







### 4, 容器的资源限制

#### 4.1 内存限制

+ -m
  + 如果限制--memory是200M，swap不限制的话，swap默认也会拿200M，总共需要400M

```shell
############### 先构建压测工具stress的镜像 ##############
#Dockerfile
FROM ubuntu:16.04
RUN apt-get update && apt-get -y install stress
ENTRYPOINT ["/usr/bin/stress"]
#docker run在这里[]传参数，让 stress运行后面跟不同的参数
CMD []

############### 通过镜像生成容器 ###############
# 指定memory 400M(不指定swap，swap也会起200M，总共内存会用400M)
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df2  docker run --memory=200M wyzerg/stress --vm 1 --verbose

# 指定内存400M，但是起进程占500M内存会异常退出
 wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df2  docker run --memory=200M wyzerg/stress --vm 1 --verbose --vm-bytes 500M
stress: info: [1] dispatching hogs: 0 cpu, 0 io, 1 vm, 0 hdd
stress: dbug: [1] using backoff sleep of 3000us
stress: dbug: [1] --> hogvm worker 1 [7] forked
stress: dbug: [7] allocating 524288000 bytes ...
stress: dbug: [7] touching bytes in strides of 4096 bytes ...
stress: FAIL: [1] (415) <-- worker 7 got signal 9
stress: WARN: [1] (417) now reaping child worker processes
stress: FAIL: [1] (421) kill error: No such process
stress: FAIL: [1] (451) failed run completed in 1s
```

#### 4.2, CPU限制

+ -c 
  + --cpu-shares int  cpu权重
  + 如果第一个容器占10 第二个占5 如果



```shell
# 起一个权重10的test1
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df2  docker run --cpu-shares=10 --name=test1 wyzerg/stress --cpu 1
# 再起一个权重5的test2
wangjunxiang@My_MacBook_pro_2018  ~  docker run --cpu-shares=5 --name=test2 wyzerg/stress --cpu 1
# 结论：
# 当top显示cpu打满的时候，test1+test2 总共跑满100%的cpu利用率
# 并且 test1使用cpu 是test2的2倍因为权重是10 和 5
```



















## 4,镜像 和 仓库



### 1,镜像存储地址

```shell
# 通过docker info 可以看到 Docker Root Dir 镜像存储地址
wangjunxiang@My_MacBook_pro_2018  ~  docker info |grep Dir
 Docker Root Dir: /var/lib/docker

```

----



### 2,查看和删除镜像

#### 2.1,images 列出镜像

`docker images [选项] [仓库]`

+ -a --all=false	显示所有镜像
+ -f --filter=[]     显示时的过滤条件
+ --no-trunc=false    不使用截断形式显示数据
+ -q --quiet=false    只显示镜像的唯一ID

```shell

wangjunxiang@My_MacBook_pro_2018  ~  docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
# 镜像所属仓库名      镜像标签名						镜像唯一ID					镜像创建时间					镜像大小
alpine              latest              a187dde48cd2        3 weeks ago         5.6MB
ubuntu              16.04               77be327e4b63        7 weeks ago         124MB
centos              latest              0f3e07c0138f        6 months ago        220MB
centos              7.2.1511            9aec5c5fe4ba        13 months ago       195MB

# --no-trunc 查看完整的镜像ID
wangjunxiang@My_MacBook_pro_2018  ~  docker images --no-trunc
REPOSITORY          TAG                 IMAGE ID                                                                  CREATED             SIZE
alpine              latest              sha256:a187dde48cd289ac374ad8539930628314bc581a481cdb41409c9289419ddb72   3 weeks ago         5.6MB
ubuntu              16.04               sha256:77be327e4b63498e0faaa065d30d7c0e5499e42a09275ee19e27fd9a93cde7d7   7 weeks ago         124MB
centos              latest              sha256:0f3e07c0138fbe05abcb7a9cc7d63d9bd4c980c3f61fea5efa32e7c4217ef4da   6 months ago        220MB
centos              7.2.1511            sha256:9aec5c5fe4ba9cf7a8d2a50713dd197c3b0cbd5f5fcd03babe4c1d65c455dabf   13 months ago       195MB

# -a 显示所有的镜像 如果出现Repository和tag名为<none>的镜像，这些镜像是中间镜像
wangjunxiang@My_MacBook_pro_2018  ~  docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
alpine              latest              a187dde48cd2        3 weeks ago         5.6MB
ubuntu              16.04               77be327e4b63        7 weeks ago         124MB
centos              latest              0f3e07c0138f        6 months ago        220MB
centos              7.2.1511            9aec5c5fe4ba        13 months ago       195MB
<none> 							<none>      				21dfiasuher1				14 months ago				120MB

# -q 只显示镜像ID
wangjunxiang@My_MacBook_pro_2018  ~  docker images -q
a187dde48cd2
77be327e4b63
0f3e07c0138f
9aec5c5fe4ba
```



#### 2.2,Repository & Tag 镜像标签和仓库

Registry	仓库

+ 镜像的存储服务，并且包含很多Repository仓库

Repository		仓库

+ 包含了一个个独立的镜像

Tag    标签

+ 在Repository仓库中不同的镜像是以标签的形式区分的
+ 一个 Repository仓库名+Tag标签名 构成了完整的镜像名 
  + `Centos:7.2.1511`
  
  + `centos:latest`
  
    

#### 2.3,inspect 查看镜像以及详细信息

`docker inspect [选项] 容器|镜像 [容器|镜像]`

+ -f --format=""



实例

```shell
# 查看镜像
wangjunxiang@My_MacBook_pro_2018  ~  docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
centos              latest              0f3e07c0138f        6 months ago        220MB
centos              7.2.1511            9aec5c5fe4ba        13 months ago       195MB

# 查看指定镜像详细信息
wangjunxiang@My_MacBook_pro_2018  ~  docker inspect centos:latest
[
    {
        "Id": "sha256:0f3e07c0138fbe05abcb7a9cc7d63d9bd4c980c3f61fea5efa32e7c4217ef4da",
        "RepoTags": [
            "centos:latest"
        ],
        "RepoDigests": [
            "centos@sha256:72942f390a71a1ecdebd4151c9bc4ff186fe8e480adcd7ddec84427118785482"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2019-10-01T23:19:57.105928163Z",
        "Container": "711572e3c0c1ac06d5c13c4e668ec170b8ad8786b5f0949f884a5f7fd350d856",
        "ContainerConfig": {
            "Hostname": "711572e3c0c1",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:c0bda62fdbad65a3c6a1843d293a3a47d8233115cc6d384e3cb07c53580a2b43",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20190927",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "DockerVersion": "18.06.1-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:c0bda62fdbad65a3c6a1843d293a3a47d8233115cc6d384e3cb07c53580a2b43",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20190927",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 219583055,
        "VirtualSize": 219583055,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/e7fde2694a56cff65b8913ae5efa8e5e07134f9cc1a2cbb076efae7199eeae9b/merged",
                "UpperDir": "/var/lib/docker/overlay2/e7fde2694a56cff65b8913ae5efa8e5e07134f9cc1a2cbb076efae7199eeae9b/diff",
                "WorkDir": "/var/lib/docker/overlay2/e7fde2694a56cff65b8913ae5efa8e5e07134f9cc1a2cbb076efae7199eeae9b/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:9e607bb861a7d58bece26dd2c02874beedd6a097c1b6eca5255d5eb0d2236983"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]


```



#### 2.4,rmi 删除镜像

`docker rmi [选项] 镜像 [镜像2 镜像3..]`

+ -f    --force=false   强制删除
+ --no-prune=false  保留被删除镜像中被打标签的父镜像

**注意：**

`docker rmi ubuntu:14.04`

+ 如果多个镜像 都是同一ID，那么只删除这个tag

`docker rmi 1cewfczxwezs`

+ 如果直接删除镜像唯一ID，那么这个镜像ID对应的文件会被删除，包括存在多个标签的镜像也会删除

`docker rmi $(docker -q)`

+ 删除所有镜像

实例


```shell

# 查看同一个镜像 多个标签，如果删除 镜像ID，那么对应的镜像文件会被删除，包括多个标签
wangjunxiang@My_MacBook_pro_2018  ~  docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              trusty              6e4f1fe62ff1        4 months ago        197MB
ubuntu              trusty-20110101     6e4f1fe62ff1        4 months ago        197MB
ubuntu              14.04-01            6e4f1fe62ff1        4 months ago        197MB
ubuntu              14.04               6e4f1fe62ff1        4 months ago        197MB
```

#### 

---------

### 3,获取和推送镜像

#### 3.1, search 查找镜像

##### 3.1.1,Docker Hub 官网

`https://registry.hub.docker.com`



##### 3.1.2,Docker 命令行工具

`docker search [选项] 组`

+ search                         最多返回25个结果
+ --automated=false    自动化构建镜像
+ --no-trunc=false        不以截断的方式显示输出 
+ -s,--stars=0                 显示结果的最低star数量



```shell
# 搜索ubuntu镜像
wangjunxiang@My_MacBook_pro_2018  ~  docker search ubuntu
NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   10775               [OK]
dorowu/ubuntu-desktop-lxde-vnc                            Docker image to provide HTML5 VNC interface …   415                                     [OK]
...
..

# 按star数量查找 大于5个star的镜像
wangjunxiang@My_MacBook_pro_2018  ~  docker search -s 5 ubuntu
Flag --stars has been deprecated, use --filter=stars=3 instead
NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   10775               [OK]
...
..


```





#### 3.2, pull 拉取镜像

`docker pull [选项] 镜像名[:标签]`

+ a --all-tags=false  该选项 匹配 名字对应的镜像仓库repository中 所有标记的镜像都下载下来



##### 镜像加速(--registry-mirror)

如果用国内 DaoCloud 镜像加速

方式1：

+ Linux下
  + 该脚本可以将 --registry-mirror 加入到你的 Docker 配置文件 /etc/docker/daemon.json 中。适用于 Ubuntu14.04、Debian、CentOS6 、CentOS7、Fedora、Arch Linux、openSUSE Leap 42.1，其他版本可能有细微不同。更多详情请访问文档。
  + `curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io`

+ Mac下
  + docker 图标，选择 Preferences ，在 Daemon 标签下的 Registry mirrors 列表中加入下面的镜像地址:
  + `http://f1361db2.m.daocloud.io` 添加之后 Apply & Restart 使设置生效

方式2：

+ 1，修改 /etc/default/docker

+ 2，添加国内镜像加速地址 DOCKER_OPTS="--registry-mirror=http://f1361db2.m.daocloud.io"
+ 3，重启docker守护进程 `service docker restart`



查看`docker info`下有

```shell
wangjunxiang@My_MacBook_pro_2018  ~  docker info
Registry Mirrors:
  http://f1361db2.m.daocloud.io/
  http://hub-mirror.c.163.com/
```





#### 3.3, push 推送镜像(本地->远程docker仓库)

`docker push 镜像名[:标签]`

+ 需要输入docker hub 账户名和密码
+ 因为有不安全因素，更推荐分享Dockerfile的形式分享镜像

注意，上传只会提交修改的部分，并不会吧镜像全部上传

上传成功遍可以在docker hub 上看到自己的镜像

 ```shell
# 先登录docker hub的账号和密码
[root@localhost ~]# docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: wyzerg
Password: 输入docker hub 密码
Login Succeeded

# 构建新的镜像
[root@localhost ~]# mkdir -p dockerfile/df1
[root@localhost ~]# cd dockerfile/df1/ && vi Dockerfile
FROM hello-world
MAINTAINER wyzerg "wyzerg@163.com"
CMD echo "hello world"

# 构建新的镜像
[root@localhost df1]# docker build -t wyzerg/hello-world-echo .

# 推送dockerhub 自己的镜像
[root@localhost df1]# docker push wyzerg/hello-world-echo:latest
The push refers to repository [docker.io/wyzerg/hello-world-echo]
9c27e219663c: Pushing [==================================================>]  15.36kB
 ```



##### 3.3.1 私有仓库 介绍

在 https://registry.hub.docker.com/ 搜索 registry

https://registry.hub.docker.com/_/registry

通过 `docker run -d -p 5000:5000 --restart always --name registry registry:2` 启动 registry 仓库

就能在本地 push和pull

![image-20200422220009565](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200422220009565.png)



##### 3.3.2 私有仓库 搭建

2台机器:docker1,docker2，保证网络互通

docker 2 部署私有registry实例

```shell
# 参考 https://registry.hub.docker.com/_/registry
# docker2 部署registry 并 查看 是端口号 5000
[root@docker2 ~]# docker run -d -p 5000:5000 --restart always --name registry registry:2
[root@docker2 ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
dd1a7a3b3b0d        registry:2          "/entrypoint.sh /etc…"   32 seconds ago      Up 31 seconds       0.0.0.0:5000->5000/tcp   registry

# docker1 测试 docker2的端口是否通
[root@docker1 ~]# telnet 192.168.50.90 5000
Trying 192.168.50.90...
telnet: connect to address 192.168.50.90: No route to host

################## dcoker1 构建新镜像并 推送docker2 的registry中 ##################
# 1,docker1 删除原有的hello-world镜像
# Dockerfile
FROM busybox
MAINTAINER wyzerg "wyzerg@163.com"
RUN echo hello-world

# 2,docker1 通过 Dockerfile 重新构建新的镜像 并构建到docker2的ip:port 地址上
[root@docker1 df1]# docker build -t 192.168.50.90:5000/hello-world .
...
Successfully tagged 192.168.50.90:5000/hello-world:latest

# 3,docker1查看新镜像，镜像会以仓库地址命名
[root@docker1 df1]# docker images
REPOSITORY                       TAG                 IMAGE ID            CREATED             SIZE
192.168.50.90:5000/hello-world   latest              91525b7cd538        35 seconds ago      13.3kB
s
# 4,为了让docker1 信任docker2的registry，需要修改docker1 daemon.json配置指定 私有仓库
[root@docker1 df1]# vim /etc/docker/daemon.json
{
  "insecure-registries": [
    "192.168.50.90:5000"
  ]
}

# 5，编辑docker 1 的docker.service文件，让docker进程读取daemon.json配置文件加载配置
[root@docker1 df1]# vim /lib/systemd/system/docker.service
新增加一行：EnvironmentFile=-/etc/docker/daemon.json

# 6，修改2处配置后，重启docker
[root@docker1 df1]# systemctl daemon-reload && systemctl restart docker

# 7，docker1 push 新的镜像到 docker2 的192.168.50.90:5000仓库
[root@docker1 df1]# docker push 192.168.50.90:5000/hello-world
The push refers to repository [192.168.50.112:5000/hello-world]
5b0d2d635df8: Pushed
latest: digest: sha256:2ab0d8bd0bcafd2c78dbcfa7d665e40861c33c7ccf42242209348066fed2685e size: 527

# 8，在docker2上验证是否上传成功，没有ui界面通过api接口 /v2/_catalog 验证
[root@docker-host 1df]# curl 192.168.50.112:5000/v2/_catalog
{"repositories":["hello-world"]}


```









































#### 3.4, 构建镜像(重要)

+ 保存对容器的修改，并在此使用

+ 自定义镜像的能力
+ 以软件的形式打包并分发服务包括运行环境
  + 其他docker都可以以这个镜像生成容器直接运行提供服务

##### 3.4.1 commit 通过容器构建镜像

`docker commit [选项] 容器名 [新镜像名[:标签名]]`

+ -a,--author=""        指定镜像作者 e.g.,"wyzerg  wyzerg@163.com"

+ -m,--message=""   记录镜像构建的信息
+ -p,--pause=true     让commit命令不暂停容器 (不加-p会将正在执行的容器暂停)

实例

```shell
# 通过镜像生成容器并进入容器
wangjunxiang@My_MacBook_pro_2018  ~  docker run -it -p 80 --name commit_test ubuntu:16.04 /bin/bash
root@12617cb5c6f3:/#

# 更新源
root@14eec3b759d0:/# apt-get clean && apt-get update -y

# 容器内部署nginx 和 vim
root@12617cb5c6f3:/# apt-get install -y nginx vim

root@12617cb5c6f3:/# mkdir -p /var/www/html
root@12617cb5c6f3:/# nginx

# 安装好退出容器 后台运行着
control+p control+q

# 查看刚才的容器
wangjunxiang@My_MacBook_pro_2018  ~  docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS                        PORTS               NAMES
9304be4abb9e        centos:7.2.1511     "/bin/bash"         About a minute ago   Exited (130) 21 seconds ago                       commit_test

# 可以根据 commit_test 容器名生成新的镜像名为 wyzerg/commit_test 增加 -a作者信息，-m镜像信息
wangjunxiang@My_MacBook_pro_2018  ~  docker commit -a 'wyzerg' -m 'nginx' commit_test wyzerg/commit_test
sha256:3d92e99d8e069d19ed2b6f5df3b79ba731f19d391a908e8552b769cbb5518c04   #生成新镜像唯一ID

# 查看通过容器生成的镜像
wangjunxiang@My_MacBook_pro_2018  ~  docker images
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
wyzerg/commit_test   latest              3d92e99d8e06        8 seconds ago       260MB
ubuntu               16.04               77be327e4b63        7 weeks ago         124MB

# 在用构建的镜像 wyzerg/commit_test ，实例化一个新的容器 nginx_web1
# nginx -g "daemon off" 以前台模式运行
wangjunxiang@My_MacBook_pro_2018  ~  docker run -d --name nginx_web1 -p 80 wyzerg/commit_test nginx -g "daemon off;"
3c4b7204d4b3c1082abc038d899a02ef41910f48715e52786e89a749a32e38e0

# 查看通过容器->镜像->的容器 nginx_web1
wangjunxiang@My_MacBook_pro_2018  ~  docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                   NAMES
b09e0286a47a        wyzerg/commit_test   "nginx -g 'daemon of…"   3 seconds ago       Up 2 seconds        0.0.0.0:32780->80/tcp   nginx_web1

# 访问容器 nginx_web1 映射后的端口的nginx
wangjunxiang@My_MacBook_pro_2018  ~  curl http://127.0.0.1:32780
<!DOCTYPE html>
<html>
<head>
</head>
<body>
<h1>Welcome to nginx!</h1>
</body>
</html>
```



##### 3.4.2  build 通过Dockerfile文件构建新镜像

包含一系列命令的文本文件

`docker build [选项] Dockerfile文件路径|URL`

+ --force-rm=false
+ --no-cache=false
+ --pull=false
+ -q,--quiet=false
+ --rm=true
+ -t,--tag=''         指定构建镜像的tag

实例

```shell
wangjunxiang@My_MacBook_pro_2018  /tmp  mkdir -p dockerfile/df1
wangjunxiang@My_MacBook_pro_2018  /tmp  cd dockerfile/df1
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  vim Dockerfile

# Dockerfile文件内容
# 基础镜像
FROM ubuntu:16.04
# 维护人
MAINTAINER wyzerg "wyzerg@163.com"
# 镜像中执行命令 更换源
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update -y
RUN apt-get install -y nginx
# 暴露端口
EXPOSE 80
# :wq


# 在Dockerfile文件当前位置，执行 build 命令 并指定文件存放当前位置 . 构建新镜像名为 wyzerg/dockerfile_test1
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/dockerfile_test1' .
Sending build context to Docker daemon  2.048kB
Step 1/5 : FROM ubuntu:16.04
 ---> 77be327e4b63										# 构建过程的中间层镜像
Step 2/5 : MAINTAINER wyzerg "wyzerg@163.com"
 ---> Running in bdb6e9f7215c	
Removing intermediate container bdb6e9f7215c
 ---> 685c91c002ca
Step 3/6 : RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
 ---> Running in 9103e7518290
Removing intermediate container 9103e7518290
 ---> 66056b6ad2e7
Step 4/6 : RUN apt-get update -y
 ---> Running in 047a6d6cdf24
Get:1 http://mirrors.tuna.tsinghua.edu.cn/ubuntu xenial InRelease [247 kB]
Get:2 http://mirrors.tuna.tsinghua.edu.cn/ubuntu xenial-updates InRelease [109 kB]
....
..
Fetched 16.4 MB in 22s (729 kB/s)
Reading package lists...
Removing intermediate container 047a6d6cdf24
 ---> 321d177eeb11
Step 5/6 : RUN apt-get install -y nginx
 ---> Running in 8f37f68a0d73
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
.....
..
Processing triggers for systemd (229-4ubuntu21.27) ...
Removing intermediate container 8f37f68a0d73
 ---> e12ce8205696
Step 6/6 : EXPOSE 80
 ---> Running in b1b91a33254d
Removing intermediate container b1b91a33254d
 ---> 424d1d2b67d8
Successfully built 424d1d2b67d8
Successfully tagged wyzerg/dockerfile_test1:latest

# 新镜像已经生成
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
wyzerg/dockerfile_test1   latest              424d1d2b67d8        2 minutes ago       207MB

# 用新镜像 wyzerg/dockerfile_test1 实例化 容器 nginx_web2
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -d --name nginx_web2 -p 80 wyzerg/dockerfile_test1 nginx -g "daemon off;"
dc6544bae1cf59c06f0ff014caaddf6d61f3e3b600e073a0504ea2fc66dc239e

# 查看新容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                   NAMES
dc6544bae1cf        wyzerg/dockerfile_test1   "nginx -g 'daemon of…"   12 seconds ago      Up 10 seconds       0.0.0.0:32781->80/tcp   nginx_web2

# 根据新容器的随机映射主机端口32781 测试访问web2的nginx
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  curl http://127.0.0.1:32781
<!DOCTYPE html>
<html>
	<head>
		<title>Welcome to nginx!</title>
	</head>
	<body>
		<h1>Welcome to nginx!</h1>
	</body>
</html>

```



------

## 5, docker 客户端和服务端

### 4.1,docker C/S模式

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200419144558187.png" alt="image-20200419144558187" style="zoom:33%;" />



### 4.2,docker Remote API

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200419150820313.png" alt="image-20200419150820313" style="zoom:33%;" />

RESTful风格API,自己编写程序调用 API 与服务端交互

STDIN，STDOUT，STDERR

 ### 4.3 C/S模式连接方式

+ unix:///var/run/docker.sock (默认方式)

  + ```shell
    # 安装nc
    [root@localhost ~]# yum -y install nmap-ncat
    
    [root@localhost ~]# nc -U /var/run/docker.sock
    GET /info HTTP/1.1
    ```

+ tcp://host:port

+ fd://socketfd





### 4.4 docker 守护进程/服务端 的配置和操作

#### 4.4.1 Docker 启动选项

`docker -d [选项]` 以守护形式运行docker程序

+ ##### 运行相关

  + -D,--debug=fales						debug模式
  + -e,---exec-driver="native"           运行时驱动模式
  + -g,--graph="/var/lib/docker"     docker目录写入文件地址
  + --icc=true
  + -l,--log-devel="info"                     日志级别
  + --label=[]
  + -p,--pidfile="/var/run/docker.pid"    进程id

+ ##### 服务器连接相关

  + -G,--group="docker"        用户组
  + -H,--host=[]                 客户端链接服务端选项
  + --tls=false                  安全性链接选项
  + --tlscacert="/home/sven/.docker/ca.pem"
  + --tlscert="/home/sven/.docker/cert.pem"
  + --tlskey="/home/sven/.docker/hey.pem"
  + --tlsverify=false

+ ##### Remote API相关

  + --api-enable-cors=false  

+ ##### 存储相关

  + -s,--storage-driver=""
  + --selinux-enabled=false
  + --storage-opt=[]

+ ##### Registry相关

  + --insecure-registry=[]
  + --registry-mirror=[]

+ 网络设置相关
  + -b,--bridge=""
  + --bip=""
  + --fixed-cidr=""
  + --fixed-cidr-v6=""
  + --dns=[]
  + --dns-search=[]
  + --ip=0.0.0.0
  + --ip-forward=true
  + --ip-masq=true
  + --iptables=true
  + --ipv6=false
  + --mtu=0

#### 4.4.2 Docker 启动配置文件

/etc/defalut/docker

```shell
# 不存在的话 需要新建配置文件
[root@localhost ~]# vim /etc/default/docker
添加
DOCKER_OPTS="--label name=docker_server_1"

# 在服务端指定配置文件 /etc/deaulut/docker
[root@localhost ~]# vim /usr/lib/systemd/system/docker.service
添加
EnvironmentFile=-/etc/default/docker	#添加配置文件，（-代表ignore error）
修改后面新增读取 /etc/default/docker 中的变量 $DOCKER_OPTS 
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock $DOCKER_OPTS

# 重启docker服务
[root@localhost ~]# systemctl daemon-reload && systemctl restart docker

# 查看修改 服务端名称是否生效
[root@localhost ~]# ps -ef |grep docker
root      34771      1  2 13:48 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --label name=docker_server_1

```







### 4.5 docker远程访问

#### 4.5.1 环境准备

+ 二台安装docker服务器(centos系统，hostname: docker1,docker2)

+ 修改docker服务端进程启动选项，区别服务器

+ 保证Client API和Server API版本一致



```shell
# docker1机器版本
[root@docker1 ~]# docker version
Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
...

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
...

# 1，给docker1机器配置docker服务名 docker_server_1
[root@docker1 ~]# vim /etc/default/docker
添加
DOCKER_OPTS="--label name=docker_server_1"

# 2，在服务端指定配置文件 /etc/default/docker
[root@docker1 ~]# vim /usr/lib/systemd/system/docker.service
添加
EnvironmentFile=-/etc/default/docker	#添加配置文件，（-代表ignore error）
修改后面新增读取 /etc/default/docker 中的变量 $DOCKER_OPTS 
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock $DOCKER_OPTS 

# 3，重启docker服务 并 查看修改 服务端名称是否生效
[root@docker1 ~]# systemctl daemon-reload && systemctl restart docker && ps -ef |grep docker
root      34771      1  2 13:48 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --label name=docker_server_1



```



```shell
# docker2机器版本
[root@docker2 ~]# docker version
Client: Docker Engine - Community
 Version:           19.03.8
 API version:       1.40
....

Server: Docker Engine - Community
 Engine:
  Version:          19.03.8
  API version:      1.40 (minimum version 1.12)
....


# 1，给docker1机器配置docker服务名 docker_server_2
[root@docker2 ~]# vim /etc/defalut/docker
添加
DOCKER_OPTS="--label name=docker_server_2"

# 2，在服务端指定配置文件 /etc/defalut/docker
[root@docker2 ~]# vim /usr/lib/systemd/system/docker.service
添加
EnvironmentFile=-/etc/default/docker	#添加配置文件，（-代表ignore error）
修改后面新增读取 /etc/default/docker 中的变量 $DOCKER_OPTS 
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock $DOCKER_OPTS

# 3，重启docker服务 并 查看修改 服务端名称是否生效
[root@docker2 ~]# systemctl daemon-reload && systemctl restart docker && ps -ef |grep docker
root      34771      1  2 13:48 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --label name=docker_server_2

```



#### 4.5.2 修改服务端配置方式 或 修改客户端配置方式

##### 

修改docker 服务端启动选项3种

+ -H tcp://host:port
+ unix:///path/to/socket,
+ fd://*  or  fd://socketfd

服务端默认配置

+ -H unix:///var/run/docker.sock

若远程访问，需要修改 -H的值



##### 方式1：docker2 通过TCP地址和API接口访问 docker1 的docker服务端

docker 1机器

```shell
# 在docker1 上继续修改 DOCKER_OPTS 的值 0.0.0.0 代表绑定自身IP，用2375端口运行
[root@docker1 ~]# vim /etc/default/docker
修改为
DOCKER_OPTS="--label name=docker_server_1  -H tcp://0.0.0.0:2375 "

# 重启生效配置 并查看
[root@docker1 ~]# systemctl daemon-reload && systemctl restart docker
[root@docker1 ~]# ps -ef |grep docker
root      36190      1  5 15:07 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock --label name=docker_server_1 -H tcp://0.0.0.0:2375

```

docker 2机器

```shell
# 用docker2的cli 访问 docker1 的docker服务端

# 先查看 docker1 docker 服务端 IP地址为 192.168.50.173
[root@docker1 default]# ip a

# 方式1：用docker2 访问 docker1的地址并且用docker1的docker服务端口，api为/info 
[root@docker2 default]# curl http://192.168.50.173:2375/info
{"ID":"5Q36:ON25:WWGE:VX23:Q3QE:7OLD:LHUK:QEZX:FCEJ:CZS2:R4KP:AXSP","Containers":0,"ContainersRunning":0,"ContainersPaused":0,"ContainersStopped":0,"Images":3,"Driver":"devicemapper","DriverStatus":[["Pool Name","docker-253:0-307923-pool"],["Pool Blocksize","65.54kB"],["Base Device Size","10.74GB"],["Backing Filesystem","xfs"],["Udev Sync Supported","true"],["Data file","/dev/loop0"],["Metadata file","/dev/loop1"],["Data loop file","/var/lib/docker/devicemapper/devicemapper/data"],["Metadata loop file","/var/lib/docker/devicemapper/devicemapper/metadata"],["Data Space Used","661.5MB"],["Data Space Total","107.4GB"],["Data Space Available","46.78GB"],["Metadata Space Used","970.8kB"],["Metadata Space Total","2.147GB"],["Metadata Space Available","2.147GB"],["Thin Pool Minimum Free Space","10.74GB"],["Deferred Removal Enabled","true"],["Deferred Deletion Enabled","true"],["Deferred Deleted Device Count","0"],["Library Version","1.02.158-RHEL7 (2019-05-13)"]],"SystemStatus":null,"Plugins":{"Volume":["local"],"Network":["bridge","host","ipvlan","macvlan","null","overlay"],"Authorization":null,"Log":["awslogs","fluentd","gcplogs","gelf","journald","json-file","local","logentries","splunk","syslog"]},"MemoryLimit":true,"SwapLimit":true,"KernelMemory":true,"KernelMemoryTCP":true,"CpuCfsPeriod":true,"CpuCfsQuota":true,"CPUShares":true,"CPUSet":true,"PidsLimit":false,"IPv4Forwarding":true,"BridgeNfIptables":false,"BridgeNfIp6tables":false,"Debug":false,"NFd":24,"OomKillDisable":true,"NGoroutines":36,"SystemTime":"2020-04-03T15:11:10.470230658-04:00","LoggingDriver":"json-file","CgroupDriver":"cgroupfs","NEventsListener":0,"KernelVersion":"3.10.0-327.el7.x86_64","OperatingSystem":"CentOS Linux 7 (Core)","OSType":"linux","Architecture":"x86_64","IndexServerAddress":"https://index.docker.io/v1/","RegistryConfig":{"AllowNondistributableArtifactsCIDRs":[],"AllowNondistributableArtifactsHostnames":[],"InsecureRegistryCIDRs":["127.0.0.0/8"],"IndexConfigs":{"docker.io":{"Name":"docker.io","Mirrors":["http://hub-mirror.c.163.com/"],"Secure":true,"Official":true}},"Mirrors":["http://hub-mirror.c.163.com/"]},"NCPU":2,"MemTotal":3959754752,"GenericResources":null,"DockerRootDir":"/var/lib/docker","HttpProxy":"","HttpsProxy":"","NoProxy":"","Name":"docker1","Labels":["name=docker_server_1"],"ExperimentalBuild":false,"ServerVersion":"19.03.8","ClusterStore":"","ClusterAdvertise":"","Runtimes":{"runc":{"path":"runc"}},"DefaultRuntime":"runc","Swarm":{"NodeID":"","NodeAddr":"","LocalNodeState":"inactive","ControlAvailable":false,"Error":"","RemoteManagers":null},"LiveRestoreEnabled":false,"Isolation":"","InitBinary":"docker-init","ContainerdCommit":{"ID":"7ad184331fa3e55e52b890ea95e65ba581ae3429","Expected":"7ad184331fa3e55e52b890ea95e65ba581ae3429"},"RuncCommit":{"ID":"dc9208a3303feef5b3839f4323d9beb36df0a9dd","Expected":"dc9208a3303feef5b3839f4323d9beb36df0a9dd"},"InitCommit":{"ID":"fec3683","Expected":"fec3683"},"SecurityOptions":["name=seccomp,profile=default"],"Warnings":["WARNING: API is accessible on http://0.0.0.0:2375 without encryption.\n         Access to the remote API is equivalent to root access on the host. Refer\n         to the 'Docker daemon attack surface' section in the documentation for\n         more information: https://docs.docker.com/engine/security/security/#docker-daemon-attack-surface","WARNING: bridge-nf-call-iptables is disabled","WARNING: bridge-nf-call-ip6tables is disabled","WARNING: the devicemapper storage-driver is deprecated, and will be removed in a future release.","WARNING: devicemapper: usage of loopback devices is strongly discouraged for production use.\n         Use `--storage-opt dm.thinpooldev` to specify a custom block storage device."]}


```



##### 方式2：docker2机器通过client方式访问docker1机器上的docker服务端

使用docker cli命令选项

+ -H tcp://host:port
+ unix:///path/to/socket,
+ fd://*  or  fd://socketfd

客户端默认配置

+ -H unix:///var/run/docker.sock

docker2 访问 docker1机器的docker服务端

```shell
# docker2机器 通过cli客户端方式访问 docker1 的docker服务端
[root@docker2 default]# docker -H tcp://192.168.50.173:2375 info
```

每次命令都要输入一长串可以简化到使用环境变量

+ 使用环境变量 DOCKER_HOST

  + `export DOCKER_HOST="tcp://192.168.50.173:2375"` 

  + 实例

  + ```shell
    #在docker2 添加方便客户端访问的环境变量，避免重复输入参数
    [root@docker2 default]# export DOCKER_HOST="tcp://192.168.50.173:2375"
    ```

  + 这样 在docker2上 直接 docker info,docker ps ,docker pull 全是操作docker1上的docker服务端

+ 如果去除环境变量，只访问本机的docker
  + `export DOCKER_HOST=""`
  + 让环境变量为空，这样再次docker info,docker ps 就不再是远程docker而是本机的docker服务端



#### 4.5.3 设置远程带来的问题

当docker1 的机器开启了远程访问模式，在docker1上访问docker info会报错

##### 解决方式1:设置环境变量

+ 如4.5.2 方式2中客户端配置环境变量方式

##### 解决方式2:配置文件指定参数

+ ```shell
  # 修改配置文件
  [root@docker1 ~]# vim /etc/default/docker
  再增加选项 -H unix:///var/run/docker.sock 
  DOCKER_OPTS="--label name=docker_server_1  -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock"
  
  # 重启服务后生效
  [root@docker1 ~]# systemctl daemon-reload && systemctl restart docker
  ```

  



## 6, DockerFile 指令

### 5.1 指令格式

#### 5.1.1 FROM 指定基础镜像

+ FROM 镜像
  + `FROM scratch` 制作基础镜像
  + `FROM 镜像` 使用基础镜像
+ FROM 镜像:tag
  + 注意：
  + 1，镜像保证是已经存在的
  + 2，基础镜像
  + 3，必须是第一条非注释命令
  + 4，安全：尽量用官方镜像作为基础镜像



#### 5.1.2 LABEL 注释信息

+ Metada不可少

```dockerfile
LABEL maintainer="wyzerg@163.com"
LABEL version="1.0"
LABEL description="This is description"
```







#### 5.1.3 MAINTAINER 作者信息

+ MAINTAINER 作者名 "邮箱"
  + 指定镜像作者信息，包含镜像所有者和联系信息



#### 5.1.4 RUN 镜像构建过程中运行

+ 每一个RUN指令 都会在当前镜像的上一层创建一个新的镜像来运行指定的命令
+ 为了无用分层，将多条命令集合成1行
+ 指定当前镜像中构建过程中运行的命令
  + RUN 命令 (shell模式)
    + /bin/sh -c command
    + `RUN echo hello`
  + RUN ["exectuable","param1","param2"]  (exec模式)
    + `RUN ["/bin/bash","-c","echo hello"]`



#### 5.1.5 EXPOSE 指定端口
+ 指定运行这个镜像的容器 使用的端口 (指定1个或多个)
+ EXPOSE <port>[<port>...]
+ 出于安全考虑，docker不会自动打开某个指定的端口，仍然要在运行的时候指定端口
  + 例如: `docker run -p 80 -d wyzerg/df_test1 nginx -g "daemon off;"`   

```dockerfile
##
# Dockerfile文件内容
# 基础镜像
FROM ubuntu:16.04
# 维护人
MAINTAINER wyzerg "wyzerg@163.com"
# 镜像中执行命令 更换源
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update -y
RUN apt-get install -y nginx
# 暴露端口
EXPOSE 80
```





指定容器运行时，运行的命令

#### 5.1.6 CMD 实例化容器时运行
+ 提供容器运行的默认命令
+ exec模式：CMD ["exectuable","param1","param2"]
+ shell模式：CMD command param1 param2
+ 与ENTRYPOINT搭配使用：CMD ["param1","param2"]  (作为ENTRYPOINT指令的默认参数)

注意：

如果Dockerfile指定了CMD，生成了新的镜像，在用新镜像生成容器 在执行类似/bin/bash之后，就会覆盖Dockerfile文件中的CMD，而执行新的命令，新的覆盖旧的

实例

```shell
# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update && apt-get install -y nginx
EXPOSE 80
# 指定CMD 让容器启动时 运行nginx 并以前台模式运行
CMD ["/usr/sbin/nginx","-g","daemon off;"]


# 运行构建的镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/df_test3' .

# 查看Dockerfile生成的新镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1   docker images
REPOSITORY                TAG                 IMAGE ID            CREATED              SIZE
wyzerg/df_test3           latest              6a8cae00128a        About a minute ago   207MB

# 由于Dockerfile指定了cmd命令，容器运行是就执行，所以创建容器不需要在指定
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -p 80 --name cmd_test1 -d wyzerg/df_test3
a6c551427a5ce3b64fbc0d0abf2fc2489124f67b2569cf0459a4eae3f34e6e92

# 查看启动的容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                   NAMES
a6c551427a5c        wyzerg/df_test3     "/usr/sbin/nginx -g …"   17 seconds ago      Up 15 seconds       0.0.0.0:32782->80/tcp   cmd_test1

# 查看容器内执行的进程 验证CMD被执行
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker top cmd_test1
PID                 USER                TIME                COMMAND
11715               root                0:00                nginx: master process /usr/sbin/nginx -g daemon off;
11759               xfs                 0:00                nginx: worker process
11761               xfs                 0:00                nginx: worker process
11763               xfs                 0:00                nginx: worker process
11765               xfs                 0:00                nginx: worker process
11766               xfs                 0:00                nginx: worker process
11768               xfs                 0:00                nginx: worker process

```



#### 5.1.7 ENTRYPOINT 不会被docker run 覆盖的CMD命令
+ 与CMD区别：ENTRYPOINT 不会被docker run 命令中指定的启动命令所覆盖
  + 如果要覆盖Dockerfile中的ENTRYPOINT命令，需要用docker run --entrypoint 命令覆盖

实例

```shell

# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update && apt-get install -y nginx
EXPOSE 80
# 用ENTRYPOINT 代替CMD命令
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]

# 生成新的镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/df_test4' .

# 用新的镜像生成容器 并且docker run 执行/bin/bash
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -p 80 --name entrypoint_test1 -d wyzerg/df_test4 /bin/bash
a1c4e129eb424589c135ee04e68cfd50df2c2e73a0daacbe027a586d2959fdf2

```



或者写一个shell脚本作为entrypoint

```dockerfile
# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update && apt-get install -y nginx
# 吧当前的aaaa.sh 移动到镜像的绝对路径下
COPY aaaa.sh /usr/local/bin
ENTRYPOINT ["aaaa.sh"]
EXPOSE 80
CMD ["xxx"]
```





设置镜像的目录和文件

#### 5.1.8 ADD 带解压 复制文件到 新镜像中

+ ADD和COPY都是将文件和目录 复制到 使用Dockerfile构建的镜像中
+ `ADD 源路径(本地相对路径/远程url) .. 目标路径(镜像中的绝对路径)`
  + 适用于路径中有空格的情况  `ADD ["源路径(本地相对路径/远程url)",..,"目标路径(镜像中的绝对路径)"]`

#### 5.1.9 COPY 不带解压 复制文件到 新镜像中

+ ADD和COPY都是将文件和目录 复制到 使用Dockerfile构建的镜像中
+ `COPY 源路径(本地相对路径/远程url) .. 目标路径(镜像中的绝对路径)`
+ 适用于路径中有空格的情况  `COPY ["源路径(本地相对路径/远程url)",..,"目标路径(镜像中的绝对路径)"]`
+ ADD和COPY区别

  + ADD包含类似tar的解压功能
+ 如果单纯复制文件，Docker推荐COPY



如果添加远程文件/目录，推荐用curl或者wget



```shell

# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update && apt-get install -y nginx
# 与 Dockerfile 同目录下的 index.html 复制到镜像的绝对路径
COPY index.html /var/www/html/
EXPOSE 80
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]

# index.html
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  vim index.html
<html>
<head>
        <title>copy index in Dockerfile</title>
</head>
<body>
        <h1>copy index.html in Dockerfile</h1>
</body>
</html>

# 生成新镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/dockerfile_test3' .

# run新镜像生成容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -d -p 80 --name copy_test wyzerg/dockerfile_test3
5f4191fa2a6574b057d00a3e21bfcd0cf05aed41f347a9962edddef986f597b7

# 验证copy操作
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  curl http://127.0.0.1:32788
<html>
<head>
        <title>copy index in Dockerfile</title>
</head>
<body>
        <h1>copy index.html in Dockerfile</h1>
</body>
</html>


```







#### 5.1.10 VOLUME 添加卷
+ 基于镜像添加的容器添加卷，卷可以存在1个或者多个容器的目录
+ 这个目录可以绕过联合文件系统，提供共享数据，或者对数据持久化的功能
+ `VOLUME ["/data"]`





指定镜像构建，以及运行时的环境设置

#### 5.1.11 WORKDIR 工作目录

+ 类似于cd xx ，到当前目录下做一些事
+ 用来从镜像创建新容器的时候，在容器内部设置工作目录
+ ENTRYPOINT和CMD命令都会在WORKDIR目录下执行
+ `WORKDIR /path/to/workdir`   
  + 通常使用绝对路径，路径不存在则创建
  + 用WORKDIR，不要用RUN cd xxx

#### 5.1.12 ENV 环境变量

+ 用来设置环境变量，用于构建过程中或者运行过程中
+ `ENV <key><value>`  或者 `ENV <key>=<value>`

增加可维护性

```dockerfile
# 通过ENV 设置变量
ENV MYSQL_VERSION 5.6
# 引用变量
RUN apt-get install -y mysql-server="${MYSQL_VERSION}" && rm -rf /var/lib/apt/lists/*

```







#### 5.1.13 USER 指定用户运行

+ 指定镜像用指定用户运行，也可以指定 uid 或者gid

+ `USER nginx` 基于该镜像启动的容器会以nginx身份运行

+ ```shell
  # 如果不指定用户，默认用root
  USER user
  USER user:group
  USER user:gid
  USER uid
  USER uid:gid
  USER uid:group
  ```



触发器指令

####  5.1.14 ONBUILD 触发器

+ 为镜像添加触发器，当一个镜像被用于其他镜像的基础镜像时，这个触发器会被执行
+ 当子镜像在构建时，会插入触发器的指令
+ `ONBUILD 指令`

```shell
# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update && apt-get install -y nginx
# 定义触发器
ONBUILD COPY index.html /var/www/html/
EXPOSE 80
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]

# 构建镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/dockerfile_test4' .

# 根据新镜像运行容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -d -p 80 --name copy_test2 wyzerg/dockerfile_test4
378c2ae6009ecb11f4663a1cfd4d7422bdd74c75c6e51977fa09c9a0a295cd0c

# 查看新的容器 看下触发器执行的copy是否被执行
# 结论：没有被执行，只有这个带着触发器的新容器 作为基础镜像再构建新容器才会执行
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                   NAMES
378c2ae6009e        wyzerg/dockerfile_test4   "/usr/sbin/nginx -g …"   33 seconds ago      Up 32 seconds       0.0.0.0:32789->80/tcp   copy_test2
 wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  curl http://127.0.0.1:32789

-----------
# 在 wyzerg/dockerfile_test4 镜像基础上构建新的镜像，用来触发基础镜像的触发器
# Dockerfile
FROM wyzerg/dockerfile_test4
MAINTAINER wyzerg "wyzerg@163.com"
EXPOSE 80
ENTRYPOINT ["/usr/sbin/nginx","-g","daemon off;"]

# 在 wyzerg/dockerfile_test4 镜像基础上构建新的镜像，用来触发基础镜像的触发器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/dockerfile_test5' .

# 启动新镜像生成容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -d -p 80 --name copy_test3 wyzerg/dockerfile_test5
d0d1c17e64011dea5cca88178aaee58a9388fe0236e245d538759c107653d4a6

# 基于触发器的镜像生成新的镜像，实例化成容器，验证结论：基础镜像的触发器被触发
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -d -p 80 --name copy_test3 wyzerg/dockerfile_test5
d0d1c17e64011dea5cca88178aaee58a9388fe0236e245d538759c107653d4a6
 wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker ps
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                   NAMES
d0d1c17e6401        wyzerg/dockerfile_test5   "/usr/sbin/nginx -g …"   7 seconds ago       Up 6 seconds        0.0.0.0:32790->80/tcp   copy_test3
 wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  curl http://127.0.0.1:32790
<html>
<head>
        <title>copy index in Dockerfile</title>
</head>
<body>
        <h1>copy index.html in Dockerfile</h1>
</body>
</html>

```





### 5.2 Dockerfile构建过程

#### 5.2.1 可利用中间镜像调试(排错)



+ 从基础镜像运行一个容器
+ 执行指令，对容器进行修改
+ 对修改后的容器执行类似 docker commit的操作，提交一个新的镜像层
+ 在基于刚提交的镜像运行一个新的容器
+ 再执行Dockerfile中的吓一跳指令，直到所有指令执行完毕

实例

```shell
# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
ENV REFRESH_DATE 2020-04-20
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update
RUN apt-get install -y nginx
EXPOSE 80

#构建新的镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/dockerfile_test6' .
Step 1/7 : FROM ubuntu:16.04
 ---> 77be327e4b63																# 生成的中间镜像
Step 2/7 : MAINTAINER wyzerg "wyzerg@163.com"
 ---> Using cache
 ---> 19e44c501147																# 生成的中间镜像
Step 3/7 : ENV REFRESH_DATE 2020-04-20
 ---> Running in 9eec52a39332
Removing intermediate container 9eec52a39332
 ---> 204976f7d0b3																# 生成的中间镜像
Step 4/7 : RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
 ---> Running in 24e1745d235c
Removing intermediate container 24e1745d235c
 ---> fc8d3234d7ed																# 生成的中间镜像
Step 5/7 : RUN apt-get update
 ---> Running in 72c19649d763
Removing intermediate container 72c19649d763
 ---> 558c7ca252a0																# 生成的中间镜像
Step 6/7 : RUN apt-get install -y nginx
 ---> Running in 56f1732c6c23
Removing intermediate container 56f1732c6c23
 ---> 955521d20c64																# 生成的中间镜像
Step 7/7 : EXPOSE 80
 ---> Running in 47ac55924c52
Removing intermediate container 47ac55924c52
 ---> bc046fce8035																# 生成的中间镜像
Successfully built bc046fce8035
Successfully tagged wyzerg/dockerfile_test6:latest


# 可以调试：发现在build过程中，会删除中间层创建的容器，但不会删除中间层创建的镜像
# 直接运行中间层镜像,看看是否安装了nginx，结论：中间层已经安装了nginx，只不过没有运行到最后
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker run -it 955521d20c64 /bin/bash
root@9f7203d7be09:/# whereis nginx
nginx: /usr/sbin/nginx /etc/nginx /usr/share/nginx

```





#### 5.2.2 构建缓存/取消缓存

使用缓存

+ 每一步过程都将结果提交给镜像(包括中间层镜像)，就成为缓存，当再次运行没改变过的dockerfile构建镜像，秒级完成，因为全部使用了缓存构建

如果不想使用缓存 --no-cache

+ 比如每次都执行 apt-get update ，希望每次更新都会刷新apt-get的缓存，每次得到最新的包版本
+ 方式1：跳过缓存 `docker build --no-cache`
  + `docker build -t='wyzerg/dockerfile_test6' --no-cache .`

+ 方式2：在Dockerfile文件内做文章
  + 也就是中间1步的变化，利用后续都需要重新运行生成新的缓存

```shell
# Dockerfile
FROM ubuntu:16.04
MAINTAINER wyzerg "wyzerg@163.com"
# 定义环境变量的时间，如果修改这个环境变量就会带来后续缓存的刷新
ENV REFRESH_DATE 2020-04-19
RUN sed -i 's#http://archive.ubuntu.com/#http://mirrors.tuna.tsinghua.edu.cn/#' /etc/apt/sources.list;
RUN apt-get update
RUN apt-get install -y nginx
EXPOSE 80

# 再次构建镜像
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker build -t='wyzerg/dockerfile_test6' --no-cache .
```



#### 5.2.3 查看镜像构建过程

`docker history 镜像`

```shell
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df1  docker history wyzerg/dockerfile_test6
IMAGE               CREATED              CREATED BY                                      SIZE                COMMENT
a725465fc233        About a minute ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
1a6752f0b414        About a minute ago   /bin/sh -c apt-get install -y nginx             56.6MB
ef68d9599adc        About a minute ago   /bin/sh -c apt-get update                       26MB
3a4c3de3ca87        2 minutes ago        /bin/sh -c sed -i 's#http://archive.ubuntu.c…   2.9kB
e2b6c34293c8        2 minutes ago        /bin/sh -c #(nop)  ENV REFRESH_DATE=2020-04-…   0B
bbf47b85923d        2 minutes ago        /bin/sh -c #(nop)  MAINTAINER wyzerg "wyzerg…   0B
77be327e4b63        8 weeks ago          /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           8 weeks ago          /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>           8 weeks ago          /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   745B
<missing>           8 weeks ago          /bin/sh -c rm -rf /var/lib/apt/lists/*          0B
<missing>           8 weeks ago          /bin/sh -c #(nop) ADD file:1f70668251e2e58ce…   124MB

```



### 5.3 将python程序打包成image

```shell
################## 宿主机测试：准备Python环境和app ##################
# 先pip安装flask
[root@docker1 ~]# pip install flask -i https://pypi.tuna.tsinghua.edu.cn/simple
# 如果pip包不存在先下载pip，在pip安装flask
[root@docker1 ~]# wget https://bootstrap.pypa.io/get-pip.py && python get-pip.py && pip install flask -i https://pypi.tuna.tsinghua.edu.cn/simple

# 新建dockerfile
[root@docker1 ~]# mkdir ~/df/flask-hello-world && cd ~/df/flask-hello-world

# 下载Dockerfile python:2.7基础镜像
[root@docker1 flask-hello-world]# docker pull python:2.7

# 编写Dockerfile
[root@docker1 flask-hello-world]# vi Dockerfile
FROM python:2.7
LABEL maintainer="wyzerg<wyzerg.163.com>"
RUN pip install flask -i https://pypi.tuna.tsinghua.edu.cn/simple
# /app后面要加/不然会认为是将app.py改名为app
COPY app.py /app/
WORKDIR /app
EXPOSE 5000
CMD python app.py

# 准备flask web APP
[root@docker1 flask-hello-world]# cat ~/df/flask-hello-world/app.py
from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello():
    return "Flask : hello docker"
if __name__ == '__main__':
    app.run()

# 构建镜像
[root@docker1 df1]# docker build -t wyzerg/flask-hello-world .
```



### 5.4 构建可以传不同参数的镜像的实例

```shell
# Dockerfile
FROM ubuntu:16.04
RUN apt-get update && apt-get -y install stress
ENTRYPOINT ["/usr/bin/stress"]
#docker run在这里[]传参数，让 stress运行后面跟不同的参数
CMD []

# 构建镜像
docker build -y wyzerg/ubuntu-stress .

# 生成容器的时候可以传不同的参数
docker run -it wyzerg/ubuntu-stress 
提示  stress 后面的参数
重新输入想要的参数即可
```











## 7, Docker网络

### 1) 网络种类

单机网络

+ Bridge 
+ Host 
+ None

多机网络

+ Overlay

### 2) docker0 虚拟网桥

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200420204923461.png" alt="image-20200420204923461" style="zoom: 25%;" />

#### 虚拟网桥docker0的特点

+ 可以设置IP地址
+ 相当于拥有一个隐藏的虚拟网卡

#### docker0地址划分

+ IP：172.17.42.1   子网掩码:255.255.0.0
+ MAC: 02:42:ac:11:00:00 到 02:42:ac:11:ff:ff
+ 总共提供了 65534个地址

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200420205600462.png" alt="image-20200420205600462" style="zoom:25%;" />

### 3) 命名空间



创建/删除 network的Namespace

+ 创建network的Namespace
  + `ip netns add test1`

+ 查看network的Namespace
  + `ip netns list`
+ 删除network的Namespace
  + `ip netns delete xxx`

实例：network的Namespace中运行命令 将回环口down改为up

```shell
# 在network的Namespace中运行命令
[root@docker1 ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN	# 此时down状态
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

# 将这个lo 状态down改为up，再查看
[root@docker1 ~]# ip netns exec test1 ip link set dev lo up
[root@docker1 ~]# ip netns exec test1 ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```

#### Linux 的2个Namespace通信原理和实例

将2个namespace(test1、test2)互相连通起来，需要再2个Namespace新建接口(类似给他们2个之间插网线)，有个虚拟veth技术(创建一对veth)，分别放到test1和test2的namespace中，并且给2个veth分别配置ip地址，就会互通

![image-20200426155345611](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200426155345611.png)

```shell
#查看本机的ip link
[root@docker1 ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether 00:0c:29:68:57:8a brd ff:ff:ff:ff:ff:ff
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT
    link/ether 02:42:4e:a8:29:c9 brd ff:ff:ff:ff:ff:ff

# 新建2个network的Namespace test1和test2
[root@docker1 ~]# ip netns add test1
[root@docker1 ~]# ip netns add test2
[root@docker1 ~]# ip netns list
test2
test1

# 查看test1，并启动test1的lo 为 up状态
[root@docker1 ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
[root@docker1 ~]# ip netns exec test1 ip link set dev lo up
[root@docker1 ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever

################ 创建一对 veth ################
# 为了让test1和test2的Namespace可以通信，创建一对 veth 的link
[root@docker1 ~]# ip link add veth-test1 type veth peer name veth-test2
# 此时 ip link 多出2个veth ，都有mac地址，但是没有ip地址，而且都是down状态
[root@docker1 ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether 00:0c:29:68:57:8a brd ff:ff:ff:ff:ff:ff
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT
    link/ether 02:42:61:b4:57:e5 brd ff:ff:ff:ff:ff:ff
4: veth-test2@veth-test1: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 1e:7f:01:2b:38:fe brd ff:ff:ff:ff:ff:ff
5: veth-test1@veth-test2: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 16:ab:c0:62:1d:60 brd ff:ff:ff:ff:ff:ff

############### veth-test1 添加到 test1的Namespace中 ###############
# 吧其中一个 veth-test1 添加到test1的Namespace中
[root@docker1 ~]# ip link set veth-test1 netns test1
# 查看test1的Namespace中多了一个veth-test1
[root@docker1 ~]# ip netns exec test1 ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
5: veth-test1@if4: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 16:ab:c0:62:1d:60 brd ff:ff:ff:ff:ff:ff link-netnsid 0
# 本地的ip link 却少了一个veth-test1
[root@docker1 ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether 00:0c:29:68:57:8a brd ff:ff:ff:ff:ff:ff
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT
    link/ether 02:42:61:b4:57:e5 brd ff:ff:ff:ff:ff:ff
4: veth-test2@if5: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 1e:7f:01:2b:38:fe brd ff:ff:ff:ff:ff:ff link-netnsid 0

############### veth-test2 添加到 test2的Namespace中 ###############
# veth-test2 添加给test2的Namespace
[root@docker1 ~]# ip link set veth-test2 netns test2
[root@docker1 ~]# ip netns exec test2 ip link
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
4: veth-test2@if5: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT qlen 1000
    link/ether 1e:7f:01:2b:38:fe brd ff:ff:ff:ff:ff:ff link-netnsid 0
# 本地的veth-test2已经不在
[root@docker1 ~]# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eno16777736: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    link/ether 00:0c:29:68:57:8a brd ff:ff:ff:ff:ff:ff
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT
    link/ether 02:42:61:b4:57:e5 brd ff:ff:ff:ff:ff:ff

############### 给2个Namespace中将veth down都改为up ###############
# 查看 veth-test1 此时为down
[root@docker1 ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
5: veth-test1@if4: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 16:ab:c0:62:1d:60 brd ff:ff:ff:ff:ff:ff link-netnsid 1
# 将test1 Namespace的veth-test1 改为up
[root@docker1 ~]# ip netns exec test1 ip link set dev veth-test1 up
# veth-test1 此时为up
[root@docker1 ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
5: veth-test1@if4: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state LOWERLAYERDOWN qlen 1000
    link/ether 16:ab:c0:62:1d:60 brd ff:ff:ff:ff:ff:ff link-netnsid 1

# 同理，将test2的veth-test2 改为up
[root@docker1 ~]# ip netns exec test2 ip link set dev veth-test2 up
[root@docker1 ~]# ip netns exec test2 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
4: veth-test2@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 1e:7f:01:2b:38:fe brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::1c7f:1ff:fe2b:38fe/64 scope link
       valid_lft forever preferred_lft forever

############### 给2个Namespace中将veth 分别配置 同网段的 不同IP ###############
# 设置 test1的 veth-test1 IP地址
[root@docker1 ~]# ip netns exec test1 ip addr add 192.168.1.1/24 dev veth-test1
# 设置 test2的 veth-test2 IP地址
[root@docker1 ~]# ip netns exec test2 ip addr add 192.168.1.2/24 dev veth-test2
# 查看 2个Namespace的veth ip地址
[root@docker1 ~]# ip netns exec test1 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
5: veth-test1@if4: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 16:ab:c0:62:1d:60 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet 192.168.1.1/24 scope global veth-test1
       valid_lft forever preferred_lft forever
    inet6 fe80::14ab:c0ff:fe62:1d60/64 scope link
       valid_lft forever preferred_lft forever
[root@docker1 ~]# ip netns exec test2 ip a
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
4: veth-test2@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 1e:7f:01:2b:38:fe brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.168.1.2/24 scope global veth-test2
       valid_lft forever preferred_lft forever
    inet6 fe80::1c7f:1ff:fe2b:38fe/64 scope link
       valid_lft forever preferred_lft forever

############### 测试 Namespace test1和test2 ping连通性 ###############
# test1(192.168.1.1) ping test2(192.168.1.2)
[root@docker1 ~]# ip netns exec test1 ping 192.168.1.2
PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
64 bytes from 192.168.1.2: icmp_seq=1 ttl=64 time=0.167 ms
64 bytes from 192.168.1.2: icmp_seq=2 ttl=64 time=0.045 ms
^C
--- 192.168.1.2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.045/0.106/0.167/0.061 ms
#  test2(192.168.1.2) ping test1(192.168.1.1) 
[root@docker1 ~]# ip netns exec test2 ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=64 time=0.071 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=64 time=0.059 ms
^C
--- 192.168.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.059/0.065/0.071/0.006 ms

```



### 4) bridge 模式

+ 查看docker网络
  + `docker network ls`



```shell

#新建容器
[root@docker1 ~]# docker run -d --name test1 busybox /bin/sh -c "while true;do echo test1;sleep 3;done"

# 查看机器上docker都有哪些网络
[root@docker1 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
7ee712b35f2a        bridge              bridge              local
2051e39c62d9        host                host                local
f0e8467b43fa        none                null                local

# 对应bridge的是 7ee712b35f2a 网络，查看 7ee712b35f2a网络信息
[root@docker1 ~]# docker network inspect 7ee712b35f2a
....
..
"Containers": {
            "8332c92f04b17ec185b805c42529593a5099cae0bb9cbc762dc26249540769f0": {
                "Name": "test1",
                ###### 其中有container信息，里面显示刚才新建的容器test1是链接到 bridge网络
                "EndpointID": "e6f75b72a3c2a3b154de6e077719e76959131f23982ac19c4a1e3b6f03559029",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },

# 查看本机网卡 vethb0417fc@if6 和 容器内的虚拟网卡veth
[root@docker1 ~]# ip a
....
...
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
...
7: vethb0417fc@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP
    link/ether 82:6d:d1:2f:b3:4e brd ff:ff:ff:ff:ff:ff link-netnsid 1
    inet6 fe80::806d:d1ff:fe2f:b34e/64 scope link
       valid_lft forever preferred_lft forever
[root@docker1 ~]# docker exec test1 ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue
...
6: eth0@if7: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue
    link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever

# 网桥管理工具 bridge-utils 通过 brctl工具查看
[root@docker1 ~]# yum -y install bridge-utils
# 有一个容器bridge 就是 有一个veth连接这docker0 ，如果2个容器就有2个veth虚拟网卡
[root@docker1 ~]# brctl show
bridge name	bridge id		STP enabled	interfaces
docker0		8000.000000000000	no		veth1de1e7e

```

#### 结论-容器和容器之间通信

容器内虚拟的 eth0@if7 和 主机的docker0中的 vethb0417fc@if6 进行通信，从而实现容器和主机的通信

![image-20200426180126909](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200426180126909.png)

#### 结论-容器访问外网

主机可以通过eth0访问外网，容器通过iptables实现：docker0的bridge，通过NAT转换成eth0的地址，通过eth0 主机的数据包去访问外网

![image-20200426180339756](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200426180339756.png)





#### 容器之间link

+ 容器不需要通过IP地址访问，而通过link的name访问，不用关注动态变化的ip地址

+ 通过--link 另一个容器名，内部做了dns映射可以代替IP地址

+ 单项link  没有link无法使用

```shell
# 已知创建了一个容器test1
[root@docker1 ~]# docker run -d --name test1 busybox /bin/sh -c "while true;do echo test1;sleep 3;done"

# 再创建test2 --link test1
[root@docker1 ~]# docker run -d --name test2 --link test1 busybox /bin/sh -c "while true;do echo test1;sleep 3;done"

# 进入容器test2内 可以ping test1的IP 也可以ping通 容器名test1
[root@docker1 ~]# docker exec -it test2 /bin/sh
/ # ip a
/ # ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.363 ms
/ # ping test1
PING test1 (172.17.0.2): 56 data bytes
64 bytes from 172.17.0.2: seq=0 ttl=64 time=0.171 ms
```



#### 新建bridge实例

+ 容器新建后会默认连接到 `docker network ls` 的默认bridge下

+ 也可以指定其他网络模式

+ 甚至可以不用默认bridge，可以新建bridge，再新建容器链接到新的bridge上
  + `docker network create [选项] 自定网络名 [flags]`
    + -d driver
  + 通过 `docker run  ...  --network [自定义网络名] ... ` ，让容器不用默认的bridge，而用自定义的
  + 通过 `docker network connect [网络模式/自定义网络名] 容器名`  更改现有容器的网络模式
+ 结论：
  + 只要2个容器在同一个新建的my-bridge下，可以不需要--link，默认可以直接通过容器名互相通信



```shell
#################### 新建bridge实例 ####################
[root@docker1 ~]# docker network create -d bridge my-bridge
87f1550a1ccd035fa32ec61d6c48dbddf2733921d1beff9477b95b4cafb2f2ad

# 查看新建的网络 或者通过 yum -y install bridge-utils && brctl show 查看
[root@docker1 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
7ee712b35f2a        bridge              bridge              local
2051e39c62d9        host                host                local
87f1550a1ccd        my-bridge           bridge              local
f0e8467b43fa        none                null                local
```

![image-20200426194421342](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200426194421342.png)

```shell
################ 指定新建的容器 链接 新建的bridge ################
[root@docker1 ~]# docker run -d --name test3 --network my-bridge  busybox /bin/sh -c "while true;do echo test1;sleep 3;done"

# brctl show 查看，br-87f1550a1ccd 已经链接上了虚拟的veth
[root@docker1 ~]# brctl show
bridge name	bridge id		STP enabled	interfaces
br-87f1550a1ccd		8000.000000000000	no		vetha2c9850
docker0		8000.000000000000	no		veth0f790fa
							vethb0417fc

# docker network ls 查看网络模式
[root@docker1 ~]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
7ee712b35f2a        bridge              bridge              local
2051e39c62d9        host                host                local
87f1550a1ccd(新模式) my-bridge           bridge              local
f0e8467b43fa        none                null                local

# docker network inspect 87f1550a1ccd 查看网络模式具体信息
[root@docker1 ~]# docker network inspect 87f1550a1ccd
						"Containers": {
            "66d5834da74316bf22b5ab6b413d074f112b6838780ab345b36bd06dfa30bb3c": {
                "Name": "test3",
                "EndpointID": "86e3a5e9f6c0077d85ec630c05814d0ea6516436596ecccb04b9c3ad7cc6fc4a",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },


##################### 将默认bridge模式容器 更改为新建的 my-bridge模式 #####################
[root@docker1 ~]# docker network connect my-bridge test2

# 1，docker network inspect 查看 my-bridge模式的container信息,新的容器加入到这个模式中
[root@docker1 ~]# docker network inspect 87f1550a1ccd
				...
        "Containers": {
            "66d5834da74316bf22b5ab6b413d074f112b6838780ab345b36bd06dfa30bb3c": {
                "Name": "test3",
                "EndpointID": "86e3a5e9f6c0077d85ec630c05814d0ea6516436596ecccb04b9c3ad7cc6fc4a",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "791bbd43d374c3a8725c4d8fc10e5284124627759ea899e667e848832e3e462b": {
                "Name": "test2",
                "EndpointID": "e3afdd21eec7a90aeab27607c976c443a29a917ca6ada609aff1a724d4d49518",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            }
        },
        
# 2，docker network inspect 查看默认的bridge模式，发现加入到新模式的容器 也在默认bridge模式中
[root@docker1 ~]# docker network inspect 7ee712b35f2a
					"Containers": {
            "66d5834da74316bf22b5ab6b413d074f112b6838780ab345b36bd06dfa30bb3c": {
                "Name": "test3",
                "EndpointID": "86e3a5e9f6c0077d85ec630c05814d0ea6516436596ecccb04b9c3ad7cc6fc4a",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "791bbd43d374c3a8725c4d8fc10e5284124627759ea899e667e848832e3e462b": {
                "Name": "test2",
                "EndpointID": "e3afdd21eec7a90aeab27607c976c443a29a917ca6ada609aff1a724d4d49518",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            }
        },
```



#### 容器的端口映射



`docker run [-P] [-p]`

+ -P,--publish-all=true|false 默认为false
  + `docker run -P -it centos /bin/bash`

+ -p,--publish=[]    指定映射那些端口
  + 容器端口                       只指定容器的端口，这种情况 宿主机端口是随机映射
    + `docker run -p 80 -it centos /bin/bash`
  + 主机端口:容器端口          指定宿主机端口和容器端口，一一对应的端口映射
    + `docker run -p 8080:80 -it centos /bin/bash` 
  + ip :: 容器端口                 指定IP和容器端口
    + `docker run -p 0.0.0.0:80 -it centos /bin/bash`
  + IP : 主机端口 : 容器端口
    + `docker run -p 0.0.0.0:8080:80 -it centos /bin/bash`





### 5) host 和 none 模式

#### none模式

+ --network none 参数

+ 除了 docker exec 方式进入容器 没有其他方式可以访问到容器
+ 没有IP地址，也不会生成veth网桥
+ 用途：密码工具箱，只有本地登录容器

```shell
# 新建 none模式的容器
[root@docker1 ~]# docker run -d --name test1 --network none busybox /bin/sh -c "while true;do echo test1 ;sheep 3;done"

# 查看none模式信息
[root@docker1 ~]# docker inspect network inspect none
						"Containers": {
            "ac41d70eaf36950217a52ae23616b2c52f239ec3c7104faaf23327192f8abbf1": {
                "Name": "test1",
                "EndpointID": "af3c8919fced618f381b1bd025e4ebbe8847f0c39c2a0a628f81c24b46ae45fd",
                "MacAddress": "",
                "IPv4Address": "",
                "IPv6Address": ""
            }
        },
```



#### host模式

+ --network host 参数
+ host 也没有分配ip地址，但是共享宿主机所有网桥，ip a和宿主机ip a相同

```shell
# 创建 host模式的容器
[root@docker1 ~]# docker run -d --name test1 --network host busybox /bin/sh -c "while true;do echo test1 ;sheep 3;done"
753485f90b16e3ca431162404c49f027238d6b905c8452c1189cb49be544bacf

# 查看host模式信息
[root@docker1 ~]# docker network inspect host
						"Containers": {
            "753485f90b16e3ca431162404c49f027238d6b905c8452c1189cb49be544bacf": {
                "Name": "test1",
                "EndpointID": "3b0d03db8356c396a9b7ed572d3eb2ed7ecf5a481e6f67f1a780090e0e4b9c60",
                "MacAddress": "",
                "IPv4Address": "",
                "IPv6Address": ""
            }
        },

```





### 6) 多容器应用部署

推荐一个模块部署在一个容器内



#### 准备 app.py (flask,flask-redis )

```python
from flask import Flask
from redis import Redis
import os
import socket

app = Flask(__name__)
# REDIS_HOST 是直接获取环境变量
redis = Redis(host=os.environ.get('REDIS_HOST', '127.0.0.1'), port=6379)

@app.route('/')
def hello():
    redis.incr('hits')
    return 'Hello Container World! I have been seen %s times and my hostname is %s.\n' % (redis.get('hits'),socket.gethostname())

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200426234306386.png" alt="image-20200426234306386" style="zoom:150%;" />







#### 准备 reids (server)

+ 不指定 -p 因为不用暴露给外面的端口6379
+ redis用来容器内部互相访问，不是提供给外部 
+ 访问redis，其他app的容器通过--link redis启动容器参数通过reids容器名，获取访问redis(内部ip，端口怎么变都不影响)

```shell
# 启动redis容器服务
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker run -d --name redis redis

wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
e22789bc1491        redis               "docker-entrypoint.s…"   16 minutes ago      Up 16 minutes       6379/tcp            redis

```



#### 准备 Dockerfile

```dockerfile
FROM python:2.7
LABEL maintaner="wyzerg wyzerg@163.com"
COPY . /app/
WORKDIR /app
RUN pip install flask redis -i https://pypi.tuna.tsinghua.edu.cn/simple
EXPOSE 5000
CMD [ "python", "app.py" ]
```



##### 关于环境变量 -e xx_HOME=test1

+ docker run 启动容器 指定-e xx_HOME=test1 ，在容器内部通过env获取到环境变量

+ 用来传递配置的一种方式，将配置传到容器内部，内部调用不同的配置，生成不同的逻辑



#### 部署

```shell
# 1，生成
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker build -t wyzerg/flask-redis .

############## 测试 flask的web_app容器 和 redis 容器的连通性 ##############
# 通过 --link redis方式让容器直接就可以连接redis
# -e REDIS_HOST=redis 设置环境变量
# 代码内部连接redis是通过 Redis(host=os.environ.get('REDIS_HOST') 来获取的
# 不加-p 暴露端口，先内部测试连通性
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker run -d --link redis --name flask-redis -e REDIS_HOST=redis wyzerg/flask-redis

# 进入容器 并 查看启动容器环境变量
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker exec -it flask-redis /bin/bash
# 查看容器内环境变量
root@146848a8064f:/app# env |grep REDIS_HOST
REDIS_HOST=redis
# 创建容器用了--link，所以可以直接ping通redis容器的地址
root@146848a8064f:/app# ping redis
PING redis (172.17.0.2) 56(84) bytes of data.
64 bytes from redis (172.17.0.2): icmp_seq=1 ttl=64 time=0.117 ms
# 可以在容器访问本地的web_app，后端app获取redis的自增函数(每请求一次返回+1)
root@146848a8064f:/app# curl 127.0.0.1:5000
Hello Container World! I have been seen 1 times and my hostname is 146848a8064f.
root@146848a8064f:/app# curl 127.0.0.1:5000
Hello Container World! I have been seen 2 times and my hostname is 146848a8064f.
root@146848a8064f:/app# curl 127.0.0.1:5000
Hello Container World! I have been seen 3 times and my hostname is 146848a8064f.
# 测试完退出容器  停止 && 删除这个flask APP容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker stop flask-redis && docker rm flask-redis
flask-redis
flask-redis
############################## 测试结束 ##############################
# 2,暴露端口5000 启动容器
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker run -d -p 5000:5000 --link redis --name flask-redis -e REDIS_HOST=redis wyzerg/flask-redis
67059d0ae3a5d92be7f2fe396c162202aaecb6d7742ef1a11e3333128cd5da21

wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                    NAMES
67059d0ae3a5        wyzerg/flask-redis   "python app.py"          2 seconds ago       Up 2 seconds        0.0.0.0:5000->5000/tcp   flask-redis
e22789bc1491        redis                "docker-entrypoint.s…"   37 minutes ago      Up 37 minutes       6379/tcp                 redis

# 3,本地访问暴露出来的5000端口
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  curl 127.0.0.1:5000
Hello Container World! I have been seen 4 times and my hostname is 67059d0ae3a5.
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3 
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  curl 127.0.0.1:5000
Hello Container World! I have been seen 5 times and my hostname is 67059d0ae3a5.
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  curl 127.0.0.1:5000
Hello Container World! I have been seen 6 times and my hostname is 67059d0ae3a5.
wangjunxiang@My_MacBook_pro_2018  /tmp/dockerfile/df3  curl 127.0.0.1:5000
Hello Container World! I have been seen 7 times and my hostname is 67059d0ae3a5.
```







### 7) 多宿主机通信 (overlay)

![image-20200427144248712](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200427144248712.png)

#### 安装分布式存储 etcd

用来识别宿主机A的容器ip为0.2，宿主机B的容器IP为0.3

##### docker-node1部署etcd

```shell
# 修改hostname 为 docker-node1
[root@docker1 ~]# hostnamectl set-hostname docker-node1

# 下载etcd包 并解压
[root@docker-node1 ~]# wget https://github.com/coreos/etcd/releases/download/v3.0.12/etcd-v3.0.12-linux-amd64.tar.gz && tar zxvf etcd-v3.0.12-linux-amd64.tar.gz

[root@docker-node1 ~]# cd etcd-v3.0.12-linux-amd64

# 运行，还要指定docker-node2的ip为192.168.50.90
[root@docker-node1 etcd-v3.0.12-linux-amd64]# nohup ./etcd --name docker-node1 --initial-advertise-peer-urls http://192.168.50.173:2380 \
--listen-peer-urls http://192.168.50.173:2380 \
--listen-client-urls http://192.168.50.173:2379,http://127.0.0.1:2379 \
--advertise-client-urls http://192.168.50.173:2379 \
--initial-cluster-token etcd-cluster \
--initial-cluster docker-node1=http://192.168.50.173:2380,docker-node2=http://192.168.50.90:2380 \
--initial-cluster-state new&
```



##### docker-node2部署etcd

```shell
# 修改hostname 为 docker-node1
[root@docker2 ~]# hostnamectl set-hostname docker-node2

# 同docker-node1一样下载包 并解压
[root@docker-node2 ~]# wget https://github.com/coreos/etcd/releases/download/v3.0.12/etcd-v3.0.12-linux-amd64.tar.gz && tar zxvf etcd-v3.0.12-linux-amd64.tar.gz 

[root@docker-node2 ~]# cd etcd-v3.0.12-linux-amd64

# 运行etcd  指定参数
[root@docker-node2 etcd-v3.0.12-linux-amd64]# nohup ./etcd --name docker-node2 --initial-advertise-peer-urls http://192.168.50.90:2380 \
--listen-peer-urls http://192.168.50.90:2380 \
--listen-client-urls http://192.168.50.90:2379,http://127.0.0.1:2379 \
--advertise-client-urls http://192.168.50.90:2379 \
--initial-cluster-token etcd-cluster \
--initial-cluster docker-node1=http://192.168.50.173:2380,docker-node2=http://192.168.50.90:2380 \
--initial-cluster-state new&
```



##### ectd部署后健康检查

```shell
[root@docker-node1 etcd-v3.0.12-linux-amd64]# ./etcdctl cluster-health
member 99c6a42e9783ec13 is healthy: got healthy result from http://192.168.50.90:2379
member c1fa47d93efeaf83 is healthy: got healthy result from http://192.168.50.173:2379
cluster is healthy

```



##### 重启2台宿主机的docker服务

docker-node1

```shell
# 停止 docker-node1的docker服务
[root@docker-node1 etcd-v3.0.12-linux-amd64]# systemctl stop docker

# 重启docker服务 指定自己的ip
[root@docker-node1 etcd-v3.0.12-linux-amd64]# /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://192.168.50.173:2379 --cluster-advertise=192.168.50.173:2375&
```

docker-node2

```shell
# 停止服务 并指定参数启动docker 指定自己的ip
[root@docker-node2 etcd-v3.0.12-linux-amd64]# systemctl stop docker
[root@docker-node2 etcd-v3.0.12-linux-amd64]# /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://192.168.50.90:2379 --cluster-advertise=192.168.50.90:2375&
```

##### 创建overlay 网络模式(node1会同步到node2)

docker-node1 创建好网络模式 会同步到docker-node2上

```shell

[root@docker-node1 etcd-v3.0.12-linux-amd64]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
0e7bef3f143a        bridge              bridge              local
a5c7daf62325        host                host                local
3198cae88ab4        none                null                local

[root@docker-node1 etcd-v3.0.12-linux-amd64]# docker network create -d overlay demo
3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9
[root@docker-node1 etcd-v3.0.12-linux-amd64]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
0e7bef3f143a        bridge              bridge              local
3d430f3338a2        demo                overlay             global
a5c7daf62325        host                host                local
3198cae88ab4        none                null                local

[root@docker-node1 etcd-v3.0.12-linux-amd64]# docker network inspect demo
[
    {
        "Name": "demo",
        "Id": "3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9",
        "Scope": "global",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "10.0.0.0/24",
                    "Gateway": "10.0.0.1/24"
                }
            ]
        },
        "Internal": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

docker-node2同步到的网络模式

```shell

[root@docker-node2 etcd-v3.0.12-linux-amd64]# docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
c9947d4c3669        bridge              bridge              local
3d430f3338a2        demo                overlay             global
fa5168034de1        host                host                local
c2ca34abec2a        none                null                local
```



通过查看etcd的key-value, 我们获取到，这个demo的network是通过etcd从node1同步到node2的

```shell
[root@docker-node1 etcd-v3.0.12-linux-amd64]# ./etcdctl ls /docker
/docker/network
/docker/nodes
[root@docker-node1 etcd-v3.0.12-linux-amd64]# ./etcdctl ls /docker/nodes
/docker/nodes/192.168.205.11:2375
/docker/nodes/192.168.205.10:2375
[root@docker-node1 etcd-v3.0.12-linux-amd64]# ./etcdctl ls /docker/network/v1.0/network
/docker/network/v1.0/network/3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9
[root@docker-node1 etcd-v3.0.12-linux-amd64]# ./etcdctl get /docker/network/v1.0/network/3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9 | jq .
{
  "addrSpace": "GlobalDefault",
  "enableIPv6": false,
  "generic": {
    "com.docker.network.enable_ipv6": false,
    "com.docker.network.generic": {}
  },
  "id": "3d430f3338a2c3496e9edeccc880f0a7affa06522b4249497ef6c4cd6571eaa9",
  "inDelete": false,
  "ingress": false,
  "internal": false,
  "ipamOptions": {},
  "ipamType": "default",
  "ipamV4Config": "[{\"PreferredPool\":\"\",\"SubPool\":\"\",\"Gateway\":\"\",\"AuxAddresses\":null}]",
  "ipamV4Info": "[{\"IPAMData\":\"{\\\"AddressSpace\\\":\\\"GlobalDefault\\\",\\\"Gateway\\\":\\\"10.0.0.1/24\\\",\\\"Pool\\\":\\\"10.0.0.0/24\\\"}\",\"PoolID\":\"GlobalDefault/10.0.0.0/24\"}]",
  "labels": {},
  "name": "demo",
  "networkType": "overlay",
  "persist": true,
  "postIPv6": false,
  "scope": "global"
}

```

##### overlay网络模式 总结

+ 在overlay模式下,node1创建容器名和node2创建容器名重复会报冲突，因为在建立集群中容器名唯一
+ 并在在不同宿主机之间，容器可以跨宿主机 通过 容器名 互相ping通







## 8) docker 存储持久化和数据共享



### 8.1) Image和container的区别：

+ image只读文件

+ container是在image只读文件上一层增加了可写层
  + 如果这个容器被停止&删除，那么容器内的可写层的数据就会被删除，并不会保存在容器外
  + 为了将数据保存在container之外，docker 实现了数据持久化技术





### 8.2) docker 持久化数据的方案

基于本地文件系统的Volume

+ 执行docker create 或 docker run，通过-v参数将主机的目录作为容器的数据卷，这部分就是基于本地文件系统的Volume

基于plugin的Volume

+ 支持第三方存储方案，比如远程NAS，aws

<img src="/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200428175857184.png" alt="image-20200428175857184" style="zoom: 25%;" />



Volume的2种类型

+ 受管理的 data Volume，有docker后台自动创建
+ 绑定挂载的Volume，具体挂载位置用户指定



#### 数据持久化之 Data Volume

##### Data Volume 使用场景

+ 数据库
+ docker启动数据库参数 : http://m.zhizuobiao.com/mysql/mysql-18091200180/



如果不指定-v，就会生成随机的文件名

```shell
# 创建2个无密码的mysql
[root@docker-node1 labs]# docker run -d --name mysql1 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql

# 查看data volume 和 volume详细信息
[root@docker-node1 labs]# docker volume ls
DRIVER              VOLUME NAME
local               7a99b433546366acf45d632a34a0b8e539d5ead02e6d0746ad7c01d8316caffb
[root@docker-node1 labs]# docker volume inspect 7a99b433546366acf45d632a34a0b8e539d5ead02e6d0746ad7c01d8316caffb
[
    {
    ......
    ....
        ################# mount到本地的 目录下 #################
        "Mountpoint": "/var/lib/docker/volumes/7a99b433546366acf45d632a34a0b8e539d5ead02e6d0746ad7c01d8316caffb/_data",
		}
]

# 当删除容器 mysql1时，volume还是存在
[root@docker-node1 labs]# docker stop mysql1
mysql1
[root@docker-node1 labs]# docker rm mysql1
mysql1

# 查看volume
[root@docker-node1 labs]# docker volume ls
DRIVER              VOLUME NAME
local               7a99b433546366acf45d632a34a0b8e539d5ead02e6d0746ad7c01d8316caffb
local               d7568361685a31421113093acb8f8e663eb889794dd706c393cd5d1ca533fe61

# 删除volume
[root@docker-node1 labs]# docker volume rm 7a99b433546366acf45d632a34a0b8e539d5ead02e6d0746ad7c01d8316caffb
```



指定-v  自定义volume名 : 本地目录路径

```shell
# -v mysql:/var/lib/mysql
[root@docker-node1 labs]# docker run -d -v mysql:/var/lib/mysql --name mysql1 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql

#查看volume
[root@docker-node1 labs]# docker volume ls
DRIVER              VOLUME NAME
local               mysql

#进入mysql容器
[root@docker-node1 labs]# docker exec -it mysql1 /bin/bash
# 容器内连mysql 创建新的数据库docker
root@0d2dfcb26e85:/# mysql -u root
mysql> create database docker;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| docker             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.00 sec)
mysql> exit
root@0d2dfcb26e85:/# exit
exit
 
# 停止mysql1并删除容器mysql1
[root@docker-node1 labs]# docker stop mysql1
mysql1
[root@docker-node1 labs]# docker rm mysql1
mysql1

#此时查看名为mysql的volume还存在
[root@docker-node1 labs]# docker volume ls
DRIVER              VOLUME NAME
local               mysql

# 创建新的容器并让新的容器 使用这个 volume 除了容器名改变其他都没变
[root@docker-node1 labs]# docker run -d -v mysql:/var/lib/mysql --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
608dc524b00f8ed2f1ba92b7af218f9562c4d9e768883f009df39852d353ea1f

#发现进入容器链接mysql，之前在上一个删除容器前新建的docker数据库存在
[root@docker-node1 labs]# docker exec -it mysql2 /bin/bash
root@608dc524b00f:/# mysql -uroot
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| docker             |
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
5 rows in set (0.01 sec)

```

##### 结论 

+ 当通过`-v  volume_name : 本地目录` 命令，指定docker run 的volume，容器创建销毁并不会影响volume
  + 当删除原来的容器，新建新的容器，还是用之前 `-v  volume_name : 本地目录`命令挂载同样的volume，数据还是会存在

+ 或者在Dockerfile文件中定义 `VOLUME ["/var/lib/mysql"]` 持久化的路径





#### 数据持久化之 Bind Mouting (重要)

```shell
# -v 本地目录:容器目录
docker run -v /home/aaa:/root/aaa
```

实现数据同步：

+ 如果本地文件做了修改，也会同步到容器中，因为是做了映射，还是同一个文件和目录

实例：

```shell
[root@docker-node1 docker-nginx]# pwd
/home/vagrant/labs/docker-nginx

[root@docker-node1 docker-nginx]# ls
Dockerfile  index.html

######################### Dockerfile #########################
FROM nginx:latest
WORKDIR /usr/share/nginx/html
COPY index.html index.html

######################### index.html #########################
<h1>Hello Docker! </h1>

# 通过Dockerfile构建镜像
[root@docker-node1 docker-nginx]# docker build -t='wyzerg/my-nginx' .

# 实例化容器：将 /home/vagrant/labs/docker-nginx 目录
# 映射到Dockerfile里nginx的/usr/share/nginx/
# -v 当前的pwd目录位置:容器内目录 做映射(目录下文件将会开始同步)
[root@docker-node1 docker-nginx]# ll
-rw-r--r--. 1 vagrant vagrant 414 Apr 28 11:41 Dockerfile
-rw-r--r--. 1 vagrant vagrant  26 Apr 28 14:05 index.html
[root@docker-node1 docker-nginx]# docker run -d -v $(pwd):/usr/share/nginx/html -p 80:80 --name web wyzerg/my-nginx

# 当容器映射的目录下 有文件发生改变，本地目录下会同步发生改变
# 容器内
root@docker-node1 docker-nginx]# docker exec -it web /bin/bash
root@c61d1f432df2:/usr/share/nginx/html# ls
Dockerfile  index.html
root@c61d1f432df2:/usr/share/nginx/html# touch 1.txt
root@c61d1f432df2:/usr/share/nginx/html# exit
exit
# 容器外-->本地数据也发生了改变
[root@docker-node1 docker-nginx]# ll
total 8
-rw-r--r--. 1 root    root      0 Apr 28 14:18 1.txt
-rw-r--r--. 1 vagrant vagrant 414 Apr 28 11:41 Dockerfile
-rw-r--r--. 1 vagrant vagrant  26 Apr 28 14:05 index.html	 #<-------
```





#### Bind mouting实例

+ 通过Dockerfile构建镜像，会将连同Dockerfile文件同目录下项目文件都copy到容器内目录下
+ 容器内批量安装py库，然后执行启动脚本，将项目启动运行在容器内
+ 



目录结构

```shell
[root@docker-node1 labs]# cd flask-skeleton/
[root@docker-node1 flask-skeleton]# ll
total 32
-rw-r--r--. 1 vagrant vagrant  220 Apr 28 11:41 CONTRIBUTING.md
-rw-r--r--. 1 vagrant vagrant  179 Apr 28 11:41 Dockerfile			# <--- --- ----
-rw-r--r--. 1 vagrant vagrant 1080 Apr 28 11:41 LICENSE
-rw-r--r--. 1 vagrant vagrant 1126 Apr 28 11:41 README.md
drwxr-xr-x. 3 vagrant vagrant   52 Apr 28 11:41 doc
-rw-r--r--. 1 vagrant vagrant 1059 Apr 28 11:41 manage.py
drwxr-xr-x. 2 vagrant vagrant   75 Apr 28 11:41 migrations
-rw-r--r--. 1 vagrant vagrant  439 Apr 28 11:41 requirements.txt
drwxr-xr-x. 2 vagrant vagrant   37 Apr 28 11:41 scripts
drwxr-xr-x. 4 vagrant vagrant   53 Apr 28 11:41 skeleton
-rw-r--r--. 1 vagrant vagrant   65 Apr 28 11:41 test-requirements.txt
drwxr-xr-x. 2 vagrant vagrant  102 Apr 28 11:41 tests
-rw-r--r--. 1 vagrant vagrant 1545 Apr 28 11:41 tox.ini

```

其中Dockerfile

```dockerfile
[root@docker-node1 flask-skeleton]# cat Dockerfile
FROM python:2.7
LABEL maintainer="wyzerg <wyzerg@163.com>"

# 将Dockerfile同级别所有文件都cp到容器内/skeleton目录下
COPY . /skeleton
WORKDIR /skeleton
# 并且批量安装/skeleton/requirements.txt python依赖库
RUN pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
EXPOSE 5000
# 执行/skeleton/scripts/dev.sh  启动项目的入口脚本文件
ENTRYPOINT ["scripts/dev.sh"]
```

启动项目入口脚本文件 scripts/dev.sh

```shell
#!/usr/bin/env bash
export APP_SETTINGS="skeleton.server.config.ProductionConfig"
python manage.py create_db
python manage.py create_admin
python manage.py create_data
python manage.py runserver -h 0.0.0.0
```

通过Dockerfile构建images

```shell
[root@docker-node1 flask-skeleton]# docker build -t='wyzerg/flask-skeleton' .
```

##### **通过新的镜像 实例化 容器**

目标：在宿主机修改文件可以同步到容器内的项目文件

查看当前目录下的项目文件

```shell
[root@docker-node1 flask-skeleton]# ll
total 32
-rw-r--r--. 1 vagrant vagrant  220 Apr 28 11:41 CONTRIBUTING.md
-rw-r--r--. 1 vagrant vagrant  223 Apr 28 14:43 Dockerfile
-rw-r--r--. 1 vagrant vagrant 1080 Apr 28 11:41 LICENSE
-rw-r--r--. 1 vagrant vagrant 1126 Apr 28 11:41 README.md
drwxr-xr-x. 3 vagrant vagrant   52 Apr 28 11:41 doc
-rw-r--r--. 1 vagrant vagrant 1059 Apr 28 11:41 manage.py
drwxr-xr-x. 2 vagrant vagrant   75 Apr 28 11:41 migrations
-rw-r--r--. 1 vagrant vagrant  439 Apr 28 11:41 requirements.txt
drwxr-xr-x. 2 vagrant vagrant   37 Apr 28 11:41 scripts
drwxr-xr-x. 4 vagrant vagrant   53 Apr 28 11:41 skeleton
-rw-r--r--. 1 vagrant vagrant   65 Apr 28 11:41 test-requirements.txt
drwxr-xr-x. 2 vagrant vagrant  102 Apr 28 11:41 tests
-rw-r--r--. 1 vagrant vagrant 1545 Apr 28 11:41 tox.ini
```

启动容器

将当前目录下所有文件 映射到 容器内的 /skeleton

```shell
[root@docker-node1 flask-skeleton]# docker run -d -v $(pwd):/skeleton -p 80:5000 --name flask wyzerg/flask-skeleton
```

测试效果：

```shell
[root@docker-node1 flask-skeleton]# vi skeleton/client/templates/main/home.html
#修改主页字符串
```

实现：修改宿主机文件，容器内项目文件随之改变，web页面内容随之改变



### 8.3) 部署 wordpress



查看镜像

```shell
[root@docker-node1 labs]# docker images
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
mysql                             latest              a7a67c95e831        14 hours ago        541MB
wordpress                         latest              1ab4107e168c        4 days ago          540MB
```

实例化mysql 并且定义 volume

+ 设置mysql环境变量，设置mysql root密码 ，设置运行数据库  

+ 由于mysql不是对外暴露端口供外接访问，而是与同机器上其他容器互联

```python
[root@docker-node1 labs]# docker run -d --name mysql -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=wordpress mysql:5.7
```



docker启动wordpress参数：https://hub.docker.com/_/wordpress

+ WORDPRESS_DB_PASSWORD 
  + 当docker run --link到一个已经启动的mysql容器，那么 WORDPRESS_DB_PASSWORD 默认使用MYSQL_ROOT_PASSWORD 环境变量的值
    + 所以mysql 容器启动参数指定了 MYSQL_ROOT_PASSWORD，那么wordpress容器 只要 --link 到mysql容器，那么启动参数不需要指定WORDPRESS_DB_PASSWORD

实例化wordpress

+ -e WORDPRESS_DB_HOST=mysql:3306 
  + mysql 是mysql容器启动的容器名mysql
+ --link mysql
  + 通过link，实现不用关注mysql地址，只需要使用mysql容器名就能实现 容器间通信
+ -p 8080:80
  + wordpress容器80端口映射到宿主机8080端口

```shell
[root@docker-node1 labs]# docker run -d -e WORDPRESS_DB_HOST=mysql:3306 --link mysql -p 8080:80 wordpress
```

访问 http:// xxx.xxx.xx.xx:8080











## 8, Docker Compose



不方便的点：

+ 从Dockerfile build image，或者dockerhub拉取image

+ 创建多个container，并管理多个container



Docker-compose 批处理工具

+ 通过yml文件定义多容器的docker应用
+ 通过yml文件定义：去创建或者管理多个容器



### 8.1) 安装 Docker-compose

mac/win版本的docker，随着docker安装后自带docker-compose工具

linux版本docker需要额外安装docker-compose工具 (https://docs.docker.com/compose/install/)

步骤1：下载docker-compose源 (国外/国内方式二选一)

```shell
# 国外源安装太慢，建议换国内的源
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 国内源安装
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

步骤2：给docker-compose可执行权限

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

查看下载好的docker-compose版本

```shell
[root@docker-host ~]# docker-compose --version
docker-compose version 1.25.0, build 0a186604
```



### 8.2) docker-compose.yml

docker-compose 工具的版本和 docker版本对应关系：https://docs.docker.com/compose/compose-file/

+ docker 19.03版本 对应  compose的3.8版本



#### docker-compose概念

services

+ 一个service代表一个container，这个container可以从dockerhub的image来创建 ，或者本地 dockerfile build出来的image来创建
+ service启动类似docker run ，可以给service指定network和volume

Networks

Volumes

```yaml
# 实例1
services:
	# services定义的名称db
	db:
		# 基础镜像
		images:postgres:9.4
		# 指定持久化存储volume
		volumes:
			- "db-data:/var/lib/postgresql/data"
		# 类似 --network back-tier参数
		networks:
			- back-tier
```

```yaml
# 实例2
services:
	# 本地build
	worker:
		build: ./worker
		# 这个worker和另外2个容器做links
		# 实际上下面只要container都连载同一个自定bridge网络
		# 不需要link就可以实现本来的功能
		links:
			- db
			- redis
		networks:
			- back-tier
```



### 8.3) 使用Docker-compose



#### docker-compose常用命令

+ https://www.cnblogs.com/beyang/p/11416646.html

docker-compose start

```shell
[root@docker-host 1dc]# docker-compose start
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
Starting mysql     ... done
Starting wordpress ... done

[root@docker-host 1dc]# docker-compose ps
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
     Name                    Command               State          Ports
-------------------------------------------------------------------------------
1dc_mysql_1       docker-entrypoint.sh mysqld      Up      3306/tcp, 33060/tcp
1dc_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:8080->80/tcp
```

docker-compose stop

+ 停止不删除容器



docker-compose down

+ 停止 并 删除容器 (不会删除镜像)



docker-compose images

+ 列出 docker-compose.yml里定义的container

```shell
[root@docker-host 1dc]# docker-compose images
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
   Container      Repository    Tag       Image Id       Size
---------------------------------------------------------------
1dc_mysql_1       mysql        5.7      f965319e89de   448.1 MB
1dc_wordpress_1   wordpress    latest   1ab4107e168c   540.2 MB
```



docker-compose exec

+ docker-compose exec    yml文件里定义容器名   bash 
+ 进入容器

```shell
[root@docker-host 1dc]# docker-compose exec mysql bash
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
root@bbcb24ba2e22:/#
```







#### 部署wordpress实例

新建目录在新目录下创建 docker-compose.yml文件

```yaml
# 定义docker-compose是3版本
version: '3'

services:

  wordpress:
    image: wordpress
    ports:
    	# 本地8080和容器内的80做映射
      - 8080:80
    depends_on:
      - mysql
    # environment 类似-e参数传递环境变量
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_PASSWORD: root
    # 网络使用最下面定义的bridge网络
    networks:
      - my-bridge

  mysql:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: wordpress
    # volume 是在下面 volumes创建的
    volumes:
      - mysql-data:/var/lib/mysql
    networks:
      - my-bridge

#定义 新建volume
volumes:
  mysql-data:

#定义新建network
networks:
  my-bridge:
    driver: bridge
```

当前目录下 如果文件名是 docker-compose.yml，不需要 -f 指定xxx.yml文件

 ```shell
# 运行docker-compose -d 后台运行
[root@docker-host 1dc]# docker-compose up -d
 ```











#### 部署flask app实例

让docker-compose使用的其中一个web的container 是通过Dockerfile创建的镜像生成的

目录结构

```shell
[root@docker-host labs]# cd flask-redis/
[root@docker-host flask-redis]# ll
total 12
-rw-r--r-- 1 vagrant vagrant 154 May  1 10:17 Dockerfile
-rw-r--r-- 1 vagrant vagrant 432 May  1 10:17 app.py
-rw-r--r-- 1 vagrant vagrant 186 May  1 10:17 docker-compose.yml
```

Dockerfile

```shell
FROM python:2.7
LABEL maintaner="wyzerg wyzerg@163.com"
COPY . /app
WORKDIR /app
RUN pip install flask redis -i https://pypi.tuna.tsinghua.edu.cn/simple
EXPOSE 5000
CMD [ "python", "app.py" ]
```

app.py

```python
from flask import Flask
from redis import Redis
import os
import socket

app = Flask(__name__)
redis = Redis(host=os.environ.get('REDIS_HOST', '127.0.0.1'), port=6379)

@app.route('/')
def hello():
    redis.incr('hits')
    return 'Hello Container World! I have been seen %s times and my hostname is %s.\n' % (redis.get('hits'),socket.gethostname())

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

docker-compose.yml

```yaml
version: "3"

services:

  redis:
    image: redis

  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - 8080:5000
    environment:
      REDIS_HOST: redis
```

docker-compose构建一组容器

```shell
[root@docker-host flask-redis]# docker-compose up -d
```

验证：测试访问web暴露出来的8080端口

```shell
[root@docker-host flask-redis]# docker-compose ps
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
       Name                      Command               State           Ports
-------------------------------------------------------------------------------------
flask-redis_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp
flask-redis_web_1     python app.py                    Up      0.0.0.0:8080->5000/tcp

[root@docker-host flask-redis]# docker-compose images
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
     Container          Repository       Tag       Image Id       Size
------------------------------------------------------------------------
flask-redis_redis_1   redis             latest   96812ab2b931   104.1 MB
flask-redis_web_1     flask-redis_web   latest   535ffb624fe2   907.1 MB

[root@docker-host flask-redis]# curl http://192.168.50.111:8080/
Hello Container World! I have been seen 3 times and my hostname is dfc7cc52f82c.
```



### 8.4) 单机 水平扩展和负载均衡 实例

docker-compose  up  --scale 可以水平扩展同一个容器的数量

+ 如果在yml文件中绑定了宿主机的端口会报错，因为多个容器不能同时映射宿主机同一个端口
  + 可以去掉 宿主机和容器的映射，什么都不暴露



![image-20200501234109374](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200501234109374.png)



项目目录结构

```shell
[root@docker-host lb-scale]# ll
total 12
-rw-r--r-- 1 vagrant vagrant 152 May  1 10:17 Dockerfile
-rw-r--r-- 1 vagrant vagrant 428 May  1 15:41 app.py
-rw-r--r-- 1 vagrant vagrant 327 May  1 10:17 docker-compose.yml
```

Dockerfile 容器内暴露80端口

```shell
FROM python:2.7
LABEL maintaner="wyzerg wyzerg@163.com"
COPY . /app
WORKDIR /app
RUN pip install flask redis -i https://pypi.tuna.tsinghua.edu.cn/simple
EXPOSE 80
CMD [ "python", "app.py" ]
```

app.py  web服务启动使用80端口

```python
from flask import Flask
from redis import Redis
import os
import socket

app = Flask(__name__)
redis = Redis(host=os.environ.get('REDIS_HOST', '127.0.0.1'), port=6379)

@app.route('/')
def hello():
    redis.incr('hits')
    return 'Hello Container World! I have been seen %s times and my hostname is %s.\n' % (redis.get('hits'),socket.gethostname())

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=80, debug=True)
```

docker-compose.yml 

```yaml
version: "3"

services:

  redis:
    image: redis

  web:
    build:
      context: .
      dockerfile: Dockerfile
    #由于水平扩展，不能多个容器web映射宿主机1个端口
    #ports: ["8080"]
    environment:
      REDIS_HOST: redis
	# 负载均衡容器
  lb:
    image: dockercloud/haproxy
    links:
      - web
    #宿主机8080和容器80互相映射
    ports:
      - 8080:80
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

运行yml文件批量起容器

```shell
[root@docker-host lb-scale]# docker-compose up -d

[root@docker-host lb-scale]# docker-compose ps
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
      Name                    Command               State                    Ports
---------------------------------------------------------------------------------------------------
lb-scale_lb_1      /sbin/tini -- dockercloud- ...   Up      1936/tcp, 443/tcp, 0.0.0.0:8080->80/tcp
lb-scale_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp
lb-scale_web_1     python app.py                    Up      80/tcp
```

此时架构只有1台haproxy - 1台web服务器 - 1台数据库

```shell
# 70988b55ee87 这个就是唯一一台起的容器ID
[root@docker-host lb-scale]# curl 127.0.0.1:8080
Hello Container World! I have been seen 1 times and my hostname is 70988b55ee87.
```

#### 水平扩展 --scale

```shell
[root@docker-host lb-scale]# docker-compose up --scale web=3 -d
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
lb-scale_redis_1 is up-to-date
Starting lb-scale_web_1 ... done
Creating lb-scale_web_2 ... done
Creating lb-scale_web_3 ... done
lb-scale_lb_1 is up-to-date
```

此时架构是 1台proxy  3台web  1台数据库

```shell
[root@docker-host lb-scale]# docker-compose ps
/bin/sh: warning: setlocale: LC_ALL: cannot change locale (zh_CN.UTF-8)
      Name                    Command               State                    Ports
---------------------------------------------------------------------------------------------------
lb-scale_lb_1      /sbin/tini -- dockercloud- ...   Up      1936/tcp, 443/tcp, 0.0.0.0:8080->80/tcp
lb-scale_redis_1   docker-entrypoint.sh redis ...   Up      6379/tcp
lb-scale_web_1     python app.py                    Up      80/tcp
lb-scale_web_2     python app.py                    Up      80/tcp
lb-scale_web_3     python app.py                    Up      80/tcp
```

这时多次访问web服务器，就会通过负载均衡将请求分发到3台不同的web上

```shell
# 分发到不同的web上面
[root@docker-host lb-scale]# curl 127.0.0.1:8080
Hello Container World! I have been seen 8 times and my hostname is 70988b55ee87.
[root@docker-host lb-scale]# curl 127.0.0.1:8080
Hello Container World! I have been seen 9 times and my hostname is fc1856ac91b0.
[root@docker-host lb-scale]# curl 127.0.0.1:8080
Hello Container World! I have been seen 10 times and my hostname is 1c0600b44ae1.
```



### 8.5) docker-compose 复杂的应用部署





#### 架构模型

![image-20200502000817310](/Users/wangjunxiang/Library/Application Support/typora-user-images/image-20200502000817310.png)

+ voting App web服务，负责获取投票结果数据
+ result app web服务，负责实时显示当前候选人得票情况，从PG数据库中获取数据
+ redis queue，作为缓存数据库，为了应对voting高并发而设计，先拿到投票数据写到redis缓存
+ java worker进程会实时取到redis缓存数据，拿到缓存数据写入 PG数据库中
+ PG后端数据库



#### 项目目录结构

```shell
[root@docker-host example-voting-app]# tree -C -L 2
.
|-- README.md
|-- architecture.png
|-- docker-compose.yml	#<-----
|-- result-app
|   |-- Dockerfile			#result容器使用的的Dockerfile
|   |-- package.json
|   |-- server.js
|   `-- views
|-- voting-app
|   |-- Dockerfile			#voting容器使用的Dockerfile
|   |-- app.py
|   |-- requirements.txt
|   |-- static
|   |-- templates
|   `-- utils
`-- worker
    |-- Dockerfile			#java进程容器使用的Dockerfile
    |-- pom.xml
    |-- settings.xml
    |-- sources.list
    `-- src
```

result-app web服务 容器的Dockerfile

```Dockerfile
FROM node:0.10

RUN mkdir /app
WORKDIR /app

ADD package.json /app/package.json
RUN npm install && npm ls
RUN mv /app/node_modules /node_modules

ADD . /app

ENV PORT 80
EXPOSE 80

CMD ["node", "server.js"]
```

voting-app web服务 容器的Dockerfile

```dockerfile
# Using official python runtime base image
FROM python:2.7

# Set the application directory
WORKDIR /app

# Install our requirements.txt
ADD requirements.txt /app/requirements.txt
RUN pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# Copy our code from the current folder to /app inside the container
ADD . /app

# Make port 5000 available for links and/or publish
EXPOSE 80 

# Define our command to be run when launching the container
CMD ["python", "app.py"]
```

java的worker程序 Dockerfile

```dockerfile
FROM maven:3.5-jdk-8-alpine

WORKDIR /code

ADD settings.xml /etc/maven/settings.xml
ADD pom.xml /code/pom.xml
RUN ["mvn", "dependency:resolve"]
RUN ["mvn", "verify"]

# Adding source, compile and package into a fat jar
ADD src /code/src
RUN ["mvn", "package"]

CMD ["/usr/lib/jvm/java-7-openjdk-amd64/bin/java", "-jar", "target/worker-jar-with-dependencies.jar"]
```

docker-compose.yml

```yaml
version: "3"

services:
  voting-app:
    #dockerfile构建位置
    build: ./voting-app/.
    volumes:
     - ./voting-app:/app
    #宿主机5000和容器80映射
    ports:
      - "5000:80"
    links:
      - redis
    networks:
      - front-tier
      - back-tier

  result-app:
    build: ./result-app/.
    volumes:
      - ./result-app:/app
    #宿主机5001和容器80映射
    ports:
      - "5001:80"
    links:
      - db
    networks:
      - front-tier
      - back-tier

  worker:
    build: ./worker
    links:
      - db
      - redis
    networks:
      - back-tier

  redis:
    image: redis
    ports: ["6379"]
    networks:
      - back-tier

  db:
    image: postgres:9.4
    volumes:
      - "db-data:/var/lib/postgresql/data"
    networks:
      - back-tier

volumes:
  db-data:

networks:
	#定义2个网络如果没有指明driver，默认bridge模式
  front-tier:
  back-tier:
```





#### 构建项目

```shell
[root@docker-host example-voting-app]# docker-compose up -d
```

或者 先docker-compose build 先把images build起来，再docker-compose up

验证：

访问 5000和5001 端口可以访问2个web服务



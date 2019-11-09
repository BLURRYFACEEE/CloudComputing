# DOCKER相关操作（centos7环境下）

- ### CentOS7安装Docker

- ### Docker加载CentOS7

- ### Docker中CentOS7安装WordPress

- ### 推送带有WordPress的镜像



## CentOS7安装Docker

### 要求：CentOS7且内核版本大于等于3.10

### ①安装Docker

#### 先查看**内核**版本

```
uname -r
```

<img src="./image/Install1.png" style="zoom:200%;" />

OK!是大于等于3.10的

#### CentOS 7的应用程序库可能不是最新的，因此首先更新应用程序数据库吧

```
yum check-update
```

#### 设置 Docker CE 资源库:  

```
    sudo yum install -y yum-utils    
    sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    sudo yum makecache fast
```

#### 安装Docker

```
sudo yum -y install docker-ce
```

#### 启动Docker

```
sudo systemctl start docker
```



### ②设置mirror

#### 新版的 Docker 使用 /etc/docker/daemon.json 来配置 Daemon

#### 在该配置文件中加入（没有该文件的话，请先创建一个）：

```
vim /etc/docker/daemon.json
```

 **{      "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]    }**



### ③开放管理端口映射

#### 管理端口在 /lib/systemd/system/docker.service 文件中

####     将其中第11行的 ExecStart=/usr/bin/dockerd 替换为：

#####     ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock -H tcp://0.0.0.0:7654

#####     （此处默认2375为主管理端口，unix:///var/run/docker.sock用于本地管理，7654是备用的端口）

#### 将管理地址写入 /etc/profile 

```
echo 'export DOCKER_HOST=tcp://0.0.0.0:2375' >> /etc/profile
```

####    使profile生效 

```
source /etc/profile
```

### ④启动Docker

```
systemctl daemon-reload && service docker restart
```

### ⑤测试docker

```
sudo docker run hello-world
```





## Docker加载CentOS7

### ①直接拉取Centos7

```
docker pull centos:7
```

#### 拉完查看下成功否

```
docker images
```

**运行Docker容器（为了方便检测后续wordpress搭建是否成功，需设置端口映射（-p），将容器端口80 
映射到主机端口8888，Apache和MySQL需要 systemctl 管理服务启动，需要加上参数 –privileged 
来增加权，并且不能使用默认的bash，换成 init，否则会提示 Failed to get D-Bus connection: 
Operation not permitted ，-name 容器名  ，命令如下 ）**

```
docker run -d -it --privileged --name wordpress -p 8888:80 -d centos:7 /usr/sbin/init
```

#### 查看已启动的容器

```
docker ps
```

<img src="./image/Docker1.png" style="zoom:200%;" />

#### 输入以下命令进入容器shell（f14为容器ID，可为简写）

```
docker exec -it f14 /bin/bash
```

变成这样即为成功

#### 你可以在此shell运行任何命令，比如安装Apache Web服务器：

```
yum install httpd
```





## Docker中CentOS7安装WordPress

详见这个链接哈[WordPress安装教程](https://github.com/BLURRYFACEEE/CloudComputing/tree/master/Website)

#### 最后安装完在浏览器输入IP:8888出现这个表示容器安装成功

<img src="./image/WordPress1.png" style="zoom:200%;" />

#### 出现这个表示安装成功！

<img src="./image/WordPress2.png" style="zoom:200%;" />





## 推送带有WordPress的镜像

### 先前往[DockerHub网站](https://hub.docker.com/)注册账号（若访问速度慢就进别的网站镜像）

**注册完后将容器生成镜像  (所生成的镜像名由   “Docker用户名/Docker仓库名“组成  ，否则推送会报错： denied: requested access to the resource is denied )**

docker commit -a "Docker用户名" -m "提交描述" 容器id 镜像名:tag标签

```
docker commit -a "1481604320" -m "wordpress on centos7" f1421c82af6c 1481604320/centos:v1
```

**登录Docker**

```
docker login
```

**推送镜像**

```
docker push 镜像名:tag标签
# 举例  docker push 1481604320/centos:v1
```

若出现以下界面即为成功！

<img src="./image/DockerCentOS.png" style="zoom:200%;" />
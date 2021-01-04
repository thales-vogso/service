# docker


Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。



## 官网


https://www.docker.com/



# 安装


## windows


直接下载exe，然后一直next就可以了


https://docs.docker.com/docker-for-windows/install/


## mac


下载app，然后拖到application里就可以了


https://docs.docker.com/docker-for-mac/install/


## linux


可以根据内核下载rpm或者deb，当然我们服务器最常用的还是yum和apt-get


https://docs.docker.com/engine/install/


### centos安装docker


repo初始状态是没有docker引擎的


我们查一下


```bash
yum search docker

```


果然没有


```bash
CentOS Linux 8 - AppStream                                                              1.8 MB/s | 6.3 MB     00:03
CentOS Linux 8 - BaseOS                                                                 1.8 MB/s | 2.3 MB     00:01
CentOS Linux 8 - Extras                                                                  13 kB/s | 8.6 kB     00:00
============================================ Name & Summary Matched: docker ============================================
pcp-pmda-docker.x86_64 : Performance Co-Pilot (PCP) metrics from the Docker daemon
podman-docker.noarch : Emulate Docker CLI using podman
```


这时候，我们添加一个repo


```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```


现在有了


```bash
Docker CE Stable - x86_64                                                               7.5 kB/s | 7.1 kB     00:00
============================================ Name & Summary Matched: docker ============================================
docker-ce-rootless-extras.x86_64 : Rootless support for Docker
pcp-pmda-docker.x86_64 : Performance Co-Pilot (PCP) metrics from the Docker daemon
podman-docker.noarch : Emulate Docker CLI using podman
================================================= Name Matched: docker =================================================
docker-ce.x86_64 : The open-source application container engine
docker-ce-cli.x86_64 : The open-source application container engine
```


安装吧


```bash
sudo yum install docker-ce docker-ce-cli containerd.io
```

启动


```bash
sudo systemctl start docker
```


如果要卸载


```bash
sudo yum remove docker-ce docker-ce-cli containerd.io
```


如果想彻底不要创建的镜像和容器，需要删除一下


```bash
sudo rm -rf /var/lib/docker
```


旧版本的卸载不一样


```bash
sudo yum remove docker \
                docker-client \
                docker-client-latest \
                docker-common \
                docker-latest \
                docker-latest-logrotate \
                docker-logrotate \
                docker-engine
```

### 二进制安装


如果服务器是freeBSD之类的或者就想修改一下源码，我们可以使用二进制安装


根据内核下载相应的源码


https://download.docker.com/linux/static/stable/


```
aarch64/
armel/
armhf/
ppc64le/
s390x/
x86_64/
```


解压下载的包


```bash
sudo tar xzvf /path/to/<FILE>.taz
```


复制到/usr/bin位置或者也可以ln一下


```bash
sudo cp docker/* /usr/bin/
```


后台启动，并且要自己写一个自启动脚本


```bash
sudo dockerd &
```


可以了


```bash
docker run hello-world
```


# 使用


比较简单的来说，先创建一个镜像(image)，然后根据这个镜像创建容器(container)



## 选择镜像


就像github一样，docker也有自己的hub


https://registry.hub.docker.com/search?q=&type=image


我们可以使用别人创建好的镜像，也能上传自己的镜像让别人使用


## 拉取镜像


首先找到我们想要的镜像，必须一个基础服务centos


https://registry.hub.docker.com/_/centos


我们拉取镜像


```bash
docker pull centos
```


可以查看现在有的镜像


```bash
docker images
```


有我们刚才拉取的centos


```bash
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
centos        latest    300e315adb2f   3 weeks ago     209MB
```


## 根据镜像创建容器


我们现在根据刚才的centos镜像创建一个容器，名字叫mycentos1


```bash
docker run -itd --name mycentos1 centos /bin/bash
```


可以查看现在运行的容器


```bash
docker ps
```


可以看见有我们刚才创建的容器


```bash
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
1706021d46eb   centos    "/bin/bash"   7 seconds ago   Up 5 seconds             mycentos1
```

看见我们的containerid是1706021d46eb，后面我们要用到


## 进入容器


通过刚才的id:1706021d46eb，我们可以进入容器


```bash
docker exec -it 1706021d46eb /bin/bash
```


## 导出容器


通过刚才的id:1706021d46eb，我们可以导出容器


```bash
docker export 1706021d46eb > centosbak.tar
```


## 导入


可以把我们导出的容器导入


```bash
docker import ./centosbak.tar - bak/cent:v1
```


但是记住，我们是容器中导出，但是导入的是镜像


就是说我们可以做好一个配置，然后导出备份，以后只用导入这个备份，就可以一步完成之前的配置了


# 后记


这只是介绍了基本内容，更详细内容请看官网


https://docs.docker.com/get-started/


我们使用docker基本是用dockerfile还有compose


[dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)


[compose](https://docs.docker.com/compose/reference/overview/)

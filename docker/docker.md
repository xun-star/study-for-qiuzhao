# docker基础篇

## centos安装docker

官网：https://docs.docker.com/engine/install/centos/

```bash
# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3
sudo sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
# Step 4: 更新并安装Docker-CE
sudo yum makecache fast
sudo yum -y install docker-ce
# Step 4: 开启Docker服务
sudo service docker start

# 注意：
# 官方软件源默认启用了最新的软件，您可以通过编辑软件源的方式获取各个版本的软件包。例如官方并没有将测试版本的软件源置为可用，您可以通过以下方式开启。同理可以开启各种测试版本等。
# vim /etc/yum.repos.d/docker-ce.repo
#   将[docker-ce-test]下方的enabled=0修改为enabled=1
#
# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# yum list docker-ce.x86_64 --showduplicates | sort -r
#   Loading mirror speeds from cached hostfile
#   Loaded plugins: branch, fastestmirror, langpacks
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            @docker-ce-stable
#   docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable
#   Available Packages
# Step2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.0.ce.1-1.el7.centos)
# sudo yum -y install docker-ce-[VERSION]

```

## 卸载docker

```bash
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2.删除资源
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
# /var/lib/docker  docker的默认工作路径
```

## docker常用命令

### 镜像命令

```bash
docker images #查看镜像
docker search +镜像名 #搜索镜像
docker pull +镜像名 #下载镜像
docker rmi +镜像名 #删除镜像
docker rmi -f 镜像id                    # 删除指定的镜像
docker rmi -f 镜像id 镜像id 镜像id    # 删除多个镜像（空格分隔）
docker rmi -f $(docker images -aq)    # 删除全部的镜像
```

### 容器命令

```bash

docker run [可选参数] image #新建容器并启动
# 参数说明
--name="name"        容器名字：用来区分容器
-d                    后台方式运行：相当于nohup
-it                    使用交互式运行：进入容器查看内容
-p                    指定容器的端口（四种方式）小写字母p
    -p ip:主机端口：容器端口
    -p 主机端口：容器端口
    -p 容器端口
    容器端口
-P                     随机指定端口（大写字母P）
```

```bash
docker ps    # 列出当前正在运行的容器
# 命令参数可选项
-a        # 列出当前正在运行的容器+历史运行过的容器
-n=?    # 显示最近创建的容器（可以指定显示几条，比如-n=1）
-q        # 只显示容器的编号
```

```bash
exit        # 容器直接停止，并退出
ctrl+P+Q    # 容器不停止，退出
```

```bash
docker rm 容器id                    # 删除容器（不能删除正在运行的容器）如果要强制删除：docker rm -f 容器id
docker rm -f $(docker ps -aq)        # 删除全部容器
docker ps -a -q|xargs docker rm        # 删除所有容器
```

```bash
docker start 容器id        # 启动容器
docker restart 容器id    # 重启容器
docker stop 容器id        # 停止当前正在运行的容器
docker kill 容器id        # 强制停止当前容器
```

```bash
#命令docker run -d 镜像名
[root@JWei_0124 //]# docker run -d centos
5b06d0d14b3312e589a411dd9ae15589dc9321f771e5615b7ae26e85017de080
# 问题：docker ps发现centos停止了
# 常见的坑：docker容器使用后台运行，就必须要有要一个前台进程，docker发现没有应用，就会自动停止。
# 比如：nginx，容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```

```bash
docker logs -tf --tail 容器id
[root@JWei_0124 //]# docker logs -tf  容器id
[root@JWei_0124 //]# docker logs -tf --tail 10  容器id
# 显示日志
-tf                        # 显示日志
--tail number    # 要显示的日志条数
```

```bash
docker exec -it 容器id /bin/bash
docker attach 容器id
# docker exec        # 进入容器后开启一个新的终端，可以再里面操作（常用）
# docker attach        # 进入容器正在执行的终端，不会启动新的进程。
```

```bash
docker inspect 镜像id #查看镜像的元数据
[root@xuecheng ~]# docker inspect nginx
[
    {
        "Id": "sha256:a6bd71f48f6839d9faae1f29d3babef831e76bc213107682c5cc80f0cbb30866",
        "RepoTags": [
            "nginx:latest"
        ],
        "RepoDigests": [
            "nginx@sha256:10d1f5b58f74683ad34eb29287e07dab1e90f10af243f151bb50aa5dbb4d62ee"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-11-21T09:05:32.482668371Z",
        "Container": "a85c8fa20980a9a2650ddfe6d0c466ed963ea55d15a5ebee25a87a98ac6b0206",
        "ContainerConfig": {
            "Hostname": "a85c8fa20980",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.25.3",
                "NJS_VERSION=0.8.2",
                "PKG_RELEASE=1~bookworm"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
            ],
            "Image": "sha256:4a4f6f7325711db4e0d3ec9bfccb00ba81cd430304aa0075f6d93fd72347aa63",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "DockerVersion": "20.10.23",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.25.3",
                "NJS_VERSION=0.8.2",
                "PKG_RELEASE=1~bookworm"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "sha256:4a4f6f7325711db4e0d3ec9bfccb00ba81cd430304aa0075f6d93fd72347aa63",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 186779305,
        "VirtualSize": 186779305,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/d977f19341e093e470d812b235ff344bb8eaedaf66b8c02bba611911c1e9bc58/diff:/var/lib/docker/overlay2/9ad763ba936d6e2556d16801b891742ba717e6a9e1c1bd006e4aa8c4e6d6d2c1/diff:/var/lib/docker/overlay2/ab6139a1974cb2e287d69ef5c616dea2b0043ef0cf57089b223a5fa08221a597/diff:/var/lib/docker/overlay2/221bfee62278d15bfa2b956ec7af4241d72c2056f645ebeb606d81fb55e7f514/diff:/var/lib/docker/overlay2/1bc8b5ad2466dda5c15cbd2f745390ec145f4acdc0514539b2ed0166076065b6/diff:/var/lib/docker/overlay2/464d48e5369a26991e7df6c6de63a5defcf325427a63f9077ff84ac37d662dbf/diff",
                "MergedDir": "/var/lib/docker/overlay2/c65ec22d03111ed2d74b9e58e28703a02b95c223703110e107548831afd0e2e3/merged",
                "UpperDir": "/var/lib/docker/overlay2/c65ec22d03111ed2d74b9e58e28703a02b95c223703110e107548831afd0e2e3/diff",
                "WorkDir": "/var/lib/docker/overlay2/c65ec22d03111ed2d74b9e58e28703a02b95c223703110e107548831afd0e2e3/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:92770f546e065c4942829b1f0d7d1f02c2eb1e6acf0d1bc08ef0bf6be4972839",
                "sha256:8ae474e0cc8f5a81405b04143604f78bfac4756c523e276a36921a8c4da36567",
                "sha256:f5525891d9e9b43a95b4aa1f79405087922489eb300864a2683262aae0fa5b3a",
                "sha256:66283570f41bca3619443d121a79e810b8a72849b5329319993e538d563b3e2f",
                "sha256:c2d3ab485d1b375fdd309458d69d93f8eb9aba8472e928efa32d9e5eda631440",
                "sha256:cddc309885a283a35ef142af78bc6f2e9c9db10e1981c4ea9cfb2c00b83e68ff",
                "sha256:0d0e9c83b6f775d68c7517aabf39ec9123ffca29672e3c3f83c5af7fc6aa242b"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]


```

## 镜像/容器的导入导出

```bash
docker save -o <保存路径>/myimage.tar myimage:latest #导出镜像
docker load -i <路径>/myimage.tar #导入镜像

docker export <容器ID> > mycontainer.tar #导出容器
docker import mycontainer.tar #导入容器


```



## docker容器卷

>
>
>当Docker容器运行的时候，会产生一系列的数据文件，这些数据文件会在关闭Docker容器时，直接消失的。但是其中产生部分的数据内容，我们是希望能够把它给保存起来，另作它用的。
>
>​    关闭Docker容器=删除内部除了image底层数据的其他全部内容，即删库跑路
>
>期望：将应用与运行的环境打包形成容器运行，伴随着容器运行产生的数据，我们希望这些数据能够持久化。
>希望容器之间也能够实现数据的共享



* Docker容器产生的数据同步到本地,这样关闭容器的时候，数据是在本地的，不会影响数据的安全性。

```bash
docker volume create todo-db #创建卷
docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos getting-started
```



```bash
docker run -it -v 主机目录:容器内目录 镜像名 /bin/bash
# 通过 -v挂载目录后  会对这个俩个目录中的数据进行 双向绑定（bind） 
```

```bash
#mysql容器卷挂载
docker -v /root/mysql/data:/var/lib/mysql #挂载数据库中的表
docker -v /root/mysql/init:/docker-entrypoint-initdb.d#挂载初始化文件，脚本文件
docker -v /root/mysql/conf:/etc/mysql/conf.d#挂载配置文件
```



## dockerfile文件

```bash
FROM        # 基础镜像，一切从这里开始构建
MAINTAINER    # 镜像是谁写的：姓名+邮箱
RUN            # 镜像构建的时候需要运行的命令
ADD            # 步骤：tomcat镜像，这个tomcat压缩包！添加内容
WORKDIR        # 镜像的工作目录
VOLUME        # 挂载的目录
EXPOSE        # 暴露端口配置
CMD            # 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代
ENTRYPOINT    # 指定这个容器启动的时候要运行的命令，可以追加命令
ONBUILD        # 当构建一个被继承DockerFile这个时候就会运行ONBUILD的指令。触发指令。
COPY        # 类似ADD，将我们文件拷贝到镜像中
ENV            # 构建的时候设置环境变量！
```

```bash
# 1. 编写dockerfile的文件
FROM centos:7
MAINTAINER sywl<xxx@qq.com>
ENV MYPATH /usr/local
WORKDIR $MYPATH
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80
CMD echo $MYPATH
CMD echo "-----end-----"
CMD /bin/bash
# 2. 通过这个文件构建镜像
# 命令：docker build -f dockerfile文件路径 -t 镜像名:[tag]
docker build -f mydockerfile-centos -t mycentos:0.1 .
Successfully built 285c2064af01
Successfully tagged mycentos:0.1
# 3. 测试运行
```




### 获取镜像

从 Docker 镜像仓库获取镜像的命令是 docker pull。其命令格式为：

```
$ docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```

- Docker 镜像仓库地址：地址的格式一般是 <域名/IP>[:端口号]。默认地址是 Docker Hub(docker.io)。
- 仓库名：如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名>。对于 Docker Hub，如果不给出用户名，则默认为 library，也就是官方镜像。

比如：
```
$ docker pull ubuntu:18.04
18.04: Pulling from library/ubuntu
40dd5be53814: Pull complete 
Digest: sha256:d21b6ba9e19feffa328cb3864316e6918e30acfd55e285b5d3df1d8ca3c7fd3f
Status: Downloaded newer image for ubuntu:18.04
docker.io/library/ubuntu:18.04
```

上面的命令中没有给出 Docker 镜像仓库地址，因此将会从 Docker Hub （docker.io）获取镜像。而镜像名称是 ubuntu:18.04，因此将会获取官方镜像 library/ubuntu 仓库中标签为 18.04 的镜像。docker pull 命令的输出结果最后一行给出了镜像的完整名称，即： docker.io/library/ubuntu:18.04。

### 列出镜像
要想列出已经下载下来的镜像，可以使用 `docker image ls` 命令。

```
$ docker image ls
REPOSITORY                                      TAG       IMAGE ID       CREATED         SIZE
ubuntu                                          18.04     c6ad7e71ba7d   8 days ago      63.2MB
docker.elastic.co/kibana/kibana                 8.1.2     a6d3a3c39d21   5 weeks ago     843MB
docker.elastic.co/elasticsearch/elasticsearch   8.1.2     0652ab468732   5 weeks ago     1.19GB
fhsinchy/hello-dock   
```

列表包含了`仓库名`、`标签`、`镜像 ID`、`创建时间` 以及 `所占用的空间`。

其中仓库名、标签在之前的基础概念章节已经介绍过了。镜像 ID 则是镜像的唯一标识，一个镜像可以对应多个 标签。因此，在上面的例子中，我们可以看到 ubuntu:18.04 和 ubuntu:bionic 拥有相同的 ID，因为它们对应的是同一个镜像。

### 删除镜像

`docker image rm [选项] <镜像1> [<镜像2> ...]`



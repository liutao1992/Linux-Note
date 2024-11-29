### 查看当前正在运行或过去运行的所有容器：

`docker ps -a`

```
liutao@ubuntu:~$ docker ps -a
CONTAINER ID   IMAGE                                                 COMMAND                  CREATED        STATUS                    PORTS     NAMES
85495bd51f5a   docker.elastic.co/kibana/kibana:8.1.2                 "/bin/tini -- /usr/l…"   13 days ago    Exited (0) 7 days ago               kib-01
238a2bf95657   docker.elastic.co/elasticsearch/elasticsearch:8.1.2   "/bin/tini -- /usr/l…"   13 days ago    Exited (143) 7 days ago             es01
d42d3978fc05   fhsinchy/hello-dock                                   "/docker-entrypoint.…"   2 months ago   Exited (0) 2 months ago             agitated_solomon
3726653627d7   fhsinchy/hello-dock                                   "/docker-entrypoint.…"   2 months ago   Exited (0) 2 months ago             gracious_bhabha
```

### 运行容器

`docker container run <image_name>`

有了镜像后，我们就能够以这个镜像为基础启动并运行一个容器。以上面的 ubuntu:18.04 为例，如果我们打算启动里面的 bash 并且进行交互式操作的话，可以执行下面的命令。

```
$ docker container run -it --rm ubuntu:18.04 bash 
root@35229b0f4c26:/# cat /etc/os-release 
NAME="Ubuntu"
VERSION="18.04.6 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.6 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
root@35229b0f4c26:/# 
```

`docker container run` 就是运行容器的命令。

- `-it`: 这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
- `--rm`: 这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
- `ubuntu:18.04`: 这是指用 ubuntu:18.04 镜像为基础来启动容器。
- `bash`: 放在镜像名后的是 命令，这里我们希望有个交互式 Shell，因此用的是 bash。
  
进入容器后，我们可以在 Shell 下操作，执行任何所需的命令。这里，我们执行了 cat /etc/os-release，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 Ubuntu 18.04.1 LTS 系统。



### 开放端口

容器是隔离的环境。主机系统对容器内部发生的事情一无所知。因此，从外部无法访问在容器内部运行的应用程序。要允许从容器外部进行访问，必须将容器内的相应端口发布到本地网络上的端口。

`--publish <host port>:<container port>`

如我们运行以下指令：

`docker container run --publish 8080:80 fhsinchy/hello-dock`

`--publish 8080:80` 时，这意味着发送到主机系统端口 8080 的任何请求都将转发到容器内的端口 80。现在要在浏览器上访问该应用程序，只需访问 `http://127.0.0.1:8080`即可。


### 如何使用分离模式

run 命令的另一个非常流行的选项是 `---detach 或 -d 选项`。 在上面的示例中，为了使容器继续运行，必须将终端窗口保持打开状态。关闭终端窗口会停止正在运行的容器。这是因为，默认情况下，容器在前台运行，并像从终端调用的任何其他普通程序一样将其自身附加到终端。为了覆盖此行为并保持容器在后台运行，可以在 run 命令中包含 `--detach` 选项，如下所示：

```
docker container run --detach --publish 8080:80 fhsinchy/hello-dock
```

>提供选项的顺序并不重要。 如果将 `--publish 选项放在 --detach 选项之前`，效果相同。使用 run 命令时必须记住的一件事是镜像名称必须最后出现。如果在镜像名称后放置任何内容，则将其作为参数传递给容器入口点（在在容器内执行命令小节做了解释），可能会导致意外情况。

### 列出当前系统运行的容器

`docker container ls`

```
docker container ls

# CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                  NAMES
# 9f21cb777058        fhsinchy/hello-dock   "/docker-entrypoint.…"   5 seconds ago  
```

### 列出过去运行的所有容器

`docker container ls --all`


### 容器命名/重命名

默认情况下，每个容器都有两个标识符。 如下：

- CONTAINER ID - 64 个字符的随机字符串。
- NAME - 两个随机词的组合，下划线连接。

基于这两个随机标识符来引用容器非常不方便。如果可以使用自定义的名称来引用容器，那就太好了。

可以使用 `--name` 选项来命名容器。要使用名为 hello-dock-container 的 fhsinchy/hello-dock 镜像运行另一个容器，可以执行以下命令：

```
docker container run --detach --publish 8888:80 --name hello-dock-container fhsinchy/hello-dock

# b1db06e400c4c5e81a93a64d30acc1bf821bed63af36cab5cdb95d25e114f5fb
```

甚至可以使用 `container rename` 命令来重命名旧容器。该命令的语法如下:

`docker container rename <container identifier> <new name>`

该命令不会产生任何输出，但是可以使用 `container ls` 命令来验证是否已进行更改。 `rename` 命令不仅适用于处于运行状态的容器和还适用于处于停止状态的容器。


### 怎样停止或者杀死运行中的容器

可以通过简单地关闭终端窗口或单击 ctrl + c 来停止在前台运行的容器。但是，不能以相同方式停止在后台运行的容器。

有两个命令可以完成此任务。 第一个是 `container stop` 命令。该命令的通用语法如下：

`docker container stop <container identifier>`

> 其中 container identifier 可以是容器的 ID 或名称。

如果使用 name 作为标识符，则 name 将作为输出返回。stop 命令通过发送信号SIGTERM 来正常关闭容器。如果容器在一定时间内没有停止运行，则会发出 SIGKILL 信号，该信号会立即关闭容器。

如果要发送 SIGKILL 信号而不是 SIGTERM 信号，则可以改用 `container kill` 命令。`container kill` 命令遵循与 stop 命令相同的语法。

`docker container kill <container identifier>`

### 重新启动容器

当我说重启时，我指的如下是两种情况：

- 重新启动先前已停止或终止的容器。
- 重新启动正在运行的容器。

正如上一小节中学到的，停止的容器保留在系统中。如果需要，可以重新启动它们。`container start` 命令可用于启动任何已停止或终止的容器。该命令的语法如下：

`docker container start <container identifier>`

> 可以通过执行 container ls --all 命令来获取所有容器的列表，然后寻找状态为 Exited 的容器。

现在，可以使用 container ls 命令查看正在运行的容器列表，以确保该容器正在运行。默认情况下，`container start` 命令以分离模式启动容器，并保留之前进行的端口配置。因此，如果现在访问 `http：//127.0.0.1：8080`，应该能够像以前一样访问 hello-dock 应用程序。

现在，在想重新启动正在运行的容器，可以使用 `container restart` 命令。`container restart` 命令遵循与 `container start` 命令完全相同的语法。

这两个命令之间的主要区别在于，`container restart` 命令尝试停止目标容器，然后再次启动它，而 start 命令只是启动一个已经停止的容器。

在容器停止的情况下，两个命令完全相同。但是如果容器正在运行，则必须使用`container restart` 命令。

### 删除容器

如你所见，已被停止或终止的容器仍保留在系统中。这些挂起的容器可能会占用空间或与较新的容器发生冲突。可以使用 container rm 命令删除停止的容器。 通用语法如下：

`docker container rm <container identifier>`

要找出哪些容器没有运行，使用 `docker container ls --all` 命令并查找状态为 Exited 的容器。

```
$ docker container ls --all
CONTAINER ID   IMAGE                                                 COMMAND                  CREATED        STATUS                     PORTS     NAMES
85495bd51f5a   docker.elastic.co/kibana/kibana:8.1.2                 "/bin/tini -- /usr/l…"   3 weeks ago    Exited (0) 2 weeks ago               kib-01
238a2bf95657   docker.elastic.co/elasticsearch/elasticsearch:8.1.2   "/bin/tini -- /usr/l…"   3 weeks ago    Exited (143) 2 weeks ago             es01
d42d3978fc05   fhsinchy/hello-dock                                   "/docker-entrypoint.…"   2 months ago   Exited (0) 2 months ago              agitated_solomon
3726653627d7   fhsinchy/hello-dock  
```


也可以使用 `container prune` 命令来一次性删除所有挂起的容器。

```
$ docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N] y
Deleted Containers:
85495bd51f5a43c0d35be1e19da52b9c32851b1033b691f4bf526fab4d594592
238a2bf95657c08be2233b991477167725de38d9d332b3c7a168bea2ae4e98b4
d42d3978fc0515a3f85432044eb56d9138c0ffa77f21ccd5da554cbf3b4d6cc5
3726653627d701a8e73bd6d3b531c58e70078b483ec1875c73d2cf9d6da6d626

Total reclaimed space: 147MB
```















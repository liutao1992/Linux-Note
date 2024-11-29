
### 辅助命令

```bash
docker version 用来查看docker客户端引擎和server端引擎版本信息

docker info    用来查看docker引擎详细信息 

```

### 镜像相关操操作

```bash

a. 查看当前本地仓库中存在哪些镜像

docker image ls 或者 docker images

b. 下载一个镜像: docker pull 镜像名称:版本信息

如获取redis最新版本：docker pull redis
如获取redis指定版本：docker pull redis:6.0 # 获取Redis6.0


c. 搜索镜像: docker search 镜像名称

d. 正常删除镜像:  docker image rm 镜像名称/镜像ID    # 必须是没有运行过的镜像

   强制性删除:    docker image rm -f 镜像名称/镜像ID
```


### 容器相关操作

#### 查看当前docker引擎中存在的容器
```bash
docker ps    # 查看正在运行的容器

docker ps -a # 查看所有容器（运行、停止）
```

#### 如何运行一个容器 docker run 镜像名/镜像ID

```bash
如我们在本地运行一个tomcat容器，在运行时，我们需要设置容器与宿主机端口映射关系，否则无法访问

docker run -p 8000 (宿主机):8000 tomcat:8.0
```

#### 运行tomcat，开放端口映射，并且后台运行 -d 

```bash
docker run -dp 8000 (宿主机):8000 tomcat:8.0
```

#### 启动容器时，指定一个容器名称 --name 容器名称

```bash
docker run -dp 8000 (宿主机):8000 tomcat:8.0 --name tomcat8.0
```

#### 容器启动、停止、重启、暂停、恢复
```bash
docker start   容器名称/容器ID

docker stop    容器名称/容器ID

docker restart 容器名称/容器ID

docker pause   容器名称/容器ID

docker unpause  容器名称/容器ID
```

#### 删除容器

```bash
docker rm 容器名称/容器ID     # 只能删除已经停止运行的容器

docker rm -f 容器名称/容器ID  # 强制删除容器
```

#### 查看容器日志
```bash
docker logs [options] 容器名称/容器ID

-f        实时更新日志
-t        加入时间戳
-- tail   数字  显示最后多少条
```

#### 进入容器内部
```bash
docker exec -it 容器名称/容器ID  bash # it 表示进入交互模式
# 退出容器 exit
```

#### 文件拷贝

```bash
# 将容器中的文件拷贝到宿主机
docker cp 容器ID/name 容器内部资源路径 宿主机目录路径
# docker cp 34e861a54eee:/cf/app.py /home/workspace/

# 将宿主机中的文件拷贝到容器内部
docker cp 文件|目录 容器ID 容器路径 
# 如：docker cp ./app.py 34e861a54eee:/cf
```

#### 查看容器内部进程
```
docker top  容器名称/容器ID  # 查看当前容器内部进程
```

#### 查看容器内部细节
```
docker inspect  容器名称/容器ID 
```

#### 容器数据卷机制

```bash
# 用来实现容器中数据和宿主机中数据进行映射(数据同步)
# 注意：数据卷使用必须在容器首次启动时设置

使用：docker run -v 宿主机目录:容器内目录:ro

# 使用绝对路径设置数据卷
docker run -v 宿主机绝对路径：容器内路径 ......
# 注意：这种方式会将容器路径的内容全部清空，始终以宿主机路径为主

# 如这条指令的则会将宿主机中目录/Users/liutao/Documents/LinuxNote与容器中/data目录映射起来没实现数据同步
docker run -it -v /Users/liutao/Documents/LinuxNote:/data/ mycentos

# 若只想宿主机目录的改变同步容器，容器内的改变则不同步宿主机，我们可以加ro参数
# ro：ready only 如果在设置数据卷时指定ro，代表日后容器内路径为只读的
```

#### 定制镜像

容器可读可写，基于这个特性，我们就可以对容器进行深度定制，将修改的容器打包成为一个新的镜像，日后基于该镜像运行。

```bash
# 将容器打包成一个新的镜像

docker commit -m "描述信息" -a "作者信息" 容器名称/容器ID 打包的镜像名称:标签

# 如：docker commit -m "修改启动页" d2092365c504 codeformer-001
```

#### 备份镜像
```bash
docker save 镜像名/镜像ID -o 镜像名称.tar

# docker save a8dddcc6ffc5 -o codeformer-001.tar
```

#### 恢复镜像
```
docker load -i 镜像名称.tar

# (base) ➜  Desktop docker load -i codeformer-001.tar
# Loaded image ID: sha256:a8dddcc6ffc5c293cacd4c98390a2b0d649aec73902534201d254219b3a9cbd0
```

#### 构建镜像
```bash
# 基于dockerfile上下文制作镜像。
# 为了减少镜像大小，我们通常新建一个空文件夹，在该文件下构建镜像。

# 这将创建一个带有标签buildme的镜像。镜像的标签是镜像的名称。默认情况下是基于缓存构建镜像的。
docker build -t buildme . # buildme 镜像名称 

# 若我们不想使用缓存进行构建，则--no-cache即可
docker build -t buildme . --no-cache
```
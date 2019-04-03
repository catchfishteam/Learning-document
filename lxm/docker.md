# Docker command
> date: 2019-04-01

docker 运行portainer -d -v "/var/run/docker.sock:/var/run/docker.sock" -p 9000:9000 portainer/portainer

docker 启动images hello-world
docker container run hello-world

输出这段提示以后，hello world就会停止运行，容器自动终止。
有些容器不会自动终止，因为提供的是服务。比如，安装运行 Ubuntu 的 image，就可以在命令行体验 Ubuntu 系统。

对于那些不会自动终止的容器，必须使用docker container kill 命令手动终止。
$ docker container kill [containID]

image 文件生成的容器实例，本身也是一个文件，称为容器文件。也就是说，一旦容器生成，就会同时存在两个文件： image 文件和容器文件。而且关闭容器并不会删除容器文件，只是容器停止运行而已。

列出本机正在运行的容器
$ docker container ls

列出本机所有容器，包括终止运行的容器
$ docker container ls --all


终止运行的容器文件，依然会占据硬盘空间，可以使用docker container rm命令删除。

$ docker container rm [containerID]

运行上面的命令之后，再使用docker container ls --all命令，就会发现被删除的容器文件已经消失了。

docker container run命令会从 image 文件生成容器。

$ docker container run -p 8000:3000 -it koa-demo /bin/bash

或者

$ docker container run -p 8000:3000 -it koa-demo:0.0.1 /bin/bash
上面命令的各个参数含义如下：

-p参数：容器的 3000 端口映射到本机的 8000 端口。
-it参数：容器的 Shell 映射到当前的 Shell，然后你在本机窗口输入的命令，就会传入容器。
koa-demo:0.0.1：image 文件的名字（如果有标签，还需要提供标签，默认是 latest 标签）。
/bin/bash：容器启动以后，内部第一个执行的命令。这里是启动 Bash，保证用户可以使用 Shell。


## 发布 image 文件
容器运行成功后，就确认了 image 文件的有效性。这时，我们就可以考虑把 image 文件分享到网上，让其他人使用。

首先，去 hub.docker.com 或 cloud.docker.com 注册一个账户。然后，用下面的命令登录。

$ docker login
接着，为本地的 image 标注用户名和版本。

$ docker image tag [imageName] [username]/[repository]:[tag]

#### 实例
$ docker image tag koa-demos:0.0.1 ruanyf/koa-demos:0.0.1
也可以不标注用户名，重新构建一下 image 文件。


$ docker image build -t [username]/[repository]:[tag] .
最后，发布 image 文件。


$ docker image push [username]/[repository]:[tag]
发布成功以后，登录 hub.docker.com，就可以看到已经发布的 image 文件。


docker其他有用的命令

1）docker container start

前面的docker container run命令是新建容器，每运行一次，就会新建一个容器。同样的命令运行两次，就会生成两个一模一样的容器文件。如果希望重复使用容器，就要使用docker container start命令，它用来启动已经生成、已经停止运行的容器文件。

$ docker container start [containerID]

2）docker container stop

前面的docker container kill命令终止容器运行，相当于向容器里面的主进程发出 SIGKILL 信号。而docker container stop命令也是用来终止容器运行，相当于向容器里面的主进程发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号。

$ bash container stop [containerID]
这两个信号的差别是，应用程序收到 SIGTERM 信号以后，可以自行进行收尾清理工作，但也可以不理会这个信号。如果收到 SIGKILL 信号，就会强行立即终止，那些正在进行中的操作会全部丢失。

3）docker container logs

docker container logs命令用来查看 docker 容器的输出，即容器里面 Shell 的标准输出。如果docker run命令运行容器的时候，没有使用-it参数，就要用这个命令查看输出。

$ docker container logs [containerID]

4）docker container exec

docker container exec命令用于进入一个正在运行的 docker 容器。如果docker run命令运行容器的时候，没有使用-it参数，就要用这个命令进入容器。一旦进入了容器，就可以在容器的 Shell 执行命令了。

$ docker container exec -it [containerID] /bin/bash

5）docker container cp

docker container cp命令用于从正在运行的 Docker 容器里面，将文件拷贝到本机。下面是拷贝到当前目录的写法。

$ docker container cp [containID]:[/path/to/file] 
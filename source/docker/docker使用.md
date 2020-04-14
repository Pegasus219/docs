### docker使用

#### 设置镜像代理
    1、Docker 官方中国区：https://registry.docker-cn.com
    2、网易（推荐）：http://hub-mirror.c.163.com

    $ docker-machine ssh default
    $ sudo sed -i "s|EXTRA_ARGS='|EXTRA_ARGS='--registry-mirror=加速地址 |g" /var/lib/boot2docker/profile
	$ exit
    $ docker-machine restart default

    检查代理是否成功：
    $ docker info
    看是否有Registry Mirrors 信息

#### 下载golang镜像
    $ docker search golang 	#查询可用的golang资源
	$ docker pull golang:alpine	#下载镜像资源
	$ docker images		#检查已安装的镜像

#### 制作第一个go服务镜像
    在go代码项目根级目录下，创建Dockerfile文件
    
    FROM golang:alpine		#不加alpine会造成镜像很大
    
    EXPOSE 8001			#开放容器端口
    
    ENV MAIN_PORT=8001	#设置环境变量
    ENV GO111MODULE=on
    ENV GOPROXY=https://goproxy.cn,direct
    ENV GOSUMDB=sum.golang.org
    
    WORKDIR /go/src/demo	#定义工作目录（届时容器内go项目所在目录）
    
    RUN mkdir -p /go/src/demo #创建容器内go项目目录（/go/src固定）
    
    COPY . /go/src/demo		#将当前docker toolbox所在目录内的文件copy到容器环境内的go项目地址
    
    RUN go mod init demo	#创建go.mod引入外部包（项目中已有则可以不写）
    
    CMD go run main.go		#CMD和ENTRYPOINT表示在容器启动那一刻执行的命令
    
    或者将上述CMD写成：
    RUN go build main.go
    ENTRYPOINT ["./main"]

    从dockerfile生成docker镜像：
    $ docker build . -t <demo>:<v1>

#### 运行docker镜像，创建docker容器
    $ docker run -d -p 80:8001 <demo>:<v1>
    
    # -d 表示在后台运行
    # -it 表示以控制台方式运行
    # -p 80:8001 将宿主机的80端口映射到容器的8001端口（外部访问到80）
    # -v /d/DockerToolbox/logs:/go/src/demo/logs	将宿主目录与容器目录挂载
    
#### 其他一些操作命令:
    $ docker ps		#查看运行中的容器，参数-a表示查看全部容器
    
    $ docker stop <容器id>	#停止运行中的容器
    
    $ docker stop $(docker ps -q)	#停止所有运行中的容器
    
    $ docker rm <容器id>	#删除容器
    
    $ docker rm $(docker ps -aq)	#删除所有容器
    	
    $ docker rmi <镜像id或 镜像名:版本号>		#删除镜像前要先删除容器
    
    $ docker cp <容器id>:/go/src/demo/logs/. /Docker Toolbox/<目标目录>
    #将docker容器内生成的文件copy出来到宿主

#### docker-compose使用
    创建docker-compose.yml文件
		version: "3"
        services:
            <自定义项目名1>:
                image:  <docker镜像>:<v1>
                ports:
                    - "8001:8001"
                volumes:
                    - "<宿主机挂载点1>:<容器内挂载点>"
            <自定义项目名2>:
                image:  <docker镜像>:<v1>
                ports:
                    - "8002:8001"
                volumes:
                    - "<宿主机挂载点2>:<容器内挂载点>"

    #以上用于在一个节点服务器内创建多个go的docker容器

    运行docker-compose：
	$ docker-compose up -d

    查看已运行的容器实例：
	$ docker ps

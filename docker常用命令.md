attach     # 当前 shell 下 attach 连接指定运行镜像（直接进入容器启动命令的终端，不会启动新的进程） 
           ```dokcer attach 容器id```

build      # 通过 Dockerfile 定制镜像  
           ```docker build -t xxxx .```

commit     # 提交当前容器为新的镜像 
           ```docker commit -m="提交的描述信息" -a="作者" 容器id 要创建的目标镜像名:[标签名]```

cp         # 从容器中拷贝指定文件或者目录到宿主机中 
           ```docker cp 容器id:容器内路径 目标主机路径```

create     # 创建一个新的容器，同 run，但不启动容器

diff       # 查看 docker 容器变化 
           ```docker diff CONTAINER```

events     # 从docker服务获取容器实时事件 
	   ```docker events```
           
exec       # 在已存在的容器上运行命令(在容器中打开新的终端，并且可以启动新的进程) 
           ```docker exec -it 容器id /bin/bash```

exit       # 容器停止退出

export     # 导出容器的内容流作为一个tar归档文件[对应import] 
           ```docker export 容器id > xxx.tar```

ctrl+P+Q   # 容器不停止退出

history    # 展示一个镜像形成历史 
	   ```docker history 镜像id```

images     # 列出系统当前镜像 
           ```docker images```

import     # 导入tar包生成镜像[对应export] 
           ```docker import xxx.tar - 镜像名：tag```

info       # 显示系统相关信息 
           ```docker info```

inspect    # 查看容器详细信息 
           ```docker inspect 容器id```

kill       # kill 指定docker容器 
           ```docker kill (容器id or 容器名)```

load       # 从一个tar包中加载一个镜像[对应save] 
           ```docker load < xxxx.tar.gz```

login      # 注册或者登陆一个docker源服务器  
           ```docker login -u 用户名 -p 密码 仓库地址```

logout     # 从当前Docker registry退出 
           ```docker logout```

logs       # 输出当前容器日志信息 
           ```docker logs -t(显示时间戳)f(打印最新日志) --tail(显示多少条)数量 容器id```

port       # 查看映射端口对应的容器内部源端口

pause      # 暂停一个或多个容器 
           ```docker pause 容器id```

ps         # 列出容器列表 
           ```dock ps```

pull       # 从docker镜像源服务器拉取指定镜像或者库镜像
           ```docker pull repo/image_name:tag```
	   
push       # 推送指定镜像或者库镜像至docker源服务器
           ```docker push registry地址/目标仓库内路径/image_name:tag```

restart    # 重启运行的容器 
           ```docker restart (容器id or 容器名)```

rm         # 移除一个或者多个容器 
           ```docker rm 容器id / docker rm -f $(docker ps -a -q) #s删除所有容器```

rmi        # 移除一个或多个镜像[无容器使用该 镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除]

run        # 创建一个新的容器并运行一个命令 
           ```dock run -i(以交互模式运行)t(给容器重新分配一个终端)/-d（以后台方式运行容器并返回容器id) --name 容器起名 -p(指定端口映射)3344:80 镜像名```

save       # 保存一个镜像为一个 tar包[对应load] 
           ```docker save [OPTIONS] IMAGE [IMAGE...]```

search     # 在docker hub中搜索镜像 
           ```docker search 镜像名```

start      # 启动容器 
           ```docker start (容器id or 容器名)```

stop       # 停止容器 
           ```docker stop (容器id or 容器名)```

tag        # 给源中镜像打标签 
           ```docker tag image_id registry地址/目标仓库内路径/image_name:tag```

top        # 查看容器中运行的进程信息 
           ```docker top 容器id```

unpause    # 取消暂停容器 
           ```docker unpause 容器id```

version    # 查看docker版本号 
           ```docker version```

wait       # 截取容器停止时的退出状态值

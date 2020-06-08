# docker-learning
## 什么是Docker

Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。
Docker 在容器的基础上，进行了进一步的封装，从**文件系统**、**网络互联**到**进程隔离**等等，极大的简化了容器的创建和维护。使得**Docker 技术比虚拟机技术更为轻便、快捷**。

---

## docker不是虚拟机！！！
## docker容器与虚拟机的区别
### **传统虚拟机技术**
传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程。

![vm.png](https://i.loli.net/2020/01/13/PpdfqkZSDzjG6tm.png)

### **docker容器**
而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。

![docker.png](https://i.loli.net/2020/01/13/ZEvNldGTL4umMQ7.png)

**所以** 容器要比传统虚拟机更为轻便。

---

## Docker对于传统虚拟化的优势有哪些？
### **高效地利用系统资源**
由于容器不需要进行硬件虚拟以及运行完整操作系统等额外开销，Docker 对系统资源的利用率更高。无论是应用执行速度、内存损耗或者文件存储速度，都要比传统虚拟机技术更高效。因此，相比虚拟机技术，一个相同配置的主机，往往可以运行更多数量的应用。
### **更快速的启动时间**
Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间
### **一致的运行环境**
Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。
### **更轻松的迁移**
Docker 可以在很多平台上运行，无论是物理机、虚拟机、公有云、私有云，甚至是笔记本，其运行结果是一致的。
### **更轻松的维护和扩展**
Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易，也使得应用的维护更新更加简单，基于基础镜像进一步扩展镜像也变得非常简单。

## 对比传统虚拟机的总结：
| 特性  | 容器 | 虚拟机 |
|:------:|:--------:|:------:|
|    启动      |    秒级   | 分钟级 |
|    硬盘使用   | 一般为MB | 一般为GB |
|      性能    |    接近原生   | 弱于 |
| 系统支持量   |    单机支持上千个容器   | 一般几十个 |

---

## 基本概念：
### **Docker 镜像**
Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。

### **Docker 容器**
镜像（Image）和容器（Container）的关系，就像是面向对象程序设计中的 类 和 实例 一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。

容器的**实质是进程**，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的 命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个**隔离**的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。

每一个容器运行时，是以镜像为基础层，在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为 **容器存储层**。

容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。

按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 [数据卷](https://docker_practice.gitee.io/zh-cn/data_management/volume.html)（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。

数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。

### **Docker仓库**
镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

一个 Docker Registry 中可以包含多个**仓库（Repository）**；每个仓库可以包含多个**标签（Tag）**；每个标签对应一个**镜像**。

我们可以通过<仓库名>:<标签>的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 **latest** 作为默认标签。

最常使用的 Registry 公开服务是官方的 [Docker Hub](https://hub.docker.com/)，这也是默认的 Registry，并拥有大量的高质量的官方镜像

国内加速器 [阿里云加速器](https://cr.console.aliyun.com/)

---

## 安装Docker
安装方法[自己看去](https://docker_practice.gitee.io/zh-cn/install/)

---

# 这里以MYSQL为例

## 仓库位置
[docker-mysql](https://hub.docker.com/_/mysql/),里面有很多从未体验过的全新版本

## 获取镜像
```
docker pull mysql           #这个默认拉取的是最新版本的mysql

docker pull mysql:tag       #这个拉取对应tag版本的mysql
-----------------------------------------------------------
docker pull mysql:5.7       #我这里拉取5.7版本的mysql

docker image ls             #我们看一下我们已经拉取的镜像有哪些

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              c7460dfcab50        5 days ago          126MB
mysql               5.7                 db39680b63ac        2 weeks ago         437MB
centos              latest              0f3e07c0138f        3 months ago        220MB


```

## 让mysql跑起来，我这里使用docker-compose启动

## docker-compose的安装
```
curl -L https://github.com/docker/compose/releases/download/1.24.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

# 下载完毕过后我们需要赋予其执行权限
chmod +x /usr/local/bin/docker-compose

# 检查是否安装成功
docker-compose -v
# 显示以下表示安装成功！
docker-compose version 1.24.1, build 4667896b
```

## 编写docker-compose.yml
```
# 感谢DJ学长
version: '3'
services:
  mysql:
    image: mysql:5.7
    container_name: mysql57
    restart: always
    ports:
      - 3306:3306
    environment:
      - MYSQL_ROOT_HOST=%
      - MYSQL_ROOT_PASSWORD=YOUR_PASSWORD
    volumes:
      - /www/bak/data:/var/lib/mysql
      # 这个是宿主目录与数据卷的绑定，用于数据持久化

```
## 启用mysql
```
docker-compose -f docker-compose.yml up
# 启动完毕后我们查看启动的docker容器
docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
e8e732f5bb4f        mysql:5.7           "docker-entrypoint.s…"   2 hours ago         Up 2 hours          0.0.0.0:3306->3306/tcp, 33060/tcp   mysql57

# 这里我们也看到了本机的3306端口对应到了容器的3306端口，我们可以验证一下
# 本机查看3306端口占用情况
netstat -nult | grep 3306
tcp6       0      0 :::3306                 :::*                    LISTEN
# 我们也看到3306端口开启了监听
```
肯定有人会说我本机之前跑了mysql，这个3306是我本机的mysql。Emmm...好吧，我把我本机的mysql服务给关掉了，所以在启用启用之前呢3306端口是没有开放的。

说道mysql，我们就会想到Navicat，是的没错，我们在docker里面开了一个mysql，用navicat连不上的话等于没开。因为容器的网络和宿主是隔离的所以我们必须让宿主的3306端口与容器中的3306端口对应起来，这些在docker-compose.yml里已经有配置了，可以翻上去看看。

**好了，到此我们可以使用navicat连接一下数据库看看了**

---

# 验证一些东西。。。
总觉得有写地方还是没有完全弄懂，所以还是验证一下看看。
- 之前绑定了宿主目录和容器的目录，两边的东西应该是一样的才对
```
# 进到我们绑定的目录下查看文件 ls 指令就好啦
auto.cnf    ca.pem           client-key.pem  ibdata1      ib_logfile1  mysql               private_key.pem  server-cert.pem  sys
ca-key.pem  client-cert.pem  ib_buffer_pool  ib_logfile0  ibtmp1       performance_schema  public_key.pem   server-key.pem

# 是这些东西，fine~我们去绑定容器目录下看看是否一致
# 首先我们需要进入容器并且以交互控制台的模式
docker exec -it mysql57 /bin/bash
# 进到绑定的容器目录 敲ls
auto.cnf    ca.pem	     client-key.pem  ib_logfile0  ibdata1  mysql	       private_key.pem	server-cert.pem  sys
ca-key.pem  client-cert.pem  ib_buffer_pool  ib_logfile1  ibtmp1   performance_schema  public_key.pem	server-key.pem

# 嗯~一致的呢~
```

- 官方说经过这样持久化后，我们删除容器，数据是还在的。假设一个场景，我们sb地误删了mysql容器，我们之前有很多表在其中，我们重新启动mysql容器，那么数据还在吗？

总是有种感觉我在说废话~TAT~

## 首先我们先创建一个数据库
本人较懒，用navicat建了一个test库里面有张name表
```
# 我们进入容器看看，是不是有test数据库并且里面有name表
docker exec -it mysql57 /bin/bash # 熟悉的配方

mysql -uroot -p123456
# 看到这是不是就有种套娃的感觉了？仿佛我们在centos上又开了一个linux系统，我们在这个系统里起了一个mysql服务，我们需要进入mysql，就好比我们在自己系统上敲命令行就进去了。 
# 但是！！！！我还是要重申一遍，docker不是虚拟机！
show databases;
# 展示以下结果
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
+--------------------+
5 rows in set (0.00 sec)
# ok我们的test已经写入到mysql当中去了，我么再看看name表在不在test数据库中
use test;

show tables;
+----------------+
| Tables_in_test |
+----------------+
| name           |
+----------------+
1 row in set (0.00 sec)

desc name;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int(11)      | NO   | PRI | NULL    | auto_increment |
| name  | varchar(255) | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
2 rows in set (0.00 sec)
# 以上均为mysql语法，不懂自学mysql基础语法去~
```
到此，我们看到我们在本地可以对容器内的mysql进行操作了。好的接下来我们将容器关闭，再重启看看效果

## 关闭mysql容器
关闭容器的方式有很多种，各种花式操作，哎呀随便了目的达成就行了，我这里使用name关闭容器
```
# 我们停掉一个容器
docker container stop
# 我们看看navicat是否还能连接上mysql，答案是连接不上的
# 我们看看正在运行的容器
docker ps
# 没有mysql
# 我们看看所有容器包括停止的
docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS                  NAMES
e8e732f5bb4f        mysql:5.7           "docker-entrypoint.s…"   3 hours ago         Exited (0) 8 seconds ago                          mysql57
# 这里显示我们的mysql57已经退出了，还是正常退出呢~
# 我们restart一下看看
docker container restart
# 哇~~ 
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
e8e732f5bb4f        mysql:5.7           "docker-entrypoint.s…"   3 hours ago         Up 7 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   mysql57
# 想都不用想了，我们之前的配置都还在哎~，打开navicat连接成功，数据库表都还在
```

## 进一步萌生更大胆的想法
我们干脆把mysql容器删掉，我们重新启动一个mysql容器，看看是否还能连接上。额，这里也不需要删掉镜像了，反正删掉了也是重新拉取一个样~，而且启动容器的步骤也是与之前使用docker-compose.yml一样，所以此处省略一部分字，我们直接看效果。

```
# 我们直接删除docker容器，因为容器在跑着，我们需要强力♂删除
docker container rm -f mysql57 
# 敲下回车的一瞬间，世界都安静了~
# 容器被彻底删除掉了，ok，我们需要恢复，在恢复之前我们看看我们本地的数据还在不在？
此处省略几行代码，ok在的……
# 那就好办了，我们重新起一个docker容器，还是熟悉的配方
docker-compose -f docker-compose.yml up 
# 启动完成！ 我们再看看docker已经启动的容器
docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                               NAMES
6b8f366895c8        mysql:5.7           "docker-entrypoint.s…"   49 seconds ago      Up 3 seconds        0.0.0.0:3306->3306/tcp, 33060/tcp   mysql57
# 熟悉的配方有着熟悉的味道~
# navicat连接一下看看，都在的，boss再也不用担心我买机票跑路啦
```
---

# 小结一下
至此，我们已经成功的在docker里跑起了一个mysql，乍一看感觉有点难，其实自己一步一步跟着官方文档走下去也就那么一回事啦。
docker不是虚拟机，这一点要牢记在心，但是作为理解我们可以理解为我们起了一个docker容器就相当于我们在本地开了一个虚拟机。然后容器里跑的镜像就相当于我们的操作系统里跑了一个软件。

我们开了一个mysql容器，我们进入之后发现很想我们的linux操作系统，我们在里面输入mysql -uroot -pXXXX 我们也能进入到mysql中。哎，说好的禁止套娃呢，这个就可以看成套娃行为，在我们本机上又开了一个linux系统，我们在里面操作我们的mysql，就是这样。

对于数据持久化，我们在写yml配置文件的时候，就发现了我们将docker容器的的目录与本机的目录对应了起来，容器被删除了，我们本地的数据都还在。我们只需要在重新启动一个docker容器的时候将数据卷挂载到我们本地这边绑定就好了，所以说，熟悉的配方熟悉的味道~

---

# 这次是使用Docker部署Vue项目

## 背景：
可以说是某种机缘巧合或者就是我自己想造作。。。大作业需要部署emmm...明明可以直接使用nginx正向代理完事了，但是都2020年了，2020耶，不在项目外边包一层docker真的拿不出手（误）

---
# Let's do it!
## 首先Vue的项目得先打包（如何打包这里不做赘述）
打包成dist文件扔到服务器上。

## 重头戏开始！
- ### 在dist的根目录下也就是与dist同级目录下，创建nginx目录并进入
  ```
  mkdir nginx
  cd nginx
  ```

- ### 创建default.conf并且编辑
  
  ```
  server {
    listen       8005;
    server_name  localhost;

    #charset koi8-r;
    access_log  /var/log/nginx/host.access.log  main;
    error_log  /var/log/nginx/error.log  error;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
    # 配置反向代理，这里一定要问清楚前端他的根路径！！！
    location /v1/ {
      # 配置反向代理将前台的请求转发成如下，也就是服务端api
      proxy_pass http://120.27.247.78:3000/v1/;
    }


    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
  }
  ```
- ### 创建Dockerfile准备打包镜像
  ```
  vi Dockerfile
  
  # 这里是Dockerfile编辑内容
  FROM nginx
  ```
  没错相信你的眼睛，我没在开玩笑，就是这样，为什么？我下面会解释

- ### 求豆麻袋~我这里还是先说一下正常打包镜像如何配置Dockerfile
  ```
  FROM nginx
  COPY dist/ /usr/share/nginx/html/
  COPY nginx/default.conf /etc/nginx/conf.d/default.conf
  ```
  ’COPY dist/ /usr/share/nginx/html/‘ 这个命令是将与Dockerfile同级目录下dist目录中的内容复制到docker容器中的/usr/share/nginx/html/下
  
  那’COPY nginx/default.conf /etc/nginx/conf.d/default.conf‘也是同理咯，不需要再说了吧？

- ### 然后就是打包镜像
  ```
  docker build -t vuenginxcontainer .
  ```
  -t 是给image命名，同时不要忘记最后的  **"."**

  然后是查看一下我们的镜像文件
  ```
  docker image ls | grep vuenginxcontainer
  ```
  最后启动我们的docker容器
  ```
  docker run -p 8005:8005 -d --name vueApp vuenginxcontainer
  ```
  
  ### 结束！但是真的结束了吗？
  假如我们的docker内配置文件不用更改以后都不用更改，那么真的结束了。如果以后需要更改配置文件，那么我们需要的工作量，先停止容器，删除容器，删除镜像，更改配置文件，重新打包，运行容器。

  那么，能不能简化一下呢？只需要更改配置文件再重启容器那岂不美哉？还记得刚刚Dockerfile里只有FROM nginx这一句话的Dockerfile文件吗？这就是我们接下来要解决的——使用挂载启动容器。

  ```
  docker run -p 8005:8005 -d --name vuenginxnew --mount type=bind,source=$HOME/SelfWork/docker/vueclidemo/nginx,target=/etc/nginx/conf.d --mount type=bind,source=$HOME/SelfWork/docker/vueclidemo/dist,target=/usr/share/nginx/html nginx
  ```
  **上面的路径是我本地的路径，一个是dist文件夹的路径另一个是default.conf所在的目录，根据自己情况选择路径，使用绝对路径也是可以的**

  我们使用数据挂载来部署容器，每当我们更改挂载的配置文件并且重新启动容器就可以完成更改。总结一下流程，创建镜像时输入上述命令，需要更改配置文件：修改配置文件然后docker restart {containerId} 结束~，虽然创建image麻烦了一点，但是今后需要进行修改配置文件，咱们又是一条好汉！

- ### 好了结束了，真的结束了，可能有些地方阐述的不准确但是自己若干个月后能看懂就行~如果你在看，那么谢谢你，忍住想揍我的心看到了这里。感谢！

# Nginx proxy_pass的那些事儿
```
server {
   listen       80;
   server_name  localhost;

   location /api1/ {
           proxy_pass http://localhost:8080;
        }
   # http://localhost/api1/xxx -> http://localhost:8080/api1/xxx


   location /api2/ {
           proxy_pass http://localhost:8080/;
        }
   # http://localhost/api2/xxx -> http://localhost:8080/xxx


   location /api3 {
           proxy_pass http://localhost:8080;
        }
   # http://localhost/api3/xxx -> http://localhost:8080/api3/xxx


   location /api4 {
           proxy_pass http://localhost:8080/;
        }
   # http://localhost/api4/xxx -> http://localhost:8080//xxx，请注意这里的双斜线，好好分析一下。


   location /api5/ {
           proxy_pass http://localhost:8080/haha;
        }
   # http://localhost/api5/xxx -> http://localhost:8080/hahaxxx，请注意这里的haha和xxx之间没有斜杠，分析一下原因。

   location /api6/ {
           proxy_pass http://localhost:8080/haha/;
        }
   # http://localhost/api6/xxx -> http://localhost:8080/haha/xxx

   location /api7 {
           proxy_pass http://localhost:8080/haha;
        }
   # http://localhost/api7/xxx -> http://localhost:8080/haha/xxx

   location /api8 {
           proxy_pass http://localhost:8080/haha/;
        }
  # http://localhost/api8/xxx -> http://localhost:8080/haha//xxx，请注意这里的双斜杠。
}
```
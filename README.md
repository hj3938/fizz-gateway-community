A Managerment API Gateway in Java . Fizz Gateway 是一个基于 Java开发的微服务网关，能够实现热服务编排、自动授权选择、线上服务脚本编码、在线测试、高性能路由、API审核管理等目的，拥有强大的自定义插件系统可以自行扩展，并且提供友好的图形化配置界面，能够快速帮助企业进行API服务治理、减少中间层胶水代码以及降低编码投入、提高 API 服务的稳定性和安全性。

## Fizz的设计

<img width="600" src="https://github.com/wehotel/fizz-gateway-community/blob/master/docs/fizz_design.png" />

## 产品特性

- 集群管理：Fizz网关节点是无状态的，配置信息自动同步，支持节点水平拓展和多集群部署。
- 服务编排：支持热服务编排能力，支持前后端编码，随时随地更新API。
- 负载均衡：支持round-robin负载均衡。
- 服务发现：支持从Eureka注册中心发现后端服务器。
- 配置中心：支持接入apollo配置中心。
- HTTP反向代理：隐藏真实后端服务，支持 Rest API反向代理。
- 访问策略：支持不同策略访问不同的API、配置不同的鉴权等。
- IP黑白名单：支持配置IP黑白名单。
- 自定义插件：强大的插件机制支持自由扩展。
- 可扩展：简单易用的插件机制方便扩展功能。
- 高性能：性能在众多网关之中表现优异。
- 版本控制：支持操作的发布和多次回滚。
- 管理后台：通过管理后台界面对网关集群进行各项配置。

## 基准测试

我们将Fizz与Spring官方spring-cloud-gateway进行比较，使用相同的环境和条件，测试对象均为单个节点。

- Intel(R) Xeon(R) CPU X5675 @ 3.07GHz * 4
- Linux version 3.10.0-327.el7.x86_64
- 8G RAM


|         产品         | QPS     | 90% Latency(ms) |
| :------------------: | ------- | -------------------- |
|    直接访问后端服务    | 9087.46 | 10.76 |
|     fizz-gateway     | 5927.13 | 19.86 |
| spring-cloud-gateway | 5044.04 | 22.91 |

## 版本对照

- Fizz-gateway-community： 社区版

- Fizz-manager-professional：管理后台专业版（服务端）

- Fizz-admin-professional：管理后台专业版（前端）

| Fizz-gateway-community | Fizz-manager-professional | Fizz-admin-professional |
| ---------------------- | ------------------------- | ----------------------- |
| v1.0.0                 | v1.0.0                    | v1.0.0                  |
|                        |                           |                         |

请根据社区版的版本下载对应的管理后台版本


## 部署说明

[详细部署教程>>>](https://wehotel.github.io/fizz-gateway-community/guide/installation/) 

### 安装依赖

安装以下依赖软件：

- Redis 2.8或以上版本
- MySQL 5.7或以上版本
- Apollo配置中心 (可选)
- Eureka服务注册中心

依赖的安装可参考详细部署教程

### 安装Fizz

#### 管理后台

从github的releases(https://github.com/wehotel/fizz-gateway-community/releases)下载 fizz-manager-professional 和 fizz-admin-professional 的安装包

-  管理后台服务端（fizz-manager-professional）

1. 首次安装执行`fizz-manager-professional-1.0.0-mysql.sql`数据库脚本
2. 将`application-prod.yml`、`boot.sh`、`fizz-manager-professional-1.0.0.jar`拷贝到`/data/webapps/fizz-manager-professional`目录下
3. 修改`application-prod.yml`文件，将相关配置修改成部署环境的配置
4. 修改`boot.sh`文件，将`RUN_CMD`变量值修改成部署环境的JAVA实际路径
5. 执行 `chmod +x boot.sh` 命令给`boot.sh`增加执行权限
6. 执行 `./boot.sh start` 命令启动服务，支持 start/stop/restart/status命令
7. 服务启动后访问 http://IP:8000/fizz-manager （将IP替换成服务部署机器IP地址），使用超级管理员账户`admin`密码`Aa123!`登录

-  管理后台前端（fizz-admin-professional）

zip资源包解压后，取文件夹【fizzAdmin】放置于服务器静态数据存放目录 如：/home/data/  

nginx配置

```
server {
  listen 9000;
  server_name localhost:9000;
  location / {
    root /home/data/fizzAdmin;
  }
  location ^~ /api {
    rewrite ^/api/(.*) /$1 break;
    proxy_pass http://127.0.0.1:8000;
  }
}

# 注：root中地址需与资源包存放目录路径一致
# 注：http://127.0.0.1:8000 为管理后台(fizz-manager-professional)的访问地址
```

访问地址

【资源部署服务器IP + 端口号】如：http://127.0.0.1:9000/    

（端口号与nginx配置端口号一致）

#### fizz-gateway-community社区版

说明：如果使用apollo配置中心，可把application.yml文件内容迁到配置中心（apollo上应用名为：fizz-gateway）；使用不使用apollo可去掉下面启动命令里的apollo参数。

脚本启动:

1. 下载fizz-gateway-community的最新代码，修改application.yml配置文件里eureka、redis的配置，使用maven构建好并把构建好的fizz-gateway-community-1.0.0.jar和boot.sh放同一目录
2. 修改boot.sh脚本的apollo连接，JVM内存配置
3. 执行 `./boot.sh start` 命令启动服务，支持 start/stop/restart/status命令

IDE启动:

1. 本地clone仓库上的最新代码
2. 将项目fizz-gateway导入IDE
3. 导入完成后设置项目启动配置及修改application.yml配置文件里eureka、redis的配置，在VM选项中加入`-Denv=dev -Dapollo.meta=http://localhost:66`(Apollo配置中心地址)

jar启动: 

1. 本地clone仓库上的最新代码，修改application.yml配置文件里eureka、redis的配置
2. 在项目根目录fizz-gateway-community下执行Maven命令`mvn clean package -DskipTests=true`打包
3. 进入target目录，使用命令`java -jar -Denv=DEV -Dapollo.meta=http://localhost:66 fizz-gateway-community-1.0.0.jar`启动服务

网关访问地址格式：

http://127.0.0.1:8600/proxy/[服务名]/[API Path]



## 官方技术交流群

Fizz官方技术交流①群（已满）

Fizz官方技术交流②群（已满）

Fizz官方技术交流③群：512164278

![](https://github.com/wehotel/fizz-gateway-community/blob/master/docs/fizz_qq_group.png)



## 文章列表

[服务器减少50%，研发效率提高86%，我们的管理型网关Fizz自研之路](https://www.infoq.cn/article/9wdfiOILJ0CYsVyBQFpl)

[微服务之聚合网关Fizz安装教程](https://www.jianshu.com/p/96c1f306aa2b)



## 授权说明

1. 网关核心项目fizz-gateway-community社区版本以GNU v3的方式进行的开放，可以免费使用。

2. 管理后台项目(fizz-manager-professional和fizz-admin-professional)作为商业版本仅开放二进制包 [免费下载](https://github.com/wehotel/fizz-gateway-community/releases)，而商业项目请联系我们（524423586@qq.com）进行授权。



## 系统截图

![](https://github.com/wehotel/fizz-gateway-community/blob/master/docs/ui_intro_aggr1.png)

![](https://github.com/wehotel/fizz-gateway-community/blob/master/docs/ui_intro_aggr2.png)

![](https://github.com/wehotel/fizz-gateway-community/blob/master/docs/ui_intro_appid.png)

![](https://github.com/wehotel/fizz-gateway-community/blob/master/docs/ui_intro_plugin.png)

![](https://github.com/wehotel/fizz-gateway-community/blob/master/docs/ui_intro_route.png)
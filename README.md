node-deploy-practice
====================


## 如何选择node版本？

从目前的测试数据来看，iojs新版本性能会更好一些，如果测试写的不够多的话，老老实实的用开发时的版本吧

推荐nvm，是node version manager，是借鉴rvm的一个开源项目

- nvm 支持多版本
- nvm支持多版本npm包切换，而且安装在用户目录下面，不需要使用sudo安装


## 统一环境

开发环境

- nvm
- nrm
- nodejs 0.10.38
- node-inspector

部署环境

- nvm
- nrm
- iojs 2.x
- pm2
- nginx

## express 部署

expressjs里默认给出的是`node bin/www`

它有2个问题

1. 没有cluster，不适合产品模式
1. 代码变动不能自动reload，不适合开发模式

所以我们需要把`bin/www`干掉，把启动服务器的代码放到app.js，这样就可以使用pm2了。安装supervisor模块，自动relaod

**EXPRESS最新版本需要测测**

本文主要介绍express线上部署实践

先看一下3个模式

- 开发模式
- 调试模式
- 产品模式

## 启动开发模式

因为我们在开发过程中总是希望修改后立即看到效果，而不是每次都要终止进程并重启。

supervisor 可以帮助你实现这个功能，它会监视你对代码的改动，并自动重启 Node.js

开发模式我推荐 supervisor

安装

    npm install --save supervisor

启动

    supervisor app.js or supervisor www/bin

我的实践是把package.json里的start

    "scripts": {
      "d": "node-debug app.js",
      "start": "./node_modules/.bin/supervisor app.js"
    }

然后

    npm start


还有一个库也不错，叫nodemon

see http://nodemon.io/

## 调试模式

推荐node-inspector

安装

    npm install --save node-inspector

启动

    node-inspector app.js or node-debug www/bin

我的实践是把package.json里的d里

    "scripts": {
      "d": "node-debug app.js",
      "start": "./node_modules/.bin/supervisor app.js"
    }
    
然后执行

    npm run d
    
    
npm run命令详解，见https://github.com/i5ting/npm-run-test

## 启动产品上线模式

产品模式我们推荐使用pm2

pm2是非常优秀工具，它提供对基于node.js的项目运行托管服务。它基于命令行界面，提供很多特性：

- 内置的负载均衡器（使用nodecluster module）
- 以守护进程运行
- 0s(不间断)重启
- 为ubuntu/ CentOS 提供启动脚本
- 关闭不稳定的进程（避免无限死循环）
- 基于控制台监控
- HTTP API
- 远程控制以及实时监控接口

pm2使用nodecluster构建一个内置的负载均衡器。部署多个app的实例来达到分流的目的以减轻单app处理的压力。

安装pm2

    sudo npm install -g pm2
 
查看基本帮助文档

```
  Basic Examples:

    Start an app using all CPUs available + set a name :
    $ pm2 start app.js -i 0 --name "api"

    Restart the previous app launched, by name :
    $ pm2 restart api

    Stop the app :
    $ pm2 stop api

    Restart the app that is stopped :
    $ pm2 restart api

    Remove the app from the process list :
    $ pm2 delete api

    Kill daemon pm2 :
    $ pm2 kill

    Update pm2 :
    $ npm install pm2@latest -g ; pm2 updatePM2

    More examples in https://github.com/Unitech/pm2#usagefeatures

  Deployment help:

    $ pm2 deploy help
```

上面基本是我们比较常用的

## pm2的最佳实践

把启动写到json配置文件中

```
// 20150531071243
// https://raw.githubusercontent.com/RocketChat/Rocket.Chat/master/pm2.production.json

{
  "apps": [
    {
      "name": "rocket.chat.0",
      "max_memory_restart": "1024M",
      "log_date_format": "YYYY-MM-DD HH:mm:ss SSS",
      "script": "/var/www/rocket.chat/bundle/main.js",
      "out_file": "/var/log/rocket.chat/app.log",
      "error_file": "/var/log/rocket.chat/err.log",
      "port": "8080",
      "env": {
        "CDN_PREFIX": "//dbde4sd21oahf.cloudfront.net",
        "MONGO_URL": "mongodb://localhost:27017/rocketchat",
        "MONGO_OPLOG_URL": "mongodb://localhost:27017/local",
        "ROOT_URL": "http://rocket.chat",
        "PORT": "8080"
      }
    },
    {
      "name": "rocket.chat.1",
      "max_memory_restart": "1024M",
      "log_date_format": "YYYY-MM-DD HH:mm:ss SSS",
      "script": "/var/www/rocket.chat/bundle/main.js",
      "out_file": "/var/log/rocket.chat/app.log",
      "error_file": "/var/log/rocket.chat/err.log",
      "port": "8081",
      "env": {
        "CDN_PREFIX": "//dbde4sd21oahf.cloudfront.net",
        "MONGO_URL": "mongodb://localhost:27017/rocketchat",
        "MONGO_OPLOG_URL": "mongodb://localhost:27017/local",
        "ROOT_URL": "http://rocket.chat",
        "PORT": "8081"
      }
    },
    {
      "name": "rocket.chat.2",
      "max_memory_restart": "1024M",
      "log_date_format": "YYYY-MM-DD HH:mm:ss SSS",
      "script": "/var/www/rocket.chat/bundle/main.js",
      "out_file": "/var/log/rocket.chat/app.log",
      "error_file": "/var/log/rocket.chat/err.log",
      "port": "8082",
      "env": {
        "CDN_PREFIX": "//dbde4sd21oahf.cloudfront.net",
        "MONGO_URL": "mongodb://localhost:27017/rocketchat",
        "MONGO_OPLOG_URL": "mongodb://localhost:27017/local",
        "ROOT_URL": "http://rocket.chat",
        "PORT": "8082"
      }
    },
    {
      "name": "rocket.chat.3",
      "max_memory_restart": "1024M",
      "log_date_format": "YYYY-MM-DD HH:mm:ss SSS",
      "script": "/var/www/rocket.chat/bundle/main.js",
      "out_file": "/var/log/rocket.chat/app.log",
      "error_file": "/var/log/rocket.chat/err.log",
      "port": "8083",
      "env": {
        "CDN_PREFIX": "//dbde4sd21oahf.cloudfront.net",
        "MONGO_URL": "mongodb://localhost:27017/rocketchat",
        "MONGO_OPLOG_URL": "mongodb://localhost:27017/local",
        "ROOT_URL": "http://rocket.chat",
        "PORT": "8083"
      }
    }
  ]
}
```

部署

    pm2 deploy ecosystem.json production setup
    

第一个傻瓜原则，你的机器有几核，你就几个实例

    Start an app using all CPUs available + set a name :
    $ pm2 start app.js -i 0 --name "api"
    
上面的配置文件了配置了4个，你应该能自己估算出点什么

第二个傻瓜原则，想机器瘫痪么？最简单的办法是内存不足，怎么样能防止内存被吃尽呢？

很简单，和lru cache一样，设置最大可用内存，防止内存用爆

参数里

     --max-memory-restart <memory>        specify max memory amount used to autorestart (in megaoctets)
     
配置文件了也一样

    "max_memory_restart": "1024M",
    
4个实例，每个可占用内存为1G，那么你应该能自己估算出点什么

第三个傻瓜原则，敏感信息咋办？有2种办法

1. 使用config/default.example.json。部署的时候拷贝一份，然后按需修改
1. 使用pm2的env，在环境变量里配置，pm2的json是在服务器上生成的，每个服务器都要生成一次的

推荐pm2的env这种方式，相对更简单点

第四个傻瓜原则，集群状态下的应用应该是stateless的（无状态），不能依赖代码去做一些事儿的，比如自己写队列处理cache

因为每个实例都是独立的，你读写不是全局的，一定会有各种问题，尽量使用第三方独立的服务，比如配置文件（非实例下面），比如redis等缓存，db等

## pm2高级用法

### reload

启动的时候会指定一个名字，以后根据该名字，进行特定操作

	pm2 reload some-app

它的好处是0秒延时，可以和`nginx -s reload`媲美
	
### monit

	pm2 monit

更多内容 https://github.com/Unitech/PM2#monitoring

### 日志

更多内容 https://github.com/Unitech/PM2#log-facilities

	$ pm2 logs
	$ pm2 logs big-api
	$ pm2 flush          # Clear all the logs

### web

	npm run web

访问 http://127.0.0.1:9615

### 开机启动

	pm2 startup centos
	
注意：示例的package.json里给出的是centos里增加开机启动项，如果是其他操作系统，请自己更改

更多内容 https://github.com/Unitech/PM2#startup-script-generation

	$ pm2 startup <ubuntu|centos|gentoo|systemd>
    
开机启动，还要注意的一点是要保存一次pm2运行数据的

    pm2 save
    

## 要不要nginx 或者 haproxy ？

nginx

- 主要做反向代理和静态资源处理（如果没有cdn，就把public下的内容放到nginx里）
- 负载
- 阻止了很多常见的攻击和慢客户端

nginx负载对应多台机器，每台机器里起多个（cpu个数）实例，单机负载使用pm2管理。

nginx和haproxy都是针对多台机器的负载，功能差不过，使用和插件方面，nginx稍强一点，知名度更高一点


## 另外一种部署方式

passenger是rails部署时常用的服务器，不过庆幸的是这帮货出了node版本，而且性能不错

有兴趣的可以去研究一下 nginx + passenger


## mongodb的部署

- 安全 https://cnodejs.org/topic/54cdd026ef1b48510c27e07a
- 集群 http://www.linuxidc.com/Linux/2015-02/113296.htm

## redis部署

http://www.linuxidc.com/Linux/2014-07/104306.htm

## 简单压测

- apache ab
- wrk

## node cluster和nginx负载性能比较

todo

别人测试的node的性能稍高，不过我还是想自己测测


## 性能调优

todo

## keeplive

- http://www.bubuko.com/infodetail-260176.html

## 监控

- newrelic https://cnodejs.org/topic/53fde58d7c1e2284785cd39e


## 资源

- http://promotion.pm2.io/
- https://github.com/Unitech/PM2
- https://www.digitalocean.com/community/tutorials/how-to-use-pm2-to-setup-a-node-js-production-environment-on-an-ubuntu-vps
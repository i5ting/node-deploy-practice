node-deploy-practice
====================

## 启动服务器

expressjs里默认给出的是`node bin/www`

它有2个问题

1. 没有cluster，不适合产品模式
1. 代码变动不能自动reload，不适合开发模式

### 启动产品上线模式

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
 
启动服务器

	npm start product

### 启动开发模式

开发模式我们推荐 supervisor

因为我们在开发过程中总是希望修改后立即看到效果，而不是每次都要终止进程并重启。

supervisor 可以帮助你实现这个功能，它会监视你对代码的改动，并自动重启 Node.js


	npm start dev

### package.json示例

```
{
  "name": "node-deploy-practice",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "npm run product",
    "product": "sudo NODE_ENV=production pm2 start bin/www -f -x -i max --name push",
    "dev": "./node_modules/.bin/supervisor ./bin/www",
		"test": "./node_modules/.bin/mocha -u tdd",
		"status": "sudo pm2 status",
		"stop": "sudo pm2 kill",
		"web": "sudo pm2 web",
		"monit": "sudo pm2 monit",
		"log": "sudo pm2 logs",
		"startup": "sudo pm2 startup centos"
  },
  "dependencies": {
    "apn": "^1.6.2",
    "body-parser": "~1.8.1",
    "cookie-parser": "~1.3.3",
    "debug": "~2.0.0",
    "express": "~4.9.0",
    "jade": "~1.6.0",
    "morgan": "~1.3.0",
    "pm2": "^0.12.1",
    "serve-favicon": "~2.1.3"
  },
  "devDependencies": {
    "mocha": "^2.0.1",
    "supervisor": "^0.6.0"
  }
}
```

### npm run命令详解

见https://github.com/i5ting/npm-run-test

## pm2高级用法

### stop 

	npm run stop
	
### monit

	npm run monit

更多内容 https://github.com/Unitech/PM2#monitoring

### log

	npm run log


更多内容 https://github.com/Unitech/PM2#log-facilities

	$ pm2 logs
	$ pm2 logs big-api
	$ pm2 flush          # Clear all the logs

### web

	npm run web

访问 http://127.0.0.1:9615

### startup

	npm run startup
	
注意：示例的package.json里给出的是centos里增加开机启动项，如果是其他操作系统，请自己更改

更多内容 https://github.com/Unitech/PM2#startup-script-generation

	$ pm2 startup <ubuntu|centos|gentoo|systemd>

## 资源

https://github.com/Unitech/PM2
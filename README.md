node-deploy-practice
====================

## 启动服务器

expressjs里默认给出的是`node bin/www`

它有2个问题

1. 没有cluster，不适合产品模式
1. 代码变动不能自动reload，不适合开发模式

### 启动产品上线模式

产品模式我们推荐使用pm2

安装pm2

 sudo npm install -g pm2
 
启动服务器

	npm start product

### 启动开发模式

开发模式我们推荐supervisor

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
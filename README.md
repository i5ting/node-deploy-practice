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
  "name": "QPush",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "npm run product",
    "product": "sudo NODE_ENV=production pm2 start bin/www -f -x -i max --name push",
    "dev": "./node_modules/.bin/supervisor ./bin/www",
		"test": "./node_modules/.bin/mocha -u tdd"
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

### monit

https://github.com/Unitech/PM2#monitoring

	$ pm2 monit

### log

https://github.com/Unitech/PM2#log-facilities

	$ pm2 logs
	$ pm2 logs big-api
	$ pm2 flush          # Clear all the logs

### web

	$ pm2 web


访问 127.0.0.1:9615

### startup

https://github.com/Unitech/PM2#startup-script-generation

	$ pm2 startup <ubuntu|centos|gentoo|systemd>

## 资源

https://github.com/Unitech/PM2
#express_mongodb_bootstrap_to_build_blog


##环境

安装express依赖和生成器

	npm install express --save
	npm install express-generator -g

通过生成器新建工程
	
	express -e ejs blog

**p.s. 通过 Express 应用生长期创建应用只是众多方法中的一种。你可以不使用它，也可以修改它让它符合你的需求**

执行完后提示：
	
	install dependencies:
	  $ cd blog && npm install
	
	run the app:
	  $ DEBUG=blog:* npm start

依照提示执行依赖的安装：

	cd blog&npm install
	
	
执行：

	$ DEBUG=* npm start
	
访问：

	localhost:3000	


##目录结构解析

##程序执行流向

	app.js 调用 app.use('/', routes); ==> routes/index.js 调用 res.render('index', { title: 'Express' });使用ejs模板引擎 ==> views/index.ejs


##路由控制

express 封装了多种 http 请求方式,我们主要只使用 get 和 post 两种。
##模板引擎

res.render() 渲染模版,并将其产生的页面直接返回给客户端。它接受两个参数,第一个是模板的名称,即 views 目录下的模板文件名,扩展名 .ejs 可选。第二个参数是传递给模板的数据,用于模板翻译。

ejs 的标签系统非常简单,它只有以下3种标签。

* <%- code %>:显示原始 HTML 内容。


###功能分析
rest api

* 首页：/
* 发布：/login
* 登录：/post
* 登出：/logout

* 首页：index.ejs
* 登录：login.ejs
* 注册：reg.ejs

页面构成文件都放在views目录下

由功能分析中，正好对应：

* 首页：/ GET
* 发布：/login GET POST
* 登录：/post GET POST
* 登出：/logout get




添加数据库依赖

	npm install mongodb --save

添加数据库配置文件settings.js


#### 数据库实例
新建文件夹models, 并创建db.js，用于创建数据库连接实例

#### 使用数据库




##实现

###页面具体设计

#### 样式

使用bootstrap

将bootstrap相关文件分别复制到public目录下对应的文件夹里

#### 页首和页尾







	
	
	
	
	
			
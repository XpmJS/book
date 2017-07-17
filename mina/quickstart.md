MINA Pages 模板开发指南
=============================================

## 一、准备知识

MP模板由 JavaScript (ES6)、 HTML、JSON 和 LESS(CSS) 组成。在开始开发MP模板前，需要掌握 JavaScript、 HTML 和 LESS(CSS) 的基础知识。

### 1. Gulp 自动化工具
MP模板采用 Gulp 做为构建工具，主要用来自动合并页面、提交编译、ES6 转换 ES5、LESS 转换 CSS、JS CSS文件压缩、静态文件同步到CDN等工作。Gulp 是开发 MP模板必备工具，开始开发前尽可能多的了解Gulp 的使用方法。

**Gulp 官网**
http://gulpjs.com/

**Gulp 入门指南**
http://www.gulpjs.com.cn/docs/getting-started/

### 2. MINA Pages 工作原理

MP 在设计阶段，结构和语法严重参考小程序框架 MINA 、VUE，主要目的是降低学习成本，采用大部分前端工程师都熟悉的开发方式（或习惯），用一套技术栈完成 WEB、小程序和移动应用（混合应用）开发。从程序结构和语法上，MP 形似 VUE，几乎与 MINA 一样，但工作原理上与小程序框架 MINA 、VUE 完全不同，具体特性如下: 


**服务端（预）渲染**

在制作MP的模板时，使用 Gulp 将MP模板、数据读取方式描述等文件，实时提交到服务端 CGI 进行编译。服务端的编译程序，将MP模板翻译成一个PHP程序，并将这个PHP程序保存到数据库，同时缓存到内存中。当通过浏览器访问一个页面时，路由程序首先根据地址信息，在缓存中寻找相匹配的PHP程序，如未找到再从数据库中读取；获取程序代码后，填入参数运行这个程序，输出HTML页面。 MP 模板中使用的变量，条件、列表等，在这个PHP程序运行时候，就已经渲染完成。这一特性对搜索引擎更友好，SEO效果更佳。同时也意味着，MP的模板文件无需上传到云端，修改模板较为方便，云端程序在迁移、分布式部署等场景下更易操作。


**VDOM和数据绑定**

MP模板在编译时，会自动集成一个 JS SDK。JSSDK 实现了一套 VDOM 机制，将模板中使用的变量，条件、列表等与VDOM绑定，当JS逻辑层调用 `setdata()` 方法修改变量数值时，通过刷新VDOM的方式，更新页面呈现。也就是说 MP模板在支持 SSR 的同时，又具备 JS逻辑层数据绑定的特性。



## 二、快速开始

### 第一步: 安装 NodeJS

在本地安装 NodeJS 环境。
参考教程: http://www.runoob.com/nodejs/nodejs-install-setup.html

### 第二步: 创建项目

#### 1. 下载 Hello World 示例包

下载地址: https://www.tuanduimao.com/mina.zip
下载示例项目包并解压缩，可以得到如下目录结构的文件夹: 

├── app/   移动应用目录
├── config.js   项目配置文件
├── gulpfile.js  Gulp 脚本文件
├── package.json  包依赖
├── web/   WEB目录 
│   ├── assets/
│   ├── common/
│   ├── hello/
│   ├── web.js     WEB 全局脚本 (每个页面都会运行 ）
│   ├── web.json   WEB 配置文件 
│   └── web.less   WEB 全局样式表
└── wxapp/  小程序目录


#### 2. 安装包依赖

命令行进入项目文件所在目录，运行 npm 指令安装 npm 包。
推荐使用淘宝镜像安装。

```bash
cd <path/mina>
npm install -g cnpm --registry=https://registry.npm.taobao.org
cnpm install
```


#### 3. 修改配置文件

**修改项目配置文件**

打开 `/config.js` 文件，根据项目情况填写服务端信息。

```javascript
config = {
	mina:{
		target: false,
		server:"<团队猫Server根地址>",
		project:"<项目英文名称>",
		appid:"<团队猫系统提供的 appid 用于鉴权>",
		secret:"<团队猫系统提供的 secret 用于鉴权>"
	}
}
```

**修改 WEB 配置文件**

打开 `/web/web.json` 文件，根据项目情况填写信息。

```json
{
	"cname":"<项目名称>",
	"pages":[ 
		"<声明页面路径，相对 /web 目录>",
		"/hello/world"
	],
	"common":[
	   "<声明共用路径，相对 /web 目录>",
		"/common"
	],
	"storage": {
		"engine":"minapages",
		"options":{
			"debug":true,
			"server":"<团队猫Server根地址>",
			"url":"/static-file/<项目英文名>",
			"origin":"/static-file/<项目英文名>",
			"prefix":"/<项目英文名>",
			"appid": "<团队猫系统提供的 appid 用于鉴权>",
			"secret":"<团队猫系统提供的 secret  用于鉴权>"
		},
		"pages":{
			"remote": "/<项目英文名>/pages", 
			"url": "/static-file/<项目英文名>/pages",
			"origin": "/static-file/<项目英文名>/pages"
		},
		"binds": [
		   {
				"local": "<本地路径， 相对项目根目录>",
				"remote": "<云端路径，本地对应的存储对象路径>",
				"url": "<CDN访问地址>",
				"origin":"<原文件访问地址>"
			},
			{
				"local": "/web/assets",
				"remote": "/deepblue/assets",
				"url": "/static-file/deepblue/assets",
				"origin": "/static-file/deepblue/assets"
			}
		]
	},
	"debug":true
}

```

说明: 
1. 只有在 `pages` 中声明的页面，才会有效。
2. `common` 中列出的目录，在 `gulp watch` 模式下，如发生变更则重新编译整个项目。
3. `cname` 定义尽量准确，这个信息会显示在页面管理后台。
4. `storage.pages` 主要用来定义编译好的 js 和  css 文件的引用路径。
5. `storage.binds` 主要用来定义图片，第三方JS库等静态文件引用路径。`binds`中定义的路径内容发生变化, 在`gulp watch`模式下会自动同步到对象存储中。
6. `storage.binds` 中声明的路径在模板中使用 `{{__STOR::}}` 引用。 例如 `<img src="{{__STOR__::/deepblue/assets}}/images/p1_01.png">`，为 `/web/assets/images/p1_01.png` 文件，编译后会自动替换成 CDN地址。
7. `storage.engine` 为 `minapages`时，使用服务端自带的存储引擎。


### 第三步: 创建页面

一个页面包含 `page`、 `json`、 `less` 和 `js` 四个文件。 `page`文件为页面 HTML 代码，`less` 定义页面样式， `json` 为页面配置信息定义, `js` 为页面脚本，支持ES6 。

#### 1. 创建并注册页面

**创建页面文件**

在 /web 目录下创建一个 `hello` 的目录，在该目录下，分别添加 `world.page`、`world.json`、`world.less` 和 `world.js` 文件。

**注册页面**

打开 WEB 配置文件 `/web/web.js`，修改 `pages` 字段，声明页面。

```json
{
	"cname":"示例项目",
	"pages":[ 
		"/hello/world"
	],
	.....
```

#### 2. 修改页面配置文件

打开页面配置文件 `/web/hello/world.json`, 填写页面信息

```json
{
	"cname":"<页面中文名称>",
	"data": {
		"<变量名>":{
			"api":"/<组织名>/<应用名>/<API名>/<方法>",
			"query":{
				"<查询条件 KEY>":"<查询条件 VALUE>",
				...
			}
		},
		"article":{
			"api":"/mina/pages/article/get",
			"query":{
				"articleId":"{{__var.id}}"
			}
		},
		"hots":{
			"api":"/mina/pages/article/search",
			"query":{
				"select":"article_id,title,publish_time",
				"category":"新闻公告",
				"perpage":6,
				"order":"publish_time desc"
			}
		},
		"navcates":"{{category.data}}",
		"crumb":"{{article.category.0}}",
		"page":{
			"title":"{{article.title}}"
		}
	},
	"entries":[
	   {"method":"<请求方式>", "router":"<路由>", "ttl":<缓存时间>},
		{"method":"GET", "router":"/article/{id:\\d+}", "ttl":0}
	],

	"align": {
		"mobile":"<手机浏览器适配页面>",
		"wechat":"<微信内打开适配页面>",
		"wxapp": "<微信小程序页面地址>"
	}
}
```

说明: 
1. `data` 字段必须存在。在`data` 中定义的变量，可以在页面中引用。如定义 "api" 和 "query"，则自定调用 API 并将结果集赋值给对应变量。
2. 入口配置 `entries` 中，路由的写法参照文档: https://github.com/nikic/FastRoute
2. 可将路由入口变量，作为API查询参数，具体写法为 `{{__var.<name>}}` 
3. 可将 API 的结果集，复制给新的变量。 具体写法为 `{{name}}`
4. `align` 适配支持的字段为: `desktop` 桌面浏览器,  `mobile` 手机浏览器, `wechat` 微信内打开 和 `wxapp` 微信小程序。适配地址不用写项目名称，与 `web.json` 中声明的地址保持一致。

#### 3. 修改页面模板

打开页面HTML文件 `/web/hello/world.page` 制作页面。(使用 Sublime 可以将.page 后缀设置为 HTML 格式高亮)

```html
....
<div class="header-nav row">
	<div>
		<ul class="nav-list">
			<li >
				<a href="/">首页</a>
			</li>
			<li mp:for="{{navcates}}" >
				<a href="/list/{{item.category_id}}">{{item.name}} </a>
			</li>
		</ul>
	</div>
</div>
....
```

#### 4. 修改页面样式

打开页面 LESS 文件 `/web/hello/world.less` 修改页面样式表。

```less
...
.page {
	.content {
		padding: 15px;
		h1, h2, h3,h4,h5,h6 {}
		p{}

		img {
			max-width: 100%;
		}
	}
}
....
```

#### 5. 修改页面脚本

打开页面脚本文件 `/web/hello/world.js` 修改页面脚本。

```javascript
...
let web = getWeb();
Page({
	data:{},
	onReady: function( get ) {
		let ping = "这是中文字符串 DONEHELLO POING";
		console.log( ping );
	},

	hello: function ( event ) {
		this.setData({title:'HELLO WORLD MPWORLD!' + new Date() });
	},

	world: null
})
....
```

说明: 
1. 每个页面必须包含脚本文件，且必须使用 `Page()` 函数创建。
2. 必须包含 `data` 字段，默认值与页面配置文件中定义的 `data` 一致。
3. `onReady` 函数页面载入完毕后执行，参数 `get` 为路由定义的入口变量。
4. 使用 `setData()` 设定 data 变量，可以同时更新页面呈现。


#### 6. 编译项目

进入项目根目录，运行 `gulp`命令编译。

```bash
gulp web
```

监听文件变化，当文件发生变化时自动编译, 进入项目根目录运行: 

```bash
gulp watch

```


### 高级用法: 全局脚本

项目 `/web/web.js` 脚本中的代码，在每个页面都会被执行。

```javascript
Web({
	title:'hello',
	onError:function( error ) {
		console.log( 'Error=', error, SERVICE_URL );
	}
});
```

在页面中可以使用 `getWeb()` 函数获取实例，并使用其定义的全局变量。

```javascript
let web = getWeb();
Page({
	data:{},
	
	onReady: function( get ) {
		console.log( 
		'page onReady data=', data ,  ' web.title=', web.title );
	}
	...
```


### 高级用法: 全局 Less 

项目 `/web/web.less` 中定义的样式表，在每个页面都会被引入。

```less
.header-top {
	color: #888888;
	font-size:12px;
	width: 1024px;
	margin-right: 0px;
	margin-left: 0px;
	line-height:1.8;
}	
```

页面 `/web/hello/world.less` 中定义

```less
.header-top {
    font-size:14px;
}
```

解析后 `header-top` 的值为

```less
{
	color: #888888;
	font-size:14px;
	width: 1024px;
	margin-right: 0px;
	margin-left: 0px;
	line-height:1.8;
}	
```


### 高级用法: 引用 Page 

可以使用 `include` 来引用HTML页面，可以用于抽离出头尾作为公用文件。

公共页面文件 `/web/common/header.page`

```html
<html>
<head>
	<title>{{page.title}}</title>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta content="text/html; charset=utf-8" http-equiv="Content-Type">
	<meta name="description" content="{{page.description}}">
	<meta name="keywords" content="{{page.keywords}}">
	<meta name="author" content="{{page.author}}">
	<meta name="generator" content="GitBook 3.1.1">
	<link rel="stylesheet" type="text/css" href="{{__STOR__::/deepblue/assets}}/bootstrap/bootstrap.min.css">
</head>
<body class="deepblue" >

```


页面文件 `/web/hello/world.page`

```html
<include src="__WEB_ROOT__/common/header.page"  />
<div class="container">
	<div class="header-top row" id="top">
	...
```

解析后的数值

```html
<html>
<head>
	<title>{{page.title}}</title>
	<meta charset="UTF-8">
	<meta http-equiv="X-UA-Compatible" content="IE=edge">
	<meta content="text/html; charset=utf-8" http-equiv="Content-Type">
	<meta name="description" content="{{page.description}}">
	<meta name="keywords" content="{{page.keywords}}">
	<meta name="author" content="{{page.author}}">
	<meta name="generator" content="GitBook 3.1.1">
	<link rel="stylesheet" type="text/css" href="{{__STOR__::/deepblue/assets}}/bootstrap/bootstrap.min.css">
</head>
<body class="deepblue" >
<div class="container">
	<div class="header-top row" id="top">
	...
```


说明:  `__WEB_ROOT__` 指代 /web 目录


### 高级用法: 引用 Less

可以使用 `@import '/vars.less';`  引入less, 根路径为 /web 目录

例如:

```less
@import '/vars.less';

*{
	margin:0;
	padding:0;
}
html,body{
	width: 100%;
}
ul,li{
	list-style: none;
}
a{
	text-decoration: none;
	color:#000;
}

...
```

### 高级用法: 引用 JavaScript 模块

可以将常用功能封装成模块，然后在页面脚本中，使用 `import` 命令引入。例如: 

`/web/common/world.js` 文件

```javascript 
class Hello {
	
	constructor( options ) {
		this.options = options;
	}

	ping() {
		console.log( this.options);
	}
}


class World extends Hello {
	constructor( name, options ) {
		super(options);
		this.name = name;
	}

	pong() {
		console.log( this.name, this.options );
	}

}
module.exports = World;
```


页面脚本 `/web/hello/world.js` 中引用

```javascript
import World from '../common/world.js';
let web = getWeb();
Page({
	data:{},
	
	onReady: function( get ) {
		let ping = "这是中文字符串 DONEHELLO POING";
		this.world = new World(get['name'], { param:'ok', get:get });
		console.log( ping );
		console.log( 'page onReady data=', data ,  ' web.title=', web.title );
	},

	hello: function ( event ) {
		this.world.pong();
		this.setData({title:'HELLO WORLD MPWORLD!' + new Date() });
	},

	world: null
})
```


### 模板语法

参考小程序文档，一般将 `wx` 换成 `mp` 即可。
https://mp.weixin.qq.com/debug/wxadoc/dev/framework/MINA.html


### 使用第三方JS库

MP 可以使用任何JS库，在模板中引入JS文件即可。也可在页面中，直接编写 JS 脚本(不推荐)
例： 

```html
...
<script type="text/javascript" src="{{__STOR__::/deepblue/assets}}/js/jquery-1.10.2.min.js"></script>
<script type="text/javascript" src="{{__STOR__::/deepblue/assets}}/bootstrap/bootstrap.min.js"></script>
<script type="text/javascript" src="{{__STOR__::/deepblue/assets}}/js/home-js.js"></script>
<script type="text/javascript" src="{{__STOR__::/deepblue/assets}}/js/jquery.validate.min.js"></script>

<script type="text/javascript">
	console.log( $('.selector').val() );
</script>
...
```



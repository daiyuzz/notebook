# splash的使用

Splash是一个JavaScript渲染服务，是一个HTTP API的轻量级浏览器，同时它对接了Python的的Twisted和QT库。利用它，我们同样可以实现动态渲染页面的抓取。

## 1、功能介绍
利用Splash我们可以实现如下功能：
- 异步方式处理多个网页渲染过程
- 获取渲染后的页面的源代码或截图
- 通过关闭图片渲染或者使用 Adblock规则来加快页面渲染速度
- 可执行特定的 JavaScript 脚本；
- 可通过Lua脚本来控制页面渲染过程
- 获取渲染的详细过程并通过 HAR(HTTP Archive)格式呈现

使用`sudo docker run -p 8050:8050 scrapinghub/splash`开启splash服务

## 2、scrapy-splash的使用

scrapy-splash是scrapy的一个插件，用来爬取动态网页，其效果和scrapy selenium phantomjs是一样的，都是通过渲染js得到动态网页然后实现网页解析，Splash是官推的js渲染引擎，和scrapy结合的比较好，使用的是webkit开发的轻量级无界面的浏览器，渲染之后结果和静态爬取一样可以直接解析处理，只是splash实在docker中运行。

与scrapy selenium相比，scrapy通过Downloader Middleware的方式实现了与selenium的对接，但是这种方式是阻塞的，也就是说这样就破坏了scrapy异步处理的逻辑，速度或收到影响，而scrapy-splash不会被破坏其异步加速逻辑。

Scrapy-Splash的安装分为两部分：
一个是Splash服务的安装，具体是通过Docker安装，会启动一个Splash服务，我们可以通过它的接口来实现JavaScript页面的加载。

另一个是Scrapy-Splash的python库安装，安装之后即可在Scrapy中使用Splash服务。



execute端点：
被用来提供如下服务：当用户想在页面中执行自己定义的js代码，如：用js代码模拟浏览器进行页面操作
这里，我们将execute看成是一个可以模拟用户行为的浏览器，而用户的行为我们用lua脚本进行定义

Splash对象常用的属性和方法总结
- splash：args属性--传入用户参数的表，通过该属性可以访问用户传入的参数，如splash.args.url、 splash.args.wait
- splash.images_enable属性--用于开启/禁止图片加载，默认值为True
- splash.go方法--请求url页面
- splash：wait方法--等待渲染秒数
- splash:evaljs方法--在当前页面下，执行一段js代码，并返回最后一句表达式的值
- splash:runjs方法--在当前页面下，执行一段js代码
- splash:url方法--获取当前页面的url
- splash：html--获取当前页面的HTML文章
- splash：get__cookies--获取cookies信息

** 在scrapy_splash中定义了一个SplashRequest类，用户只需要使用scrapy_splash.SplashRequest来替代scrapy.Request发送请求。**
该构造器常用的参数如下：
- url--待爬取的url地址
- headers
- scrapy
- 
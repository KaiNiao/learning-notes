## Django

### Django补充

Django 补充models操作，中间件， 缓存，信号，分页

https://www.cnblogs.com/jasonwang-2016/p/5910479.html

#### 静态文件

静态资源单独请求。模板中使用静态文件：

- 硬编码，绝对路径配置固定地址，要求STATIC_URL与hmtl访问地址相同；
- static编码，模板中引入模块的语法{% load static %}；
- 逻辑显示路径与磁盘存储路径可以不同，先逻辑查找STATIC_URL再物理查找STATICFILES_DIRS。

~~~python
# settings.py
# 用于生成网址，加在前面，模板中配置
# STATIC_URL = '/abc/'
STATIC_URL = '/static/'
# 静态文件存放的路径，BASE_DIR为当前项目路径，物理中寻找静态文件
STATICFILES_DIRS = [
    BASE_DIR,"static"
]

# index.html
{% load static %}
<body>
    {#<img src="/static/stator/1.jpg" alt="" width="200" height="300"><br>#}
    {#/abc/路径物理不存在，可以隐藏物理路径。要求与STATIC_URL一致#}
    <img src="/abc/stator/1.jpg" alt="" width="200" height="300"><br>
    {#Request URL:http://127.0.0.1:8000/abc/stator/1.jpg#}
    <img src="{% static 'stator/1.jpg' %}" alt="" width="200" height="300"><br>
    {#STATIC_URL变化不影响，通过static标签可以动态生成地址#}
</body>
~~~

#### 中间件

- 插件系统，可以介入django的请求与响应处理过程的不同环节，修改输入或输出；

- 可以作为面向切面过程的工具进行干预，类似于MVC中的IoC控制反转与DI依赖注入；
- 中间件的本质是一个独立的python类，可以对6种方法进行自定义声明。

~~~python
# MyException.py
class MyException(object):
    def __init__(self,get_response):
        self.get_response = get_response

    def __call__(self, request):
        return self.get_response(request)

    def process_exception(self,request,exception):
        '''处理视图中的报错'''
        return HttpResponse(exception)
        # invalid literal for int() with base 10: 'haha'
        # return HttpResponse("haha")

# views.py
def myExp(request):
    a = int("haha")
    return HttpResponse("sure")

# settings.py
MIDDLEWARE = [
    'stator.MyException.MyException',
    'django.middleware.csrf.CsrfViewMiddleware',
]
~~~

中间件图片

#### 缓存

保存动态页面，redis。

~~~python
CACHES = {
    'default':{
        "BACKEND":'redis_cache.cache.RedisCache',
        'LOCATION':'localhost:6379',
        'TIMEOUT':60,
    }
}
~~~

### 部署

在linux上实现。

- 从配置uWSGI、配置nginx、采集静态文件三个方面处理；
- 启动服务器后，加载正常，静态文件在采集之前无法加载。

#### 创建虚拟环境

安装 virtualenv：pip3 install virtualenv；

报错command not found，原因：未添加环境变量；

创建软链接：find / -name virtualenv，ln -s /usr/local/python3/bin/virtualenv /usr/bin/virtualenv；

创建虚拟环境：virtualenv myenv；

进入虚拟环境文件夹：cd myenv；

启动虚拟环境：source ./bin/activate；

退出虚拟环境：deactivate；

#### 安装MySQL

linux常见的三种centos、redhat、ubuntu（sudo apt-get install）安装mysql的方法均有所不同。

CentOS 6.5安装MySQL 5.7.10（rpm方式）部分过程：

https://blog.csdn.net/zhu19774279/article/details/50418711

- 查看CentOS自带MySQL 5.1组件 rpm -qa | grep -i mysql 并卸载；

- 安装MySQL（下面的安装顺序不能错，否则会安装失败）；

~~~mysql
rpm -ivh mysql-community-common-5.7.10-1.el6.x86_64.rpm
rpm -ivh mysql-community-libs-5.7.10-1.el6.x86_64.rpm
rpm -ivh mysql-community-client-5.7.10-1.el6.x86_64.rpm
rpm -ivh mysql-community-server-5.7.10-1.el6.x86_64.rpm
~~~

- 启动MySQ service mysqld start；
- 获得MySQL初始密码 grep 'temporary password' /var/log/mysqld.log；
- 初始密码登录MySQL，并修改初始密码 ALTER USER 'root'@'localhost' IDENTIFIED BY '新密码root'。

#### 安装Pycharm

安装pycharm报错：需要安装java JDK

CTRL + F	查找；CTRL + R	替换

#### WSGI

- python manage.py runserver是适用于开发阶段使用的服务器，生产环境中使用WSGI；
- WSGI：python web server gateway interface Web服务器网关接口，起到类似于java中tomcat的容器作用；

服务器上新建虚拟环境，进而安装python包：

pip3 freeze > list.txt，将当前生产环境下 Python 的模块收集起来存放到 list.txt 文件里；
pip3 install -r list.txt，在部署环境下将生产环境下的需要模块全部安装；

mysqlclient运行 runserver时报错：Django did you install mysqlclient?

需要在project目录的__init__.py 下写入

~~~python
import pymysql
pymysql.install_as_MySQLdb()
~~~

创建项目时wsgi.py中默认设置运行环境：

~~~python
import os

from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "day4.settings")

application = get_wsgi_application()
~~~

#### uWSGI

- uWSGI是用C编写实现WSGI规范所有接口的软件，可以监听网卡端口、遵从网络层传输协议、收发http协议级别的数据；
- 安装uWSGI模块并进行配置。

安装uwsgi：

uwsgi: command not found，解决办法：

查找路径find / -name uwsgi，/ -name中间有空格；

创建软链接 ln -s /usr/local/python3/bin/uwsgi /usr/bin/uwsgi；

检验是否安装成功：uwsgi --version；

运行uwsgi：uwsgi  --ini  uwsgi.ini，显示 [uWSGI] getting INI configuration from uwsgi.ini 表明uwsgi运行成功，可以利用 ps ajx|grep uwsgi 查看开启的进程；

关闭uwsgi：uwsgi  --stop uwsgi.pid，浏览器Unable to connect。

项目中新建uwsgi.ini文件进行配置：

~~~ python
#添加配置选择
[uwsgi]
#配置和nginx连接的socket连接
#socket=127.0.0.1:8000
#直接做web服务器，使用http连接
http = 127.0.0.1:8000
#配置项目路径，项目的所在目录
chdir=/home/kz/django/day4
#配置wsgi接口模块文件路径
wsgi-file=day4/wsgi.py
#配置启动的进程数
processes=4
#配置每个进程的线程数
threads=2
#配置启动管理主进程
master=True
#配置存放主进程的进程号文件
pidfile=uwsgi.pid
#配置dump日志记录
daemonize=uwsgi.log
~~~

#### nginx

1. 作用：

- 负载均衡，多台服务器轮流处理请求；

- 反向代理，隐藏真实服务器。

2. 实现：

客户端请求nginx，再由nginx请求uwsgi，运行django框架下的python代码。

3. 安装与配置

https://www.runoob.com/linux/nginx-install-setup.html

nginx的结构：

conf：nginx配置文件所在的目录，配置目录为/usr/local/webserver/nginx/conf/nginx.conf；

html：默认所带的页面所在的html；

logs：日志所在的目录；

sbin：nginx执行文件所在的目录，运行目录为/usr/local/webserver/nginx/sbin/nginx。

配置指的是配置server节点。

xshell中没有图形界面，不能用gedit查看文件内容，只能使用vi。

uwsgi中使用socket通信实现转发，浏览器访问127.0.0.1:80。

~~~python
server {
        listen       80;
        server_name  booktest;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;		
    	# 请求根目录时转向
        location / {
            include uwsgi_params;
            uwsgi_pass 127.0.0.1:8000;
        }
~~~

4. 启动时报错，nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)：

杀死进程：killall -9 nginx；
再次启动nginx：/usr/local/webserver/nginx/sbin/nginx；
查看是否启动：ps aux|grep nginx。

5. 处理静态文件

~~~python
# /usr/local/webserver/nginx/conf/nginx.conf
server {
        location /static {
            alias /var/www/day4/static;
        }
    
# settings.py 中进行配置
# 采集静态文件
# 命令行 python3 manage.py collectstatic
STATIC_ROOT = '/var/www/day4/static/'
~~~

将静态文件直接交给nginx进行返回，除了动态python代码其余不需要交给uwsgi；

在服务器上创建目录结构 /var/www/day4/static；

修改目录权限 sudo chmod 777 static。

### Git

git是分布式版本控制系统，可以回溯历史版本，便于多人合作的代码合并；

github实现多人的代码分发。

#### 远程仓库

- 加密连接github与本地：采用SSH加密，生成私钥id_rsa、公钥id_rsa.pub文件，github上new SSH key；
- github上创建仓库：new repository，并命名；
- 从github到本地，克隆 git clone git@github.com:KaiNiao/django1.git；
- 从本地到github，涉及本地仓库。

#### 本地仓库

- 本地仓库包括三部分，工作区（IDE打开目录）、暂存区、仓库区，其中暂存区、仓库区是版本库部分；
- 本地操作，从工作区到暂存区 git add ./，从暂存区到仓库区 git commit -m '创建day5与hello.txt'；
- 仓库区推到github，命令为 git push origin master，反之为 git pull同步，不再clone；
- 查看日志 git log --pretty=oneline，可以看到备注信息，如版本号、修改信息；
- 版本恢复，git reset HEAD与git checkout hello.txt，与提交与否无关；
- 查看暂存区信息，git status。

## Tornado

- 以django为代表的python web应用部署时采用uwsgi协议与服务器对接（被服务器托管），这类服务器通常基于多线程；

- 两类应用场景，瞬时高并发，如秒杀抢购，大量的HTTP持久连接（应用层HTTP1.1，传输层TCP），提出高并发问题C10K（Cocurrently handing ten thousand connection）；
- 设计拥有解决高性能的解决方案tornado（服务器+web框架）。

### Application

#### django与tornado

- django：管理后台、session功能、ORM；

- tornado：HTTP服务器、异步编程、WebSockets。

tornado应运行在类Unix平台，因为充分利用Linux的epoll工具与BSD的kqueue工具，是tornado不依靠多进程/多线程而达到高性能的原因。windows仅推荐在开发中使用。

#### tornado.web.Application

~~~python
# coding:utf-8

# tornado web 框架的核心模块
import tornado.web
import tornado.ioloop

class IndexHandler(tornado.web.RequestHandler):
    '''主页处理类'''
    def post(self, *args, **kwargs):
        '''get请求方式'''
        # post定义，get请求时报错，405: Method Not Allowed
        self.write("hello world")

if __name__ == "__main__":
    # 自己建立服务器，因此代码当做脚本执行
    # Application是服务器调用的接口，第一个参数是路由映射列表
    app = tornado.web.Application([(r"/",IndexHandler)])
    # 服务器绑定端口，还未监听
    app.listen(8000)
    # 输入输出循环模块ioloop，返回实例start开启监听
    tornado.ioloop.IOLoop.current().start()
~~~

与django的request和response不同，tornado将请求与响应都封装进了基类RequestHandler，因此采用面向对象的继承。

常见HTTP状态码：

- 200，OK，请求成功。一般用于GET与POST请求；
- 301/302，重定向；
- 304，Not Modified，https://jerrywang-sap.iteye.com/blog/2430734，与HTTP协议的缓存控制相关， 304 modified; restarting server；
- 403，Forbidden，服务器理解请求客户端的请求，但是拒绝执行此请求；
- 404，Not Found，服务器无法根据客户端的请求找到资源（网页）；
- 405，Method Not Allowed，客户端请求中的方法被禁止，如未定义；
- 500，Internal Server Error，服务器内部错误，无法完成请求；
- 504，Gateway Time-out，充当网关或代理的服务器，未及时从远端服务器获取请求。

#### tornado.httpserver

引入了tornado.httpserver模块，顾名思义，它就是tornado的HTTP服务器实现。

我们创建了一个HTTP服务器实例http_server，因为服务器要服务于我们刚刚建立的web应用，将接收到的客户端请求通过web应用中的路由映射表引导到对应的handler中，所以在构建http_server对象的时候需要传出web应用对象app。http_server.listen(8000)将服务器绑定到8000端口。

~~~python
if __name__ == "__main__":
    app = tornado.web.Application([(r"/",IndexHandler)])
    # 服务器绑定端口，还未监听
    # app.listen(8000)
    
    http_server = tornado.httpserver.HTTPServer(app) 
    http_server.listen(8000)
~~~

#### tornado.options

tornado.options模块用于全局参数定义、存储、转换。如用于服务端口的参数的定义。

- tornado.options.define()，用来定义options选项变量的方法，定义的变量可以在全局的tornado.options.options中获取使用；

- tornado.options.options，全局的options对象，所有定义的选项变量都会作为该对象的属性；
- tornado.options.parse_command_line()，转换命令行参数，并将转换后的值对应的设置到全局options对象相关属性上。追加命令行参数的方式是--port=9000；
- tornado.options.parse_config_file(path)，从配置文件导入option。

#### tornado.ioloop

tornado的核心io循环模块，封装了Linux的epoll工具与BSD的kqueue工具，是tornado高性能的基石。

![](F:\黑马教程\第8章Tornado\tornado代码\images\ioloop_epoll.png)

- epoll，容器，用于Linux操作系统管理socket；
- 输入输出循环模块ioloop，返回可操作epoll实例start开启循环监听；
- 通过socket请求报文解析出访问路径等信息，在路由映射列表中进行查询并处理进而响应；
- 由于单线程处理存在阻塞操作卡死的可能性，因此提出异步。

#### tornado.web.url

~~~python
# coding:utf-8

import tornado.web
import tornado.ioloop
import tornado.httpserver
import tornado.options

from tornado.options import define,options
from tornado.web import RequestHandler,url

tornado.options.define("port",type=int,default=8000,help="服务器端口")

class IndexHandler(RequestHandler):
    def get(self, *args, **kwargs):
        self.write("<a href='"+self.reverse_url('cpp_url')+"'>cpp</a>")

class SubjectHandler(RequestHandler):
    def initialize(self,subject):
        self.subject = subject

    def get(self, *args, **kwargs):
        self.write(self.subject)

if __name__ == "__main__":
    # 解析端口配置
    tornado.options.parse_command_line()
    app = tornado.web.Application(
        [
            (r"/",IndexHandler),
            (r"/python",SubjectHandler,{"subject":"python"}),
            url(r"/cpp",SubjectHandler,{"subject":"cpp"},name="cpp_url"),
        ],
        debug = True
    )
    # 创建服务器对象
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.current().start()
~~~

### RequestHandler

与django的request和response不同，tornado将请求与响应都封装进了基类RequestHandler，因此采用面向对象的继承。

#### 输入

1. 利用HTTP协议从浏览器向服务器传参的途径：

- 请求头，利用url有两种方式，包括路径/（正则匹配）及查询字符串（query string）？&；

- 请求体，form表单和利用ajax发送json；

- cookie；

- header中增加自定义字段。

2. 获取参数的方法：

传入参数名，同时可以设置default，strip默认开启截断空格。argument返回unicode字符串（普通字符串为ASCII），arguments返回列表。

使用postman进行测试，如www-form为表单（post），raw-json为json格式，multipart/form-data为图片。

- get_query_argument，default=_ARG_DEFAULT

- get_body_argument，请求体中的数据要求为字符串，HTTP报文头Header中的Content-Type为application/x-www-form-urlencoded或multipart/form-data。对于请求体数据为json或xml，无法使用get_body_argument获取。

- get_argument，将query与body两种整合，变量同名时query在前，body在后。

3. 请求其他信息

RequestHandler.request对象存储了关于请求的其他信息，如：method、host、url（路径+查询字符串）、path、query、headers、body、files等，其中：

- headers是类字典型的对象，按照字典方式取出值。

~~~python
class IndexHandler(RequestHandler):
    '''主页处理类'''
    def post(self, *args, **kwargs):
        if self.request.headers.get("Content-Type").startswith("application/json"):
            # print(self.request.headers.get("Content-Type")) # application/json
            # print(self.request.body) # b'{\n\t"a":111\t\n}'
            # json格式
            json_data = self.request.body
            json_args = json.loads(json_data)
            # print(json_args) {'a': 111}
            self.write(json_args)
        else:
            # form格式
            arg = self.get_argument("a")
            self.write(arg)
~~~

- files是用户上传的文件，字典格式，{'name':[{filename本地图片名称},{body图片二进制}],{'content_type'}]}，windows中w与wb有区别。

  tornador.httputil.HTTPFile对象：是接收到的文件对象，每上传一个文件，生成一个文件对象。

  当上传文件的时侯，才有此文件对象。

  此文件对象的属性：

  filename：文件的实际名字；body属性：文件的数据实体，即数据内容；content_type：文件的类型。

~~~python
class IndexHandler(RequestHandler):
    def post(self, *args, **kwargs):
        # 上传图片
        # f = self.request.files["image1"][0].get("filename")
        # print(f) # 4d8126284459a498647bf.jpg
        files = self.request.files["image1"][0]
        file_name = files.get("filename")
        if file_name:
            f = open("upload/{}".format(file_name),"wb")
            # b不代表文件是二进制，而是代表以二进制流读取处理，Windows中有区别，如\n与\r\n
            # b保留二进制原始转义字符，不带b文本方式
            f.write(files["body"])
            f.close()

# files_test.py
# f = open("./wb","wb")
# f.write(b"abc\nabc") # 未换行，用windows原生记事本可以看到
# # TypeError: a bytes-like object is required, not 'str'
# f.close()

# f = open("./w","w")
# f.write("abc\nabc") # 换行，\n转换保存为\r\n
# # TypeError: write() argument must be str, not bytes
# f.close()

# f = open("./w","rb")
# a = f.read()
# print(a) # b'abc\r\nabc'

f = open("./w","r")
a = f.read() # \r\n转换保存为\n
print(a) # abc\nabc
~~~

- 正则匹配提取url参数，传参时变量可以命名，也可以不命名，后者默认从左向右传参。

#### 输出

带有self的方法，是RequestHandler已实现的方法，是实例方法，直接使用；

未带self的方法，是RequestHandler未实现的方法，需要我们重写的方法，如果有需要。

1. self.write(chuck)

- write方法写入缓冲区，写入结束后调用finish方法一次性响应输出socket；

- write方法写入json数据，可以手动写入，也可以自动写入；

~~~python
class IndexHandler(RequestHandler):
    def get(self, *args, **kwargs):
        # self.write("haha<br/>")
        # self.finish()
        # self.write("haha<br/>")
        # 发送json，json实现不同语言之间的数据传输
        stu = {
            "name":"zhang",
            "age":18,
            "gender":1,
        }
        stu_json = json.dumps(stu) # 手动序列化
        # self.set_header("Content-Type","application/json;charset=UTF-8") # 手动序列化
        self.write(stu_json)
        # self.write(stu) # 自动，finish方法转化，charset=UTF-8
~~~

2. self.set_header()设置响应头(Response Headers)
3. set_default_headers()

重写此方法可以预先设置默认的headers。

~~~python
class IndexHandler(RequestHandler):
    def set_default_headers(self):
        self.set_header("Content-Type", "application/json;charset=UTF-8")
        # 自定义
        self.set_header("subject","python")

    def get(self, *args, **kwargs):
        # self.set_header("Content-Type","application/json;charset=UTF-8")
        self.set_header("subject","java")
~~~

4. self.set_status(status_code,reason=None)

参数：status_code，状态码值，为int整型；reason，描述状态码的词组，string类型。

如果reason的值为None，则状态码必须为系统定义的值。

5. self.redirect(url)

6. self.send_error(status_code=500,**kwargs)

- 用于抛出HTTP错误状态码，默认为500；

- 抛出错误后tornado会调用writer_error()方法进行处理，并返回给浏览器错误界面；

- 类似于raise抛出错误，后边不执行。

7. write_error(status_code=500,**kwargs)

- 用来处理send_error抛出的错误信息，并并返回给浏览器错误界面。同上面的方法一起使用；

- 类似于checkout捕获错误，定义方法；

- send_error后执行write_error、finish。

~~~python
class IndexHandler(RequestHandler):
    def write_error(self, status_code, **kwargs): # 参数打包，关键字到字典
        self.write("出错<br/>")
        self.write("标题：%s<br/>" % kwargs.get("err_title",""))
        self.write("详情：%s<br/>" % kwargs.get("err_content",""))

    def get(self, *args, **kwargs):
        # self.send_error(404,err_content="abc",err_title="title")
        # def fun(status_code,*args,**kwargs)
        err_data = {
            "err_title":"title",
            "err_content":"abc"
        }
        self.send_error(404,**err_data) # 参数解包，字典到关键字
~~~

python 函数参数(必选参数、默认参数、可选参数、关键字参数)：

- 必选参数在前，默认参数在后；

- 默认参数一定要用不可变对象，如果是可变对象，运行会有逻辑错误；

- *args是可变参数，args接收的是一个tuple，可以调用list或者tuple;

- **kw是关键字参数，kw接收的是一个dict。

#### 接口

接口，先声明不实现的方法。未带self的方法，由tornado调用，可以选择性的重写。

1. initialize()

2. prepare()在进入请求之前进行预处理，如解析json数据。

3. on_finish()，客户端收到数据后执行
4. set_default_headers()
5. write_error()
6. 调用顺序：

- GET：set_default_headers--initialize--prepare--get--on_finish；

- POST：set_default_headers--initialize--prepare--post--set_default_headers--write_error--on_finish

7. 面向对象与闭包

 https://www.runoob.com/js/js-function-closures.html

~~~python
class BaseHandler(RequestHandler):
    '''面向对象：封装、继承、多态'''
    b = 2

    def __init__(self,a):
        '''
        js中没有面向对象，因此为了将函数与数据绑定，提出闭包的概念，嵌套函数。所有函数都能访问全局变量。
        闭包是可访问上一层函数作用域里变量的函数，即便上一层函数已经关闭。
        如用于使用全局变量，实现计数器递增：
        def add():
            count = 0
            def plus():
                count += 1
                return count
            return plus
        '''
        self.a = a

    def prepare(self):
        if self.request.headers.get("Content-Type","").startswith("application/json"):
            # 字符串支持startswith，None不支持，因此附默认值
            self.json_data = self.request.body
        else:
            self.json_data = None
~~~

### 模板

#### 静态文件

分别实现三种方式访问静态文件：

- http: // 127.0.0.1: 8001 / static / html / index.html

- http: // 127.0.0.1: 8001 / index.html
- http: // 127.0.0.1: 8001 /

使用tornado.web.StaticFileHandler可以自由映射静态文件与其访问路径url，即路径+文件名定位静态文件；

tornado.web.StaticFileHandler是tornado预置的用来提供静态文件的handler。

~~~python
class ListHandler(RequestHandler):
    def get(self, *args, **kwargs):
        self.write(dict(a=2,b=3)) # 优点是key字符串不用加引号

if __name__ == "__main__":
    tornado.options.parse_command_line()
    current_path = os.path.dirname(__file__)
    app = tornado.web.Application(
        [
            # (r"/",IndexHandler),
            (r"/api/list",ListHandler), # 放在后边的话报错，HTTP 404: Not Found
            # 在path目录下寻找路由中用正则表达式提取出的文件名 
            # http: // 127.0.0.1: 8001 / index.html
            (r"/(.*)",StaticFileHandler,										{"path":os.path.join(current_path,"statics/html"),"default_filename":"index.html"}),
        ],
        # os模块与操作系统做交互，sys模块与python解析器做交互，两者都有path，但功能不同
        # os.path获取当前文件的路径，join进行合并，sys.path显示搜包路径
        # static_path = os.path.join(os.path.dirname(__file__),"statics"),
        # http: // 127.0.0.1: 8001 / static / html / index.html
        debug = True
    )
~~~

#### 模板语法

1. 通过设置template_path及self.render()实现路径与渲染。

2. 常见的模板语法如：

- 变量表达式{{}}，与django不同之处在于支持运算；

- 控制语句{%%}，与django不同之处在于{% end %}；
- static_url()，在静态文件目录中使用，对文件计算哈希值，用于文件更改，另一个作用是按照配置文件补充前缀，因此可以修改默认的static前缀。

~~~html
<head> 
    <link href="static/css/index.css" rel="stylesheet">
	<!--<link href="static/css/index.css" rel="stylesheet">-->
    <link href="{{static_url('css/index.css')}}" rel="stylesheet">
	<!--<link href="/static/css/index.css?v=fe8ccdaf962ce00b725138ef260cbf0c" 				 rel="stylesheet">-->
</head>
~~~

3. Application--settings--debug模式：

- 保存时自动重启，autoreload=True；
- 取消缓存编译的模板，compiled_template_cache=True。默认为False，每一次浏览器向服务器发出请求时，服务器下的模板都将重新编译；
- 取消缓存静态文件hash值，static_hash_cache=True。默认为False，代码中使用了static_url()函数的地方都将被重新计算，因为每次调用static_url函数时它都创建了一个基于文件内容的hash值，并将其添加到URL末尾（查询字符串的参数v）。这个hash值确保浏览器总是加载一个文件的最新版而不是之前的缓存版本；
- 提供追踪信息，serve_traceback=True，抛出异常时生成一个包含追踪信息的页面。

4. 前后端分离与后端渲染的区别：

- 建立网站时服务于爬虫，后端渲染时爬虫可以得到完整的页面数据，按照网络架构进行解析提取数据；

- 前后端分离访问时只能得到架构，没有数据，对于爬虫没有参考价值，不利于SEO搜索引擎的优化；

- API接口，json数据的发送与接收。

5. 转义

tornado默认开启模板自动转义功能，将<、>等转换为对应的html字符，防止网站受到恶意攻击。关闭方法：

- 全局关闭：Application构造函数中传递，autoescape=None；
- 局部关闭：当前模板中修改自动转义行为，关闭整个页面，{% autoescape None %}；
- 单独一句话原始输出：{% raw text %}。

~~~python
class NewHandler(RequestHandler):
    def get(self):
        self.render("new.html",text="")

    def post(self):
        text = self.get_argument("text")
        self.set_header("X-XSS-Protection",0)
        self.render("new.html",text=text)
        # <script>alert("hello")</script>
        # & lt;script & gt;alert( & quot;hello & quot;) & lt; / script & gt;
~~~

6. 自定义函数

与django不同，tornado模板中支持传入函数。

~~~python
def title_join(titles):
    return "-".join(titles)

class IndexHandler(RequestHandler):
    def get(self, *args, **kwargs):
        info = {
            "price":2048,
            "titles":["海淀区","200平米","学区房"]
        }
        self.render("index.html",info=info,fun_title_join=title_join)
~~~

### 数据库

#### 建表语法

https://blog.csdn.net/weixin_44458228/article/details/87861584

1. 数值类型：

存储值的大小范围不同。

- tinyint：占用1个字节，相对于java中的byte；
- smallint：占用2个字节，相对于java中的short；
- int：占用4个字节，相对于java中的int；
- bigint：占用8个字节，相对于java中的long；

2. 字符串类型：

- char()：定长字符串，最长255个字符。定长会浪费空间，截取插入或空格填满。查询效率高，如手机号；
- varchar()：变长(不定长)字符串，最长不超过 65535个字节，英文字符。需要有一个prefix来存储字符串的长度。

3. 日期类型：

- date：年月日

- time：时分秒

- datetime：年月日 时分秒

- timestamp：时间戳，与datetime存储相同的数据。
  timestamp最大表示2038年，而datetime范围是1000~9999

  timestamp在插入数、修改数据时，可以自动更新成系统当前时间

4. 字段约束：

- 主键约束，默认索引，primary key；

- 唯一约束，默认索引，unique；

- 非空约束，not null；

- 外键约束，foreign key；

- 建立索引，key / index。

5. 数据库引擎

InnoDB



#### 建表

多对多的关系建立第三张表，如房屋与用户（房东/房客）多对一建两张表；

建表时需要考虑数据库的扩展问题与冗余问题，如房屋与图片一对多建两张表；

字段要求不为空时可以考虑传入默认值；

重定向执行建表语句，F:\Python\mytornado\day7>mysql -uroot -p < db.sql。

~~~mysql
create database itcast charset=utf8;
use itcast;

create table it_user_info(
    ui_user_id bigint unsigned auto_increment comment '用户ID',
    ui_name_id varchar(64) not null comment '用户名',
    ui_passwd varchar(128) not null comment '密码',
    ui_mobile char(11) not null comment '手机号',
    ui_age int null comment '年龄',
    ui_avatar varchar(128) null comment '头像',
    ui_ctime datetime not null default current_timestamp comment '创建时间',
    ui_utime datetime not null default current_timestamp 
    	on update current_timestamp comment '更新时间',
    primary key (ui_user_id),
    unique (ui_mobile)
) engine=InnoDB default charset=utf8 comment '用户表';
~~~

#### CRUD

1. WHERE语句中的BETWEEN与IN

https://www.cnblogs.com/tianzeng/p/9279593.html

- BETWEEN 运算符（连续）用于 WHERE 表达式中，选取介于两个值/字符串/时间之间的数据范围，在MySQL中包含边界；
- IN 运算符（离散）对一个字段进行范围比对，更多情况下，IN 列表项的值是不明确的，而可能是通过一个子查询得到的。

2. 一张表

~~~mysql
# 添加语句，一次多行
insert into it_user_info(ui_name,ui_age,ui_passwd,ui_mobile) values("a",12,"A","123788601"),("b",12,"A","123788602"),("c",12,"A","123788603");
# order by默认升序，order by desc降序
select * from it_user_info where ui_age between 12 and 18 order by ui_age desc;
# limit 2,1，从第二条开始取一条
select * from it_user_info where ui_age between 12 and 18 order by ui_age desc limit 2,1;
# 操作非分组group by字段时，只能显示聚合运算结果，否则报错。因为聚合只返回一个汇总结果
#  select ui_age,ui_name,count(ui_age) from it_user_info group by ui_age;
select ui_age,max(ui_name),count(ui_age) from it_user_info group by ui_age;
# 添加字段，int，默认填充为0，Query OK, 0 rows affected
alter table it_user_info add ui_area_id int unsigned not null comment '区域ID';
# 添加字段，varchar，默认填充为空，Query OK, 0 rows affected
alter table it_user_info add ui_area_id varchar(11) not null comment '区域ID';
# 删除字段
alter table it_user_info drop ui_area_id;
# 添加字段，varchar，设置为not null时给定默认值default
alter table it_user_info add ui_area_id varchar(11) not null default 'T' comment '区域ID';
# 更新update/delete之前先查询where，并保留查询语句
select * from it_user_info where ui_user_id=1;
# 删除表格方法一，truncate，id从1重新开始，整表删除，执行速度快
truncate table it_house_image;
# 删除表格方法二，delete，继续向后分配id，一条条删除，执行速度慢
delete from it_house_image;
~~~

3. 两张表

优先使用联合查询，因为嵌套查询需要执行两次查询。

~~~mysql
# 子查询（嵌套查询），房屋编号为2的用户信息
select * from it_user_info where ui_user_id=(select hi_user_id 
                                             from it_house_info where hi_house_id=2);
# 联合查询，先将两张表组合为一张表，生成笛卡尔积，on加条件
select * from it_user_info inner join it_house_info;
select * from it_user_info inner join it_house_info on ui_user_id=hi_user_id;
# join = inner join，下面与内连接等价
select * from it_user_info,it_house_info where hi_user_id=ui_user_id;
# left join左表完全显示
select * from it_user_info left join it_house_info on ui_user_id=hi_user_id;
~~~

4. 多张表

~~~mysql
select * from (it_user_info a inner join it_house_info b on a.ui_user_id=b.hi_user_id)
	left join it_house_image c on b.hi_house_id=c.hi_house_id where a.ui_user_id=1; 
~~~

#### 与Tornado交互

python3.x tornd b解决MySQLdb问题
环境：win10 python3.6 pycharm
在使用torndb的过程中发现其底层是对MySQLdb的封装，而MySQLdb不支持python3.x
解决：安装mysqlclient包，其完全兼容MySQLdb

### 安全应用

#### Cookie

1. set_cookie

set_cookie(name, value, domain=None, expires=None, path='/', expires_days=None)

设置cookie实际就是通过设置header的Set-Cookie来实现的。下面两种定义方式等价。

~~~python
self.set_cookie("itcast","abc")
self.set_headers("Set-Cookie","itcast=abc;Path=/")
~~~

time与datetime的区别：

datetime是date和time的组合。

时间格式化函数：

- strftime(format[,t])：从时间转换为字符串，t如果不输入，默认使用localtime()返回结果，即当前时间；

- strptime(string,format)：从字符串转换为时间，得到struct_time对象。

~~~python
>>> time.strftime("%H:%M:%S")
'20:08:46'
>>> time.strptime("20:08:46","%H:%M:%S")
time.struct_time(tm_year=1900, tm_mon=1, tm_mday=1, tm_hour=20, tm_min=8, tm_sec=46, tm_wday=0, tm_yday=1, tm_isdst=-1)
~~~

2. get_cookie(name,default=None)

没有时返回None。

#### 安全Cookie

Tornado提供了一种对Cookie进行简易加密签名的方法来防止Cookie被恶意篡改。安全Cookie只是一定程度的安全，不能修改，但可以使用。

使用安全Cookie需要为应用配置一个用来给Cookie进行混淆的密钥cookie_secret，将其传递给Application的构造函数。

1. uuid

通用唯一识别码（英语：Universally Unique Identifier，简称UUID），是由一组32个16进制数字所构成（两个16进制数是一个字节，总共16字节），因此UUID理论上的总数为16^32=2^128。

uuid模块的uuid4()函数可以随机产生一个uuid码，bytes属性将此uuid码作为16字节字符串。

之后采用Base64（一种基于64个可打印字符来表示二进制数据的表示方法）对字符串进行编码，作为cookie_secret的值。

2. dir([object])函数

参数说明：object -- 对象、变量、类型。

- 不带参数时，返回当前范围内的变量、方法和定义的类型列表；

- 带参数时，返回参数的属性、方法列表。

~~~python
>>> import uuid,base64
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'time', 'uuid']
>>> dir(uuid)
['uuid1', 'uuid3', 'uuid4', 'uuid5']
>>> help(uuid.uuid4)
Help on function uuid4 in module uuid:
uuid4()
    Generate a random UUID.
>>> a = uuid.uuid4()
UUID('6e05a6ed-4f0f-4ee6-94d4-98a0f09af55a')
>>> a.hex
'6e05a6ed4f0f4ee694d498a0f09af55a'
>>> a.bytes
b'n\x05\xa6\xedO\x0fN\xe6\x94\xd4\x98\xa0\xf0\x9a\xf5Z'
~~~

3. 获取和设置

set_secure_cookie(name, value, expires_days=30)，设置一个带签名和时间戳的cookie，防止cookie被伪造；

get_secure_cookie(name, value=None, max_age_days=31)

如用于计算访问次数，cookie网页计数器。

#### XSRF

浏览器有一个很重要的概念——同源策略(Same-Origin Policy)。 所谓同源是指，域名，协议，端口相同。 不同源的客户端脚本(javascript、ActionScript)在没明确授权的情况下，不能读写对方的资源。但是通过浏览器可以读写对方的cookie。

CSRF（Cross-site request forgery）跨站请求伪造（跨站攻击或跨域攻击的一种）。通常缩写为CSRF或者XSRF。

- GET，如图片。对于会产生副作用的HTTP请求，比如点击购买按钮、编辑账户设置、改变密码或删除文档，都应该使用HTTP POST方法；

- POST，XSRF保护主要用于保护POST请求，携带数据时进行验证。

三种方式：

- 在模板中使用XSRF保护，只需在模板中添加 {% module xsrf_form_html() %}；

模板中添加的语句帮我们做了两件事：

为浏览器设置了_xsrf的Cookie（注意此Cookie浏览器关闭时就会失效）；
为模板的表单中添加了一个隐藏的输入名为_xsrf，其值为_xsrf的Cookie值。

对于不使用模板的应用，需要后端手动设置xsrf_token的值，前端通过正则表达式进行提取，获取cookie的值。

- 若请求体是表单编码格式的，可以在请求体中添加_xsrf参数；
- 若请求体是其他格式的（如json或xml等），可以通过设置HTTP头X-XSRFToken来传递_xsrf值。

```javascript
# console
document.cookie
"_xsrf=2|a02e9063|f9c4713f6cd16d477038c2a3420eaf90|1557145724; itcast=2|1:0|10:1557145724|6:itcast|4:YWJj|2307b2f0995bc4722757d6f458b85d5b2a6b2b5b280bb391b3b0b4856581adb4"
document.cookie.match("\\b_xsrf=([^;]*)\\b")
["_xsrf=2|a02e9063|f9c4713f6cd16d477038c2a3420eaf90|1557145724", "2|a02e9063|f9c4713f6cd16d477038c2a3420eaf90|1557145724", index: 0, input: "_xsrf=2|a02e9063|f9c4713f6cd16d477038c2a3420eaf90|…22757d6f458b85d5b2a6b2b5b280bb391b3b0b4856581adb4"]
document.cookie.match("\\b_xsrf=([^;]*)\\b")[1]
"2|a02e9063|f9c4713f6cd16d477038c2a3420eaf90|1557145724"
```

~~~html
<body>
<!--    <form method="post">-->
<!--        <input type="hidden" name="_xsrf" value="">-->
<!--    渲染后为：<input type="hidden" name="_xsrf" 		value="2|fea5ce1f|c2972d5ea2807905e639f0e146cff26b|1557144032"/>-->
<!--        <input type="text" name="message"/>-->
<!--        <input type="submit" value="Post"/>-->
<!--    </form>-->

    <a href="javascript:;" onclick="xsrfPost()">发送POST请求</a>
    <script src="http://cdn.bootcss.com/jquery/3.1.1/jquery.min.js"></script>
    <script type="text/javascript">
        //获取指定Cookie的函数
        function getCookie(name) {
            var r = document.cookie.match("\\b" + name + "=([^;]*)\\b");
            return r ? r[1] : undefined;
        }
        //AJAX发送post请求，表单格式数据
        function xsrfPost() {
            var xsrf = getCookie("_xsrf");
            // data对象
            var data = {
                "_xsrf":xsrf,
                "key1":"abc"
            };
            // 转换为post基本表单格式key=value，图片为自描述格式
            // $.post("/",data,function(data) {
            //     alert("OK");
            // });
            // json字符串
            var json_data = JSON.stringify(data);
            $.ajax({
                url:"",
                method:"POST",
                headers:{
                  "X-XSRFToken":xsrf,  
                },
                dataType:"json",
                data:json_data,
                success:function (data) {
                    alert("OK")
                }
            });
        }
    </script>
</body>
~~~

#### 用户验证

如用于查看登录状态，从首页跳转至登录页，登陆完成后返回首页。

Application中设置login_url="/login"，并对路由进行配置。

~~~python
class IndexHandler(BaseHandler):
    def get_current_user(self):
        # session获取登录状态，暂不用
        f = self.get_argument("f",None)
        if f:
            return True
        else:
            return False

    @tornado.web.authenticated
    def get(self, *args, **kwargs):
        self.write("AAA")

class LoginHandler(RequestHandler):
    def get(self, *args, **kwargs):
        next_url = self.get_argument("next","")
        if next_url:
            self.redirect(next_url+"?f=login")
        else:
            self.write("login")
            # http: // 127.0.0.1: 8000 / login?next = % 2F 记录跳转来的时候的路由
~~~

### 异步

IOLoop负责调度socket，单线程处理，有阻塞的风险。

同步--回调--yield--epoll

#### 回调与yield异步

异步指有多条可执行路径，且步调独立不一致。用多线程实现异步，不代表多线程等于异步。异步的实现方式：

- 回调方式，拆开处理请求的逻辑，同一个业务逻辑分别在主、子进程按照不同的步调执行，如ajax；

- yield生成器，不用拆分业务逻辑，实现主、子进程的切换，业务逻辑完全在子进程中执行；
- 装饰器，实现调用方式不变。

~~~python
import time, _thread

# 全局对象
gen = None

def long_io():
    '''模拟耗时操作'''
    def fun():
        print("444")
        global gen
        print("开始执行耗时操作")
        time.sleep(5)
        print("完成执行耗时操作")
        result = "io result"
        try:
            # 唤醒子线程
            gen.send(result)
        except StopIteration:
            pass
    # 开启新线程执行耗时操作，主线程向下执行。tornado中使用epoll
    _thread.start_new_thread(fun,())

def gen_corotine(f):
    def wrapper():
        print("111")
        global gen
        # 获取生成器数据,包括req_a函数与断点上下文
        gen = f()
        # 执行到yield
        gen.__next__()
        print("222")
    return wrapper

@gen_corotine
def req_a():
    print("开始处理请求a")
    # yield返回值
    ret = yield long_io()
    print("666")
    print(ret)
    print("完成处理请求a")

def req_b():
    print("开始处理请求b")
    print("333")
    time.sleep(2)
    print("555")
    print("完成处理请求b")

def main():
    '''模拟IOLoop'''
    # 调用函数时生成生成器并调用next方法,使用装饰器实现
    req_a()
    req_b()
    # 保证主进程不退出
    while 1:
        pass

if __name__ == "__main__":
    main()
~~~

#### Tornado异步

Tornado.IOLoop调度用于解决网络IO并发问题。

- Tornado的实现异步的机制不是线程，而是epoll，即将异步过程交给epoll执行并进行监视回调；
- 单进程中使用协程实现程序的切换，多进程是通过操作系统实现调度；
- 协程与生成器类似，都是定义体中包含 yield 关键字的函数；
- 协程可以把控制器让步给中心调度程序，从而激活其他的协程；
- 在Tornado中关于异步最基本的应用：把本身当做客户端向第三方接口发请求；
- 两种方式：回调异步，yield + 装饰器。

- 深入理解生成器及协程

https://mp.weixin.qq.com/s/vjtMjSOjSo1vcGNJu0opWA

~~~python
class IndexHandler(BaseHandler):
    # 回调异步
    # 不调用finish,不断开连接
    # @tornado.web.asynchronous
    # def get(self, *args, **kwargs):
    #     client = AsyncHTTPClient()
    #     client.fetch("http://api.map.baidu.com/location/ip?ak=F454f8a5efe5e577997931cc01de3974&ip=14.130.112.24",
    #                  callback=self.on_response)

    # def on_response(self,resp):
    #     json_data = resp.body
    #     # json的解析
    #     data = json.loads(json_data)
    #     self.write(data.get("content","").get("address_detail","").get("city","")) # 北京市
    #     self.finish()

    # 协程异步
    @tornado.gen.coroutine
    def get(self, *args, **kwargs):
        client = AsyncHTTPClient()
        resp = yield client.fetch("http://api.map.baidu.com/location/ip?ak=F454f8a5efe5e577997931cc01de3974&ip=14.130.112.24")
        json_data = resp.body
        data = json.loads(json_data)
        self.write(data.get("content","").get("address_detail","").get("city",""))
~~~

#### json的解析

json是一种字符串。

1. json.dumps()和json.loads()是json格式处理函数（可以这么理解，json是字符串）

- json.dumps()函数是将一个Python数据类型列表进行json格式的编码（可以这么理解，json.dumps()函数是将字典转化为字符串）；

- json.loads()函数是将json格式数据转换为字典（可以这么理解，json.loads()函数是将字符串转化为字典）

2. json.dump()和json.load()主要用来读写json文件函数（其中json.dump()写，json.load()读）

~~~python
>>> import json
>>> dict1 = {"city":"beijing"}
>>> type(dict1)
<class 'dict'>
>>> json_data = dict1.dumps()
AttributeError: 'dict' object has no attribute 'dumps'
>>> json_data = json.dumps(dict1)
>>> type(json_data)
<class 'str'>
>>> dict1
{'city': 'beijing'}
>>> json_data
'{"city": "beijing"}'
>>> json.loads(json_data)
{'city': 'beijing'}

>>> file = open("1.json","w",encoding="utf-8")
>>> json.dump(json_data,file)
>>> file.close()
>>> file = open("1.json","r",encoding="utf-8")
>>> file.read()
'"{\\"city\\": \\"beijing\\"}"'
>>> file.close()
>>> file = open("1.json","r",encoding="utf-8")
>>> json.load(file)
'{"city": "beijing"}'
~~~

### WebSocket

- 客户端建立连接，开始双向通信，后端服务器主动发消息给客户端，socket传输层长连接，WebSocket没有GET/POST接口。

- WebSocket是HTML5规范中新提出的客户端-服务器通讯协议，协议本身使用新的ws://URL格式。

- WebSocket 是独立的、创建在 TCP 上的协议，和 HTTP 的唯一关联是使用 HTTP 协议的101状态码进行协议切换，使用的 TCP 端口是80，可以用于绕过大多数防火墙的限制。

#### 前端

~~~html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>聊天室</title>
</head>
<body>
    <div id="contents" style="height:500px;overflow:auto;"></div>
    <div>
        <textarea id="msg"></textarea>
        <a href="" onclick="sendMsg()">发送</a>
    </div>
    <script src="{{static_url('js/jquery.min.js')}}"></script>
    <script type="text/javascript">
        var ws = new WebSocket("ws://192.168.170.1:8000/chat");
        ws.onmessage = function (data) {
            $("#contents").append("<p>" + data.data + "</p>")
        };
        function sendMsg(data) {
            var msg = $("#msg").val();
            if (msg){
                ws.send(msg);
                $("#contents").append("<p>" + data.data + "</p>");
            }
        }
    </script>
</body>
</html>
~~~

#### 后端

~~~python
class IndexHandler(BaseHandler):
    def get(self, *args, **kwargs):
        self.render("webchat.html")

class ChatHandler(WebSocketHandler):
    # 类属性，对象访问类属性
    users = []

    def open(self):
        # 广播
        self.users.append(self)
        for user in self.users:
            user.write_message("%s上线了" % self.request.remote_ip)

    def on_message(self,data):
        for user in self.users:
            user.write_message("%s说了：%s" % (self.request.remote_ip,data))

    def on_close(self):
        self.users.remove(self)
        for user in self.users:
            user.write_message("%s下线了" % self.request.remote_ip)

    def check_origin(self, origin):
        # windows+linux
        return True
~~~

### 部署

Supervisor
一个运行在类Unix系统下的进程管理系统工具

由Python进行编写

采用C/S结构：

- supervisord是服务进程
- supervisorctl是客户端管理工具

使用ini格式的配置文件



Linux：echo命令：用于字符串的输出

如显示结果定向至文件
echo "It is a test" > myfile



supervisord

Daemon()程序是一直运行的服务端程序，又称为守护进程。通常在系统后台运行，没有控制终端，不与前台交互，Daemon程序一般作为系统服务使用。



linux命令ps aux|grep xxx

对进程进行监测和控制,首先必须要了解当前进程的情况,也就是需要查看当前进程, 而ps命令（Process Status）就是最基本同时也是非常强大的进程查看命令.

使用该命令可以确定有哪些进程正在运行和运行的状态、进程是否结束、进程有没有僵尸、哪些进程占用了过多的资源等等.总之大部分信息都是可以通过执行该命令得到的.

ps 为我们提供了进程的一次性的查看，它所提供的查看结果并不动态连续的；如果想对进程时间监控，应该用 top 工具。

如果直接用ps命令，会显示所有进程的状态，通常结合grep命令查看某进程的状态。

grep （global search regular expression(RE) and print out the line,全面搜索正则表达式并把行打印出来）是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。



浅析三种特殊进程:孤儿进程,僵尸进程和守护进程

https://www.cnblogs.com/wannable/p/6021617.html

守护进程，后台运行
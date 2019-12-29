## Tornado

网络框架核心知识：路由的配置、模板的使用、静态资源的使用。

### 一、Torando介绍

#### 基本代码

- 项目project中创建包package。Pycharm中快捷键 ALT+ENTER 显示错误时提示解决方案，如导入，定义类与函数。

- 定义handler实现功能。Application是最重要的类。浏览器解释、渲染、呈现。

基本代码：

~~~python
from tornado.httpserver import HTTPServer
from tornado.ioloop import IOLoop
from tornado.web import Application, RequestHandler

# 子类继承自框架的类，并重写父类的两个方法
class IndexHandler(RequestHandler):
    def get(self,*args,**kwargs):
        self.write("hello tornado")
    def post(self,*args,**kwargs):
        pass

# 路由列表以参数传入构造器，元组形式包括路径及处理程序的信息
app = Application(handlers=[("/",IndexHandler)])
# 创建服务器对象
server = HTTPServer(app)
# 监听端口号，分离路径
server.listen(8888)
# 启动当前进程中的服务
IOLoop.current().start()
~~~

#### 配置文件

项目project中创建目录directory。如将端口号写入配置文件的步骤：

- 定义端口号在配置文件中的名称、类型、默认值：define("名称",type=int,default=8888)，未定义时使用默认值；
- 解析配置文件：parse_config_file("配置文件路径")，相对路径../指的是相对于当前文件所在文件夹的上一层文件夹；
- 读取配置文件：options.名称。

~~~python
# tornado.py
# 定义端口号
define("duankou",type=int,default=8888)
# 解析配置文件
parse_config_file("../config/config")
server.listen(options.duankou)

# config.text
duankou = 9999
~~~

#### 请求资源的方式

1. 利用路径的变化请求不同的资源，服务器端使用正则表达式获取不同的路径，生成不同的响应；

2. 利用参数的变化请求不同的资源，服务器端调用RequestHandler中的相关方法获取请求参数，不同参数响应不同。

- GET方式：get_query_argument与get_query_arguments；
- POST方式：get_body_argument与get_body_arguments。

~~~python
class JavaHandler(RequestHandler):
    # 给定默认值，否则出现400错误
    def get(self, p1=None,p2=None,*args, **kwargs):
        # TypeError: get() missing 2 required positional arguments: 'p1' and 'p2'
        self.write("hello java<br>")
        if p1:
            self.write("今天是："+p1+'<br>')
        else:
            self.write("day1,day2")
        if p2:
            self.write("课程是："+p2)
            
class PythonHandler(RequestHandler):
    def get(self, *args, **kwargs):
        self.write("你大爷还是你大爷")
        # 获取用于的请求参数，提供默认值
        d = self.get_query_argument("day",None)
        s = self.get_query_argument("subject",None)
        print(d,s)

        # 生成列表，不提供默认值也不报错，列表保存
        ds = self.get_query_arguments("day")
        ss = self.get_query_arguments("subject")
        print(ds,ss)
~~~

#### 页面跳转与传参

页面跳转，访问方式为GET，语法self.redirect("/")。

~~~python
# 专门一个handler处理登录
class LoginHandler(RequestHandler):
    def post(self, *args, **kwargs):
        username = self.get_body_argument("username",None)
        upwd = self.get_body_argument("upwd",None)
        if username == 'abc' and upwd == '123':
            # self.write('Yeah')
            # 页面跳转，访问方式为GET。传参
            self.redirect("/blog?username="+username)
            print("登陆成功")
        else:
            # print(username,upwd)
            # self.write("Failure")
            # 页面跳转，重新登录
            self.redirect("/?msg=fail")
            print("用户名或密码错误")

class BlogHandler(RequestHandler):
    def get(self, *args, **kwargs):
        username = self.get_query_argument("username",None)
        # 登录方式有两种，从登录页面自动传参，与从路径手动传参进入，后者存在免登录的bug
        # localhost:8888/blog?username=abc
        # TypeError: must be str, not NoneType
        if username:
            self.write("欢迎"+username)
        else:
            self.write("欢迎")
~~~

#### 表单提交文件

- self.request是RequestHandler的一个属性，引用HttpServerRequest对象，该对象封装了与请求相关的所有内容。

- HttpServerRequest对象的files属性引用着用户通过表单上传的文件。

~~~python
class LoginHandler(RequestHandler):
    def post(self, *args, **kwargs):
        if username == 'abc' and upwd == '123':
            # 通过表单上传文件时，需要增加表单属性 enctype=multipart/form-data
            # 返回HTTPServerRequest对象，包含请求信息
            print(self.request)
            # 返回字典，{'name':[{'content_type'},{filename本地图片名称},{body图片二进制}]}
            print(self.request.files)
            # 判断是否上传了文件
            if self.request.files:
                avs = self.request.files["avatar"]
                # 上传了文件的话服务器硬盘保存文件内容，执行文件IO操作
                for av in avs:
                    body = av["body"]
                    # writer = open("upload/%s/"+av["filename"], "wb") 拼接
                    # writer = open("upload/%s"%av["filename"],"wb") 两种输出格式化
                    writer = open("upload/{}".format(av["filename"]),"wb")
                    writer.write(body)
                    writer.close()
~~~

#### 钩子方法

框架的钩子方法（hook/handler）都定义为空方法，在具体业务中进行重写，常见的如：

~~~python
class BlogHandler(RequestHandler):
    # 钩子方法
    def set_default_headers(self):
        # 自定义响应头内容，响应（响应头、响应行、响应体）
        print("set_default_headers")

    def initialize(self):
        # 在get/post方法执行之前传入参数，或执行一些初始化操作，如数据库连接操作
        print("initialize")

    def on_finish(self):
        # 在get/post方法执行完毕之后释放资源
        # finish方法不是钩子方法，作用是在get/post方法结束以后，一次性将缓冲区内信息返回给浏览器。
        print("on_finish")

    def get(self, *args, **kwargs):
        print("get")
~~~

### 二、服务器响应

服务器真正的响应内容主要是两种，json字符串与模板html。

#### 响应json字符串

json是网络通信常用的数据传递格式。响应时将缓冲区的字典转换为json字符串。可以用finish自动转换，也可以用json.dumps手动转换。

~~~python
class BlogHandler(RequestHandler):
    def get(self, *args, **kwargs):
        # 以json字符串作为响应内容，格式要求为双引号
        resp = {"key1":"value1",
                "key2":"value2"}
        # write响应时finish自动将缓冲区的字典转换为json字符串
        # self.write(resp)
        # 响应头明确告诉浏览器 Content - Type: application / json;charset = UTF - 8，json带有样式

        # 手动转换字典为字符串
        self.set_header("Content-Type","application/json;charset=UTF-8")
        json_str = json.dumps(resp)
        self.write(json_str)
~~~

#### 响应模板html页面

render方法根据模板路径找到模板文件，将页面转换为字符串，get方法写入缓冲区，finish推回浏览器。html中不加注释。

~~~python
class IndexHandler(RequestHandler):
    def get(self, *args, **kwargs):
        # 响应html页面，文件名称
        self.render("login.html")
        
# 路由列表，配置路径，文件夹名称
app = Application(handlers=[("/",IndexHandler)],
                  template_path="mytemplate")
~~~

#### 模板中插入变量

{{}}双花括号标识变量。在render中传参，render中要以字典的形式传参。

~~~python
<!--<td align="right">评论数 {{b}}</td>-->
<!--插入表达式-->
<!--<td align="right">评论数 {{a+b}}</td>-->
<!--插入内置函数-->
<!--<td align="right">评论数 {{len("aaaaafff")}}</td>-->
<!--插入自定义函数-->
<!--<td align="right">评论数 {{lambda a,b:a*b}} 报错-->
<td align="right">评论数 {{myfunc(a,b)}}
~~~

#### 模板中插入语句

{%%}单花括号标识语句，如for循环{% for %}{% end %}，就近匹配。

~~~python
<body>
    <!--去掉ul前的小圆点，保持居中-->
    <ul style="list-style-type:none;width:70%;margin:30px auto">
        {% for blog in blogs %}
        <li>
            <table width="100%">
                <tr>
                    <!--行合并，内容居中-->
                    <td rowspan="4" width="30%" align="center">
                        <table>
                            <tr>
                                <td>
                                    <!--插入图片网页地址-->
                                    <img src="http://image.uczzd.cn/15072335686070333774.jpeg" alt="" width="328px" height="228px">
                                </td>
                            </tr>
                            <tr>
                                <!--设置作者名显示格式：省略部分用三个点表示-->
                                <td align="center"><p style="width:228px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis">{{blog["author"}}</p></td>
                            </tr>
                        </table>
                    </td>
                    <!--<td>博客标题</td>-->
                    <td>{{blog["title"]}}</td>
                </tr>
                <tr>
                    <td align="right">
                        {% if blog %}
                            评论数 {{blog["count"]}}
                        {% else %}
                            抢个沙发
                        {% end %}
                    </td>
                </tr>
            </table>
        </li>
        {% end %}

    </ul>
</body>
~~~

#### 配置静态资源

常见的静态资源如图片、js、css。步骤包括：新建文件夹，配置路径。

~~~python
# 配置静态资源文件夹名称
app = Application(handlers=[handlers=[("/",IndexHandler)],
                            static_path="mystatics"])
# 配置静态资源文件名称
<img alt="" width="328px" height="228px"
src={% if blog["avatar"] %}
    <!--静态资源的格式，render-static-static_path-images-->
         static/images/{{blog["avatar"]}}
    {% else %}
    <!--设置默认值-->
         static/images/default_avatar.png
    {% end %}
>
~~~

#### 块block的使用

- Python中重复的内容可以考虑使用面向对象中的继承，也可以使用面向过程中的模块导入，模块中单独写入重复的内容。

- 模板继承，在公共模板base.html中使用block标识出不同模板可能存在不同的地方。使用模板时，记得在第一行写继承的语法。

~~~python
# login.html继承自base.html
{% extends base.html %}
{% block title %}登录{% end %}
{% block body %}
{% end %}
~~~

#### 模块的使用

- 将模板template的body内容写入模块module中，模板与模块的关系在application中进行配置。模块module文件夹建在模板template文件夹下。
- 模板传参同样使用request引用HTTPServerRequest对象获取属性，self.request.uri = 相对路径+请求参数。没有get_query_argument方法。
- 使用模块的优点在于可以实现复用，拼接组合生成新的模板，有利于协同开发。

~~~python
# login.html中关联模板与模块
{% block body %}
    {% module loginmodule() %}
{% end %}

# 配置模块
app = Application(handlers=[("/",IndexHandler),
                  ui_modules={"loginmodule":LoginModule})

# 将模块module转化为字符串，填充进模板template中的body位置处
class LoginModule(UIModule):
    '''最终显示内容由三部分组成，base.html+login.html+login_module.html'''
    def render(self, *args, **kwargs):
        # return "ngrjiogno"
        # NameError: name 'result' is not defined
        # login.html中存在result值，需要传参获取result值，同样使用HTTPServerRequest传参

        print(self.request) # 所有请求参数
        print(self.request.uri) # uri = 路径+参数 uri = /?msg=fail
        print(self.request.path) # uri中的路径
        print(self.request.query) # uri中的参数

        r = ""
        # 输入错误时会带有请求参数 msg=fail
        if self.request.query:
            r = "用户名或密码错误"

        return self.render_string("mymodule/login_module.html",result=r)
~~~

#### Tornado的转义

将js的标签转化为字符串。一种安全机制，防止用户在页面中嵌入js代码，自动开启，可以手动关闭。

~~~python
app = Application(handlers=[("/",IndexHandler)],
                  autoescape=None)
~~~

### 三、与数据库交互

#### 数据库建表

mysql查询标准写法：help create database;

~~~mysql
# tb_user
create table if not exists tb_user(
user_id int auto_increment,
user_name varchar(32) not null,
user_password varchar(64) not null,
user_avatar varchar(128) default null,
user_city varchar(32) not null,
user_createdat datetime default current_timestamp,
user_updatedat datetime default current_timestamp on update current_timestamp,
primary key(user_id),
unique(user_name)
)default charset = utf8;

# tb_blog
create table if not exists tb_blog(
blog_id int auto_increment,
blog_user_id int not null,
blog_title varchar(100) not null,
blog_content varchar(1024) not null,
blog_createdat datetime default current_timestamp,
blog_updatedat datetime default current_timestamp on update current_timestamp,
primary key(blog_id),
foreign key(blog_user_id) references tb_user(user_id) on delete cascade on update cascade
)default charset = utf8;

# tb_tag 标签与博客是多对多的关系，共需三张表
create table if not exists tb_tag(
tag_id int auto_increment,
tag_content varchar(16) not null,
primary key(tag_id)
)default charset = utf8;

# tb_blog_tag
create table if not exists tb_blog_tag(
blog_tag_id int auto_increment,
rel_blog_id int not null,
rel_tag_id int not null,
primary key(blog_tag_id),
foreign key(rel_blog_id) references tb_blog(blog_id) on delete cascade on update cascade,
foreign key(rel_tag_id) references tb_tag(tag_id) on delete cascade on update cascade
)default charset = utf8;

# tb_comment
create table if not exists tb_comment(
comment_id int auto_increment,
comment_user_id int not null,
comment_blog_id int not null,
comment_content varchar(256) not null,
comment_createdat datetime default current_timestamp,
comment_updatedat datetime default current_timestamp on update current_timestamp,
primary key(comment_id),
foreign key(comment_blog_id) references tb_blog(blog_id) on delete cascade on update cascade,
foreign key(comment_user_id) references tb_user(user_id) on delete cascade on update cascade
)default charset = utf8;
~~~

#### 数据库查询语句

Pycharm2019.1专业版安装，使用激活码，有效期至2020.3

URL：jdbc:mysql://localhost:3306/blog_db?serverTimezone=PRC，JDBC URL配置解决时区问题

设置数据库密码：'USER': 'root'; 'PASSWARD': 'root'

数据库查询语句：

1. 聚合aggregate分组group by，聚合可以不分组，分组必须聚合。
2. 多表联合查询

- 内连接查询 inner join，如select * from t1 join t2得到t1与t2的笛卡尔积，select * from t1 join t2 on t1.cid=t2.custome对有关联的列做筛选后得到部分的笛卡尔积；
- 外连接查询：在筛选的基础上要求显示主表的全部信息，左外连接查询 left outer join、右外连接查询 right outer joIn，如select * from t1 left join t2 on t1.cid=t2.custome。

~~~mysql
# 1.从用户表中查询最晚注册用户的信息
# 报错原因：聚合时未分组，导致出现一对多的情况，可以加上group by user_name，但是没有意义
mysql> select max(user_createdat)user_createdat,user_name
    -> from tb_user;   
# 正确写法
mysql> select *
    -> from tb_user
    -> where user_createdat = (select max(user_createdat) from tb_user);

# 2.从用户表中查询每个城市的最晚注册用户的信息
# 2.1 获取笛卡尔积
# 报错原因：派生表与重名列需要起别名
# ERROR 1052 (23000): Column 'user_city' in field list is ambiguous；ERROR 1248 (42000): Every derived table must have its own alias
mysql> select user_name,tb_user.user_city,user_createdat,m
    -> from tb_user
    -> join (select user_city,max(user_createdat) as m from tb_user group by user_city) as t;
# 2.2 对包括所有可能结果的笛卡尔积进行筛选
mysql> select user_name,tb_user.user_city,user_createdat,m
    -> from tb_user
    -> join (select user_city,max(user_createdat) as m from tb_user group by user_city) as t
    -> on tb_user.user_city = t.user_city and
    -> tb_user.user_createdat = m;

# 3. 查询所有的博客及其标签信息，三张表联合查询。分步执行
# 3.1 先查blog_title对应的rel_tag_id，不建议，原因是无法外连接查询
select blog_title,rel_tag_id
from tb_blog
join tb_blog_tag
on rel_blog_id = blog_id;

select tag.blog_title,group_concat(tag_content)
from (select blog_title,blog_id,rel_tag_id from tb_blog join tb_blog_tag on rel_blog_id = blog_id) as tag
join tb_tag
on rel_tag_id = tag_id
group by tag.blog_title;

# 3. 查询所有的博客及其标签信息，三张表联合查询。分步执行
# 3.2 先查rel_blog_id对应的tag_content
select rel_blog_id, group_concat(tag_content)
from tb_tag
join (select rel_blog_id,rel_tag_id from tb_blog_tag) as t
on rel_tag_id = tag_id
group by rel_blog_id;

# 等效于上面3.2
select rel_blog_id, group_concat(tag_content)
from tb_tag
join tb_blog_tag
on rel_tag_id = tag_id
group by rel_blog_id;

select blog_id,blog_title,tc
from tb_blog
left join (select rel_blog_id, group_concat(tag_content) as tc
	from tb_tag
	join (select rel_blog_id,rel_tag_id from tb_blog_tag) as t
	on rel_tag_id = tag_id
	group by rel_blog_id) as t1
on blog_id = rel_blog_id;
~~~

#### 多表联合查询

分步执行，生成派生表。from与join后面都可以接派生表。

最终效果如下图所示：

![](C:\Users\HMZ\Desktop\mysql.PNG)

~~~mysql
# 4. 查询所有的博客及其标签信息，作者信息
select blog_id,blog_title,user_id,user_name,tc
from tb_user
left join (
    select blog_user_id,blog_id,blog_title,tc
	from tb_blog
	left join (select rel_blog_id, group_concat(tag_content) as tc
		from tb_tag
		join (select rel_blog_id,rel_tag_id from tb_blog_tag) as t
		on rel_tag_id = tag_id
		group by rel_blog_id) as t1
	on blog_id = rel_blog_id) as t2
on user_id = blog_user_id;

# 5. 查询所有的博客及其标签信息，作者信息和评论条数
# 5.1 先查询每条博客的评论条数
select comment_blog_id,count(*)
from tb_comment
group by comment_blog_id;

# 5.2 关联两张派生表，关联博客及其标签信息，作者信息与评论条数
select blog_id,blog_title,user_id,user_name,tc,c
from (
	select comment_blog_id,count(*) as c
	from tb_comment
	group by comment_blog_id) as t3
right join (
	select blog_id,blog_title,user_id,user_name,tc
	from tb_user
	left join (
    	select blog_user_id,blog_id,blog_title,tc
		from tb_blog
		left join (select rel_blog_id, group_concat(tag_content) as tc
			from tb_tag
			join (select rel_blog_id,rel_tag_id from tb_blog_tag) as t
			on rel_tag_id = tag_id
			group by rel_blog_id) as t1
		on blog_id = rel_blog_id) as t2
	on user_id = blog_user_id) as t4
on comment_blog_id = blog_id;

select blog_id,blog_title,user_id,user_name,tc,c,blog_content,user_avatar
from (
	select comment_blog_id,count(*) as c
	from tb_comment
	group by comment_blog_id) as t3
right join (
	select blog_id,blog_title,user_id,user_name,tc,blog_content,user_avatar
	from tb_user
	left join (
    	select blog_user_id,blog_id,blog_title,tc,blog_content
		from tb_blog
		left join (select rel_blog_id, group_concat(tag_content) as tc
			from tb_tag
			join (select rel_blog_id,rel_tag_id from tb_blog_tag) as t
			on rel_tag_id = tag_id
			group by rel_blog_id) as t1
		on blog_id = rel_blog_id) as t2
	on user_id = blog_user_id) as t4
on comment_blog_id = blog_id;
~~~

#### pymysql操作数据库

- 安装pymysql=0.9.3。
- 传入变量时的引号有sql注入攻击的风险，pymysql可以自动转义引号。
- pymysql快捷键：编辑代码的时候经常的要换下一行，但是光标没有在行末，可以用这个命令直接换行：**Shift+Enter**
- 快速查看文档：**Ctrl + q**
- 查看内置函数介绍:Ctrl+鼠标点击函数名

~~~python
class LoginHandler(RequestHandler):
    def post(self, *args, **kwargs):
        username = self.get_body_argument("username",None)
        upwd = self.get_body_argument("upwd",None)

        # 1.建立连接
        settings = {"host":"127.0.0.1",
                    "port":3306,
                    "user":"root",
                    "password":"root",
                    "database":"blog_db",
                    "charset":"utf8"}
        connection = pymysql.connect(**settings) # 字典传参
        # 2.获取游标
        cursor = connection.cursor()
        # 3.利用游标发送sql语句，操作数据库
        # 传入变量，使用占位符，文本带引号，引号有sql注入攻击的风险
        # sql = "select count(*) from tb_user " \
        #       "where user_name='%s' and " \
        #       "user_password='%s'"%(username,upwd)
        # cursor.execute(sql)

        # sql注入攻击
        print("sql---->",sql)
        # sql -->select count(*) from tb_user where user_name = '1' or 1 = 1 or ('1'='1' and user_password='a') or '1' = '1'

        # pymysql自动转义引号
        sql = "select count(*) from tb_user " \
              "where user_name=%s and " \
              "user_password=%s"
        params = (username, upwd)
        cursor.execute(sql,params)
        # sql----> select count(*) from tb_user where user_name=%s and user_password=%s   
        
        # 4.如果有需要，利用游标获取数据库的返回结果集
        # result = cursor.fetchall() # ((1,),)
        result = cursor.fetchone() # (0,)
        print("result----->",result)

        if result[0]:
            self.redirect("/blog?username"+username)
        else:
            self.redirect("/?msg=fail")
~~~

#### tornado中使用mysql

- 建立util工具模块，新建dbutil.py文件。声明handler与module中调用进行的方法，即封装；
- 建立app模块，新建myapp.py文件，建立全局数据库对象，避免重复创建；
- tornadoserver.py文件中，handler与module中调用数据库对象的方法不同。

~~~python
# dbutil.py
class DBUtil:
    def __init__(self,**kwargs):
        '''建立与数据库的连接'''
        pass
    def isloginsuccess(self,username,password):
        '''根据用户输入的用户名与密码判断登陆是否成功'''
        pass
    def saveuser(self,username,password,avatar,city):
        '''根据用户输入的信息完成注册'''
        pass
    def getblogs(self):
        '''获取数据库中博客信息，并组织成正确的数据格式'''
        pass
 
# myapp.py
class MyApplication(Application):
    def __init__(self,hs,tp,sp,um):
        '''继承父类部分属性，并添加dbutil属性建立全局数据库对象，这样三个功能不需要单独创建对象'''
        super(MyApplication, self).__init__(handlers=hs,
                                            template_path=tp,
                                            static_path=sp,
                                            ui_modules=um)
        self.dbutil = DBUtil()

# tornadoserver.py
class BlogModule(UIModule):
    def render(self, *args, **kwargs):
        # dbutil = DBUtil()

        # 无法直接找到application，在blogmodulez中的handler指向bloghandler模板
        dbutil = self.handler.application.dbutil
        blogs = dbutil.getblogs()
        
class LoginHandler(RequestHandler):
    def get(self, *args, **kwargs):
        self.render("login.html")

    def post(self, *args, **kwargs):
        # dbutil = DBUtil()
        dbutil = self.application.dbutil
~~~

#### AJAX发送请求

1. AJAX的概念：

- asynchronous， 异步的，同步是需要等待。

- JavaScript，JSON格式与JavaScript配合好

- and 

- XML，extensive markup language 可扩展标记语言，ML标记语言使用标签，与HTML的区别在于可扩展，自行定义标签。格式化描述文档用于设备与设置之间传递数据，与平台无关，缺点是浪费字节。后来出现JSON格式 {}。

- JSON：JavaScript Object Notation 对象描方式

- AJAX是纯前端技术（浏览器使用），浏览器中内嵌XMLHTTPRequest对象，该对象也能发起一个请求。单独发送请求，单独等待响应，单独刷新局部页面。也可以用于注册页面单独验证用户名。

2. AJAX的使用

- 获取XMLHTTPRequest对象；
- 进行配置，包括请求路径、发送的数据、如何处理受到的数据；
- 发送请求。

~~~javascript
// regist.html
<script src="static/js/jquery-3.2.1.js"></script>
<script type="text/javascript">
    $(function () {
        var inputusername = $("#input_username");
        var spanusername = $("#span_username");
        inputusername.blur(function () {
            // 失去焦点时回调函数发送请求
            var name = inputusername.val();
            $.ajax({
                url: "/check", 
                // 请求提交的路径
                data: {"username": name},
                // 请求提交的参数
                type: "post",
                // 请求提交的方式
                datatype: "json", 
                // 可接受的服务器响应内容的格式
                success: function (data) {
                    // 服务器正常响应时回调的函数，内容以参数形式传入回调函数
                    // spanusername.html('<span style="color: green">success</span>');
                    // console.log(data);
                    if(data.msg == "fail"){
                        spanusername.text("该用户名已被占用");
                    }
                    else{
                        spanusername.text("该用户名可使用");
                    }
                },
                error: function (err) {
                    // 服务器无法正常响应时回调的函数，内容以参数形式传入回调函数
                    // spanusername.html('<span style="color: red">err</span>');
                    console.log("错误信息："+err);
                }
            });
        });
    });
</script>
~~~

~~~python
# myapp.py
class CheckHandler(RequestHandler):
    def post(self, *args, **kwargs):
        # 后端接收前端请求的信息，以post形式
        username = self.get_body_argument("username", None)
        print(username)

        # 将username送入数据库查询，根据查询结果生成响应内容json格式
        # 前端接收后端响应的信息，以json格式
        if self.application.dbutil.isexists(username):
            # 用户名已存在，需要报错
            self.write({"msg":"fail"})
        else:
            self.write({"msg":"ok"})
~~~

#### cookie与session

- cookie：服务器写入到浏览器中的一些键值对，浏览器在访问服务器时发送，保存set_cookie，读取get_cookie；
- session：将本次访问的相关信息存储在服务器，然后向浏览器写入一个键值对，该键值对就是下一次访问服务器时，找到存储信息的“凭证”，不重复。可以手动使用字典实现，一般用内存型数据库，如mongodb。

~~~python
# mysession.py
# 全局变量，凭证以cookie的值保存在浏览器 {uid:uuid}
s = {"128位uuid1":{"k1":"v1","k2":"v2"},
     "128位uuid2":{"k1":"v3","k2":"v4"}}

class Session:
    def __init__(self,handler):
        '''handler用于获取cookie'''
        self.handler = handler

    def __getitem__(self, key):
        '''魔术方法'''
        # print("getitem被触发，key是",key)
        c = self.handler.get_cookie("uid") # uid:uuid
        if c:
            d = s.get(c,None) # d = {"islogin":"True"}
            print("get:", d)
            if d:
                return d.get(key,None)
            else:
                return None
        else:
            return None

    def __setitem__(self, key, value):
        # print("setitem被触发，key是",key,"value是",value)
        c = self.handler.get_cookie("uid")
        if c:
            d = s.get(c,None)
            print("set:",d)
            if d:
                d[key] = value
            else:
                d = {}
                d[key] = value
                s[c] = d
        else:
            # TypeError: Expected bytes, unicode, or None; got <class 'uuid.UUID'>
            # UnicodeDecodeError: 'utf-8'code can't decode byte 0xcc in position 0: invalid continuation byte
            # 用十六进制描述的md5格式的字符串，可以用作cookie的值
            u = myuuid(uuid4()) # uuid
            d = {}
            d[key] = value # d = {"islogin":"True"}
            s[u] = d
            self.handler.set_cookie("uid",u,expires_days=10) # 将u保存至cookie

# s = Session()
# s["a"] = "b" # __setitem__
# print(s["a"]) # __getitem__
~~~


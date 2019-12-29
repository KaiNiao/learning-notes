## Django项目

### 天天果园
#### 项目
1. 流程

- 注册--登录--首页展示--查看商品--加购物车--下订单--查看订单

- 注册--登录--修改个人信息--添加收获地址

- 支付流程

2. 功能

- 首页展示、查看商品详情、登录、注册、搜索、购物车、下订单、付款、查看订单；管理个人信息、管理收货地址

#### 设计表

表之间关联会联动发生变化。

建立模型时，不为空的字段，设置默认值。

- 商品分类表
- 商品表：商品分类表，一对多关系
- 用户表
- 地址表：用户表，一对多关系
- 购物车表：商品表，一对多/多对多；用户表，一对多/一对一，前者生成中间表，后者建立购物车表
- 订单表：关联用户表，不可变，因此不可以关联地址表与商品表

#### 分模块

模块化开发。

- 用户模块：userinfo，包括用户表、地址表
- 商品模块：memberapp，包括商品分类表、商品表
- 购物车模块：cartinfo，包括购物车表、订单表
- 订单模块：cartinfo
- 支付模块（扩展）

#### 项目准备

新建开发环境：(venv) F:\Python\work\fruitday>virtualenv fruitdayenv

切换开发环境：

(venv) F:\Python\work\fruitday>cd fruitdayenv

(venv) F:\Python\work\fruitday\fruitdayenv>Scripts\activate.bat

(fruitdayenv) F:\Python\work\fruitday\fruitdayenv>

退出环境：(fruitday) F:\Python\work\fruitday\fruitday>Scripts\deactivate.bat

建立项目：django-admin startproject fruitday

建立app：python manage.py startapp userinfo

app新建urls.py：模块化，方便协同开发

新建templates：前后端分离时不存在模板

配置settings文件：添加应用INSTALLED_APPS，数据库DATABASES

创建管理员：python manage.py createsuperuser；kz；912912root

#### django 基础

1. 响应对象

- HttpResponse，它是作用是内部传入一个字符串参数，然后发给浏览器；
- render，可接收三个参数，一是request参数，二是待渲染的html模板文件,三是保存具体数据的字典参数。作用是将数据填充进模板文件，最后把结果返回给浏览器；
- redirect，接收一个URL参数，表示让浏览器跳转去指定的URL。

2. 一对多查询映射

- 一是主表，多是子表，子表查询主表是一对一，主表查询子表是一对多，使用主表.子表_set属性进行查询。外键返回对象。

~~~python
class Book(models.Model):
    # 一对多，添加属性
    # 子表ForeignKey关联到主表
    # publisher = models.ForeignKey(Publisher,verbose_name='出版社',default=True)
    publisher = models.ForeignKey(Publisher,verbose_name='出版社',null=True)

def otm_views(request):
    # 正向查询,book--publisher，子表--主表
    # book = Book.objects.get(id=4)
    # publisher = book.publisher

    # 反向查询,publisher--book，主表--子表
    # django在publisher实体中增加关联对象book_set属性
    # 主表对象可以通过外键的属性查询属于主表的子表的信息
    publisher = Publisher.objects.get(id=10)
    # 可以循环读取books
    books = publisher.book_set.all()

    return render(request,"04_otm.html",locals())
~~~

3. 实现页面自动跳转的两种方式：转发与重定向。

~~~python
def delete_views(request,id):
    # get影响一行或0
    # 物理删除，删除
    # Author.objects.get(id=id).delete()
    # return HttpResponse('删除成功')

    # 逻辑删除，修改状态列
    au = Author.objects.get(id=id)
    au.isActive = False
    au.save()
    # 请求转发，视图调用视图，实现页面跳转，地址不变，需要进行修改，转发只有一次请求
    # return aulist_views(request)
    
    # 重定向，服务器端通知客户端向指定访问地址发送请求，html返回字符串，重定向有两次请求，浏览器显示最后一次的请求地址
    return HttpResponseRedirect("/03_aulist")
~~~

### 模块代码

#### 注册

- 流程：页面html--views--url--页面html；

- 采用pbkdf2_sha1加密方法；
- html中{% load static %}加载静态资源，使用标签；
- html中{% csrf_token %}跨站点攻击。

~~~python
from django.shortcuts import render,redirect
from django.contrib.auth.hashers import make_password,check_password
from django.db import DataError,DatabaseError
import logging.

# Create your views here.
from .models import *


def register_in(request):
    # if request.method == "GET":
    # 不需要写路径，因为默认访问所有templates
    return render(request,"register.html")


def register_(request):
    '''对post提交的数据进行处理'''
    if request.method == "POST":
        new_user = UserInfo()
        # 1.获取注册信息
        new_user.uname = request.POST.get("user_name")
        # 2.判断用户是否存在
        a = UserInfo.objects.filter(uname=new_user.uname)
        if len(a) > 0:
            # 路径地址为/user/registerin/，原因是post的action进行url跳转提交
            return render(request,"register.html",{"message":"该用户名已存在"})
        if request.POST.get("user_pwd") != request.POST.get("user_cpwd"):
            return render(request, "register.html", {"message":"两次密码不一致"})

        # 3.注册用户，密码加密 make_password与check_password
        new_user_pwd = make_password(request.POST.get("user_pwd"),"abc","pbkdf2_sha1")

        # 验证make_password的参数
        # import django
        # from django.contrib.auth.hashers import make_password
        # import os
        # os.environ.setdefault("DJANGO_SETTINGS_MODULE", "fruitday.settings")  # project_name 项目名称
        # django.setup()
        #
        # print(make_password("ww", "abc", "pbkdf2_sha1"))  # pbkdf2_sha1$36000$abc$PzCilfXK83z2YDj6O0nN5ppGPHk=
        # print(make_password("ww", "def", "pbkdf2_sha1"))  # pbkdf2_sha1$36000$def$CyKcmmCcZD0pir32GNROXwZFLiw=
        # print(make_password("ww", None, "pbkdf2_sha1"))  # pbkdf2_sha1$36000$qEu7G1qjxlqS$23/tn/pmfI5h7GQeHFZbivCt4TA=
        # print(make_password("ww", None, "pbkdf2_sha1"))  # pbkdf2_sha1$36000$XmcHWoLTtE9v$KNSK2wkj/rZwj8NDWUkuHQw6u7E=
        # print(make_password("st", "abc", "pbkdf2_sha1"))  # pbkdf2_sha1$36000$abc$GBoaYjle3J4C71fDtdsVK0vMgIo=

        new_user.upassword = new_user_pwd
        new_user.email = request.POST.get("email")
        new_user.phone = request.POST.get("phone")

        try:
            new_user.save()
        except DatabaseError as e:
            logging.warning(e)
        return render(request,"index.html")

    # return redirect("user/register") # 无限循环 GET /user/registerin/user/user/user/
    return redirect("/user/register")
~~~

#### 登录

- 登录状态保存session，可用于如校验验证码前后对比；

- return index(request)代替return render(request, 'index.html')实现商品详细页传参。

~~~python
def login_in(request):
    return render(request,'login.html')


def login_(request):
    if request.method == "POST":
        user = UserInfo()
        user.uname = request.POST.get('user_name')
        
        # try 保护
        try:
            # 1.验证用户名是否存在
            find_user = UserInfo.objects.filter(uname=user.uname)
            print(find_user)
            if len(find_user) <= 0:
                return render(request, 'login.html', {"message": "用户名不存在"})            
            # 2.验证密码是否正确
            # AttributeError: 'QuerySet' object has no attribute 'upassword'
            if not check_password(request.POST.get('user_pwd'), find_user[0].upassword):
                return render(request, 'login.html', {"message": '密码错误'})
        except Exception as e:
            logging.warning(e)
            
        # 3.保存session，回到首页
        request.session["uname"] = find_user[0].uname
        request.session["id"] = find_user[0].id
        return render(request, 'index.html')
    return redirect("/user/login")
~~~

#### 首页

- 数据库get查询结果返回一条对象；
- 数据库filter查询结果QuerySet指的是对象集，可以使用点操作符取出其中对象的任意属性，使用[0]取出其中第一个元素对象；
- 调用django get_object_or_404方法，它会默认的调用django 的get方法， 如果查询的对象不存在的话，会抛出一个Http404的异常。

~~~python
def index(request):
    # 1.查询数据库全部商品分类与商品
    # 方案1：按照固定分类名查询商品
    try:
        good_fruit_type = get_object_or_404(GoodType,title="水果")
        fruit_goods = random.sample(list(good_fruit_type.goods_set.all()),2)
        # print(good_fruit_type.goods_set.all()) # <QuerySet [<Goods: 苹果>, <Goods: 香蕉>, <Goods: 葡萄>]>
    except Exception as e:
        logging.warning(e)
    # print(locals()) # 使用点操作符key取出字典的value
    # {'fruit_goods': [<Goods: 香蕉>, <Goods: 苹果>], 'good_fruit_type': <GoodType: 水果>, 'request': <WSGIRequest: GET '/'>}

    # 方案2：全部分类，查询全部商品
    types = GoodType.objects.all()
    goods = Goods.objects.all()
    # print(locals())
    # {'goods': <QuerySet[ < Goods: 苹果 >, < Goods: 香蕉 >, < Goods: 葡萄 >, < Goods: 猪肉 >, < Goods: 牛肉 >, < Goods: 鸡肉 >] >,'types': <QuerySet[ < GoodType: 水果 >, < GoodType: 肉类 >] >}

    # 方案3：全部分类，查询各类商品
    typess = GoodType.objects.all()
    ac = []
    for type in typess:
        b = {}
        b["type"] = type.title
        good_type = get_object_or_404(GoodType,title=type.title)
        f_goods = random.sample(list(good_type.goods_set.all()),2)
        b["goods"] = f_goods
        ac.append(b)
        # 'ac': [{'type': '水果','goods': [ < Goods: 香蕉 >, < Goods: 苹果 >]}, 
        #        {'type': '肉类','goods': [ < Goods: 猪肉 >, < Goods: 鸡肉 >]}]
    return render(request, "index.html", {"good_list": locals()})
~~~

~~~html
{#index.html#}
<head>
    {% load static %}
    <title>首页</title>
</head>
<body>
    ****方案1****
    <br>
    {% for goods in good_list.fruit_goods %}
        {{ goods.title }}
        {{ goods.price }}
    {% endfor %}
    <br>

    ****方案2****
    <br>
    {% for type in good_list.types %}
        {{ type.title }}：<br>
        {% for good in good_list.goods %}
            {% if good.type.title == type.title %}
                {{ good.title }}
            {% endif %}
        {% endfor %}
        <br>
    {% endfor %}

    ****方案3****
    <br>
    {% for m in good_list.ac %}
        {{ m.type }}：<br>
        {% for gs in m.goods %}
            {{ gs.title }}
        {% endfor %}
        <br>
    {% endfor %}
</body>
~~~

#### 退出

- 登录信息以session保存；
- 首页判断session进行页面的显示，没有session时进入登录；

- 登录login进入首页index，首页点击退出时调用login_out删除session。

~~~python
def login_out(request):
    try:
        print(request.session) # <django.contrib.sessions.backends.db.SessionStore object at 0x00000224F92EA668>
        if request.session["user_name"]:
            del request.session["user_name"]
            del request.session["user_id"]
    except KeyError as e:
        logging.warning(e)
    return redirect("/")

# index.html
<body>
    {%  if request.session.user_name %}
        欢迎您，{{ request.session.user_name }}
        <a href="{% url 'login_out' %}">退出</a>
    {% else %}
        <a href="{% url 'login' %}">登录</a>
        <a href="{% url 'register' %}">注册</a>
    {% endif %}
</body>
~~~

#### 商品详情页

- 列表页-->详情页，models.py中定义get_absolute_url可以简化前端页面代码；

- 图片F12查看路径。

~~~python
# index.html
<body>
    {% for type in good_list.types %}
        {{ type.title }}：
        {% for good in good_list.goods %}
            {% if good.type.title == type.title %}
                <a href="{{ good.get_absolute_url }}">{{ good.title }}</a>
            {% endif %}
        {% endfor %}
    {% endfor %}
</body>

# memeberapp\models.py
class Goods(models.Model):
    '''商品表'''
    # 地址栏传参，简化前端页面代码
    def get_absolute_url(self):
        if self.type.title == "水果":
            return "/detail/?goodid={}/".format(self.id)
        elif self.type.title == "肉类":
            return u"不好吃"
        
# userinfo\views.py
def detail_one(request):
    '''查询数据库该id的商品'''
    # 切片，去掉最后一个元素'/'
    good_id = request.GET.get("goodid")[:-1]
    try:
        goodone = Goods.objects.filter(id=good_id)
    except Exception as e:
        logging.warning(e)
    return render(request,"detail.html",{"goodone":goodone[0]})
~~~

#### 地址

~~~python
# userinfo\views.py
def user_ads(request):
    '''地址列表'''
    # 1.从session中获取用户id
    # 2.查询数据库中地址表中该用户id的数据
    # 3.返回页面
    user_id = request.session.get("user_id")
    # print(user_id)
    # user = get_object_or_404(UserInfo,id=user_id)
    # adss = list(user.address_set.all())
    # print(adss)
    # return HttpResponse("1234")
    adss = Address.objects.filter(user_id=user_id)
    content = {"adss":adss}
    return render(request,"",content)
~~~

#### 添加购物车

- AJAX的返回值格式datatype选择JSON；
- 使用AJAX的优点是不需要页面跳转，路径不变，同时商品数量gcount可以通过输入进行改变；
- <a href="" onclick="addcart({{ goodone.id }})">加入购物车2</a>修改为<a onclick="addcart({{ goodone.id }})">加入购物车2</a>

~~~python
def add_cart(requsest):
    '''添加购物车'''
    # 1.获取前端数据（商品id、数量）    # 2.获取用户id    # 3.查询用户、商品、购物车中用户是否有此商品    # 4.保存    # 5.返回
    new_cart = CartInfo()
    good_id = requsest.GET.get("goodid")
    good_count = requsest.GET.get("gcount")
    user_id = requsest.session.get("user_id")
    # print(good_id,good_count,user_id)
    # 用户未登录时user_id为None，报错UserInfo matching query does not exist.
    good_ = Goods.objects.filter(id=good_id)
    user_ = UserInfo.objects.get(id=user_id)
    # print(good_,user_) # <QuerySet [<Goods: 葡萄>]> abc
    if len(good_) > 0:        # 有该商品，保存进新建立的购物车对象
        new_cart.user = user_
        # WARNING: root:NOT NULL constraint failed: cartinfo_cartinfo.goods_id
        new_cart.goods = good_[0]
    else:
        # return render(requsest,"index.html")
        content = {"static":"OK","text":"无该商品"}
        # json.dumps字典转换为json字符串
        return HttpResponse(json.dumps(content))
    new_cart.ccount = int(good_count)    # 商品数量字符串转化为int
    try:
        # user_id指的是CartInfo表的user属性对应的User表中的id属性 user = models.ForeignKey(UserInfo, db_column='user_id')
        oldgo = CartInfo.objects.filter(user_id=user_id,goods_id=good_id)
        # print(oldgo) # <QuerySet [<CartInfo: abc>]>
        if len(oldgo) > 0:            # 该用户添加过该商品
            oldgo[0].ccount = oldgo[0].ccount + new_cart.ccount
            oldgo[0].save()
        else:
            new_cart.save()
    except DatabaseError as e:
        logging.warning(e)
    # return render(requsest,"index.html")
    content = {"static":"OK","text":"添加购物车成功"}
    return HttpResponse(json.dumps(content))

# detail.html
<body>
    <a href="{{ goodone.get_adcart_url }}">添加购物车1</a>
    {{ goodone.title }}
    {{ goodone.price }}
    <img src="{% static goodone.picture %}" alt="">

    <a onclick="addcart({{ goodone.id }})">加入购物车2</a>
<script type="text/javascript" src="{% static 'js/jquery-3.2.1.js' %}"></script>
<script type="text/javascript">
    function addcart(goodid) {
        $.ajax(
            {
                url:"/cartinfo/addcart",
                type:"get",
                dataType:"json",
                data:{
                    "goodid":goodid,
                    "gcount":1,
                },
                success:function (data) {
                    location.href = "{% url 'cart' %}";
                },
                error:function (error) {
                    console.log(error);
                }
            }
        )
    }
</script>
</body>
~~~

#### 购物车显示页

- 之前的购物车是在后端进行显示，新建购物车页继承自首页；
- 添加商品至购物车后进行跳转显示；
- 为订单页在前端保存商品数据，html5：会话存储sessionStorage保存在浏览器端 / 本地存储localStorage；
- input中使用带下划线自定义属性。

~~~python
def cart_info(request):
    user_id = request.session.get("user_id")
    # 查找到指定用户的购物车信息，一对多的关系
    find_carts = CartInfo.objects.filter(user_id=user_id)
    # print(find_carts) # <QuerySet [<CartInfo: abc>, <CartInfo: abc>, <CartInfo: abc>]>
    return render(request,"cart.html",{"find_carts":find_carts})

# cart.html
{% extends "index.html" %}
{% load static %}
{% block title %}购物车{% endblock %}
{% block content %}
    <br>
    {% for cart in find_carts %}
        <input type="checkbox" name="check" _i="{{ cart.id }}" _n="{{ cart.goods.title }}" _p="{{ cart.goods.price }}">
        {{ cart.goods.title }}
        {{ cart.goods.price }}
        <input type="text" value="{{ cart.ccount }}" name="cot">
        <br>
    {% endfor %}
    <a onclick="caToor()">下订单</a>
    <script type="text/javascript" src="{% static 'js/jquery-3.2.1.js' %}"></script>
    <script>
        function caToor() {
            var carts = [];
            $.each($("input:checkbox:checked"),function () {
                var cartg = {};
                cartg["id"] = $(this).attr("_i");
                cartg["name"] = $(this).attr("_n");
                cartg["price"] = $(this).attr("_p");
                cartg["count"] = $(this).next().attr("value");
                carts.push(cartg);
            });
            sessionStorage.setItem("acot",JSON.stringify(carts));
            location.href = "/cartinfo/order";
        }
    </script>
{% endblock %}
~~~

#### 订单页

- 购物车页面的显示+页面的跳转；
- 从购物车页到订单页，对信息进行保存，使用js获取商品信息，不经过后端，地址来自后端；
- 确认订单可以采用form表单或者ajax，这里采用ajax。 

~~~python
def add_order(request):
    user_id = request.session.get("user_id")
    orderdetail = request.POST.get("acot")
    adsname = request.POST.get("adsname")
    adsphone = request.POST.get("adsphone")
    ads = request.POST.get("ads")
    orderNo = datetime.datetime.now().strftime("%Y%m%d%H%M%S")
    print(user_id,orderdetail,adsname,adsphone,ads)
    # 3 [{"id": "2", "name": "香蕉", "price": "11.00", "count": "10"}, {"id": "3", "name": "葡萄", "price": "14.00","count": "17"}] ee 33 dd
    acot = 5
    acount = 55.00
    try:
        user = UserInfo.objects.get(id=user_id)
        order = Order.objects.create(orderNo=orderNo,orderdetail=orderdetail,adsname=adsname,                      			adsphone=adsphone,ads=ads,acot=acot,acount=acount,user=user)
    except DatabaseError as e:
        logging.warning(e)
    content = {"static":"OK"}
    return HttpResponse(json.dumps(content))

# order.html
{% block content %}
    {% for ads in adss %}
        <input type="radio" name="adds" _n="{{ ads.aname }}" _ads="{{ ads.ads }}" _ap="{{ ads.phone }}" value="{{ ads.id }}">
        {{ ads.aname }}
        {{ ads.ads }}
        {{ ads.phone }}
        <br>
    {% endfor %}

    <div id="show"></div>
    <button onclick="porder()">订单提交</button>
    <script type="text/javascript" src="{% static 'js/jquery-3.2.1.js' %}"></script>
    <script>
        $(function () {
            var cals = JSON.parse(sessionStorage.getItem("acot"));
            s = "";
            $.each(cals,function (index,obj) {
                s = s + obj.id + obj.name + obj.price + obj.count + "<br>";
            });
            $("#show").append(s);
        });

        function porder() {
            {#console.log($("input:radio:checked").attr("_n"))#}
            $.ajax({
                url:"addorder",
                type:"post",
                datatype:"json",
                data:{
                    {#Forbidden (CSRF token missing or incorrect.): /cartinfo/addorder#}
                    {#[20/Apr/2019 11:17:56] "POST /cartinfo/addorder HTTP/1.1" 403 2502#}
                    "csrfmiddlewaretoken":"{{ csrf_token }}",
                    "acot":sessionStorage.getItem("acot"),
                    "adsname":$("input:radio:checked").attr("_n"),
                    "adsphone":$("input:radio:checked").attr("_ap"),
                    "ads":$("input:radio:checked").attr("_ads"),
                },
                success:function (data) {
                    console.log(data);
                },
                error:function(error){
                    console.log(error);
                }
            })
        }
    </script>
{% endblock %}
~~~














# 第六模块 Django框架

## WEB框架

### http协议

#### http简介

```
http协议是超文本传输协议（hyper text transfer protocol）的缩写，用于从万维网服务器传输超文本到本地浏览器的传送协议。

1. 基于TCP/IP通信协议来传递数据（HTML文件，图片文件，查询结果等等）。

2. 属于应用层的面向对象的协议，由于其简洁、快速的方式，适用于分布式超媒体信息系统。
	五层模型：应用层，传输层，网络层，数据链路层，物理层
	OSI七层模型：应用层，表示层，会话层

3. 工作于客户端-服务端架构上。
```

#### http特点

```
1. 简单快速：客户向服务器请求服务时，只需要传送请求方法和路径，因此HTTP服务器的程序规模较小，通信速度很快。
	常用方法：GET, POST

2. 灵活：HTTP允许传输任意类型的数据对象，由Content-Type加以标记。

3. 无连接：每次连接只处理一个请求，节省传输时间。

4. 无状态：协议对于事务处理没有记忆能力。
```

#### http请求协议

```
请求首行；      # 请求方式 请求路径 协议和版本，如：GET /index.html HTTP/1.1
请求头信息；    # 请求头名称：请求头内容，即为key: value格式，如：HOST: localhost
空行；         # 用来与请求体分隔开  
请求体；       # GET没有请求体，只有POST有请求体
```

##### get请求

http默认的请求方式是get，特点如下：

```
1. 没有请求体

2. 数据量有限制

3. get请求数据会暴露在浏览器的地址栏中
```

请求头：

```
1、Host
	请求的web服务器域名地址

2、User-Agent
	HTTP客户端运行的浏览器类型的详细信息。通过该头部信息，web服务器可以判断出http请求的客户端的浏览器的类型。

3、Accept
	指定客户端能够接收的内容类型，内容类型的先后次序表示客户都接收的先后次序

4、Accept-Lanuage
	指定HTTP客户端浏览器用来展示返回信息优先选择的语言

5、Accept-Encoding
	指定客户端浏览器可以支持的web服务器返回内容压缩编码类型。表示允许服务器在将输出内容发送到客户端以前进行压缩，以节约带宽。
	而这里设置的就是客户端浏览器所能够支持的返回压缩格式。

6、Accept-Charset
	HTTP客户端浏览器可以接受的字符编码集

7、Content-Type
	显示此HTTP请求提交的内容类型。一般只有post提交时才需要设置该属性
    当提交为表单数据时，可以使用“application/x-www-form-urlencoded”；当提交的是文件时，就需要使用“multipart/form-data”编码类型。
```

##### get和post请求的区别

```
get：提交的数据会附在URL之后（就是把数据放置在HTTP协议头中），以？分割URL和传输数据，多个参数用&连接

post：把提交的数据放置在HTTP包的包体中，所传数据没有大小限制。

区别：
	1. get提交数据会放在URL之后，以？分割URL和传输数据，参数之间以&相连。
	2. get提交数据有数据大小的限制，post没有。
	3. get需要使用Request.QueryString来取得变形的值，而post通过Request.Form来获取变量的值。
	4. get提交数据会带来安全问题。
```

#### http响应协议

##### 响应格式

服务器接收并处理客户端发过来的请求后会返回一个HTTP的响应消息。

HTTP响应分为四个部分，分别是：状态行、消息报头、空行和响应正文。

![img](https://images2017.cnblogs.com/blog/877318/201710/877318-20171019075402771-704070831.png)

```
状态行：由HTTP协议版本号，状态码，状态消息三部分组成。

消息报头：用来说明客户端要使用的一些附加信息。

空行：消息报头后面的空行是必须的。

响应正文：服务器返回给客户端的文本消息。
```

##### 响应状态码

```
状态代码有三位数字组成，第一个数字定义了响应的类别，共分五种类别:
    1xx：指示信息--表示请求已接收，继续处理
    2xx：成功--表示请求已被成功接收、理解、接受
    3xx：重定向--要完成请求必须进行更进一步的操作
    4xx：客户端错误--请求有语法错误或请求无法实现
    5xx：服务器端错误--服务器未能实现合法的请求

常见状态码：
    200 OK                        //客户端请求成功
    400 Bad Request               //客户端请求有语法错误，不能被服务器所理解
    401 Unauthorized              //请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
    403 Forbidden                 //服务器收到请求，但是拒绝提供服务
    404 Not Found                 //请求资源不存在，eg：输入了错误的URL
    500 Internal Server Error     //服务器发生不可预期的错误
    503 Server Unavailable        //服务器当前不能处理客户端的请求，一段时间后可能恢复正常
```

### web应用与web框架

对于所有的web应用，本质上都是一个socket服务端，用户的浏览器是一个socket的客户端。

```python
import socket

response_header = 'HTTP/1.1 200 0K\r\n\r\n'
response_body = '<h1>hello world</h1>'


def handle_request(conn):
    but = conn.recv(1024)
    print(but)
    conn.send(response_header.encode('utf-8'))
    conn.send(response_body.encode('utf-8'))


def main():
    server = socket.socket()
    server.bind(('127.0.0.1', 8888))
    server.listen(5)
    while True:
        conn, addr = server.accept()
        handle_request(conn)
        conn.close()


if __name__ == '__main__':
    main()

```

上述自己处理过于复杂，因此提供接口，让我们专心编写web业务，即WSGI接口。

#### wsgiref模块

```python
from wsgiref.simple_server import make_server


def application(environ, start_response):
    start_response('200 OK', [('Content-Type', 'text/html')])
    return [b'<h1>hello world</h1>']


httped = make_server('127.0.0.1', 8888, application)

print('开始监听', 8888)
httped.serve_forever()

```

## Django简介

### MVC与MTV模型

#### MVC模型

![img](https://images2018.cnblogs.com/blog/877318/201804/877318-20180418162558974-92667466.png)

WEB服务器开发领域里著名的MVC模式，所谓MVC就是把Web应用分为模型（M），控制器（C）和视图（V）三层，他们之间以一种插件式、松耦合的方式连接在一起，模型负责业务对象与数据库的映射(ORM)，视图负责与用户的交互(页面)，控制器接受用户的输入调用模型和视图完成用户的请求。

#### MTV模型

```
M：代表模型，负责业务对象和数据库的关系映射（ORM）
T：代表模板，负责如何把页面展示给用户（html）
V：代表视图，负责业务逻辑，并在适当的时候调用M和T

除了以上三层之外，还需要一个URL分发器，它的作用是将一个个URL的页面请求分发给不同的View处理，View再调用相应的Model和Template。
```

![img](https://images2018.cnblogs.com/blog/877318/201804/877318-20180418162350672-193671507.png)

一般是用户通过浏览器向我们的服务器发起一个请求(request)，这个请求回去访问视图函数，（如果不涉及到数据调用，那么这个时候视图函数返回一个模板也就是一个网页给用户），视图函数调用模型，模型去数据库查找数据，然后逐级返回，视图函数把返回的数据填充到模板中空格中，最后返回网页给用户。

### 静态文件配置

本身不配带静态文件夹，需要自己创建static文件夹，并在settings中添加一行路径。

```
STATICFILEPATH_DIR = [
	os.path.join(BASE_DIR, 'static'),
]
```

```
一般用来将不会经常变化的文件放在其中，比如jquery文件，但在引入时需要注意，永远是static/+路径。
```

### 路由层

路由 ---------》》》》视图层

#### 简单配置

```
re_path(r'^articles/2003/$', views.special_case_2003),
re_path(r'^articles/([0-9]{4})/$', views.year_archive),
re_path(r'^articles/(?P<y>[0-9]{4})/(?P<m>[0-9]{2})/$', views.month_archive),
```

URL配置(URLconf)就像Django 所支撑网站的目录。它的本质是URL与要为该URL调用的视图函数之间的映射表；你就是以这种方式告诉Django，对于客户端发来的某个URL调用哪一段逻辑代码对应执行。

```
注意：
    1. 若要从URL 中捕获一个值，只需要在它周围放置一对圆括号。
    
    2. 不需要添加一个前导的反斜杠，因为每个URL 都有。例如，应该是^articles 而不是 ^/articles。
    
    3. 每个正则表达式前面的'r' 是可选的但是建议加上。它告诉Python 这个字符串是“原始的” —— 字符串中任何字符都不应该转义
```

#### 有名分组

上面的示例使用简单的、没有命名的正则表达式组（通过圆括号）来捕获URL 中的值并以位置 参数传递给视图。在更高级的用法中，可以使用命名的正则表达式组来捕获URL 中的值并以关键字 参数传递给视图。

在Python 正则表达式中，命名正则表达式组的语法是`(?P<name>pattern)`，其中`name` 是组的名称，`pattern` 是要匹配的模式。

防止出现参数混乱的情况。

```
需要注意，前面分了多少组，那么后面就需要多少形参进行接收。
```



#### 分发

使用include，前面匹配所有，后面将不同的路径分开，使用include('路径')来分发路径。

#### 反向解析

在使用Django 项目时，一个常见的需求是获得URL 的最终形式，以用于嵌入到生成的内容中（视图中和显示给用户的URL等）或者用于处理服务器端的导航（重定向等）。人们强烈希望不要硬编码这些URL（费力、不可扩展且容易产生错误）或者设计一种与URLconf 毫不相关的专门的URL 生成机制，因为这样容易导致一定程度上产生过期的URL。

```
在需要URL的地方，不同的层级，Django提供不同的工具进行反查：
	1. 在模板中，使用URL模板标签。
		在html中，使用{% url '......' %}来
		
	2. 在python代码中，使用reverse函数。注意：正则表达式分组，需要参数替换
```

当命名你的URL 模式时，请确保使用的名称不会与其它应用中名称冲突。如果你的URL 模式叫做`comment`，而另外一个应用中也有一个同样的名称，当你在模板中使用这个名称的时候不能保证将插入哪个URL。在URL 名称中加上一个前缀，比如应用的名称，将减少冲突的可能。我们建议使用`myapp-comment` 而不是`comment`。

#### 名称空间

命名空间（英语：Namespace）是表示标识符的可见范围。一个标识符可在多个命名空间中定义，它在不同命名空间中的含义是互不相干的。这样，在一个新的命名空间中可定义任何标识符，它们不会与任何已有的标识符发生冲突，因为已有的定义都处于其它命名空间中。

由于name没有作用域，Django在反解URL时，会在项目全局顺序搜索，当查找到第一个name指定URL时，立即返回，如果name重复就会导致返回错误，因此需要使用namespace来区分。

```
在include里面使用元组来传入两个参数，一个是分发的文件路径，一个是namespace名称。
```

注意：在django2.0中的path方法，可以自定义匹配方法，还提供了匹配格式转换器。

### 视图层

视图函数，负责接收web请求并返回web响应，具体的响应内容可以是一张网页，一个重定向，一个404错误，但无论视图本身包含什么逻辑，都要返回响应，约定将视图放置在项目或者应用程序目录中的名为views.py文件中。

```
request参数实际上接收的是HttpRequest对象，通常称之为request。
视图层返回HttpResponse对象。
```

#### HttpRequest对象

##### request属性

```
HttpRequest.method: 返回此次请求的方法（get/post等）

HttpRequest.GET: 返回get请求的内容
	返回QueryDict对象
	
HttpRequest.POST: 返回post请求的内容
	返回QueryDict对象
	
HttpRequest.path: 返回字符串，路径
```

##### request常用方法

```
HttpRequest.get_full_path(): 返回路径+get请求的内容

HttpRequest.is_ajax()
```

#### HttpResponse对象

响应对象主要有三种形式：

- HttpResponse()
- render()
- redirect()

HttpResponse()括号内直接跟字符串作为响应体，直接简单。

```
render(request, template_name[, context])

    request: 用于生成响应的请求对象，必须传入

    template_name: 模板名称
    	会先查看html文件里面是否有模板语法，如果有，则先将值传入，再将html文件传给浏览器。

    context: 添加到模板上下文的一个字典，默认是空字典，可以不传入。
    	使用locals()函数可以将变量按照变量名+变量的方式全部储存到字典中，传入给template_name。
```

### 模板层

如果直接将HTML硬编码到视图层中，会导致耦合度太高，一旦需要修改，python的代码也需要相应修改。

#### 模板语法之变量

句点字符：遍历复杂数据结构，也可以用来引用对象的方法（无参数方法）。

语法：{{var_name}}

#### 模板语法之过滤器

就是在大括号里面加个|。

```
{{obj|filter_name: param}}

filter_name: 
	default:'无内容'
	length
	filesizeformat
	date
	slice
	truncatechar: 9  # 截断字符串
	safe
```

#### 模板语法之标签

标签：{% tag %}

```
for标签：
	{% for foo in list %}
		<p>{{ foo.name }}</p>
	{% empty %}
		<p>sorry, no person here</p>
	{% endfor %}
	
if标签：
	{% if num>100 %}
		<p>哟</p>
	{% else %}
		<p>哎</p>
	{% endif %}

with标签：
	{% with total=business.employees.count %}
		{{ total }} employee {{ total|pluralize }}
	{ % endwith %}
```

#### 自定义标签和过滤器

可以自己去网站上看。

两个最大的区别在于：过滤器可以用在if等语句的后面，标签不可以。

#### 模板继承

include方法：只能够将html文件的所有代码都拿来，不可以选择修改。

extend方法：在html文件中留好block块，可以用作后续的修改。

注意：

```
1. 如果在模板中使用{% extends %}标签，则它必须处在第一个标签。

2. 在base模板中设置越多的{% block %}越好。

3. 使用{{ block.super }}可以拿到初始的值。

4. 不能在一个模板中定义多个相同名字的block块。
```

### ORM

ORM，它实现了数据模型与数据库的解耦，数据模型的设计不需要依赖于特定的数据库，通过简单的数据库配置可以轻松更换数据库，极大的减轻了开发人员的工作量。

ORM：对象-关系-映射。

![img](https://images2018.cnblogs.com/blog/877318/201804/877318-20180425153356710-1116321211.png)

#### 创建表

models中可以创建表，但是无法创建数据库，即数据库的创建需要MySQL来完成。

固定模式，可以参考https://www.cnblogs.com/yuanchenqi/articles/8933283.html，不是全对。

#### 添加表记录

```
方法一：
	book_obj = Book(id=1, title='python', state=True, price=100, pub_date='2012-12-12', publish='人民日报')
	book_obj.save()

方法二：
	book_obj = Book.objects.create(title='python', state=True, price=100, pub_date='2012-12-12', publish='人民日报')
```

#### 查询表记录（重点）

总共十二种方法：

```
1. all()方法：返回queryset对象，调用者为Book.objects
	book_list = Book.objects.all()  # 类似[obj1, obj2]
	book_list支持序列，索引，for循环。
	select * from ...
	
2. first(), last()方法：返回models对象，调用者为queryset
	相当于：Book.objects.all()[0]
	
3. filter()方法：返回queryset对象，调用者为Book.objects
	book_list = Book.objects.filter(title='go')
	select * from ... where title='go';
	
4. exclude()方法：返回queryset对象，调用者为Book.objects
	与filter相反。
	book_list = Book.objects.exclude(title='go')
	select * from where title != 'go';

5. get()方法：返回一个models对象，注意这里只能查询有且只有一个的结果，否则报错。
	book_obj = Book.objects.get(title='go')
	
6. orderby()方法：调用者queryset对象，返回queryset
	book_list = Book.objects.orderby('id')  # 按照ASC排序
	book_list = Book.objects.orderby('-id')
	
7. reverse()方法：调用者queryset对象，返回queryset

8. count()方法：返回int值，调用者queryset对象
	val = Book.objects.all().count()
	
9. exists()方法：返回布尔值，个人觉得意义不大
	bol = Book.objects.all().exists()
	
10. values()方法：返回queryset，以[dic1, dic2]形式，调用者queryset对象
	book_list = Book.objects.values('price', 'title')
	
11. value_list()方法：返回queryset，以[tuple1, tuple2]形式，调用者queryset对象
	book_list = Book.objects.value_list('price', 'title')
	
12. distinct()方法：一般配合values和value_list使用，调用者queryset，返回queryset
	book_list = Book.objects.values('price').distinct()
```

##### 模糊查询

```
所有的模糊查询都是双下划线方法：
	1. 大于：Book.objects.filter(price__gt=100)
		select * from ... where price>100
	
	2. 小于：Book.objects.filter(price__lt=100)
	
	3. 或：Book.objects.filter(price__in=[100, 120])
	
	4. 范围：Book.objects.filter(price__range=[100,200])
	
	5. 包含：Book.objects.filter(title__contains='h')
	
	6. 包含且不分大小写：Book.objects.filter(title_icontains='h')
	
	7. 开头：Book.objects.filter(title__startswith='py')
	
	8. 日期：Book.objects.filter(pub_date__year=2012)
	
```

#### 删除表记录

需要注意，删除操作只能在queryset上调用，防止出现Book.objects.delete()全部误删的现象。

```
两种方法：
	1. Book.objects.all().delete()
	
	2. book_obj.delete()
```

注意：删除一个对象也会删除与它关联的外键对象。

#### 修改表记录

```
Book.objects.filter(title='python').update(price=100)
```

update()方法对于任何结果集（QuerySet）均有效，这意味着你可以同时更新多条记录update()方法会返回一个整型数值，表示受影响的记录条数。

#### 多表操作

##### 创建表

主要需要注意不同对应关系，创建表结构时需要添加不同的外键，另外，外键的数据结构必须相同。

一对多：多的那张表创建外键。

一对一：任意一张表创建外键。

多对多：创建第三张表，在第三张表中两个关联的字段创建外键。

创建操作查阅即可：

```python
class Author(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=32)
    age = models.IntegerField(4)
    authorDetail = models.OneToOneField(to="AuthorDetail", to_field="id", on_delete="models.CASCADE")


class AuthorDetail(models.Model):
    id = models.AutoField(primary_key=True)
    birthday = models.DateField()
    telephone = models.CharField(max_length=15)
    addr = models.CharField(max_length=32)


class Book(models.Model):
    id = models.AutoField(primary_key=True)
    title = models.CharField(max_length=32)
    publish_date = models.DateField()
    price = models.DecimalField(max_digits=8, decimal_places=2)
    publish = models.ForeignKey(to="Publish", to_field="id", on_delete="models.CASCADE")
    authors = models.ManyToManyField(to="Author")


class Publish(models.Model):
    id = models.AutoField(primary_key=True)
    name = models.CharField(max_length=32)
    city = models.CharField(max_length=32)
    email = models.EmailField()
```

##### 添加表记录

没有任何关联的就是单表，添加以及查询都是基于单表查询，因此先添加单表记录。

###### 一对多

```python
pub_obj = Publish.objects.create(name='人民出版社')
pub_obj = Publish.objects.filter(name='人民出版社').first()
方法一：
	book_obj = Book.objects.create(title='三国', publish_id=1)
方法二：
	book_obj = Book.objects.create(title='水浒', publish=pub_obj)

book_obj.publish.name   # 无论用哪种方式进行添加，都可以查询到publish对象
```

需要注意，无论用哪种方式进行添加记录，在查询时，都可以通过对象加属性查询到publish的对象。

###### 多对多

```python
book_obj = Book.objects.create(title='红楼梦', publish_id=1)
book_obj.author.add(1, 2)  # 放入对象也可以，会自动查询主键值进行添加。
```

进行多对多的绑定，需要先找到manytomanyfield那个关键字，使用add属性进行传参。

其他方法：all, remove, clear

##### 基于对象跨表查询（子查询）

跟MySQL一样，先找内部，再找外部。

正向查询：从绑定关联属性的一头查询关联属性，正向按照字段。

反向查询：相反，反向按照表名（小写+_set）。

###### 一对多查询

正向：

```python
book_obj = Book.objects.filter(title='红楼梦')[0]
pub_obj = book_obj.publish
```

反向：

```python
pub_obj = Publish.objects.filter(id=1)
book_list = pub_obj.book_set.all()  # 返回queryset对象
for book_obj in book_list:
    print(book_obj.title)
```

###### 多对多查询

正向：

```python
book_obj = Book.objects.filter(id=1)
author_list = book_obj.authors.all()  # 返回queryset对象
for author in author_list:
    print(author.name)
```

反向：

```python
author_obj = Author.objects.filter(name='alex')
book_list = author_obj.book_set.all()  # 返回queryset对象
for book_obj in book_list:
    print(book_obj.title)
```

###### 一对一查询

非常特殊，因为是一对一，因此反向不需要加set，不返回queryset对象。

正向：

```python
author_obj = Author.objects.filter(name='alex')[0]
author_detail_obj = author_obj.authorDetail
print(author_detail_obj.telephone)
```

反向：

```python
author_detail_obj = AuthorDetail.objects.filter(id=1).first()
author_obj = author_detail_obj.author
print(author_obj.name)
```

##### 基于双下划线的跨表查询（连表查询）

正向：按字段名。

反向：按表名小写。

双下划线链接模型间关联字段的名称。

###### 一对多查询

正向：

```python
ret = Book.objects.filter(title='三国').values('publish__name')   # 返回queryset对象[dict1, dict2]
print(ret)
```

反向：

```python
ret = Publish.objects.filter(book__title='三国').values('name')  # 返回queryset对象
```

###### 多对多查询

正向：

```python
ret = Book.objects.filter(title='三国').values('authors__name')  # 返回queryset对象
print(ret)  # 正向使用字段，即ManyToMany那个字段
```

反向：

```python
ret = Author.objects.filter(book__title='三国').values('name')  # 返回queryset对象
```

###### 一对一查询

正向：

```python
ret = Author.objects.filter(name='alex').values('authorDetail__telephone')  # 返回queryset对象
print(ret)
```

反向：

```python
ret = AuthorDetail.objects.filter(author__name='alex').values('telephone')  # 返回queryset对象
```

###### 连续跨表

无所谓正向，需要综合使用。

```python
ret = Book.objects.filter(publish__name='人民出版社').values('title', 'authors__name')
```

```python
ret = Publish.objects.filter(name='人民出版社').values('book__title', 'book__authors__name')  # 要有联系，不能横空跨
```

```python
# 手机号以151开头的作者出版过的所有书籍名称以及出版社名称
ret = AuthorDetail.objects.filter(telephone__startswith('151')).values('author__book__title', 'author__book__publish__name')

ret = Author.objects.filter(authorDetail__telephone__startswith('151')).values('book__title', 'book__publish__name')
```

#### 聚合与分组（aggregate&annotate）

##### 聚合（aggregate）

记住先分组后聚合。

正向：用字段。

反向：用表名小写。

需要注意：聚合之后，返回的是一个字典，而不是queryset对象。

聚合函数：from django.db.models import Aug, Max, Min, Count

```python
ret = Book.objects.all().aggregate(max_price=Aug('price'))
```

##### 分组（annotate）

单表较为简单。

单表查询公式：

```python
单表模型.objects.values("group by字段").annotate(聚合函数("统计字段"))
```

```python
ret = Emp.objects.values("dep").annotate(avg_salary=Aug("salary"))
```

###### 跨表分组查询

记住返回的是queryset，因此可以对返回值进行继续操作。一定是先join，再分组。

正向：字段名。

反向：表名小写。

模板：

```python
ret = 每一个的表模型.objects.values("pk").annotate(聚合函数("关联表__统计字段"))  # 返回queryset对象
ret.values("属性")
```

```python
ret = 每一个后的表模型.objects.annotate(聚合函数("关联表__统计字段")).values(所有字段+聚合函数字段)
```

#### F与Q查询

F查询：用来解决拿到变量值问题。类似于python中f{}功能。

Q查询：用来解决或与非操作问题。

先Q后键值对。

```python
from django.db.models import F, Q


ret = Book.objects.filter(comment__num__gt=F("read__num"))

ret = Book.objects.filter(title="红楼梦", price=100)
```
































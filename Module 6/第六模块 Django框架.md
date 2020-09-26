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






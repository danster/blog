# Python网络基础
<!--toc-->

> 使用Python标准库中的一些高级包简化Python服务器，最终的效果分离静态内容、CGI应用和服务器，降低三者之间的耦合，让代码变得简单而容易维护。

### **TCP/IP和socket**

> socket是进程间通信的一种方法 (参考Linux进程间通信)，它是基于网络传输协议的上层接口。socket有许多种类型，比如基于TCP协议或者UDP协议(两种网络传输协议)。其中又以TCP socket最为常用。
> 
> 然而，socket传输自由度太高，从而带来很多安全和兼容的问题。我们往往利用一些应用层的协议(比如HTTP协议)来规定socket使用规则，以及所传输信息的格式。
> 
> 一个socket包含四个地址信息: 两台计算机的IP地址和两个进程所使用的端口(port)。IP地址用于定位计算机，而port用于定位进。

### TCP socket

```python
# Server side
import socket

# 创建一个socket对象, 使用IPv4(AF_INET, IP version 4)和TCP协议(SOCK_STREAM)
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('', 8000)) # HOST and PORT

s.listen(3) # passively wait, 3: maximum number of connections in the queue
conn, addr = s.accept()   # accept and establish connection
request = conn.recv(1024) # receive message
conn.sendall(reply)       # send message
conn.close()              # close connection

print 'request is: ',request,' Connected by', addr
```
```
# Client side
import socket

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect(('172.20.202.155', 8000))

s.sendall('can you hear me?') # send message
reply   = s.recv(1024)        # receive message
s.close()                     # close connection

print 'reply is: ',reply
```

> HTTP协议利用请求-回应(request-response)的方式来使用TCP socket。客户端向服务器发一段文本作为request，服务器端在接收到request之后，向客户端发送一段文本作为response。在完成了这样一次request-response交易之后，TCP socket被废弃。下次的request将建立新的socket。request和response本质上说是两个文本，只是HTTP协议对这两个文本都有一定的格式要求。

> socket包是比较底层的包。Python标准库中还有高层的包，比如SocketServer，SimpleHTTPServer，CGIHTTPServer，cgi。这些都包都是在帮助我们更容易的使用socket。

### HTTP服务器端

```python
import socket

# Prepare HTTP response
text_content = '''HTTP/1.x 200 OK  
Content-Type: text/html

<head>
<title>WOW</title>
</head>
<html>
<p>Wow, Python Server</p>
<IMG src="test.jpg"/>
</html>
'''

# Read picture, put into HTTP format
f = open('test.jpg','rb')
pic_content = '''
HTTP/1.x 200 OK  
Content-Type: image/jpg

'''python
pic_content = pic_content + f.read()
f.close()

################ read code from here ################

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(('', 8000)) # HOST PORT

# infinite loop, server forever
while True:
    s.listen(3)
    conn, addr = s.accept()
    request    = conn.recv(1024)
    method     = request.split(' ')[0] 
    url        = request.split(' ')[1]

    if method == 'GET':  
        if url == '/test.jpg':  content = pic_content
        else:                   content = text_content
        conn.sendall(content)
        
    if method == 'POST': #从POST请求中提取数据 再显示在屏幕上
        form = request.split('\r\n')
        idx = form.index('')   # Find the empty line
        entry = form[idx:]     # Main content of the request

        value = entry[-1].split('=')[-1]
        conn.sendall(text_content + '\n <p>' + value + '</p>')
        
    conn.close()
```

HTTP Request

```
GET /test.jpg HTTP/1.x #请求方法(GET PUT POST DELETE HEAD) + URL + HTTP版本
Host: **.**.**.**:8000
User-Agent: Mozilla/5.0 ....
Accept: text/*
Accept-Language: ...
Accept-Encoding: gzip, deflate
Connection: keep-alive
...
```

HTTP Response

```xml
HTTP/1.x 200 OK # start line: HTTP版本+status code(200 HTTP协议规定的)+status code(OK 是供人来阅读的)
Content-Type: text/html # head (它和body之间有一个空行)

<head>                  # body
<title>WOW</title>
</head>
<html>
<p>Wow, Python Server</p>
<IMG src="test.jpg"/>
</html>
```

### **SocketServer**

```python
import SocketServer

# This class defines response to each request
class MyTCPHandler(SocketServer.BaseRequestHandler):

    def handle(self):
        # self.request is the TCP socket connected to the client
        request = self.request.recv(1024)

		# self.address来引用socket的客户端地址
        print 'Connected by',self.client_address[0]
        print 'Request is', request

        method    =  request.split(' ')[0]
        url       =  request.split(' ')[1]
        if method == 'GET':  # 同上
        if method == 'POST': # 同上

server = SocketServer.TCPServer(('', 8000), MyTCPHandler) # Create the server
server.serve_forever() # Start the server, and work forever

# 建立了一个TCPServer对象，即一个使用TCP socket的服务器。在建立TCPServe的同时，设置该服务器的IP地址和端口。
# 使用server_forever()方法来让服务器不断工作(就像原始程序中的while循环一样)。
# 我们传递给TCPServer一个MyTCPHandler类。这个类通过改写handler()方法定义了如何操作socket。
# 在handler()中，通过self.request来查询通过socket进入服务器的请求。

# 这里的通信基于TCP协议, 而不是HTTP协议,因此我们必须手动的解析HTTP协议.
```

### **SimpleHTTPServer**  
> SimpleHTTPServer可以用于处理GET方法和HEAD方法的请求。它读取request中的URL地址，找到对应的静态文件，分析文件类型，用HTTP协议将文件发送给客户

```
import SocketServer
import SimpleHTTPServer

# Create the server, SimpleHTTPRequestHander is pre-defined handler
server = SocketServer.TCPServer(('', 8000), SimpleHTTPServer.SimpleHTTPRequestHandler)
server.serve_forever()

# 这里的程序不能处理POST请求 
# 如果URL指向一文件夹而没有指定文件时，SimpleHTTPServer会读取该文件夹下的index.html文件
```
### **CGIHTTPServer**  
> CGI (Common Gateway Interface)是服务器和应用脚本之间的一套接口标准。它的功能是让服务器程序运行脚本程序，将程序的输出作为response发送给客户。总体的效果，是允许服务器动态的生成回复内容，而不必局限于静态文件。
> 
> CGIHTTPServer包中的CGIHTTPRequestHandler类继承自SimpleHTTPRequestHandler类，所以可以用来提供静态文件的服务。此外，CGIHTTPRequestHandler类还可以用来运行CGI脚本。

```python
import BaseHTTPServer
import CGIHTTPServer

# 需要使用BaseHTTPServer包中的HTTPServer类来构建服务器
# Create the server, CGIHTTPRequestHandler is pre-defined handler
server = BaseHTTPServer.HTTPServer(('', 8000), CGIHTTPServer.CGIHTTPRequestHandler)
server.serve_forever()

# CGIHTTPRequestHandler默认当前目录下的cgi-bin和ht-bin文件夹中的文件为CGI脚本，而存放于其他地方的文件被认为是静态文件。
# 我们需要修改一下html，将其中form元素指向的action改为cgi-bin/post.py
# 我们再创建一个cgi-bin的文件夹，并放入如下post.py文件(post.py需要有执行权限)，也就是我们的CGI脚本
#!/usr/bin/env python
import cgi
form = cgi.FieldStorage()

# Output to stdout, CGIHttpServer will take this as response to the client
print "Content-Type: text/html"     # HTML is following
print                               # blank line, end of headers
print "<p>Hello world!</p>"         # Start of content
print "<p>" +  repr(form['firstname']) + "</p>"

# cgi包用于提取请求中包含的表格信息。脚本只负责将所有的结果输出到标准输出(使用print)。
# CGIHTTPRequestHandler会收集这些输出，封装成HTTP回复，传送给客户端。
# 更进一步，可以让CGI脚本执行数据库操作，比如将接收到的数据放入到数据库中，以及更丰富的程序操作
# CGIHTTPRequestHandler继承自SimpleHTTPRequestHandler，所以也可以处理GET方法和HEAD方法的请求。
# 此时，如果URL指向CGI脚本时，服务器将脚本的运行结果传送到客户端；当此时URL指向静态文件时，服务器将文件的内容传送到客户端。
```

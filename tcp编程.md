##TCP编程
[TOC]
###1.socket
>Socket是网络编程的一个抽象概念。通常我们用一个socket表示打开了一个网络连接，而打开一个socket需要知道目标计算机的IP地址和端口号，再指定协议类型即可。
>[Socket语法的具体介绍](./socket常用语法.md)

###2.客户端
>	大多数连接都是可靠的TCP连接。创建TCP连接时，主动发起连接的叫客户端，被动响应连接的叫服务器。

>	举个例子，当我们在浏览器中访问新浪时，我们自己的计算机就是客户端，浏览器会主动向新浪的服务器发起连接。如果一切顺利，新浪的服务器接受了我们的连接，一个TCP连接就建立起来的，后面的通信就是发送网页内容了。
>#####创建socket
>所以，我们要创建一个基于TCP连接的Socket，可以这样做：
>>```python  
#Socket client example in python
#for socket
import socket
#create an AF_INET (IPV4),STREAM socket(TCP)
#地址簇  AF_INET ----> IPV4
#类型     SOCK_STREAM-----> 使用tcp控制协议
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
print 'Socket Created'
>>```
>函数 socket.socket 创建了一个 Socket，并返回 Socket 的描述符可用于其他 Socket 相关的函数。

>上述代码使用了下面两个属性来创建 Socket：

>地址簇 : AF_INET (IPv4)
>类型: SOCK_STREAM (使用 TCP 传输控制协议)
>#####错误处理
>如果socket函数失败了，python将会抛出一个名为socket.error的异常，这个异常必须给予处理
>>```python
#!/usr/bin/env python
#coding=utf-8


>>#Socket client example in python

>>#for socket
import socket
#for exit
import sys
try:
    #create an AF_INET (IPV4),STREAM socket(TCP)
        #地址簇  AF_INET ----> IPV4
        #类型     SOCK_STREAM-----> 使用tcp控制协议
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
except socket.error,msg:
    print 'Faild to create socket, Error code: ' + str(msg[0]) + ',Error message:' + msg[1]
    sys.exit(); #返回 systemexit异常，退出整个程序，
print 'Socket Created'
>>```
>好了，假设你已经成功创建了 Socket，下一步该做什么呢？接下来我们将使用这个 Socket 来连接到服务器。

>注意

>与 SOCK_STREAM 相对应的其他类型是 SOCK_DGRAM 用于 UDP 通讯协议，UDP 通讯是非连接 Socket
>#####连接服务器
>连接服务器需要知道服务器的地址和端口号，这里测试www.sina.com.cn
>+ 获取远程主机的ip地址
>>连接到远程主机之前，我们需要知道它的ip地址，在Python中，获取ip地址是很简单的。
>>```python
#!/usr/bin/env python
#coding=utf-8
#Socket client example in python
#for socket
import socket
#for exit
import sys
try:
    #create an AF_INET (IPV4),STREAM socket(TCP)
        #地址簇  AF_INET ----> IPV4
        #类型     SOCK_STREAM-----> 使用tcp控制协议
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
except socket.error,msg:
    print 'Faild to create socket, Error code: ' + str(msg[0]) + ',Error message:' + msg[1]
    sys.exit(); #返回 systemexit异常，退出整个程序，
print 'Socket Created'
#连接到服务器
#-----------------------------------------------------
#获取服务器的ip地址
host = 'www.sina.com.cn'
port = 80
try:
    remote_ip = socket.gethostbyname(host)
    #socket.gethostbyname  可以通过主机名称解析为ip地址
except socket.gaierror:
    #could not resolve
    print 'Hostname could not be resolved .Exiting'
    sys.exit()
#指定服务器的ip 和端口
print 'Ip address of ' + host + 'is' + remote_ip
print 'Socket Connected to ' + host + 'on ip ' + remote_ip
>>```
>+ 连接服务器
>>使用s.connet()函数连接到服务器
>>```python
#!/usr/bin/env python
#coding=utf-8
#Socket client example in python
#for socket
import socket
#for exit
import sys
try:
    #create an AF_INET (IPV4),STREAM socket(TCP)
        #地址簇  AF_INET ----> IPV4
        #类型     SOCK_STREAM-----> 使用tcp控制协议
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
except socket.error,msg:
    print 'Faild to create socket, Error code: ' + str(msg[0]) + ',Error message:' + msg[1]
    sys.exit(); #返回 systemexit异常，退出整个程序，
print 'Socket Created'
#连接到服务器
#-----------------------------------------------------
#获取服务器的ip地址
host = 'www.sina.com.cn'
port = 80
try:
    remote_ip = socket.gethostbyname(host)
    #socket.gethostbyname  可以通过主机名称解析为ip地址
except socket.gaierror:
    #could not resolve
    print 'Hostname could not be resolved .Exiting'
    sys.exit()
#指定服务器的ip 和端口
print 'Ip address of ' + host + 'is' + remote_ip
#connect to remote server
s.connect((remote_ip,port))
print 'Socket Connected to ' + host + 'on ip ' + remote_ip
>>```
>+ 现在运行程序
>>```bash
>> $ python client.py
>>Socket Created
>>Ip address of www.oschina.net is 61.145.122.155
>>Socket Connected to www.oschina.net on ip 61.145.122.155
>>```

>>这段程序创建了一个 Socket 并进行连接，试试使用其他一些不存在的端口（如81）会是怎样？这个逻辑相当于构建了一个端口扫描器。
>>已经连接上了，接下来就是往服务器上发送数据。
>>
>>免费提示

>>使用 SOCK_STREAM/TCP 套接字才有“连接”的概念。连接意味着可靠的数据流通讯机制，可以同时有多个数据流。可以想象成一个数据互不干扰的管道。另外一个重要的提示是：数据包的发送和接收是有顺序的。

>>其他一些 Socket 如 UDP、ICMP 和 ARP 没有“连接”的概念，它们是无连接通讯，意味着你可从任何人或者给任何人发送和接收数据包。

>#####发送数据
>send(),sendall()函数可以用于简单的发送数据，我们可以向服务器发送数据
>>``` python
#!/usr/bin/env python
#coding=utf-8
#Socket client example in python
#for socket
import socket
#for exit
import sys
#处理异常
try:
    #create an AF_INET (IPV4),STREAM socket(TCP)
        #地址簇  AF_INET ----> IPV4
        #类型     SOCK_STREAM-----> 使用tcp控制协议
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
except socket.error,msg:
    print 'Faild to create socket, Error code: ' + str(msg[0]) + ',Error message:' + msg[1]
    sys.exit(); #返回 systemexit异常，退出整个程序，
print 'Socket Created'
#连接到服务器
#-----------------------------------------------------
#获取服务器的ip地址
host = 'www.sina.com.cn'
port = 80
try:
    remote_ip = socket.gethostbyname(host)
    #socket.gethostbyname  可以通过主机名称解析为ip地址
except socket.gaierror:
    #could not resolve
    print 'Hostname could not be resolved .Exiting'
    sys.exit()
#指定服务器的ip 和端口
print 'Ip address of ' + host + 'is' + remote_ip
#connect to remote server
s.connect((remote_ip,port))
print 'Socket Connected to ' + host + 'on ip ' + remote_ip
#-------------------------------------------------------------
#向服务器发送数据
#Send some data to remote server
#GET / HTTP/1.1\r\n\r\n 这是一个http协议的命令，用来获取网站首页的内容
message = 'GET / HTTP/1.1\r\n\r\n'
try:
    #Set the whole string
    s.sendall(message)
except socket.error:
    #Send failed
    print 'Send failed'
    sys.exit()
print 'Message send successefully'
>>```
>>GET / HTTP/1.1\r\n\r\n是一个http协议的内容，用来获取网站首页的内容
>
>#####接收数据
>recv()函数可以用来从socket接收数据
>>``` python
#!/usr/bin/env python
#coding=utf-8
#Socket client example in python
#for socket
import socket
#for exit
import sys
#处理异常
try:
    #create an AF_INET (IPV4),STREAM socket(TCP)
        #地址簇  AF_INET ----> IPV4
        #类型     SOCK_STREAM-----> 使用tcp控制协议
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
except socket.error,msg:
    print 'Faild to create socket, Error code: ' + str(msg[0]) + ',Error message:' + msg[1]
    sys.exit(); #返回 systemexit异常，退出整个程序，
print 'Socket Created'
#连接到服务器
#-----------------------------------------------------
#获取服务器的ip地址
host = 'www.sina.com.cn'
port = 80
try:
    remote_ip = socket.gethostbyname(host)
    #socket.gethostbyname  可以通过主机名称解析为ip地址
except socket.gaierror:
    #could not resolve
    print 'Hostname could not be resolved .Exiting'
    sys.exit()
#指定服务器的ip 和端口
print 'Ip address of ' + host + 'is' + remote_ip
#connect to remote server
s.connect((remote_ip,port))
print 'Socket Connected to ' + host + 'on ip ' + remote_ip
#-------------------------------------------------------------
#向服务器发送数据
#Send some data to remote server
#GET / HTTP/1.1\r\n\r\n 这是一个http协议的命令，用来获取网站首页的内容
message = 'GET / HTTP/1.1\r\n\r\n'
try:
    #Set the whole string
    s.sendall(message)
except socket.error:
    #Send failed
    print 'Send failed'
    sys.exit()
print 'Message send successefully'
#Now recive data
reply = s.recv(4096)
>>```
>>接收数据完了，就可以关闭socket
>
>#####关闭socket
>>```python
s.close()
>>```
>
>#####回顾
>>从上面的实例中我们学会了基本的socket客户端连接方法
>>+ 创建socket
>>+ 连接远程服务器
>>+ 发送数据
>>+ 接收回应
>>以上全部来自与客户段的连接，在我们访问www.sina.com.cn中，我们的浏览器就是客户端，sina是服务起端，服务器也使用socket接收进入的连接并提供数据。
>
>#####一个完整的实例
>得到一个完整的sina首页，生成的sina.html可以在浏览器中打开
>>``` python
#!/usr/bin/env python
#coding=utf-8
#Socket client example in python
#for socket
import socket
#for exit
import sys
#处理异常
try:
    #create an AF_INET (IPV4),STREAM socket(TCP)
        #地址簇  AF_INET ----> IPV4
        #类型     SOCK_STREAM-----> 使用tcp控制协议
    s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
except socket.error,msg:
    print 'Faild to create socket, Error code: ' + str(msg[0]) + ',Error message:' + msg[1]
    sys.exit(); #返回 systemexit异常，退出整个程序，
print 'Socket Created'
#连接到服务器
#-----------------------------------------------------
#获取服务器的ip地址
host = 'www.sina.com.cn'
port = 80
try:
    remote_ip = socket.gethostbyname(host)
    #socket.gethostbyname  可以通过主机名称解析为ip地址
except socket.gaierror:
    #could not resolve
    print 'Hostname could not be resolved .Exiting'
    sys.exit()
#指定服务器的ip 和端口
print 'Ip address of ' + host + 'is' + remote_ip
#connect to remote server
s.connect((remote_ip,port))
print 'Socket Connected to ' + host + 'on ip ' + remote_ip
#-------------------------------------------------------------
#向服务器发送数据
#Send some data to remote server
#GET / HTTP/1.1\r\n\r\n 这是一个http协议的命令，用来获取网站首页的内容
message = 'GET / HTTP/1.1\r\nHost: www.sina.com.cn\r\nConnection: close\r\n\r\n'
try:
    #Set the whole string
    s.sendall(message)
except socket.error:
    #Send failed
    print 'Send failed'
    sys.exit()
print 'Message send successefully'
#接受服务器返回的数据
buffer = []
while True:
    #recv(max)方法，表示一次最多接受指定的字节数
    d = s.recv(1024)
    if d:
        buffer.append(d)
    else:
        break
#拼接了字符串data
data = ''.join(buffer)
#关闭连接
s.close()
#print data
#print buffer
header,html = data.split('\r\n\r\n',1)
print header
with open('sina.html','wb') as f:
    f.write(html)
>>```

###3.服务器
>服务器编程包括一下几步
>+ 1.打开socket
>+ 2.绑定到一个地址和端口
>+ 3.监听进来的连接
>+ 4.接受连接
>+ 5.读写数据
>
>#####绑定socket
>bind函数用于讲socket绑定到一个特定的地址和端口，它需要一个类似connect函数所需要的sockaddress的结构体
>示例代码：
>>```python
#!/usr/bin/evn python
#coding=utf-8
import socket
import sys
HOST = '127.0.0.1'   #symbolic name meaning all available interfaces
PORT = 8888  #Arbirrary nono-privileged prort
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
print 'Socket created '
#创建绑定，
#bind函数用于将socket绑定到一个特定的地址和端口，它需要一个类似与connect函数所需的sockaddr_in结构体
try:
    s.bind((HOST,PORT))
except socket.error,msg:
    print 'Bind faild ,Error Code: ' + str(msg[0]) + ' Message ' + msg[1]
    sys.exit()
print 'Socket bind complete'
>>```
>
>绑定完成后，就需要让socket开始侦听连接
>#####连接监听
>绑定后，就可以就行监听，使用socket的listen函数实现监听
>>```python
#!/usr/bin/evn python
#coding=utf-8
import socket
import sys
HOST = '127.0.0.1'   #symbolic name meaning all available interfaces
PORT = 8888  #Arbirrary nono-privileged prort
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
print 'Socket created '
#创建绑定，
#bind函数用于将socket绑定到一个特定的地址和端口，它需要一个类似与connect函数所需的sockaddr_in结构体
try:
    s.bind((HOST,PORT))
except socket.error,msg:
    print 'Bind faild ,Error Code: ' + str(msg[0]) + ' Message ' + msg[1]
    sys.exit()
print 'Socket bind complete'
#开始监听链接
#listen函数所需的参数成为 backlog 用来控制程序忙时可保持等待状态的连接数。这里我们传递的是10
#意味着如果已经有10个链接在等待处理，那么第11个被拒绝
s.listen(10)
print  'Socket now listening'
>>```
>
>#####接受连接
>接受来自客户端的连接，使用accept()函数得到客户端的socket数据和客户端的地址端口
>>```python
#!/usr/bin/evn python
#coding=utf-8
import socket
import sys
HOST = '127.0.0.1'   #symbolic name meaning all available interfaces
PORT = 8888  #Arbirrary nono-privileged prort
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
print 'Socket created '
#创建绑定，
#bind函数用于将socket绑定到一个特定的地址和端口，它需要一个类似与connect函数所需的sockaddr_in结构体
try:
    s.bind((HOST,PORT))
except socket.error,msg:
    print 'Bind faild ,Error Code: ' + str(msg[0]) + ' Message ' + msg[1]
    sys.exit()
print 'Socket bind complete'
#开始监听链接
#listen函数所需的参数成为 backlog 用来控制程序忙时可保持等待状态的连接数。这里我们传递的是10
#意味着如果已经有10个链接在等待处理，那么第11个被拒绝
s.listen(10)
print  'Socket now listening'
#wait to accept a connetction-blocking call
conn,addr = s.accept() 
#display client information
print 'Connected with' + addr[0] + ':' + str([1])
>>```
>
>使用telnet来测试这个服务器的连接
>>服务端
>>```bash
$ python server.py
Socket created
Socket bind complete
Socket now listening
>>```
>>客户端
>>```bash
$ telnet localhost 8888
It will immediately show
$ telnet localhost 8888
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Connection closed by foreign host.
>>```
>>而服务器端的窗口显示
>>```bash
$ python server.py
Socket created
Socket bind complete
Socket now listening
Connected with 127.0.0.1:59954
>>```
>>我们可以看到客户端已经成功连接到服务器
>但是上面的例子中我们接收到连接后就立即关闭了，这样的程序没有实际的价值，连接建立后都会有大量的事情需要处理，因此我们来给客户端做出回应
>
>#####向客户端发送数据
>sendall 函数可通过 Socket 给客户端发送数据：
>>```python
#!/usr/bin/evn python
#coding=utf-8
import socket
import sys
HOST = '127.0.0.1'   #symbolic name meaning all available interfaces
PORT = 8888  #Arbirrary nono-privileged prort
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
print 'Socket created '
#创建绑定，
#bind函数用于将socket绑定到一个特定的地址和端口，它需要一个类似与connect函数所需的sockaddr_in结构体
try:
    s.bind((HOST,PORT))
except socket.error,msg:
    print 'Bind faild ,Error Code: ' + str(msg[0]) + ' Message ' + msg[1]
    sys.exit()
print 'Socket bind complete'
#开始监听链接
#listen函数所需的参数成为 backlog 用来控制程序忙时可保持等待状态的连接数。这里我们传递的是10
#意味着如果已经有10个链接在等待处理，那么第11个被拒绝
s.listen(10)
print  'Socket now listening'
#wait to accept a connetction-blocking call
conn,addr = s.accept() 
#display client information
print 'Connected with' + addr[0] + ':' + str([1])
#now keep talking with the client
data = conn.resv(1024)
conn.sendall(data)
#关闭打开的新生成的套接字conn，关闭最开始生成的套接字s
conn.close()
s.close()
>>```
>>继续运行上述代码后，然后用客户端进行连接
>>```bash
$ telnet localhost 8888
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
happy
happy
Connection closed by foreign host.
>>```
>>可以看到客户端接收了来自客户端的回应内容
>
>服务器端应该一直处于运行状态，否则就不能称为'服务'了，因此让服务端一直运行，最简单是把accept放入一个循环中
>#####服务器一直运行
>>accep()放入循环中
>>```python
#接受链接
while 1:
    #wait to accept a connection - blocking call
    conn, addr = s.accept()
    #display client information
    print 'connected with' + addr[0] + ':' + str(addr[1])
    #print conn
    #print addr
    #now keep taling with the client
    data = conn.recv(1024)
    reply = 'OK...' + data
    if not data:
        break
    #sendall 可以通过socket给客户端发送数据
    conn.sendall(reply)
conn.close()
s.close()
>>```
>
>#####多线程处理多个连接
>>服务器端配置
>>```python
#!/usr/bin/env
#coding=utf-8
import  socket
import  threading
import  time
#创建一个基于ipv4 和tcp 面向流的socket
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
#绑定监听的地址和端口
s.bind(('127.0.0.1',9999))
#调用listen方法进行监听，传入的参数指定等待连接的最大数量
s.listen(5)
print 'Waiting for connection'
def tcplink(sock,addr):
    print 'Accept new connection from %s:%s...' % addr
    sock.send('Welcome')
    while True:
        # recv(max)方法，表示一次最多接受指定的字节数
        data = sock.recv(1024)
        time.spleep(1)
        if data == 'exit' or not data:
            break
        sock.send('Hello,%s!' % data)
    sock.close()
    print 'Conection from %s:%s closed' % addr
#服务器程序通过一个永久玄幻接受来自客户端的连接
#accept()会等待返回一个客户端的连接
while True:
    #接受一个新连接
    sock, addr = s.accept()
    #创建一个新线程来处理tcp连接
    t = threading.Thread(target = tcplink,args=(sock,addr))
    t.start()
>>```
>>客户端连接code
>>```python
#!/usr/bin/env python
#coding=utf-8
import socket
#创建一个IPV4，和TCP 面向流的连接
s = socket.socket(socket.AF_INET,socket.SOCK_STREAM)
#创建tcp连接
    #连接参数是一个tuple 包含服务器地址和端口号
s.connect(('127.0.0.1',9999))
#s.recv(max)方法，表示一次做多接受指定的字节数
#打印出第一次接收到的服务器的1024个字节
print s.recv(1024)
for data in ['ning','yan','ke']:
    #s.send向服务器发送请求要求，要求返回相应的内容
    s.send(data)
    print s.recv(1024)
s.send('exit')
s.close()
>>```

###4.总结
>以上，需要的是进行多联系，结合思维导图进行理解


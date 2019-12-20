# HTTP协议概述

## 协议

约定规则



### 网络协议

数据在网络上传输规则 http，pop3，pop，imap，ftp，流媒体协议



### HTTP协议

超文本传输协议(htyper text transform protocal)：如何在互联网上传输超文本

HTML(htyper text markup language)：超本文标记语言 



### HTTP协议格式

由于我们平时访问互联网上的网页都是请求之后才响应，所以HTTP协议是

基于请求-响应模型，协议分为请求部分和响应部分



### HTTP协议分为请求部分和响应部分

- 请求部分格式
  - 请求行 请求头 请求体
- 响应部分格式
  - 响应行 响应头 响应体



## 请求部分

请求行/请求头/请求体

```txt
POST	/xxx/xxx/xxx.png	http/1.1
K1:V1
K2:V2
K3:V3
K4:V4

Username=tom&password=1234
```



### 请求行

请求方式 本次请求路径 协议/版本

```txt
POST	/index.html	http/1.1(换行)
```

请求方式(GET/POST，一共设计7个方法)

本次请求哪些内容

本次请求采用的协议以及协议版本



### 请求头

作用：

- 告诉服务器对客户端描述
- 对本次请求描述

格式：

K1:V1(换行)
K2:V2(换行)
K3:V3(换行)
K4:V4(换行)



常见的请求头有：

Host：本次请求的主机路径

User-Agent：告诉服务端本次请求客户端所在的平台以及本次请求采用的浏览器

Accept：用于指定客户端接受哪些类型的信息

Accept-Language：告诉服务端，浏览器可以识别的语言种类

Accept-Encoding：告诉服务端，浏览器可以哪些类型压缩格式数据



### 请求体

约定用户的表单数据向服务端传递格式



## 响应体

响应部分格式

```txt
HTTP/1.1	200		OK(换行)
K1:V1(换行)
K2:V2(换行)
K3:V3(换行)
空行
<html>
	xxxx....
</html>
```

### 响应行

```txt
HTTP/1.1	200		OK(换行)
```

本次响应采用的协议

状态码

状态描述



#### 状态码

1xx：指示信息--表示请求已接收，继续处理

2xx：成功--表示请求已被成功接收、理解、接受

3xx：重定向--要完成请求必须进行更进一步的操作

4xx：客户端错误--请求有语法错误或请求无法实现

5xx：服务器错误--服务器未能实现合法的请求



### 响应头

```txt
Date: Fri, 21 Apr 2019 02:04:45 GMT
Content-Type: text/html
Connection: keep-alive
Content-Encoding: gzip
Content-length
Server
```

Date:响应时间

content-Type:本次响应内容类型

content-Encoding:本次内容采用的压缩格式

content-Length:本次内容长度



### 响应体

服务端响应到客户端部分，可能是HTML页面，JS文件，CSS文件，图片




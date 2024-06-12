	hyper text transfer protocol 超文本传输协议

# 状态
![[Pasted image 20240607112451.png|600]]

## 2xx
	成功处理需求

1. 200 OK                    ：响应头有body数据
2. 200 No Content      ：没有body数据
3. 206 Partial Content ：HTTP 分块下载或断点续传，表示响应返回的 body 数据并不是资源的全部，而是其中的一部分，也是服务器处理成功的状态。

## 3xx
	客户端请求资源变动，需要客户端使用新url重新发送请求，重定向

1. 301 Moved Permanently：永久重定向
2. 302 Found                      ：临时重定向
3. 304 Not Modified           :  不具有跳转的含义，表示资源未修改，重定向已存在的缓冲文件，也称缓存重定向，也就是告诉客户端可以继续使用缓存资源，用于缓存控制。

## 4xx
	客户端发送报文错误

1. 400 Bad Request              ：表示客户端请求的报文有错误，但只是个笼统的错误。
2. 403 Forbidden                 ：表示服务器禁止访问资源，并不是客户端的请求出错。
3. 404 Not Found                ：表示请求的资源在服务器上不存在或未找到，所以无法提供给客户端。

## 5xx
	服务器内部错误，客户端请求报文正确

1. 500 Internal Server Error     ：与 400 类似，是个笼统通用的错误码
2. 501 Not Implemented         ：表示客户端请求的功能还不支持
3. 502 Bad Gateway                ：通常是服务器作为网关或代理时返回的错误码，表示服务器自身工作正常，访问后端服务器发生了错误。
4. 503 Service Unavailable      ：表示服务器当前很忙，暂时无法响应客户端

# 字段

1. Host
2. Content-Length
3. Connection
4. Content-type
5. Content-Encoding

## GET和POST
	GET 从服务器中获取指定的资源
	POST 根据请求负荷（报文body）对指定的资源做出处理
## 缓存
	都需要配合强制缓存中 Cache-Control 字段来使用，只有在未能命中强制缓存的时候，才能发起带有协商缓存字段的请求。
	
![[Pasted image 20240607145546.png|600]]
### 强制缓存


Cache-Control， 是一个相对时间；
Expires，是一个绝对时间；

Cache-control 选项更多一些，设置更加精细，所以建议使用 Cache-Control 来实现强缓存。具体的实现流程如下：

当浏览器第一次请求访问服务器资源时，服务器会在返回这个资源的同时，在 Response 头部加上 Cache-Control，Cache-Control 中设置了过期时间大小；
浏览器再次请求访问服务器中的该资源时，会先通过请求资源的时间与 Cache-Control 中设置的过期时间大小，来计算出该资源是否过期，如果没有，则使用该缓存，否则重新请求服务器；
服务器再次收到请求后，会再次更新 Response 头部的 Cache-Control


### 协商缓存
	状态码304
	服务器通知客户端可以使用本地存储的资源

![[Pasted image 20240607145158.png|600]]
#### 工作过程

1. 请求头部中的 **If-Modified-Since** 字段与响应头部中的 **Last-Modified** 字段实现：响应头部中的 Last-Modified：标示这个响应资源的最后修改时间；请求头部中的 If-Modified-Since：当资源过期了，发现响应头中具有 Last-Modified 声明，则再次发起请求的时候带上 Last-Modified 的时间，服务器收到请求后发现有 If-Modified-Since 则与被请求资源的最后修改时间进行对比（Last-Modified），如果最后修改时间较新（大），说明资源又被改过，则返回最新资源，HTTP 200 OK；如果最后修改时间较旧（小），说明资源无新修改，响应 HTTP 304 走缓存。
2. 请求头部中的 If-None-Match 字段与响应头部中的 ETag 字段：响应头部中 Etag：唯一标识响应资源；请求头部中的 If-None-Match：当资源过期时，浏览器发现响应头里有 Etag，则再次向服务器发起请求时，会将请求头 If-None-Match 值设置为 Etag 的值。服务器收到请求后进行比对，如果资源没有变化返回 304，如果资源变化了返回 200。

 Etag 的优先级更高？
1. 在没有修改文件内容情况下文件的最后修改时间可能也会改变，这会导致客户端认为这文件被改动了，从而重新请求；
2. 可能有些文件是在秒级以内修改的，If-Modified-Since 能检查到的粒度是秒级的，使用 Etag就能够保证这种需求下客户端在 1 秒内能刷新多次；
3. 有些服务器不能精确获取文件的最后修改时间。
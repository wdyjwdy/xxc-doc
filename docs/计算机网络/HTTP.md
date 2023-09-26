## http 1.0
![http-1-0](/img/doc/http-1-0.png)
## http 1.1
长连接
![http-1-1](/img/doc/http-1-1.png)
## http 2.0
多路复用
![http-2-0](/img/doc/http-2-0.png)
## 请求
- GET：查询
```txt
HEAD: GET URL?key=value HTTP/1.1
```
- POST：新增
```txt
HEAD: POST URL HTTP/1.1
BODY: key=value
```
- PUT：修改
- DELETE：删除
## 状态码
- `200 OK`：客户端请求成功
- `301 Moved Permanently`：请求永久重定向（缓存新域名）
- `302 Moved Temporarily`：请求临时重定向（不缓存新域名）
- `304 Not Modified`：文件未修改，可以直接使用缓存的文件
- `400 Bad Request`：请求有语法错误
- `401 Unauthorized`：请求未经授权
- `403 Forbidden`：服务器收到请求，但是拒绝提供服务
- `404 Not Found`：资源不存在
- `500 Internal Server Error`：服务器错误
- `503 Service Unavailable`：服务器不可用
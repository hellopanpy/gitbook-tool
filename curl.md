###curl

一个可以对url操作的命令行工具,可以完成浏览器所有功能

###使用

#####基本使用

>`-v`参数,可以显示一次http通信的整个过程，包括端口连接和http request头信息

>`-L`参数，curl就会跳转到新的网址

>`-o`参数，下载文件


<pre><code>
curl -vL http://www.baidu.com

* About to connect() to www.baidu.com port 80 (#0)
* Trying 14.215.177.37... connected
* Connected to www.baidu.com (14.215.177.37) port 80 (#0)
> GET / HTTP/1.1
> User-Agent: curl/7.19.7 (x86_64-redhat-linux-gnu) libcurl/7.19.7 NSS/3.15.3 zlib/1.2.3 libidn/1.18 libssh2/1.4.2
> Host: www.baidu.com
> Accept: */*
>
< HTTP/1.1 200 OK
< Server: bfe/1.0.8.18
< Date: Tue, 15 Nov 2016 03:21:44 GMT
< Content-Type: text/html
< Content-Length: 2381
< Last-Modified: Mon, 25 Jul 2016 11:11:55 GMT
< Connection: Keep-Alive
< ETag: "5795f3fb-94d"
< Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
< Pragma: no-cache
< Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/
< Accept-Ranges: bytes
<
<!DOCTYPE html>
......
</code></pre>


####http header

>`-I`参数，只显示http response的头信息

>`-H/--header`参数，增加http header信息

>`--user-agent`参数，附加客户端信息

>`--referer`参数，域跳转

>`--trace`参数，追踪

请求多节点的指定主机

`curl -H 'Host:www.baidu.com' http://220.181.111.188`

附加客户端信息

`curl -v -H "User-Agent:string" http://www.baidu.com`

`curl -v --user-agent "User-Agent:string" http://www.baidu.com`

`curl --trace output.txt http://www.baidu.com`


####http form

>GET

直接带参数即可

`curl http://httpbin.org/get?key1=value1`

>POST

<pre><code>
curl -X POST --data-urlencode '{"some": "data"}' -H 'Content-Type: application/json' http://httpbin.org/post
</code></pre>

####http 验证

`curl --user user:passwd http://example.com`

`curl  http://user:passwd@example.com`

####参考

[curl doc](https://www.gitbook.com/book/bagder/everything-curl/details)





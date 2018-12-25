### build some server
```javascript
#lsof -i:port                                 #查看port被哪些进程占用。
#docker run -d -p 10080:80 --rm httpd         #apache2  ->It works!
#docker run -d -p 10080:80 --rm nginx         #nginx    ->Welcome to nginx!
#docker run -d -p 8080:8080 -p 50000:50000 --rm jenkins    #jenkins
#docker run -d -e MYSQL_ROOT_PASSWORD=123 --rm mysql       #mysql
#docker run -d --rm mongon                                 #mongodb
#docker run -d -p 5000:5000 --restart=always --name registry resistry
$curl -i 192.168.1.6:5000/v2/
HTTP/1.1 200 OK
#docker tag busybox 192.168.1.6:5000/busybox:test
#docker push 192.168.1.6:5000/busybox:test 
The push refers to repository [192.168.1.6:5000/busybox]
Get https://192.168.1.6:5000/v2/:http: server gave HTTP response to HTTPS client
#ubuntu环境下解决这类问题：
#vi /etc/default/docker
ADD_REGISTRY="--add-registry 192.168.1.6:5000"
DOCKER_OPTS="--insecure-registry 192.168.1.6:5000"
INSECURE_REGISTRY="--insecure-registry 192.168.1.6:5000"
#这样做了之后并没有作用。
#service docker restart     #通过service重启不起作用。
#/usr/bin/dockerd --insecure-registry 192.168.1.6:5000 & 
#这样重新启动docker守护进程之后，可以push成功，但是会显示很多警告信息。
#这种方式的私有registry被认为是不安全的，只能本地访问，其他主机无法访问。
#curl -X GET 192.168.1.6:5000/v2/
{}
#curl -X GET 192.168.1.6:5000/v2/busybox/tags/list
{"name":"busybox","tags":["test"]}
#curl -X GET 192.168.1.6:5000/v2/_catalog
{"repositories":["busybox"]}
#可以直接在网址中输入GET后面的内容，得到的结果是一样的。
```

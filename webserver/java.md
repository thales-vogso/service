# java


java服务配置，使用docker的tomcat


一般来说，把日志以及网站专门指向本地目录


```yml
mytomcat:
  restart: always
  image: tomcat
  container_name: mytomcat
  privileged: true
  ports:
    - 8001:8080
  volumes:
    - ./tomcat/webapps:/usr/local/tomcat/webapps/
    - ./tomcat/logs:/usr/local/tomcat/logs/
  environment:
    TZ: Asia/Shanghai
```


直接使用docker-compose就可以完成


```bash
docker-compose up -d
```


我们可以多个tomcat都在nginx中代理


```conf
server {
    listen 80;
    server_name  tomcat1.vogso.com;
    location / {  
      proxy_pass  127.0.0.1:8001;
      index index.html index.htm;  
    }
}
server {
    listen 80;
    server_name  tomcat2.vogso.com;
    location / {  
      proxy_pass  192.168.1.180:8001;
      index index.html index.htm;  
    }
}
```

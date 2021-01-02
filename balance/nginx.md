# nginx


nginx可以使用代理设置方式设置负载均衡


比如我们的《抓水母》游戏，有三个服务器，我们创建一个jelly的upstream


一个配置好的服务器作为主服务器，另外一个作为辅服务器，还有一个备份服务器，平时不运行，作为应急(这个服务器上做报警，一旦启用就要查看服务器了)


然后在常规的server里面配置刚才的jelly为代理


```conf
upstream jelly { # 配置服务器
  server proxy1.vogso.com:10101  weight=5; # 设置比较大的权重
  server proxy2.vogso.com:10102;
  server backup.vogso.com:10201   backup; # 备份服务器，别的服务器出问题时候使用此服务器
}

server { # 常规服务
    listen 80;
    server_name  www.vogso.com;
    location / {  
      proxy_pass  jelly; # 设置代理
      index index.html index.htm;  
    }
}
```


upstream中的server我们可以远程，可以本地，比如本地有tomcat的war或者是js做的server都可以，并且可以使用unix domain socket


更详细的参数可以看官网


[nginx官网](http://nginx.org/en/docs/stream/ngx_stream_upstream_module.html)


```conf
worker_processes auto;

error_log /var/log/nginx/error.log info;

events {
    worker_connections  1024;
}

stream {
    upstream backend {
        hash $remote_addr consistent;

        server backend1.example.com:12345 weight=5;
        server 127.0.0.1:12345            max_fails=3 fail_timeout=30s;
        server unix:/tmp/backend3;
    }

    upstream dns {
       server 192.168.0.1:53535;
       server dns.example.com:53;
    }

    server {
        listen 12345;
        proxy_connect_timeout 1s;
        proxy_timeout 3s;
        proxy_pass backend;
    }

    server {
        listen 127.0.0.1:53 udp reuseport;
        proxy_timeout 20s;
        proxy_pass dns;
    }

    server {
        listen [::1]:12345;
        proxy_pass unix:/tmp/stream.socket;
    }
}
```
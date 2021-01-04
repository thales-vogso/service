# PHP


虽然php的使用量在减少，但是为了兼容之前的内容还是需要记录


# nginx + fpm


nginx + fpm 的方式是php比较常见的方式


比如在win的电脑上，我指向一个本地目录(D:\docker\env\nginx\html)跟docker的服务器目录(/var/www/html/)绑定，创建fpm的docker，名字为myphp


```
docker run --name myphp -v D:\docker\env\nginx\html:/var/www/html/ -d php:7.4-fpm
```


然后创建nginx容器，并且服务器的40端口指向到本地的4001端口，把刚才的myphp设定一个host为php


```bash
docker run --name mynginx -v D:\docker\env\nginx\html:/usr/share/nginx/html -v D:\docker\env\nginx\logs:/etc/nginx/logs -v D:\docker\env\nginx\conf.d:/etc/nginx/conf.d -p 4001:80 -d --link myphp:php nginx
```


我们本地的conf目录(D:\docker\env\nginx\conf.d)创建一个default.conf


```conf
server {
    listen       80;
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm index.php;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        fastcgi_pass   php:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /var/www/html/$fastcgi_script_name;
        include        fastcgi_params;
    }
}
```


在网站目录(D:\docker\env\nginx\html)放置文件就可以用127.0.0.1:4001看我们的文件了


实际上，真正搭建需要的服务时候没那么难，只要执行基本的命令就行了


#  laravel


https://laravel.com/



我们从github获取资源


https://github.com/laravel/laravel.git


下载内容，根目录直接就有 docker-compose.yml


我们使用docker命令直接把需要的mysql redis nginx php都装好了


```bash
docker-compose up -d
```


# thinkphp


依然可以使用docker-compose创建


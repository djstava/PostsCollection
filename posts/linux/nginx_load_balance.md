软硬件环境


* ubuntu 16.04 desktop 32bit
* nginx 1.2.4

### 前言

在实际应用中，负载平衡是一种常见的技术，它用来优化利用资源、提高系统的吞吐量、减少访问等待时间并且有很好的容错性。在实际项目中刚好使用了nginx，本文介绍如何将nginx作为反向代理服务器来实现负载均衡，将流量分配到多个应用服务器上，提高整个系统的可用性、可靠性、扩展性和性能。

### 配置步骤

##### 准备工作

准备3台服务器，分别为

​	10.10.10.77 		装有nginx web server

​	10.10.10.188 		应用服务器1

​	10.10.10.107 		应用服务器2

nginx我用的是1.2.4版本，从源码编译的，如果嫌麻烦的话，用apt-get install安装也行，2台应用服务器也分别安装了nginx，web root下的index.html做了区别。

为了查看效果，我们简单访问10.10.10.77，看看通过负载均衡的设置，最终访问的应用服务器到底是哪一台？

##### 修改10.10.10.77服务器上的nginx.conf

```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    upstream localhost {
		server 10.10.10.188:8081 weight=1;
		server 10.10.10.107:80 weight=5;
    }

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
	    proxy_pass http://localhost;
            # root   html;
            # index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }

    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

    # HTTPS server
    #
    #server {
    #    listen       443;
    #    server_name  localhost;

    #    ssl                  on;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_timeout  5m;

    #    ssl_protocols  SSLv2 SSLv3 TLSv1;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers   on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

##### 测试

通过多次访问http://10.10.10.77可以看到，有的转向了10.10.10.188，有的转向了10.10.10.107，比例是1:5。

![nginx_cdn_1](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/nginx/nginx_cdn_01.png)

![nginx_cdn_2](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/nginx/nginx_cdn_02.png)

###  nginx的负载均衡策略

nginx 提供了轮询(Round Robin)、用户 IP 哈希(Client IP)和权重(Weight) 3 种方式。默认情况下使用的是轮询策略，这种方式比较好理解应用服务器的压力也很均衡。在上例中使用的是权重方式，根据设置的权重来分配client的请求。如果想长时间保持会话的话，就需要使用用户IP哈希的方式，这种方式保证了一个 client 每次请求都将到达同一个 应用服务器。如果所 hash 到的 应用服务器当前不可用，则请求会被转移到其他 应用服务器。

配置文件使用方法如下

```
upstream localhost {
		ip_hash;
		server 10.10.10.188:8081;
		server 10.10.10.107:80;
}
```

### 参考文献

* <https://www.nginx.com/>
* <http://www.2cto.com/os/201306/223768.html>
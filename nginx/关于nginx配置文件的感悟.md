```bash
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
	upstream cms_server_pool{ 
		server 127.0.0.1:31001 weight=10;
	}
	upstream img_server_pool{ 
		server 192.168.189.128:22122 weight=10;
	}

   server{
		listen 80;
		server_name www.xuecheng.com;
		ssi on;
		ssi_silent_errors on;
		location / {
			alias D:/xcEdu/xcEduUI01/xc-ui-pc-static-portal/;
			index index.html;
			autoindex on;
			
		}
		location /cms/preview/ { 
			proxy_pass http://cms_server_pool/cms/preview/; 
			index index.html
			autoindex on;
		}
		
	}
	server{
		listen 80;
		server_name img.xuecheng.com;
		ssi on;
		ssi_silent_errors on;
		location /group1 { 
			proxy_pass http://img_server_pool; 
			autoindex on;
		}
		
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
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

经常配的也就是上面的http域，对于这个域里面的自己的感悟：

upstream这个东西，就是相当于方法的变量，到时候可以在server在写url时可以用到，

server里面的东西，极其重要。主要的配置都是在这里面

```bash
server{
		listen 80;
		server_name img.xuecheng.com;#代理的假域名
		ssi on;
		ssi_silent_errors on;
		location /group1 { 
			proxy_pass http://img_server_pool; #代理的真实网址
			autoindex on;
		}
		
	}
```


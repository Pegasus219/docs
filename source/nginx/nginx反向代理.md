### Nginx反向代理

#### nginx.conf
    error_log /dev/null;    ##取消error_log日志
    
    events {
        worker_connections 1024;    ##default 1024
    }
    
    http {
        access_log off;     ##关闭access_log日志
        
        upstream mywebservice {
            ## 用docker运行nginx时，需要docker0的ip，而非127.0.0.1
            server 172.17.0.1:8080;
            server 172.17.0.1:8081;
        }
        
        server {
            listen 80;
            # server_name localhost;
            location ~*^.+$ {
                proxy_redirect off;
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://mywebservice;
            }
        }
    }

#### 通过docker运行nginx
    # docker run --restart=always -d -p 80:80 \ 
    -v /home/.../nginx/nginx.conf:/etc/nginx/nginx.conf \
    -v /home/.../nginx/log:/var/log/nginx \
    nginxImage:latest 
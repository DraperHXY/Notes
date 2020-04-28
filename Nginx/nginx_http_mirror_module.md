# nginx_http_mirror_module

发现了一件事，每次涉及到 Nginx 我就要重新学习一下 Nginx 是怎么配置的，我以为我是记住的...

这次主要是业务上的需求有点特殊，需要用到 Nginx 的模块 ``ngx_http_mirror_module``

一直以为这个是属于第三方模块，所以需要单独的下载进行安装，为此踩了不少坑。事实上并没有这么复杂。

1. **Nginx 1.13.4 及后续版本内置 nginx_http_mirror_module 模块**，提供流量镜像（复制）的功能。
2. 源站请求，直接原路返回；正常配置下，mirror 请求不影响源站请求及响应。将流量复制到 mirror 站后，两者不再有任何交集。

这两条是我使用的主要目的。

贴下主要配置 nginx.conf 和 shell 指令

```conf
upstream testcopy {
    server 127.0.0.1:8080;
}
server {
    listen       80;
    server_name  test.copy.com;
    #charset koi8-r;
    #access_log  logs/host.access.log  main;
    location / {
        root   html;
        index  index.html index.htm;
        proxy_pass http://testcopy;
        mirror /mirror;
    }
    location /mirror {
        internal;
        proxy_pass http://127.0.0.1:8081$request_uri;
        proxy_set_header Content-Length "";
        proxy_set_header X-Original-URI $request_uri;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

先用 nginx -t 来检查配置文件的位置

再修改 nginx.conf 配置文件后，使用 nginx -s reload 重新加载配置文件




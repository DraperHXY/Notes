```conf
location /draper/ {
    proxy_pass https://i.maoyan.com/;
}
```

maoyan.com 后面加上``/``就会把前缀``draper``去掉
---
title: npm 使用记录
category: 笔记
tag: 总结
abbrlink: 33872
date: 2023-01-12 16:36:53
---


## npm 使用记录

- npm 版本升级
```shell
    npm install -g npm   
```

- npm 版本降级  （6.14就是你想要降的版本）
```shell
    npm install npm@6.14 -g 
```
- npm 清理缓存
```shell
    npm cache clean -f 
```

## vue 通过 nginx 部署 404 问题

效果图
![vue history模式 404](/img/nginx_404_route.png)
- PS : 之所以出现上面的现象，是因为在nginx配置的文件下面压根没有 'build/index'这个真实资源存在，这些访问
资源都是在js里渲染的。

1. 问题： 出现404的原因是由于在这个域名根目录 /data/irs/irs-caster/frontend 文件下面压根没有 'build/index'这个真实资源存在
```shell
    server {
        listen       2643;
        server_name  43.192.4.68;

        index index.php index.html index.htm default.php default.htm default.html;
        root /data/irs/irs-caster/frontend;
    }
```
2. 解决问题
```shell
server {
    listen       2643;
    server_name  43.192.4.68;

    index index.php index.html index.htm default.php default.htm default.html;
    root /data/irs/irs-caster/frontend;
    #vue-router配置
    location / {
    try_files $uri $uri/ @router;
    index index.html;
    }
    location @router {
    rewrite ^.*$ /index.html last;
    }
}
```

3. 重启 nginx后，问题就迎刃而解了。

4. 测试
 
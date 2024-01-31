---
title: hexo使用问题记录
category: 笔记
tag: 总结
abbrlink: 63469
date: 2023-01-13 11:04:53
---

## hexo d timeout

- hexo d 部署到github仓库，经常超时，科学上网也用!

效果图

![提交代码超时问题](/img/hexoPushTimeOut.png)

- 解决方法
    - 找到项目的 _config.yml 文件

```shell
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  # 1. 问题： 仓库地址repository设置成https开头，会经常超时
  # repo: https://github.com/hk2012/hk2012.github.io.git

  # 2. 解决方法   
  repo: git@github.com:hk2012/hk2012.github.io.git
  
  branch: master
```


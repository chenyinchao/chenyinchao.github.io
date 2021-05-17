---
title: Github 访问问题、clone
date: 2019-11-18 14:26:46
---

#### 非代理

1. 获取Github官方CDN地址：
   
      打开站长之家查询DNS，http://tool.chinaz.com/dns ，找到响应最快的ip
      或
      打开 https://www.ipaddress.com/ 查询 DNS：

            github.com
            assets-cdn.github.com
            github.global.ssl.fastly.net

2. 修改hosts文件： `C:\Windows\System32\drivers\etc\hosts`

> **最后以管理员身份运行命令行，刷新系统DNS缓存 `ipconfig /flushdns`，即生效**

#### 代理模式
- 加快git clone速度，在git bash中：

      // 10811 改为自己的 HTTP 监听端口（在ss"编辑服务器"中查看）
      git config --global http.https://github.com.proxy https://127.0.0.1:10811
      git config --global https.https://github.com.proxy https://127.0.0.1:10811
      
      // 同时设置sock5
      git config --global http.https://github.com.proxy socks5://127.0.0.1:10811
      git config --global https.https://github.com.proxy socks5://127.0.0.1:10811

- 查看代理

      git config --global --get http.proxy
      git config --global --get https.proxy

- 取消代理

      git config --global --unset http.proxy
      git config --global --unset https.proxy


<img src="/images/20191118_Github 访问问题、clone.png" class="nofancybox"/>


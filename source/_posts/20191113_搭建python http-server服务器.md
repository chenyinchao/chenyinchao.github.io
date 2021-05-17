---
title: Python搭建Http-Server局域网服务器
date: 2019-11-13 16:24:23
---

项目地址：
https://github.com/http-party/http-server

指定目录键入命令：`http-server -c-1` 就可以把本地目录文件共享出来。//只输入`http-server`的话，更新了代码后，页面不会同步更新

访问服务器无效，解决办法：`npm i http-server@0.9.0 -g` //回退到0.9.0版本

参考链接：
http://www.imooc.com/wenda/detail/561230
Github上issues：
https://github.com/http-party/http-server/issues/525

>搭建Http-Server 是一个简单的零配置的命令行 http服务器，它足够强大便于生产和使用，用于本地测试和开发，手机访问电脑共享。
很多时候需要局域网共享文件，有些大文件也没法用微信/qq传，或者是从服务器上需要下载文件到本地scp会很慢，如果用python -m SimpleHTTPServer 会发现服务器性能很差，且不支持并发下载。这时就可以用http-server。
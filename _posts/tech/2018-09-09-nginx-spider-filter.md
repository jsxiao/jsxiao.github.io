---
layout: post
title: Nginx 拦截爬虫（频繁请求）
category: 技术
tags: Nginx
description: Nginx通过$http_user_agent拦截爬虫
---

> 目前互联网有太多的爬虫不讲究，不节制的去爬我们的网站。我的网站使用是Nginx，下面是通过使用$user_agent来判断是否为爬虫（稍微正常点的都会包含"Spider"字符）。

### Nginx配置
```
 # 如果请求中包含 "YisouSpider" 字符则返回403, 其中 [~*] 不区分大小写
 if ($http_user_agent ~* "YisouSpider") {
    return 403;
 }
```

以上仅针对 `$http_user_agent`中包含`YisouSpider`的情况。

需要注意的是使用代码请求的话完全可以伪造`$http_user_agent`，此时通过上述方式是判断不出来爬虫的。
此种情况就需要根据请求的URL以及频率来判断是否为恶意请求，如果是的话则让用户等待N秒。

后续我会将具体解决频繁请求的方法写在这里。

---
layout: post
title: "记录｜在WordPress设立HTTPS"
categories: wordpress https
tags: https
---

TL;DR: 根据 [HTTPS for WordPress – WordPress.org Forums](https://wordpress.org/support/article/https-for-wordpress/) 上的指示操作即可。

## 获取 HTTPS 证书

到 [Certbot](https://certbot.eff.org/) 上去，填 My HTTP website is running [Nginx] on [Ubuntu 20] ，跳转到[指南页面](https://certbot.eff.org/instructions?ws=nginx&os=ubuntufocal)，根据指南：

先装`snap`：（参考 [Installing snap on Ubuntu - Snapcraft documentation](https://snapcraft.io/docs/installing-snap-on-ubuntu)）

```
sudo apt update
sudo apt install snapd
```

更新`snap`：

```
sudo snap install core; sudo snap refresh core
```

移除`certbot`：`sudo apt-get remove certbot`

安装`certbot`：

```
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

HTTPS（二选一，或者`sudo certbot certonly --nginx`）：

```
sudo certbot --nginx
```

选择你的 URL 即可。

执行时，会提示把debug log存在 `/var/log/letsencrypt/letsencrypt.log`。

## 改WordPress地址（URL）

这部分参见 [HTTPS for WordPress – WordPress.org Forums](https://wordpress.org/support/article/https-for-wordpress/) ：

在你的主页URL后面加上`/wp-admin/`，形如 https://your.wordpress.site/wp-admin/ ，登录。

点击“设置”，在“WordPress地址（URL）”和“站点地址（URL）”上的http后面加上s即可。

点击“保存更改”，完成！
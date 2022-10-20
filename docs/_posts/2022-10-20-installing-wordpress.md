---
layout: post
title: "总结｜在Ubuntu 22.04上设立WordPress"
categories: wordpress installation
tags: wordpress
---

本文只是我对自己安装WordPress过程的一个总结，可能并不准确，欢迎指出。我使用的是 Nginx、MySQL和php-fpm。如果你用的是 Apache2 与 phpMyAdmin/MariaDB 的话，这篇文章可能帮不到你。

如果你嫌麻烦，可以直接用[WordPress.com](https://wordpress.com/)的免费方案，就是可能会有广告。

吐嘈：

- Google到的很多教程（比如[wpbeginner.com](https://www.wpbeginner.com/how-to-install-wordpress/)与[freecodecamp.org](https://www.freecodecamp.org/news/how-to-make-a-website-with-wordpress/)）都推荐用Bluehost/HostGator/SiteGround/WP Engine/Hostinger等解决方案……但我没法用啊……
- 网上的很多教程在如何设置 PHP 的部分过于模糊了……导致我折腾了很久。

如果你想要更详细的教程，推荐这个：[How to Install WordPress with Nginx on Ubuntu 22.04 LTS - LinuxCapable](https://www.linuxcapable.com/install-wordpress-with-nginx-mariadb-php-on-ubuntu-22-04-lts/)（但是注意该文中提到的PHP 8.1 可能会产生错误，最好使用PHP 8.0）

WordPress官方也有 [安装指南](https://wordpress.org/support/article/how-to-install-wordpress/) ，不过说得不是很详细，需要借助[官方论坛](https://wordpress.org/support/forum/installation/)或者Google。

## TODO

- [ ] 支持HTTPS？——[HTTPS for WordPress – WordPress.org Forums](https://wordpress.org/support/article/https-for-wordpress/)
- [ ] 学习使用WordPress——[Learn WordPress](https://learn.wordpress.org/)

## 如果早知道……

### WordPress

- WordPress 是一个**免费开源**的引擎（FOSS），其官网是 WordPress.org
- [WordPress.com](https://wordpress.com/) 是一个商业的 WordPress-hosting 平台。我以前只知道这个，导致我一直以为 WordPress 是个商业软件，一直不想用它……后悔为什么不早知道。

### 排雷

- 不能用PHP 8.1，需要用PHP 8.0或更低的PHP 7.4（参考[1](https://wordpress.org/support/topic/lot-of-errors-in-wordpress-dashboard/)）。
- WordPress除了`php`以外，还需要`php-fpm`或`php-cgi`——不能只安装`php`！

### 可能的报错（及其解决方案）

这里总结了我在安装过程中出现的报错信息以及我是如何解决它们的。

#### 502

如果是 502 ，说明你 `php-fpm` 没有安装，或没有设置好 nginx 的配置文件。

### Error establishing a database connection

如果你建了数据库，且 `wp-config.php` 中的参数正确，那么这应该是因为你没有给你的用户足够的权限。

如果你嫌麻烦，可以直接使用

```sql
GRANT ALL PRIVILEGES ON * . * TO 'wordpress'@'localhost';
```

来给予`'wordpress'@'localhost'`所有数据库的权限。

#### 白屏

如果你在浏览器中访问时出现了白屏（“white screen of death”），说明你的 PHP 版本不对——不能用最新的 PHP 8.1 。你可以在`wp-config.php`中设置

```
error_reporting(E_ALL); ini_set('display_errors', 1);

define( 'WP_DEBUG_LOG', true );
define( 'WP_DEBUG_DISPLAY', false );
```

来输出信息。

## 安装依赖

```
sudo apt-get update
```

### Nginx

```
sudo apt-get install nginx
```

### MySQL

参考：[1](https://stackoverflow.com/questions/11990708/error-cant-connect-to-local-mysql-server-through-socket-var-run-mysqld-mysq), [2](https://stackoverflow.com/questions/41147609/unable-to-start-the-mysql-server-in-ubuntu).

```
sudo apt-get install mysql-client-core-8.0 mysql-server-core-8.0 mysql-server mysql-client --fix-missing
sudo service mysql start
```

### PHP

```
sudo add-apt-repository ppa:ondrej/php
sudo apt-get update
sudo apt-get install php7.4 php7.4-cli php7.4-fpm php7.4-mysql php7.4-json php7.4-opcache php7.4-mbstring php7.4-xml php7.4-gd php7.4-curl php7.4-cgi
```

我使用的是 PHP7.4，如果你安装了 PHP 8.0 的话，需要用`sudo apt-get purge php8.*`或者`sudo update-alternatives --config php`来更改PHP版本。（参考[1](https://medium.com/@skounis/downgrade-php8-1-to-php8-0-or-php7-4-on-ubuntu-22-04-2fab4a6a3be3)）

### DNS 

如果你用的是腾讯云，在 [DNSPod](https://console.dnspod.cn/) 里加条解析即可。

## 开始

访问WordPress.org的[官网](https://wordpress.org/)，在远程服务器上[下载](https://wordpress.org/download/)并解压[最新版](https://wordpress.org/latest.zip)WordPress：

```
wget https://wordpress.org/latest.zip
unzip latest.zip
```

### 用MySQL建数据库

此部分对应官方指南的 [Step 2: Create the Database and a User](https://wordpress.org/support/article/how-to-install-wordpress/#step-2-create-the-database-and-a-user) 以及 [Creating Database for WordPress – Using the MySQL Client](https://wordpress.org/support/article/creating-database-for-wordpress/#using-the-mysql-client) 。

```
sudo mysql -u adminusername -p
```

运行（数据库和用户的名字任意，这里都用了wordpress）：

> 注：直接按照[指南](https://wordpress.org/support/article/creating-database-for-wordpress/#using-the-mysql-client)运行代码会报错，需要参考 [这个](https://stackoverflow.com/questions/36631676/error-in-your-sql-syntax-check-the-manual-that-corresponds-to-your-mysql-server) 改一下。

```sql
CREATE DATABASE wordpress;
CREATE USER 'wordpress'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON * . * TO 'wordpress'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

把`password`替换掉，记住它，之后要填在`wp-config.php`里。

### 改`wp-config.php`

该部分对应[Step 3: Set up wp-config.php](https://wordpress.org/support/article/how-to-install-wordpress/#step-3-set-up-wp-config-php)。（参考 [wp-config.php | Common APIs Handbook | WordPress Developer Resources](https://developer.wordpress.org/apis/wp-config-php/)）

```sh
cd wordpress
cp wp-config-sample.php wp-config.php
nano wp-config.php
```

在

```php
// ** Database settings - You can get this info from your web host ** //
```

下面把 DB_NAME、DB_USER、DB_PASSWORD、DB_HOST根据[指南](https://wordpress.org/support/article/how-to-install-wordpress/#step-3-set-up-wp-config-php)改掉。

如果你根据这个指南完成了上一步，那么改成这个即可（`password`是你前一节设置的密码）：

```php
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** Database username */
define( 'DB_USER', 'wordpress' );

/** Database password */
define( 'DB_PASSWORD', 'password' );

/** Database hostname */
define( 'DB_HOST', 'localhost' );
```

然后把

```
  * Authentication Unique Keys and Salts.
```

下面的AUTH_KEY等替换成用官方的 [生成器](https://api.wordpress.org/secret-key/1.1/salt/) 生成的secret key。

### 移动文件

该部分对应[Step 4: Upload the files](https://wordpress.org/support/article/how-to-install-wordpress/#step-4-upload-the-files)

直接

```sh
cd ..
sudo mv wordpress /var/www/html/wordpress
chown -R www-data:www-data /var/www/html
sudo find /var/www/html/wordpress -type d -exec chmod 755 {} \;
sudo find /var/www/html/wordpress -type f -exec chmod 644 {} \;
```

即可（参见 [LinuxCapable的教程](https://www.linuxcapable.com/install-wordpress-with-nginx-mariadb-php-on-ubuntu-22-04-lts/#Create_Folder_Structure_for_WordPress) ）。

### 搞nginx

- `/etc/nginx/sites-available/default`：这个文件里应该已经有`fastcgi_pass unix:/run/php/php7.4-fpm.sock;`了，不用动就可以了。
- `/etc/nginx/nginx.conf`：不用改。

只需要修改`/etc/nginx/sites-available/wordpress`即可，我是直接在 [LinuxCapable的模板](https://www.linuxcapable.com/install-wordpress-with-nginx-mariadb-php-on-ubuntu-22-04-lts/#Nginx_Server_Block_Configuration) 上把php8.1-fpm改成了php7.4-fpm。可能也可以直接用 [Nginx官网的WordPress recipe](https://www.nginx.com/resources/wiki/start/topics/recipes/wordpress/) 。

我的`/etc/nginx/sites-available/wordpress`如下：

```nginx
server {

  listen 80;
  listen [::]:80;
  server_name example.com;

  root /var/www/html/wordpress;

  index index.php index.html index.htm index.nginx-debian.html;


  location / {
  try_files $uri $uri/ /index.php?$args;
 }

  location ~* /wp-sitemap.*\.xml {
    try_files $uri $uri/ /index.php$is_args$args;
  }

  client_max_body_size 100M;

  location ~ \.php$ {
    fastcgi_pass unix:/run/php/php7.4-fpm.sock;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
    include snippets/fastcgi-php.conf;
    fastcgi_buffer_size 128k;
    fastcgi_buffers 4 128k;
    fastcgi_intercept_errors on;
  }

 gzip on;
 gzip_comp_level 6;
 gzip_min_length 1000;
 gzip_proxied any;
 gzip_disable "msie6";
 gzip_types
     application/atom+xml
     application/geo+json
     application/javascript
     application/x-javascript
     application/json
     application/ld+json
     application/manifest+json
     application/rdf+xml
     application/rss+xml
     application/xhtml+xml
     application/xml
     font/eot
     font/otf
     font/ttf
     image/svg+xml
     text/css
     text/javascript
     text/plain
     text/xml;

  # assets, media
  location ~* \.(?:css(\.map)?|js(\.map)?|jpe?g|png|gif|ico|cur|heic|webp|tiff?|mp3|m4a|aac|ogg|midi?|wav|mp4|mov|webm|mpe?g|avi|ogv|flv|wmv)$ {
      expires    90d;
      access_log off;
  }

  # svg, fonts
  location ~* \.(?:svgz?|ttf|ttc|otf|eot|woff2?)$ {
      add_header Access-Control-Allow-Origin "*";
      expires    90d;
      access_log off;
  }

  location ~ /\.ht {
      access_log off;
      log_not_found off;
      deny all;
  }
}
```

之后，运行：（参见 [LinuxCapable的教程](https://www.linuxcapable.com/install-wordpress-with-nginx-mariadb-php-on-ubuntu-22-04-lts/#Nginx_Server_Block_Configuration) ）

```sh
sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
sudo nginx -t # 如果这一步不ok，则需要根据提示检查你的nginx配置文件
sudo systemctl restart nginx
```

即可。现在应该可以在`example.com`（你的域名）上看到你的网站了
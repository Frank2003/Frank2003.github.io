---
title: "记录｜用 Jekyll 在 Github Pages 上搭建个人网站"
tags:
- Jekyll
- GitHub Pages
---

## 开始

按照[Jekyll官网](https://jekyllrb.com/)的指示：

```
gem install bundler jekyll
```

但是进行下一步时，显示`command not found: jekyll`。

查了下，根据[这篇文章](https://blog.csdn.net/weixin_39718665/article/details/78142515)，尝试`sudo`：

```
sudo gem install jekyll
```

之后就可以正常运行`jekyll`了，继续

```
jekyll new my-awesome-site
cd my-awesome-site
bundle exec jekyll serve
```

然而报错`cannot load such file -- webrick (LoadError)`。Google一下，找到一些[讨论串](https://talk.jekyllrb.com/t/load-error-cannot-load-such-file-webrick/5417/2)（或者[博文](https://www.cnblogs.com/huyuchengus/p/15473035.html)），顺着找到Github上的[issue](https://github.com/jekyll/jekyll/issues/8523)，根据它说的运行

```
bundle add webrick
```

之后，可以正常运行`bundle exec jekyll serve`了，问题解决！

现在，可以用浏览器打开`http://127.0.0.1:4000/`上看到示例
网站了。

### 没有用的操作

参照[官网的Troubleshooting部分](https://jekyllrb.com/docs/troubleshooting/#installation-problems)，更新`gem`：

```
gem update --system
```

这一步很慢，不知道有什么解决办法……

```
jekyll new my-awesome-site
cd my-awesome-site
bundle exec jekyll serve
```

## Github Pages

根据[Github Pages](https://pages.github.com/)的说明，创建一个叫 _username_.github.io 的仓库即可。先把它克隆到本地，以进一步操作。

之后，参照官方的[使用 Jekyll 设置 GitHub Pages 站点](https://docs.github.com/cn/pages/setting-up-a-github-pages-site-with-jekyll)的介绍进行操作，正常进行[Creating a GitHub Pages site with Jekyll - GitHub Docs](https://docs.github.com/cn/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)的操作即可。

可以用[Github Desktop](https://desktop.github.com/)来进行某些操作。

### 关于baseurl

如果你的仓库就是 _username_.github.io 的话，baseurl留空就行了，即使你把你的站点放在了类似 `/docs` 的子文件夹里，而不要按照[Creating a GitHub Pages site with Jekyll - GitHub Docs](https://docs.github.com/cn/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)的第13步操作，在 `_config.yml` 里进行如下操作：

```yml
baseurl: /REPOSITORY-NAME/      # place folder name if the site is served in a subfolder
```

但是如果真在 _username_.github.io 仓库下这么做了的话，网站会崩掉。

如果真想在 `/docs` 下访问，根据[这个回答](https://stackoverflow.com/questions/42743494/baseurl-and-url-config-didnt-work-for-jekyll-on-github)，似乎需要把这个文件夹 split 到一个叫 docs 的仓库中，详见[Splitting a subfolder out into a new repository - GitHub Docs](https://docs.github.com/cn/get-started/using-git/splitting-a-subfolder-out-into-a-new-repository)。
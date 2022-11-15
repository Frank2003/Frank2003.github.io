---
title: "记录｜简单修复cnblogs报错｜在Org Mode中发布文章至博客园"
tags: cnblogs, org-mode
category: 记录, 修
---

找到了个2012年的[cnblogs](https://github.com/yangwen0228/unimacs/tree/bbeb3195000d8c1fd8ba436b47aa65d868769206/packages/vendor/cnblogs)的方案。

运行时，报了这两个错：

- `Symbol’s function definition is void: find-if`
  - 解决方法：把`find-if`改成`cl-find-if`即可。

- 解决方法：`Symbol’s function definition is void: s-trim`
  - 解决方法：安装字符串处理包`s`（`M-x package-install RET s RET`）。

最后另外魔改了一下，变成了[这样](https://gist.github.com/frank3215/9648eb1d9bfec594298fead382b90351)。

后记：后来发现了个star更多的2016年的[cnblogs](https://www.cnblogs.com/halberd-lee/p/10989782.html)方案，但懒得折腾了……
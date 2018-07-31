---
title: 使用hexo写第一篇博客
date: 2018-07-31 18:45:16
tags: [hexo,入门]
---
# 如何使用hexo写一遍博客
  经过一系列的踩坑，终于将博客从wordpress迁移到hexo上面，放弃wordpress的目的是对markdown的支持太差，而且没找到简洁的主题。而hexo写博客其实比起wordpress的在线编辑预览发布要麻烦许多的。
  这里先采用的最简单的方式，使用vscode写md,本地hexo s预览，使用git与服务器同步，然后在服务器上进行hexo clean&&hexo g.有时间再试试试别的办法。
  1.  先生成一个文章
  > hexo new 使用hexo写第一篇博客
  
  这是会在 /source/_posts下面生成同名的md文件。打开看到头部已有文章默认配置
 ```
 ---
title: 使用hexo写第一篇博客
date: 2018-07-31 18:45:16
tags: 
    - 入门
    - hexo
---
```
2. 启动hexo的调试模式。
使用命令
> hexo s

可以实时的预览写好的md,使用vscode之后，只要ctrl +s 保存之后，浏览器打开本地的博客会自动刷新。

3. vscode markdown实时预览
> ctrl + shift + p  输入 markdown选择在左侧打开实时的预览即可。

4. 同步
> git add .  
git commit -m "使用hexo写第一篇博客"  
git push 

5. 到服务器上使用 git pull&& hexo clean && hexo g

至此，一篇博客就可以对外发布了。

----
注：
挖个坑，后续会有一系列的相关文章，如何更方便的发布博客，hexo下的markdown语法。

---
title: 集成 Hexo, Github Pages, Travis 搭建个人博客
date: 2017-02-25
categories: tool
tags: [hexo, github, travis]
toc: true
---

Reference 很详细地说明了安装、配置 Github Pages，Hexo，Travis 的方法。这里主要分享一下我的方法。
https://github.com/irgb/irgb.github.io 中包含3个分支：

- master: 包含Hexo生成的静态文件。Github Pages 就是以这个分支创建网站。
- source: 源码分支. push 这个分支会触发 Travis 进行构建.
- hexo-skeleton: hexo 项目文件夹,因为其中包含了一些个性化的修改,所以也保存起来.push 这个分支也会触发 Travis 进行构建.

重要的是 '.travis.yml' 文件:

```yml
language: node_js
node_js: stable
branches:
  only:
    - source
    - hexo-skeleton
before_install:
  - git fetch origin source:source # 先 fetch source 分支
  - git checkout source
  - tar zcvf ./source.tar.gz ./* # 创建 source 文件夹
  - mkdir source
  - tar zxvf source.tar.gz -C ./source
  - rm source.tar.gz
  - git fetch origin hexo-skeleton:hexo-skeleton
  - git checkout hexo-skeleton # 获取 hexo-skeleton 分支, 刚才生成的 source 文件夹仍然存在.

install:
  - npm install # 根据 hexo-skeleton 分支下的 package.json 安装需要的依赖

script:
  - hexo cl # 调用 hexo 对 source 文件夹进行编译, 生成网站静态文件.
  - hexo g

after_script:
  - cd public # Public 文件夹包含了生成的静态文件.
  - git init # 接下来的操作就是将 Public 下的文件提交到 master 分支上.
  - git config user.name "linjapyc"
  - git config user.email "linjapyc@gmail.com"
  - git remote add origin https://${GH_TOKEN}@github.com/irgb/irgb.github.io.git
  - git add .
  - git commit -am "`date +'%Y-%m-%d %H:%M:%S %z'`"
  - git push -f origin master:master
```

# Reference

- [｜Hexo优化｜如何向google提交sitemap](http://fionat.github.io/2016/04/02/sitemap/)
- [大道至简——Hexo简洁主题推荐](https://www.haomwei.com/technology/maupassant-hexo.html)
- [定制Hexo - maupassant](http://codingpub.github.io/2016/04/10/%E5%AE%9A%E5%88%B6Hexo-maupassant/)
- [Github 搭建 hexo （四）——更换主题，disqus，RSS](http://blog.csdn.net/u010053344/article/details/50701191)
-[使用 Travis CI 自动部署 Hexo](http://www.jianshu.com/p/5e74046e7a0f)
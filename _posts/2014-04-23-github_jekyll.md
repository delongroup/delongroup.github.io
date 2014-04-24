---
layout: post
title: Github + Jekyll 配置
description: Github + Jekyll 配置
tags: [Jekyll]
---
##创建 [GitHub Pages](https://pages.github.com/)

**你的GitHub账户名称为USERNAME,创建的仓储名称应该为USERNAME.github.io,过10分钟页面才能访问。**  

##设置Jekyll主题  
下载[hpstr](https://github.com/mmistakes/hpstr-jekyll-theme),替换GitHub默认的Jekyll文件。

##替换markdown渲染引擎
修改 **_config.yml** 配置文件：  

```yaml

markdown: redcarpet
redcarpet:
    extensions: ["no_intra_emphasis", "fenced_code_blocks", "autolink",
    "strikethrough", "superscript", "tables", "highlight", "prettify"]
    
```
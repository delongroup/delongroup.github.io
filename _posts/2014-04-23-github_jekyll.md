---
layout: post
title: Github + Jekyll 配置
description: Github + Jekyll 配置
tags: [Jekyll]
---
redcarpet渲染引擎支持中文标题，修改 **_config.yml** 配置文件：  

```yaml

markdown: redcarpet
redcarpet:
    extensions: ["no_intra_emphasis", "fenced_code_blocks", "autolink",
    "strikethrough", "superscript", "tables", "highlight", "prettify"]
    
```
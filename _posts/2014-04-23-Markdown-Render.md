---
layout: post
title: markdown渲染引擎配置
description: "markdown渲染引擎配置"
tags: [markdown]
---
redcarpet渲染引擎支持中文标题，修改**_config.yml**配置文件：  

```yaml
markdown: redcarpet
redcarpet:
    extensions: ["no_intra_emphasis", "fenced_code_blocks", "autolink",
    "strikethrough", "superscript", "tables", "highlight", "prettify"]```
    
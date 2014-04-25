---
layout: post
title: Showdown + Prettify 配置
description: Showdown + Prettify 配置
tags: [Showdown]
---
[Showdown](https://github.com/coreyti/showdown)是处理markdown的javascript库,支持markdown的扩展，比如GitHub的[GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown)。  
[Prettify](https://code.google.com/p/google-code-prettify/)是Google的代码高亮javascript库,支持多种语言和模版。  

##下载库  
* [Prettify](https://google-code-prettify.googlecode.com/files/prettify-small-4-Mar-2013.tar.bz2)
* [Showdown](https://github.com/coreyti/showdown)  

##配置
1. 解压Showdown和Prettify，把Prettify放入Showdown文件夹中。
2. 新建立Demo.html。  


添加`showdown`库  

```javascript

<script type="text/javascript" src="src/showdown.js"></script>

```  
添加`Prettify`库和样式  

```javascript
<link href="prettify/prettify.css" type="text/css" rel="stylesheet" />
<script type="text/javascript" src="prettify/prettify.js"></script>
```

代码块背景和边框  

```javascript
<style type="text/css">
pre{background-color: #f7f7f9;border:1px solid #e1e1e8;word-break: break-word;}
</style>
```

初始化代码

```javascript
<script type="text/javascript">
var converter;
window.onload = function()
{
/**
* Showdown对象
*/
converter = new Showdown.converter();

/**
* 高亮代码
*/
prettyPrint();
};
</script>
```

`Markdown`转`Html`

```javascript

<script type="text/javascript">
function onConvert()
{
var code = document.getElementById('in');
var v =  converter.makeHtml(code.value);
var out = document.getElementById('out');
out.innerHTML = v;
prettyPrint();
}
</script>

```  

##其他
1. 添加行号

 	 ```javascript
  	<pre class="prettyprint linenums"></pre>
  	```
2. 显示所用行号

   修改`prettify.css`中`list-style-type: none`为`list-style-type: decimal`
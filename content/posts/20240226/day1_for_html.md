+++
title = '前端第一天'
date = 2024-02-26T19:21:29+08:00
draft = false
+++
# What is HTML
	It's full name is"HyperText Markup Language"(超文本标记语言)
	HyperText:hyperlink.  
	markup:It needs variable mark symbols to make it become a hypertext.  
	Language:every symbol,using rule make it a mark language.  
# First experience of HTML
	```html
	<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>First experience of html</title>
</head>
<body>
<marquee>开始学习前端</marquee>
</body>
</html>
	```
# 标签
	<marquee>   开始学习前端  </marquee>
	起始标签（左）    标签体         结束标签（右）
	标签内为标签名，不区分大小写，但规范全小写
	有的成对出现，有的单独出现（单标签：例如input）
	标签可以嵌套:
	```html
	<marquee>
		开始学习前端
		<input>
	</marquee>
	```
# 标签属性
		```html
	<marquee loop="1" bgcolor="green">
		开始学习前端
		<input type="password">
	</marquee>
	```
	marquee的loop属性，设定循环次数。bgcolor属性设定了背景颜色。
	input的type属性使得输入内容为密码，输入时加密

	<标签名 属性名="属性值" 属性名=“属性值”>（起始标签 或 单标签）
	个别属性没有属性名，只有值：例如disabled（禁用某些标签）
	一个标签可以写多个属性，但不能重复
# head和body里面是什么呢？
	head：
	```html
	<head>
		<title>标题</title>
	</head>
	```
	body：展示在网页上的内容

# 网页的基本结构
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>...</title>
</head>
<body>
	...
</body>
</html>
```
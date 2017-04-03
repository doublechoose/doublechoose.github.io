---
layout: post
title: sass 语法
category: 工具
keywords: 工具,2017
---

#SASS

##命令
	sass test.scss test.css
	
##四种编译风格
1. **nested**：嵌套缩进的css，默认
2. **expanded**：没有锁紧，扩展的css
3. **compact**：简洁格式的css
4. **compressed**：压缩后的css

生产环境，用最后一个选项
	
	sass --style compressed test.sass test.css

##可以通过监听，自动编译

	// watch a file
	sass --watch input.scss:output.css
	// watch a directory
	sass --watch app/sass:public/stylesheets
	
##基本用法

###变量

	$blue : #1875e7
	div {
		color : $blue;
	}
	
> 如果变量需要镶嵌在字符串中，需要些在#{}中

	$side : left;
	
	.rounded  {
		border-#{$side}-radius : 5px;
	}

###计算能力
	body{
		margin: (14px/2);
		top: 50px + 100px;
		right:$var * 10%;
	}

###嵌套

	div h1 {
		color : red;
	}
-》
	
	div {
		hi {
			color:red;
		}
	}

属性也可以嵌套

	p{
		border: {
			color:red;
		}
	}

>可以使用&引用父元素。`a:hover`可以写成：

	a{
		&:hover { color: #ffb3ff; }
	}


#### 注释

	/*comment*/
	
	//comment
	
	/*!
		重要注释！
	*/


#### 继承

>允许一个选择器继承另一个选择器

	.class1 {
		border: 1px solid #ddd;
	}

>class2要继承class1，就要使用@extend命令

	.class2 {
		@extend .class1;
		font-size:120%;
	}

#### Mixin

>使用@mixin命令定义一个代码块，可以重用

	@mixin left{
		float: left;
		margin-left: 10px;
	}	

>使用@include命令调用该mixin

	div {
		@include left;
	}
>mixin 的强大之处在于可以指定参数和缺省值 （？）

	@mixin left($value: 10px) {
		float: left;
		margin-right: $value;
	}
>使用的时候，根据需要加入参数：

	div{
		@include left(20px);
	}
> 实例：

	@mixin rounded($vert, $horz, $radius: 10px) {
		border-#{$vert}-#{$horz}-radius: $radius;
		-moz-border-radius-#{$vert}#{$horz}: $radius;
		-webkit-border-#{$vert}-#{$horz}-radius: $radius;
	}

>使用的时候这样调用

	#navbar li { @include rounded(top, left); }
	#footer { @include rounded(top,left,5px); }

#### 颜色函数

	lighten(#cc3, 10%) // #d6d65c
	darken(#cc3, 10%) // #a3a329
	grayscale(#cc3) // #808080
	complement(#cc3) // #33c


#### 插入文件

	@import “path/filename.scss";
	@import "foo.css";

###高级用法
#### 条件语句

	p {
		@if 1 + 1 == 2 { border: 1px solid; }
		@if 5 < 3 { border: 2px dotted; }
	}
	
	@if lightness($color) > 30% {
		background-color: #000;
	}@else{
		background-color: #fff;
	}
	
####循环语句

	for $i from 1 to 10 {
		.border-#{$i} {
			border: #{$i}px solid blue;
		}
	}
	
	$i: 6;
	@while $i > 0 {
		.item-#{$i} { width: 2em * $i; }
		$i: $i - 2;
	}

	@each $member in a,b,c,d {
		.#{$member} {
			background-image: url("/image/#{$member}.jpg);
		}
	}
	
####自定义函数

	@function double($n){
		@return $n * 2;
	}
	#sidebar {
		width: double(5px);
	}
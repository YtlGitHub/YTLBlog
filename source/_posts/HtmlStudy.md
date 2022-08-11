---
title: HtmlStudy
date: 2021-09-08 17:44:14
tags: Html
categories: Html
---
# 认识并学习[HTML](https://www.w3school.com.cn/html/index.asp)标签
## HTML格式
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>HTML格式</title>
    <style type="text/css"></style>
        /*样式*/
        body {
            width: 10%;
            background: #ccc;
            border-collapse: collapse;/*borrder-collapse合并内外边距(去除表格单元格默认的两个像素内外边距)*/
            height: 25px;
            line-height: 25px;  /*边距*/
            #text-align: center;  /*剧中*/
        }
</head>
<body>
    <form action="{{ next_url }}" method="post">
		<h2>Login</h2>
		<input id='user' type="text" class="form-control" required="required" placeholder="用户名" name="username"/><br>
		<input id='pwd' type="password" class="form-control" required="required" placeholder="密码" maxlength="6" name="password"/><br>
		<input type="submit" value="登入" onclick="return checkuser()"/>
		<input type="reset">
		<input id='next' type="hidden" name="next_url" value="{{ next_url }}"/>
	</form>
		{{login_status}}
</body>
</html>
```
HTML5 移动页面自适应手机屏幕四
使用meta标签：viewport
```
<meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
```
每个属性的详细介绍：

属性名	取值	描述
width	正整数 或 device-width	定义视口的宽度，单位为像素
height	正整数 或 device-height	定义视口的高度，单位为像素，一般不用
initial-scale	[0.0-10.0]	定义初始缩放值
minimum-scale	[0.0-10.0]	定义缩小最小比例，它必须小于或等于maximum-scale设置
maximum-scale	[0.0-10.0]	定义放大最大比例，它必须大于或等于minimum-scale设置
user-scalable	yes/no	定义是否允许用户手动缩放页面，默认值yes
## 认识HTML标签
### 文本标签
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>文本标签</title>
    <style type="text/css">
        /*样式*/
        body {
            width: 10%;
            background: #ccc;
            border-collapse: collapse;/*borrder-collapse合并内外边距(去除表格单元格默认的两个像素内外边距)*/
            height: 25px;
            line-height: 25px;  /*边距*/
            #text-align: center;  /*剧中*/
        }
    </style>
</head>
<body>
    <h1>H1标题标签</h1>
    <h2>H1标题标签</h2>
    <h3>H1标题标签</h3>
    <h4>H1标题标签</h4>
    <h5>H1标题标签</h5>
    <h6>H1标题标签</h6>
    <u>u下划线标签</u><br/>
    <del>del删除线标签</del><br/>
    <b>b加粗标签</b><br/>
    <strong>strong强调加粗标签</strong><br/>
    <i>i斜体标签</i><br/>
    <em>em强调斜体标签</em><br/>
    <br/>
    <cite>请明上河图</cite>是我国十大传世名画之一 <br><br>
    水分子：H<sub>2</sub>O <br>
    2<sup>4</sup>=16
</body>
</html>
```
### 格式化标签
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>格式化标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML格式化标签</h1>
    <hr/>  <!-- 水平分割线 -->
    
    <div style="width:600px"> <!-- 常用于组合块级元素，以便通过CSS来对这些元素进行格式化 -->
        <p> <!-- 换段 -->
            【学习进行时】党的十八大以来，党中央部署了多次集中教育，习近平总书记对此都作出了重要指示。<br/> <!-- 换行 -->
            历次集中教育，虽侧重点不同，但有一个要求贯穿始终——切实为群众办实事解难题，习近平对此高度重视。
            新华社《学习进行时》原创品牌栏目“讲习所”今天推出文章，为您解读。
        </p>
        <p>
            党的十八大以来，党中央部署了多次集中教育。尽管每次的主题和侧重有所不同，但强化宗旨意识和为民情怀是
            一个共同的目的，都是为了教育广大党员干部解决群众最急最忧最盼的问题。
        </p>
    </div>
    你的爱好：
    <ul type="circle"> <!-- 无序列表 -->
        <li>看书</li> <!-- 列表项 -->
        <li>上网</li>
        <li>爬山</li>
        <li>唱歌</li>
    </ul>
    <ol type="1"> <!-- 有序列表，其中type类型值：Aali start属性表示起始值 -->
        <li>看书</li>
        <li>上网</li>
        <li>爬山</li>
        <li>唱歌</li>
    </ol>
    <dl type="circle"> <!-- 自定义列表 -->
        <dt>问：HTML是什么</dt> <!-- 自定义列表头 -->
        <dd>答：超文本标记语言</dd> <!-- 自定义列表内容 -->
        <dt>问：CSS是什么</dt>
        <dd>答：层叠样式表</dd>
    </dl>
    <div>aaaaa</div><div>bbbbb</div>
    <span>ccccc</span><span>ddddd</span> <!-- 常用于包含的文本，你可以使用CSS对他定义样式，或者使用JavaScript对他进行操作 -->
</body>
</html>
```

### 图片标签：img
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>图片标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML图片img标签</h1>
    <img src="./images/11.jog" alt="图片不显示时显示这个" width="200" border="1"> <!-- 相对路径"."指当前路径 border:指定边框 -->
    <img src="https://www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png" alt="百度图标" width="200" title="百度图片鼠标放图片上就会显示"> <!-- 绝对路径 -->
    <a href="https://www.taobao.com" target="_blank">淘宝</a>  <!-- 超文本标签，target:指定打开方式，跳转新页面打开 -->
</body>
</html>
```

### 超链接标签：a
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>图片标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML实例-超链接a标签</h1>
    <!-- 锚点链接 -->
	<a href="#mv1">超链接1</a><br/><br/>
	<a href="#mv2">超链接2</a><br/><br/><br/><br/>
	<!-- 普通超链接 -->
	<a href="https:www.baidu.com" title="百度链接">百度本页面链接</a><br/>
	<a href="https:www.baidu.com" target="_blank">百度跳新页面链接</a><br/>
	<a href="./h3图片img标签.html" target="_blank">跳转本地链接</a><br/>
	<a id="mv1"></a>
	<h4>图片1</h4>
	<img src="./images/11.jpg" title="图片1" width="100%"/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
	<a id="mv2"></a>
	<h4>图片2</h4>
	<img src="./images/22.jpg" title="图片2" width="100%"/><br/>
</body>
</html>
```

### 表格标签：table
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>图片标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML实例-表格标签</h1>
    <table border="1" width="500" cellspacing="0" cellpadding="4"> <!-- table:一个表格，border:表格边框，cellspacing:边距为0，cellpadding:字体离单元格边的距离大小 -->
		<caption><h2>学生信息表</h2></caption> <!-- caption:表格标题 -->
		<thead>  <!-- 表头 -->
			<tr> <!-- 行标签 -->
				<th>学号</th> <!-- 列头标签 -->
				<th>姓名</th>
				<th>年龄</th>
				<th>班级</th>
			</tr>
		</thead>
		<tbody> <!-- 表体 -->
			<tr>
				<td>1001</td> <!-- 列标签，跨行属性:rowspan,跨列属性:colspan -->
				<td>张三</td>
				<td>20</td>
				<td rowspan="2" align="center" valign="top">1班</td> <!-- rowspan:从上往下合并2个单元格，align:center左右居中显示 valign:top在单元格上边显示 -->
			</tr>
			<tr>
				<td>1002</td>
				<td>李四</td>
				<td>23</td>
				<!-- <td>2班</td> -->
			</tr>
			<tr>
				<td>1003</td>
				<td>王五</td>
				<td>22</td>
				<td>3班</td>
			</tr>
			<tr>
				<td>1004</td>
				<td>赵六</td>
				<td colspan="2" align="center">25</td> <!-- colspan:从左往右合并2个单元格 -->
				<!-- <td>4班</td> -->
			</tr>
		</tbody>
		<tfoot> <!-- 表尾 -->
			<tr>
				<td>1111</td>
				<td>表尾</td>
				<td>25</td>
				<td>表尾</td>
			</tr>
		</tfoot>
	</table>
</body>
</html>
```

### 表单标签：form
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>表单标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML实例-表单标签</h1>
    <form action="h3图片img标签.html" method="post">
		账号：<input type="text" name="uname"/><br/><br/>
		密码：<input type="password" name="upass"/><br/><br/>
		<input type="submit" value="登入"/>
	</form><br/><br/>
	<form action="h1.html" method="post">
		<input type="hidden" name="id" value="100"/>
		姓名：<input type="text" disabled name="uname" maxlength="8" size="10" value="lisi"/><br/>
		密码：<input type="password" name="upass"/><br/>
		性别：<input type="radio" name="sex" value="1"/>男
				<input type="radio" checked name="sex" value="1"/>女<br/><br/>
		爱好：<input type="checkbox" name="likes" value="1"/>看书
				<input type="checkbox" checked name="likes" value="1"/>爬山
				<input type="checkbox" name="likes" value="1"/>游泳
				<input type="checkbox" checked name="likes" value="1"/>唱歌<br/><br/>
		头像：<input type="file" name="pic"/><br/><br/>
		邮箱：<input type="email" placeholder="请输入你的邮箱" name="pic"/><br/><br/>
		年龄：<input type="number" min="18" max="80" name="pic"/><br/><br/>
		指数：<input type="range" min="18" max="80"name="pic"/><br/><br/>
		网址：<input type="url" value="https://www.baidu.com" readonly name="pic"/><br/><br/>
		日期：<input type="date" name="pic"/><br/><br/>
		颜色：<input type="color" name="pic"/><br/><br/>
		学历：<select name="education" id="">
				<option value="1">大专</option>
				<option value="2" selected >本科</option>
				<option value="3">研究生</option>
				<option value="4">博士</option>
				</select><br/><br/>
		简介：<textarea rows="10" cols="100" name="contents">Hello Worls</textarea><br/><br/>
		<input type="submit" value="提交"/>
		<input type="reset" value="重置"/>
		<input type="button" value="普通按钮"/>
		<input type="image" value="./images/reg.gif"/>
	</form><br/><br/>
	<form action="demo_form.php" method="post" style="width:300px">
		搜索：
		<input type="search" list="namelist" name="keywords"/>
		<datalist id="namelist">
			<option value="zhangsan">
			<option value="zhangsanfeng">
			<option value="zhangwuji">
			<option value="lisi">
			<option value="lixiaolong">
		</datalist><br/><br/>
		城市：
		<select name="city">
			<optgroup label="河北省">
				<option>石家庄</option>
				<option>保定</option>
				<option>廊坊</option>
			</optgroup>
			<optgroup label="河南省">
				<option>郑州</option>
				<option>安阳</option>
				<option>周口</option>
			</optgroup>
		</select><br/><br/>
		<fieldset>
			<legend>个人信息：</legend>
			姓名：<input type="text"/><br/>
			年龄：<input type"text"/><br/>
		</fieldset>
		<fieldset>
			<legend>健康信息：</legend>
			身高：<input type="text"/><br/>
			体重：<input type"text"/><br/>
		</fieldset>
	</form>
</body>
</html>
```

### 内联框架标签：iframe
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>表单标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML实例-框架标签</h1>
	<ul>
		<li><a href="h6表单标签-form" target="myframe">表单标签实例</a></li>
		<li><a href="h5表格标签-table.html" target="myframe">表格标签实例</a></li>
		<li><a href="h3图片标签-img.html" target="myframe">图片标签实例</a></li>
	</ul>
	<iframe src="h4超链接标签-a.html" name="myframe" frameborder="1" width="80%" height="500"></iframe>
</body>
</html>
```

### 多媒体标签：audio
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
    <title>多媒体标签</title>
    <style type="text/css">
    </style>
</head>
<body>
    <h1>HTML实例-多媒体标签</h1>
	<audio src="./images/beidahuang.mp3" controls="controls"/>
		你的浏览器不支持audio音频标签
	</audio><br/>
	<audio controls>
		<source src="./images/beidahuang.mp3" type="audio/mpeg"/>
		你的浏览器不支持audio音频标签
	</audio><br/>
	<video src="./images/fun.mp4" controls="controls" width="400" height="400">
		你的浏览器不支持video视频标签
	</video><br/>
	<video  controls="controls" width="400" height="400" poster="./images/11.jpg">
		<source src="./images/fun.mp4" type="vodeo/mp4"/>
		你的浏览器不支持video视频标签
	</video><br/>
	<embed src="./images/haowan.swf" width="300" height="300"/>
</body>
</html>
```
## CSS语法
格式：选择器{属性:值; 属性:值; ......}
案例如下：其中选择器也叫选择符
p{color:red; text-align:center;}
### CSS样式基础语法实例
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS样式基础语法实例</title>
	<style>
		/* 自定义css样式 */
		h3{font-size:30px;color:red;}
		li{color:blue;}
	</style>
</head>
<body>
	<!-- CSS样式基础语法实例 -->
    <h3>什么是CSS</h3>
		<ul>
			<li>CSS 指层叠样式表(Cascading Style Sheets)</li>
			<li>样式定义如何显示控制 HTML 元素，从而实现美化HTML网页</li>
			<li>样式通常存储在样式表中，目的也是为了解决内容与表现分离的问题</li>
			<li>外部样式(CSS文件)可以极大提高工作效率</li>
			<li>多个样式定义可层叠为一，后者可以覆盖前置样式</li>
		</ul>
</body>
</html>
```
### CSS样式的使用方法
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS样式的使用方法</title>
	<link rel="stylesheet" href="./my.css"> <!-- 外部样式 -->
	<style>
		h4{color:green;font-size:16px;line-height:30px;}
		li {color:blue;font-size:16px;line-height:30px}
	</style> <!-- 内部样式 -->
</head>
<body>
    <h1>CSS样式的使用方法</h1>
	<h4>插入样式表的方法有三种：</h4>
		<ul>
			<li>外部样式表(External style sheet)</li>
			<li>内部样式表(Internal style sheet)</li>
			<li style="color:#f0c">内联样式(Inline style)</li>
		</ul>
</body>
</html>
```
### CSS样式的常用选择器(选择符)
```html
<!DOCTYPE HTML>
<html leng="zh-CN">
<head>
    <meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>c3CSS样式的常用选择器(选择符)</title>
	<link rel="stylesheet" href="./my.css"> <!-- 外部样式 -->
	<style>
		/*1.HTML 选择器*/
		/*h1{color:red;font-size:16px;line-height:30px;}
		h3{color:aqua;font-size:16px;line-height:30px;}
		li{color:#00ff00}*/
		/*2.类选择器*/
		.CC{color:red}
		LI.CC{background-color:#DDDDDD}
		LI.dd{color:blue}
		
	</style> <!-- 内部样式 -->
</head>
<body>
    <h1 class="cc">CSS样式的常用选择器(选择符)</h1>
	<h3 id="hid">什么是CSS？</h3>
		<ul>
			<li>CSS 指层叠样式表(Cascading Style Sheets)</li>
			<li class="cc">样式定义如何显示控制 HTML 元素，从而实现美化HTML网页</li>
			<li>样式通常存储在样式表中，目的也是为了解决内部与表现分离的问题</li>
			<li class="cc dd">外部样式表(CSS 文件)可以极大提高工作效率</li>
			<li class="dd">多个样式定义可以层叠为一，后者可以覆盖前置样式</li>
		</ul>
		<span class="sc" id="sid">CSS的常用选择器</span>
		<h4>插入样式表的三种方法：</h4>
		<ol>
			<li>外部样式表</li>
			<li>内部样式表</li>
			<li>内联样式</li>
		</ol>
		<ul>
			<li><a href="c1CSS样式基础语法实例.html?id=1111">CSS语法实例</a></li>
			<li><a href="c2CSS样式的使用方法.html">CSS样式使用方法</a></li>
		</ul>
</body>
</html>
```
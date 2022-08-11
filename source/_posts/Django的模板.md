---
title: Django的模板
date: 2021-10-12 15:51:32
tags: Django
categories: server
---
# Django的模板介绍
<ul>
    <li>作为Web框架，Django需要一种很便利的方法来动态生成HTML。常见做法是使用模板</li>
    <li>模板包括所需HTML输出的静态部分，以及一些特殊的语法，描述如何将动态内容插入</li>
    <li>模板输出就是输出数据，输出方法：直接输出，过滤输出、判断输出，循环输出</li>
</ul>

## Django框架中如何使用模板：
1.在项目的settings.py配置文件中配置模板目录
```settings.py
'DIRS': [os.path.join(BASE_DIR, "templates")],
```
2.在应用的视图文件加载模板，并放置要输出的数据
```views.py
return render(request,"目录/模板文件.html",{放置字典数据})
```
3.在模板文件中使用变量、标签何过滤器等输出信息
```html
{{ 变量 }} {% 标签 %} {{ 变量|过滤器 }}
```
## 模板语法
### 标签
for标签
```html
{% for i in data %}
    循环逻辑
{% endfor %}
```
if 标签
```html
{% if ... %}
    逻辑1
{% elif ... %}
    逻辑2
{% else ... %}
    逻辑3
{% endif %}
```
comment标签
```html
{% comment %}
    多行注释
{% endcomment %}
```
include: 加载模板并以标签内的参数渲染
```html
{ % include “目录/模板名.html” % }
```
url: 反向解析
```html
{ % url 'name' p1 p2 % }
```
csrf_token: 这个标签用于跨站请求伪造保护
```html
{% csrf_token %}
```
### 过滤器
语法：使用管道符“|”来应用过滤器
通过使用过滤器来改变变量的计算结果
```html
{{ 变量|过滤器 }}
```
例如: 将变量name的值变为小写输出
```
{{ name|lower }}
```
关闭HTML自动转义
```
{{ data|safe }}
```
可以在if标签中使用过滤器结合运算符
```
if listl|length > 1
```
过滤器能够被“串联”，构成过滤器链
```
name|lower|upper
```
过滤器可以传递参数，参数使用引号包起来
```
list|join:":"
```
default: 如果一个变量没有被提供，或者值为false或空，则使用默认值，否则使用变量的值
```
value|default:"什么也没有"
```
date: 根据给定格式对一个date变量格式化,输出年月日时分秒，w：第几周
```
value|date:'Y-m-d H:i:s w'
```
## 模板注释：查看源代码看不到的
单行注释
```html
{# 注释 #}
```
多行注释
```html
{% comment %}
    多行注释
{% endcomment %}
```
## 模板运算
加
```html
{{ value|add:10 }}
note:value=5,则结果返回15
```
减
```html
{{ value|add:-10 }}
note:value=5,则结果返回-5,加一个负数就是减法了
```
乘
```html
{% widthratio 5 1 100 %}
note:等同于：(5/1)*100, 结果返回500
widthratio需要三个参数，它会使用参数1/参数2*参数3的方式进行运算，进行乘法运算，使[参数2]=1
```
除
```html
{% widthratio 5 100 1 %}
note:等同于：(5/100)*1，测结果返回0.05，和乘法一样是[参数3]=1就是除法 
```
## 模板继承
模板继承可以减少页面内容的重复定义，实现页面内容的重用
典型应用：网站的头部、尾部是一样的，这些内容可以定义在父模板中，子模版不需要重复定义
block标签：在父模板中预留区域，在子模板中填充
extends继承：继承，写在模板文件的第一行
定义父模块parent_template.html
```parent_template.html
{% block block_name %}
{% endblock %}
```
定义子模块sub_template.html
```sub_template.html
{% extends 'base.html' %}
```
在子模块中使用block填充预留区域
例：
父模板
```parent_template.html
<!DOCTYPE html>
<html lang="zh-hans">
<head>
    <meta charset="UTF-8">
    <title>{% block title1 %}父模板标题{% endblock %}</title>
	<meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
</head>
<body>
	<h2>{% block title2 %}父模板标题{% endblock %}</h2>
	<p>
		{% block mycontent %}
			此处预留内容区域
		{% endblock %}
	</p>
</body>
</html>
```
子模版继承父模板
```sub_template.html
{% extends "myapp/inherit/parent_template.html"%}

{% block title2 %}Django的模板继承实例{% endblock %}

{% block mycontent %}
    模板继承的应用场景<br/>
    通常一个网站的某几个页面都有一些公共的布局，如导航栏、侧边栏和页脚的一些地方，如果将这些公共的部分抽离出来，放在一个单独的 html 页面中用于给其他页面继承，就称它为父版，而继承父版的页面就称为子版，同一个父版可以被多个子版继承。<br/>
    子版中可以继承父版的内容和属性，这样可以使得页面更加简洁，而且在子版中可以修改母版中的部分内容。<br/>
{% endblock %}
```

## 文件上传操作
1.添加form表单模板
```html
<!DOCTYPE html>
<html lang="zh-hans">
<head>
    <meta charset="UTF-8">
    <title>文件上次</title>
	<meta name="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
</head>
<body>
    <h2>文件上传实例</h2>
    <form action="{% url 'doUpload' %}" method="post" enctype="multipart/form-data">
        {% csrf_token %}
        标题名称：<input type="text" name="title"/><br/><br/>
        上传文件：<input type="file" name="pic"><br/><br/>
        <input type="submit" value="上传"/>
    </form>
</body>
</html>
```
2.在urls,py文件里添加路由路径
```urls.py
urlpatterns = [
    path('', views.index, name="index"),  # 首页
    path('doUpload/', views.do_upload, name="doUpload"),  # 执行文件上传处理
]
```
3.在视图里面自定义函数
```views.py
def do_upload(request):
    myFile = request.FILES.get("pic", None)
    print(myFile)
    print(request.POST.get("title"))
    if not myFile:
        return HttpResponse("没有上传的文件信息")
    fileName = str(time.time())+'.'+myFile.name.split('.').pop()
    destination = open(f"./static/pics/{fileName}", "wb+")  # w读取b二进制，图片音频都属于二进制
    for chunk in myFile.chunks():  # 分块读取上传文件内容并写入目标文件
        destination.write(chunk)
    destination.close()  # 打开写入之后我们要关闭，用.close()这个方法关闭写入的文件
    # # 执行图片缩放
    # im = Image.open("./static/pics/"+fileName)
    # # 缩放到75*75(缩放后的宽高比例不变)
    # im.thumbnail((75, 75))
    # # 把缩放后的图像用jpeg格式保存
    # im.save("./static/pics/s_"+fileName, None)
    return HttpResponse("上传的文件："+fileName)
```
## 分页操作
<style type="text/css">
    p{text-indent:2em;}
    .time1{
        color: red;
    }
</style>
<ul>
    <li>Django提供了一些实现管理数据分页，这些类位于django/core/paginator.py中</li>
    <li><span class="time1">Paginator对象</span>
        <p><span class="time1" style="color: red;">Paginator(列表,int)</span>：返回分页对象，参数为列表数据，每页数据的条数</p></li>
    <li>属性
        <p><sapn class="time1">count</sapn>: 对象总数</p>
        <p><sapn class="time1">num_pages</sapn>: 页面总数</p>
        <p><sapn class="time1">page_range</sapn>: 页码列表，从1开始，例如[1,2,3,4]</p></li>
    <li>方法
        <p><sapn class="time1">page(num)</sapn>: 下标以1开始，如果提供的页面不存在，抛出InvalidPage异常</p></li>
</ul>

### 导入模块 Paginator
&nbsp; 空格
```views.py
from django.core.paginator import Paginator
```

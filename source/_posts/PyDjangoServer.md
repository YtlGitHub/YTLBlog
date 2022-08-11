---
title: PyDjangoServer
date: 2021-09-13 18:27:15
tags: Django
categories: server
---
# 这是一个使用python语言，Django框架编写的一个web服务
## 准备环境
<ul type="circle">
    <li>python：3.8.2</li>
	<li>django：3.2.7</li>
	<li>mysqlclient：2.0.3</li>
</ul>
查看pip已安装的插件：pip list

## Django安装
django安装命令：pip install django==3.2.*
默认会安装：Django==3.2.7、sqlparse==0.4.1 和 pytz==2021.1
检查当前是否安装Django及版本：python -m django --version

## 开始快速创建项目及app应用
### 一、创建项目
cmd终端输入命令：django-admin startproject myweb
```cmd
django-admin startproject myweb
```
进入你要做项目的目录输入命令，当然myweb是项目名，可自定义
查看目录：dir
```cmd
dir
```
会在当前目录创建myweb目录，目录里面也有一个同名的目录，你可以把他当成外面的目录当成项目容器，里面的目录当成项目，还有一些项目文件。
文件介绍：
<ul type="circle">
    <li>manage.py：一个命令行实用程序，可以让你以各种方式与此Django项目进行交互</li>
	<li>manage.py：一个命令行实用程序，可以让你以各种方式与此Django项目进行交互</li>
	<li>init.py：一个空文件，告示你Python这个目录应该被任务是一个Python包</li>
    <li>settings.py：这个是Django项目的设置/配置，Django设置会告诉你所有关于设置的工作原理</li>
    <li>urls.py：这个是Django项目的URL声明，你的Django动态网站的"目录"</li>
    <li>wsgi.py：WSGI兼容的Web服务器为你的项目提供服务的入口点，做服务搭建的时候涉及</li>
</ul>
在cd进入myweb项目目录输入命令启动服务
启动服务：python manage.py runserver

```cmd
python manage.py runserver
```
当然这个只是启动本地服务，
启动远程服务：python manage.py runserver 本地ip地址:端口
如：python manage.py runserver 192.168.43.136:8002
```cmd
python manage.py runserver 192.168.43.136:8002
```
这里会报：无效的 HTTP_HOST 标头：“192.168.43.136:8002”。您可能需要将“192.168.43.136”添加到 ALLOWED_HOSTS。
所以要在settings.py 文件里面把地址添加进去或者直接添加*表示所有ip地址都可以访问
如：
```settings.py
ALLOWED_HOSTS = ["192.168.43.136"] 或 ALLOWED_HOSTS = ["*"]
```
这样在同一网络下别人也可以访问你的服务了

### 二、创建app：python manage.py startapp myapp
cd到myweb即项目目录执行命令
```cmd
python manage.py startapp myapp
```
会在当前目录下创建一个myapp目录即app即项目应用
在views.py函数里面添加试图函数：
如：
```views.py
from django.shortcuts import render
from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello World!")
```
在当前目录创建urls.py文件,并设置路由
如：
```myapp/urls.py
from django.urls import path
from . import views


urlpatterns = [
    path('', views.index, name='index'),
]
```
再在主路由myweb/urls.py文件配置一下路由：
如：
```myweb/urls.py
from django.contrib import admin
from django.urls import path, include


urlpatterns = [
    path('admin/', admin.site.urls),
    path('myapp/', include('myapp.urls'))
]
```
在运行服务查看就可以在网页显示Hello World
也可以在myapp项目应用views.py再创建一个函数来增加另外一个网页显示
如：
```myapp/viewa.py
def app02(request):
    return HttpResponse("app02")
```
再在项目路由myapp/urls.py设置网页路径
如：
```myapp/urls.py
urlpatterns = [
    path('', views.index, name='index'),
    path('app02', views.app02, name='app02'),
]
```
在下面多加一个path路径'app02'表示网页地址路径，views.app02表示展示调用项目应用里面的函数，name='app02'表示给该路径取个名字自己定义，可以用来反向获取URL
		
### 三、项目的模型
1、连接数据库
在settings.py文件里面配置一下我们需要连接的数据库
如：
```settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
把这个替换成
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mydemo',
        'USER': 'root',
        'PASSWORD' 'ytl',
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```
2、下载mysqlclient，执行mysql需要用到，一个加载模块
```cmd
pip install mysqlclient
```
把之前的pymysql改成mysqlclient
3、在models.py文件里面创建数据类
如：
```models.py
class Stu(models.Model):
    '''自定义Stu表对应的Model类'''
    # 定义属性：默认主键自增id字段可不写
    id = models.AutoField(primary_key=True)
    name = models.CharField('姓名', max_length=16)
    age = models.SmallIntegerField('年龄', )
    sex = models.CharField('性别', max_length=1)
    classid = models.CharField('班级', max_length=8, blank=True)  # blank=True 表示可以为空 默认不能为空
    # 定义默认输出格式
    def __str__(self):
        return "%d:%s:%d:%s:%s:"%(self.id,self.name,self.age,seek.sex,self.classid)
    # 自定义对应的表明，默认表名：myapp_stu    
    class Meta:
        db_table="stu"
        verbose_name = '杨天龙样机表'  # 自定义表名
        verbose_name_plural = '杨天龙样机管理'  # 自定义库名
```
4、在settings.p配置文件里面把myapp应用添加进去
如：
```settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'myapp',
]
```

### 四、启动网站admin管理
先介绍一下在settings.py文件里面有添加自己的app
```myweb/settings.py
INSTALLED_APPS = [
    'django.contrib.admin', # 管理网站
    'django.contrib.auth', # 认证系统
    'django.contrib.contenttypes',  # 内容类型的框架
    'django.contrib.sessions',  # 会话框架
    'django.contrib.messages',  # 消息框架
    'django.contrib.staticfiles',  # 管理静态文件的框架
    'myapp',  # 自己增加的app
]
```
利用models.py文件生成数据库表之前，是需要手动创建数据库
create database mysite
创建完mysite库之后，我们在终端执行如下命令，他们的作用就是将models文件生成一个迁移文件
python manage.py makemigrations
迁移文件生成完毕，执行
python manage.py migrate
会将迁移文件的内容作用到数据库中，生成表或者修改字段熟悉
#### 1、数据迁移：python manage.py migrate
出现这个说明成功：
```cmd
Operations to perform:
      Apply all migrations: admin, auth, contenttypes, sessions
    Running migrations:
      Applying contenttypes.0001_initial... OK
      Applying auth.0001_initial... OK
      Applying admin.0001_initial... OK
      Applying admin.0002_logentry_remove_auto_add... OK
      Applying admin.0003_logentry_add_action_flag_choices... OK
      Applying contenttypes.0002_remove_content_type_name... OK
      Applying auth.0002_alter_permission_name_max_length... OK
      Applying auth.0003_alter_user_email_max_length... OK
      Applying auth.0004_alter_user_username_opts... OK
      Applying auth.0005_alter_user_last_login_null... OK
      Applying auth.0006_require_contenttypes_0002... OK
      Applying auth.0007_alter_validators_add_error_messages... OK
      Applying auth.0008_alter_user_username_max_length... OK
      Applying auth.0009_alter_user_last_name_max_length... OK
      Applying auth.0010_alter_group_name_max_length... OK
      Applying auth.0011_update_proxy_permissions... OK
      Applying auth.0012_alter_user_first_name_max_length... OK
      Applying sessions.0001_initial... OK
```
#### 2、创建超级用户：python manage.py createsuperuser
```cmd
Username (leave blank to use 'administrator'): admin # 输入用户名
	Email address: 1766842398@qq.com  # 邮箱
	Password:  # 密码
	Password (again): # 确认密码
	The password is too similar to the username.
	This password is too common.
	Bypass password validation and create user anyway? [y/N]: y # 是否跳过密码验证并创建用户？选择是就行
	Superuser created successfully. # 创建超级用户成功
```
#### 3、设置中文
上面创建完成超级用户登录进去之后会发现都是英文，这里也可以设中文，在settings.py配置文件里面修改一下就行了。
如：
```myweb/settings.py
LANGUAGE_CODE = 'zh-hans'
TIME_ZONE = 'Asia/Shanghai'
```
在访问就成中文了
#### 4、配置静态文件
```
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
```

#### 5、将我们自定义的应用程序加入到后台管理
打开myapp/admin.py 文件，导入自己定义好的数据类，在添加到管理员里面
```myapp/admin.py
from myapp.models import Stu
admin.site.register(Stu)
```
#### 6、更深入设计后台管理
因为直接用的话后台管理不太美观所有我们自己设计一下
再次打开myapp/admin.py 文件，在添加自定义管理员界面
如：
```myapp/admin.py
@admin.register(Stu)
class StuAdmin(admin.ModelAdmin):
    # listdisplay设置要显示在列表中的字段(id字段是Django模型的默认主键)
    list_display = ('id', 'name', 'sex', 'age', 'classid')
    # 设置哪些字段可以点击进入编辑界面
    list_display_links = ('id', 'name')
    # list_per_page设置每页显示多少条记录，默认是100条
    list_per_page = 10
    # ordering设置默认排序字段，负号表示降序排序
    ordering = ('id',)  # -id降序
    # list_editable 设置默认可编辑字段
    # list_editable = ['age', 'sex', 'classid']
```


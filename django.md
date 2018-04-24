## Django 简介
 **Django** 是一个开放源代码的Web应用框架，由 **Python** 写成。Django采用了 **MVC** 的软件设计模式，即**模型Model**，**视图View**和**控制器Controller**。
###什么是MVC  ##
 mvc全名是Model View Controller,是模型（model）----视图（view）--控制器（controller）的缩写，一种软件设计典范，用一种业务逻辑，数据，界面显示分离的方法组织代码，将业务逻辑聚集到一个部件中，在改进和个性化定制界面及用户交互的同事，不需要重新编写业务逻辑。MVC被独特的发展气力啊用于映射传统的输入，处理和输出功能在一个逻辑的图形化用户界面的结构中。
### mvc编程模式 ##
 - **Model**（模型）表示应用程序的核心（比如数据库记录列表）
 - **View**（视图）显示数据（数据库记录）
 - **Controller**（控制器）处理输入

**Model（模型）** 是应用程序用于处理应用程序数据逻辑的部分。通常模型对象负责在数据库中存取数据。
**View（视图）** 是应用程序中处理数据显示的部分。通常视图是依据模型数据创建的。
**Controller（控制器）** 是应用程序中处理用户交互的部分。通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据

## Django 的模式 - MVT模式简介 ##

 - M表示的是model，和MVC中的M功能差不多，负责和数据库交互，进行数据处理。
 - V表示的是View， 和MVC中的C的功能差不多，负责业务逻辑并适当调用Model和Template。
 - T表示的是Tempate，和MVC中的V功能差不多，负责封装构造要返回的HTML。
 >注意：Django中还有一个url分发器，也叫做路由，主要用于将url请求发送给不同的View处理，View在进行相关的业务逻辑处理。

## Django 创建第一个项目 ##

### 配置虚拟环境
先在windows环境下配置虚拟环境
```
pip install virtualenv    # 安装虚拟环境
virtualenv --no-site-packages -p xxxx env    # 在xxxx路径的env文件夹下创建虚拟环境
cd xxxx/env/Scripts    # 进入env文件夹里的Scripts文件夹
activate    # 进入虚拟环境
pip install django==1.11    # 在虚拟环境安装Django
pip install pymysql    # 安装pymysql
```
### 配置数据库
我们在项目的 **settings.py** 文件中找到DATABASES配置项，将其信息修改为：

```
DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.mysql',
		'NAME': 'test',
		'USER': 'test',
		'PASSWORD': '123456',
        'HOST':'localhost',
        'PORT':'3306',
	}
}
```
上面数据包含数据库名称和用户的信息，它们与MySQL中对应数据库和用户的设置相同。Django根据这一设置，与MySQL中相应的数据库和用户连接起来。
### 创建app

配置好虚拟环境后，创建文件夹，按以上方式进入虚拟环境后进入用于储存项目的文件夹并创建项目
```
django-admin startproject helloworld    # 创建项目helloworld
python manage.py runserver 8080    # 启动django项目并使项目可以通过8080端口被连接
python manage.py startapp test    # 创建名为test的app
```
我们修改 **test / models.py** 文件，代码如下：

```
# models.py
from django.db import models
 
class Test(models.Model):
    name = models.CharField(max_length=20)
```

以上的类名代表了数据库表名，且继承了**models.Model**，类里面的字段代表数据表中的字段（name），数据类型则由**CharField**（相当于varchar）、DateField（相当于datetime），**max_length**参数限定长度。接下来在 **settings.py** 中找到INSTALLED_APPS这一项，如下：

```
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'test',               # 添加此项
```
在命令中运行：

```
$ python manage.py migrate   # 创建表结构

$ python manage.py makemigrations TestModel  # 让 Django 知道我们在我们的模型有一些变更
$ python manage.py migrate TestModel   # 创建表结构
```
看到几行 “Creating table...” 的字样，你的数据表就创建好了。
>注意：尽管我们没有在models给表设置主键，但是Django会自动添加一个id作为主键。
### 数据库操作
接下来我们在 helloworld 目录中添加 testdb.py 文件，并修改urls.py：

```
# helloworld/helloworld/urls.py：文件代码
from django.conf.urls import *
from . import view,testdb
 
urlpatterns = [
    url(r'^hello$', view.hello),
    url(r'^testdb$', testdb.testdb),
]
```
### 添加数据
添加数据需要先创建对象，然后再执行 **save** 函数，相当于SQL中的 **insert**：

```
# -*- coding: utf-8 -*-
 
from django.http import HttpResponse
 
from test.models import Test
 
# 数据库操作
def testdb(request):
    test1 = Test(name='Jack')
    test1.save()
    return HttpResponse("<p>数据添加成功！</p>")
```
访问 **http://127.0.0.1:8000/testdb** 就可以看到数据添加成功的提示。

# Django搭建简易博客

[TOC]

`python -m django --version` 查看django是否安装成功

`django-admin startproject myblog` 当前目录下创建项目



## 创建应用

###创建步骤

1. 打开命令行，进入项目中manage.pyde同级目录
2. 命令行输入：python manage.py startapp blog
3. 添加应用名到settings.py中的INSTALLED_APPS



### 创建第一个页面（响应）

#### 编辑blog.views

- 每个响应对应一个函数，函数必须返回一个响应

  - 函数必须存在一个参数，一般约定为request


- 	每个响应对应一个URL

####配置urls.py

- 	每个URL	都以url的形式写出来


- 	url函数放在urlpatterns列表中


- 	url函数三个参数：URL（正则），对应方法。名称

####第二种URL配置

#####包含其他URL

​	1. 在根urls.py中引入include

​	2. 在APP目录下创建urls.py文件，格式与根目录的urls.py相同

​	3. 根urls.py中url函数第二个参数改为include('blog.urls')

#####注意事项

- 	根urls.py针对APP配置URL名称，是该APP所有URL的的总路径


- 	配置分URL时注意正则表达式结尾符号$和/





##开发第一个Template

###步骤

1.	在APP的根目录下创建名叫Template的目录


2.	在该目录下创建HTML文件


3.	在view.py中返回render()

###DTL初步使用

- render()函数中支持一个dict类型参数
- 该字典是后台春松到模版的参数，键为 参数名
- 在模版中使用{{参数名}}在直接使用

###Django查找Template

- Django按照INSTALLED_APPS中的添加顺序查找Templates
- 不同APP下Templates目录中的同名.html文件会造成冲突
- 解决Templates冲突方法

  1.在APP的Templates目录下创建以APP名为名称的目录


2.	将html文件放假新创建的目录下





##Models介绍

###Django中的Models是什么？

> 通常，一个Model对应数据库的一张数据表
>
> Django中Models以类的形式表现
>
> 它包含了一些基本字段以及数据的一些行为

###ORM

> 对象关系映射（Object Relation Mapping）
>
> ​	实现了对象和数据库之间的映射
>
> ​	隐藏了数据访问的细节，不需要编写SQL语句

###编写Models

####步骤

​	1.在应用根目录下创建models.py，并引入models模块

​	2.创建类，继承models.Model，该类即是一张数据表

​	3.在类中创建数据字段

###字段创建

​	字段即类里面的属性（变量）

​	attr = models.CharField(max_length=64)

​	https://docs.djangoproject.com/en/1.10/ref/models/fields/

####步骤

​	命令行中进入manage.py同级目录

​	执行python manage.py makemigrations app名 （可选）

​	再执行python manage.py migrate

####查看

​	Django会自动在app/migrations/目录下生成移植文件

​	执行python manage.py sqlmigrate 应用名 文件id 查看SQL语句

####查看并编辑db.sqllite3

​	使用第三方软件

​	SQLite Expert Personal

###页面呈现数据

####后台步骤

​	views.py中import models

​	article = models.Article.objects.get(pk=1)

​	render(request,page,{'artucle':article})



##Admin简介

> 什么是Admin？
>
> Admin是django自带的一个功能强大的自动化数据管理界面
>
> 被授权的用户可直接在Admin中管理数据库
>
> django提供了许多对Admin的定制功能
>
> 

###创建用户

python manage.py createsuperuser 创建超级用户 

localhost:8000/admin/	Admin入口

修改settings.py中LANGUAGE_CODE = 'zh-Hans'

账号名：admin

邮箱：1443818971@qq.com

密码：wuxiansong

###配置Admin

####配置应用

​	在应用下admin，py中引入自身的models模块（或里面的模型类）

​	编辑admin.py : admin.site.register(models.Article)

####修改数据

​	点击Article超链接进入Article列表页面

​	点击任意一条数据，进入编辑页面修改

​	编辑页面下面一排按钮可执行相应操作

####修改数据默认显示方法

#####步骤

​	在Article类下添加一个方法

​	根据python版本选择`__str__(self)`或`__unicode__(self)`

​	  return self.title



##博客文章页面

###URL传递参数

​	参数写在响应函数中request后，可以有默认值

​	URL正则表达式：`/article/(?P<article_id>[0-9]+)/$`

​	URL正则表达式中的组名必须和参数名一致

###Django中的超链接

####超链接目标地址

​	href后面面试目标地址

​	template中可以用`{% url 'app_name:url_name' param%}`

​	其中appp_name 和 url_name都在url中配置

####url函数的名称参数

​	根urls，写在include()的第二个参数位置，namespace='blog'

​	应用下则写在url()的第三个参数位置，name='article'

​	主要取决于是否使用include引用了另一个url配置文件



## 博客撰写页面

### 编辑响应函数

​	使用`request.POST['参数名']`获取表单数据

​	或者`request,POST.get('参数名')`

​	`models.Article.object.create(title, content)`创建对象

​	涉及到POST方法提交表单的，前端页面加上`{% csrf_token %}`



## 补充内容

### Templates过滤器

#### 什么是过滤器？

​	写在模版中，属于Django模版语言

​	可以修改模版中的变量，宠儿显示不同的内容

#### 怎么使用过滤器

`{{value | filter}}`

过滤器可叠加：`{{value | filter1 | filter2}}`

默认值过滤器：`{{|defaules:''}}`



### Django Shell

Python的交互式命令行程序

它自动引入了项目环境

#### 如何使用Django Shell

`python manage.py shell`

`from blog.models import Article`

`Article.object.all()`

#### 有什么用

测试Django新功能



### Admin

#### 创建admin配置类

`class ArticleAdmin(admin.ModelAdmin)`

注册：`admin.site.register(Article, ArtilceAdmin)`

#### 显示其他字段

`list_display = ('title', 'content')`

`list_display`同时支持tuple和list



#### 过滤器

`list_filter = ('title', )`
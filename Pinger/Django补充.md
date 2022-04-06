## non-named regular expression group(无命名分组)

``````python
//view.py

def time (request, year, month):
    return HttpResponse('%s,%s' % (year, month))
  
//urls.py

re_path('([0-9]{4})/([0-9]{2})/', views.time, )
``````

- 通过参数的位置来进行参数传递



##  named regular expression group(命名分组)

`````python
正则表达式
?P<name>pattern
`````

- view中的函数参数名要与name一致





##request方法

### redirect()

- 使用``return redirect(/url/)``跳转页面

### local()

- 将函数中的局部变量打包成字典，键值对的形式返回
- 键名与变量名一致



## Templates

Templates中用`.`进行子元素选中



## 标签、过滤器

``{% if %} {% endif %}``

``{% for %} {% endfor %}``

``{% url 'address' %}``

``{% with A = abcdefghijklmnopqrstuvwxyz %}`

将长变量名替换为短变量名

``{% load %``

加载标签库，更多是与自定义fliter和tag

### 自定义fliter

- 在app目录下创建``templatetags``目录（必须的名字）

- 在``templatetag``下创建任意的.py文件

  ```python
  //my_filiter.py
  from django import template
  from django.utils.safestring import mark_safe

  register = template.Library()
  以上是固定写法 
  @register.filter()#自定义过滤器
    def multi(value1, value2): #value2可以为数组、元组、数值等，用value2[0]方法选中  
    return value1 * value2
    
  @register.simple_tag()#自定义标签
    def simple_multi(value1, value2):
    return value1 * value2
          
  ```

  ```python
  //html
   {% load my_filter %}
   <h1>{{ value| multi:2}}</h1>
   <h1>{% simple_multi 2 3 %}</h1>
  ```

  ####总结

- filter的参数是固定的，只能有两个，第二个为``:``后的数值

- simple_tag的参数个数不固定，有多少个形式参数，就传递多少个实际参数





## MODEL

在终端中显示具体执行的sql语句

```python
//setting.py中添加
LOGGING = {
    'version': 1,
    'disable_existing_loggers': False,
    'handlers': {
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
        },
    },
    'loggers': {
        'django.db.backends': {
            'handlers': ['console'],
            'propagate': True,
            'level':'DEBUG',
        },
    }
}
```

### view.py

数据库增加项

``models.Author.objects.create(c1=value, )``

数据库删除项

``Author.objects.filter(name=username).delete``

数据库修改项

``models.AuthorDetail.objects.filter(author_id_id=id).update(addr=address)``

数据库查找项

````python
Author.objects.filter(name=username).values() #返回一个字典
Author.objects.filter(name=username).values_list() #返回一个列表
````



注意models.py

``author_id = models.OneToOneField(Author, on_delete=models.CASCADE)``

 on_delete=models.CASCADE面对应用层，不是db层，使用应用层模仿db层动作，故sql语句不能实现
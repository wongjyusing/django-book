### 关于Django的原理  
先看一下，我们之前的过程做了什么？  
* 生成了一个名为**mysite**的Django项目  
* 创建了一个名为blog的应用  
* 编写模型文件
* 编写视图文件  
* 定义路由  
* 注册模型到Django（admin.py）后台中  
* 到Django的后台填写内容  
* 编写模板文件  
* 前端显示内容。   

现在请大家思考几个问题：  
* 为什么在后台，网页会知道我们要填写的字段？？
* 我们在后台填写的数据存放到哪里了？？  
* 为什么打开`http://127.0.0.1:8000`是博客列表，`http://127.0.0.1:8000/detail/111`是文章详情页？  
* 为什么在`http://127.0.0.1:8000`可以看到文章的标题？且点击该标题可以链接到该文章的详情页？  

### 解答  
#### 为什么在后台，网页会知道我们要填写的字段？？  
这个就是Django自带的**ORM**（Object Relational Mapping），对象关系映射。  
当我们写好models.py的代码后，在admin.py中注册了我们的models.py中的类方法。  
所以在后台可以看到我们models.py的字段。   
#### 我们在后台填写的数据存放到哪里了？？  
大家现在看一下**根目录**。是不是多了一个名为**db.sqlite3**的文件。  
这个文件就是用来存储我们在后台填写的数据。  
当然，也可以换成其他的关系型数据库，例如MySQL等。  
这个是后面的话题了。现在我们开发过程，只需要sqlite3就够了。  
#### 为什么打开`http://127.0.0.1:8000`是博客列表？？  
这是因为项目中mysite目录下的**urls.py**文件所定义的。  
注意：mysite目录下的**urls.py**是包括了blog目录下的urls.py。  
如果不采取**include**这个方法。在mysite目录下的**urls.py**是写成下面的例子。  
```python
"""mysite URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/2.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
# 从django的核心方法导入管理员方法
from django.contrib import admin

# 从django的路由方法导入 路径、正则路径 方法
from django.urls import path,re_path

from blog import views
# 路由列表
urlpatterns = [
    path('',views.blog_list,name='blog_list'),
    re_path('detail/(?P<slug>[\w-]+)/', views.blog_detail, name='detail'),

    # 下面的这个路径是后台管理路径，后面我们写博文都在这个网址写
    # http://127.0.0.1:8000/admin
    path('admin/', admin.site.urls),
]
```  
由于正常项目中不可能只有一个**应用**。为了方便区分，所以用到include方法。
从上面的代码可以看到，在`path('',views.blog_list,name='blog_list'),`中。  
第一个参数定义为**空**，表明它是**根路径**。  
第二个参数是该路径绑定的方法，方法是views.py中定义的**blog_list**函数。
这个函数返回的是一个关于Blog中所有对象的**列表**。  
第三个参数等到下一个问题会解答。  

当我们在浏览器打开`http://127.0.0.1:8000`的时候，相当于发送了一个请求给Django服务器。  
Django服务器接收到这个请求，就会在路由配置中寻找，**有没有这条路径？**，我们这里是有的。  
所以Django服务器找到了`http://127.0.0.1:8000`对应的方法是**blog_list**方法。然后根据该方法返回内容。  
***
文章详情页也是同理。  
当我们在浏览器打开`http://127.0.0.1:8000/detail/111`的时候。  
注意，Django是先匹配有没有符合`http://127.0.0.1:8000/detail/xxx`的格式的路径。  
然后`xxx`会传入到该路径方法的blog_detail方法中。  
然后在数据库中寻找是否有**文章的slug和传入的xxx**，如果有则返回该对象，没有则返回404给用户。  
#### 为什么在`http://127.0.0.1:8000`可以看到文章的标题？  
这是因为我们的模板文件**blog_list.html**。  
看下面的代码：
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
    <head>
        <meta charset="utf-8">
        <title>博客列表</title>
    </head>
    <body>
        {% for blog in blogs %}
            <a href="{% url 'detail' blog.slug %}">{{ blog.title }}</a>
        {% endfor %}
    </body>
</html>
```
上面的**blogs**是我们在views.py中定义的字典的键名。  
由于blogs是一个列表。所以需要for循环来遍历出所有的对象。  
下面的a标签是什么意思呢？？  
a标签是html中的超链接语句。会链接到其他的页面。  
里面的**href**参数是链接地址。  
`{% url 'detail' blog.slug %}`是Django的模板语法。  
可以改写成`http://127.0.0.1:8000/detail/{{blog.slug}}`也可以实现同样的效果。  
这里涉及到**自定义模板标签的知识**，现在知道一下就好了，后面会介绍。   
`{{ blog.title }}`是索引该文章的**标题**。  
这里很像javascipt的**字典语法**。  
其实在blog_list函数返回的内容中的**context**这个字典是下面例子这样的格式。  
```python
context = {
    'blogs':[
        {
        'title':'第一篇博文'，
        'slug':'111',
        'body':'# 龙虾伊面真好吃
## 龙虾伊面真好吃
### 龙虾伊面真好吃
#### 龙虾伊面真好吃
##### 龙虾伊面真好吃
###### 龙虾伊面真好吃
[我是超链接](http://127.0.0.1:8000)',
        'author':'sing',
        'body_markdown':'<h1 id="_1">龙虾伊面真好吃</h1>
<h2 id="_2">龙虾伊面真好吃</h2>
<h3 id="_3">龙虾伊面真好吃</h3>
<h4 id="_4">龙虾伊面真好吃</h4>
<h5 id="_5">龙虾伊面真好吃</h5>
<h6 id="_6">龙虾伊面真好吃</h6>
<p><a href="http://127.0.0.1:8000">我是超链接</a></p>',


        },{
        'title':'第二篇博文'，
        'slug':'222',
        'body':'# 龙虾伊面不好吃
## 龙虾伊面不好吃
### 龙虾伊面不好吃
#### 龙虾伊面不好吃
##### 龙虾伊面不好吃
###### 龙虾伊面不好吃
[我是超链接](http://127.0.0.1:8000)',
        'author':'sing',
        'body_markdown':'<h1 id="_1">龙虾伊面不好吃</h1>
<h2 id="_2">龙虾伊面不好吃</h2>
<h3 id="_3">龙虾伊面不好吃</h3>
<h4 id="_4">龙虾伊面不好吃</h4>
<h5 id="_5">龙虾伊面不好吃</h5>
<h6 id="_6">龙虾伊面不好吃</h6>
<p><a href="http://127.0.0.1:8000">我是超链接</a></p>',


        },
    ]
}
```
当然还有很多内容。就不一一展示了。  
举个例子，python中字典的语法是：
```python
context = {}
context = {
    'title':'bobo',
    'slug':'111',
    'author':'sing'
    }
print(context['title'])  
print(context['slug'])
```
而在javascipt呢，`context['title']`，`context.title`。  
两种方法都行。  
简单来说，在模板上，我们需要后台的什么数据，例如需要作者信息，就`{{ blog.author}}`。  
只要后台有，就可以通过模板语句的`{{ object_name.key_name }}`来获取内容。  

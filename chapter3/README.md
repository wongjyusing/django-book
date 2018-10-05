## 模板语法  
继续之前的**最简单的博客**。  
可能之前的的讲解，看着有点懵。  
不过，没关系。我们先修改一下之前的代码。  
并引入**静态文件**，让我们更直观的观察页面来讲解。
### views.py
打开blog下的views.py
```python
# 从django的快速方法中导入 渲染、获取对象（获取到就返回对象、获取不到就返回404）
from django.shortcuts import render,get_object_or_404
import markdown
# 从django的核心中的分页器 导入 分页器、空页码、页码不为整数  等方法
from django.core.paginator import Paginator, EmptyPage, PageNotAnInteger

# 从当前目录下导入models.py 中的Blog类方法
from .models import Blog

# 分页通用函数，第一个参数是内容列表，第二个参数是页码
def page_gen(contact_list,page):
    # 分页规则 ，第一个参数，内容列表，第二个参数，每页多少内容。第三个参数，孤儿参数
    # 孤儿参数： 假如现在总共有12篇博文，正常来说每页有10篇博文，那么总页码应该有两页。
    # 但采取了孤儿参数后，第一页会有12篇博文，没有了第二页。
    # 除非有13篇博文，才会出现第二页，且第一页为10篇文章，第二页为2篇文章
    paginator = Paginator(contact_list, 10,2)
    try:
        contacts = paginator.page(page)
    except PageNotAnInteger:
        # 如果传入的参数不是整数类型，返回第一页内容
        # 假如用户输入page=abcd或者其他字符，返回第一页内容
        contacts = paginator.page(1)
    except EmptyPage:
        # 如果传入的页码大于最大的页码数，返回最后一页的内容
        # 现在总共有5页，如果用户输入第6页则返回第5页的内容
        contacts = paginator.page(paginator.num_pages)

    return contacts

# 首页
def home(request):
    return render(request, 'home.html')



# 所有博客列表，接收的参数为 请求。
# 在django的语法中，这个参数是必须的
def blog_list(request):
    # 生成一个空字典，用于包裹内容
    context = {}
    # 获取Blog中的所有对象
    contact_list = Blog.objects.all()

    # 从请求中查找是否有page参数，如果有则取page的参数
    # 没有则取1
    page = request.GET.get('page',1)

    # 用键名blog接收 page_gen返回的内容
    context['blogs'] = page_gen(contact_list,page)

    # 返回  渲染    请求       模板文件          内容
    # 在blog_list.html上渲染context的内容，返回给用户
    return render(request, 'blog_list.html', context)

# 文章详情页面
def blog_detail(request,slug): # 接收请求和slug参数
    context = {}  # 生成一个空字典
    md = markdown.Markdown(extensions=[
            'markdown.extensions.extra',
            'markdown.extensions.codehilite',
            'markdown.extensions.toc',
            ])
    # 在Blog也就是我们的数据库中寻找有没有slug字段和传进来的slug字段匹配的
    # 没有，则返回404,
    # 有则返回该对象的内容
    blog = get_object_or_404(Blog, slug=slug)
    blog.body = md.convert(blog.body)
    context['toc'] = md.toc
    context['blog'] = blog
    #return render(request,'test.html',context)
    return render(request,'blog_detail.html',context)
```  
### urls.py
打开blog目录下的urls.py
```python
# 从django的路由方法导入 路径、正则路径方法
from django.urls import path,re_path
# 从当前目录下导入views.py的内容
from . import views

urlpatterns = [

    path('',views.home,name='home'),

    path('blog/', views.blog_list,name='blog_list'),

    re_path('detail/(?P<slug>[\w-]+)/', views.blog_detail, name='detail'),
]
```   
### 首页 home.html  
在templates目录新建一个home.html文件
复制下面的代码：
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>首页</title>
    <link rel="stylesheet" href="/static/css/base.css">
    <link rel="stylesheet" href="/static/css/blog.css">
    <link rel="stylesheet" href="/static/css/bootstrap.min.css">
    <script type="application/javascript" src="/static/js/jquery.min.js"></script>
    <script type="application/javascript" src="/static/js/bootstrap.min.js"></script>
</head>
    <body>
        <nav class="navbar navbar-inverse">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" href="{% url 'home' %}">阿星的博客</a>
                </div>
                <div id="navbar" class="collapse navbar-collapse">
                    <ul class="nav navbar-nav">
                        <li class="active"><a href="{% url 'home' %}">首页</a></li>
                        <li><a href="{% url 'blog_list' %}">博客列表</a></li>
                        <li><a href="#about">About</a></li>

                    </ul>
                </div><!--/.nav-collapse -->
            </div>
        </nav>

        <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-10">
                发挥你的想象力
            </div>
            <div class="hidden-xs col-sm-2">
                占位，留待后续内容
            </div>
        </div>
    </div>

    <footer>
        <div class="author-date">
            <p>Copyright © 2018 <a href="https://github.com/wongjyusing">Sing</a>. Powered by Django2.1</p>
            <p>粤ICP备18079962号</p></div>
    </footer>

    </body>
</html>
```
### blog_list.html
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>博客列表</title>
    <link rel="stylesheet" href="/static/css/base.css">
    <link rel="stylesheet" href="/static/css/blog.css">
    <link rel="stylesheet" href="/static/css/bootstrap.min.css">
    <script type="application/javascript" src="/static/js/jquery.min.js"></script>
    <script type="application/javascript" src="/static/js/bootstrap.min.js"></script>
</head>
    <body>
        <nav class="navbar navbar-inverse">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" href="{% url 'home' %}">阿星的博客</a>
                </div>
                <div id="navbar" class="collapse navbar-collapse">
                    <ul class="nav navbar-nav">
                        <li><a href="{% url 'home' %}">首页</a></li>
                        <li class="active"><a href="{% url 'blog_list' %}">博客列表</a></li>
                        <li><a href="#about">About</a></li>

                    </ul>
                </div><!--/.nav-collapse -->
            </div>
        </nav>

        <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-10">
                {% for blog in blogs %}
                <div class="jumbotron">
                    <h2>{{blog.title}}</h2>
                    <p>{{blog.body_markdown|safe|truncatechars:120|striptags}}</p>
                    <p>
                      <a class="btn btn-lg btn-primary" href="{% url 'detail' blog.slug %}" role="button">阅读全文</a>
                    </p>
                </div>
                {% endfor %}
            </div>
            <div class="hidden-xs col-sm-2">
                占位，留待后续内容
            </div>
        </div>
    </div>

    <footer>
        <div class="author-date">
            <p>Copyright © 2018 <a href="https://github.com/wongjyusing">Sing</a>. Powered by Django2.1</p>
            <p>粤ICP备18079962号</p></div>
    </footer>

    </body>
</html>
```
### blog_detail.html
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>博客列表</title>{# 改我 #}
    <link rel="stylesheet" href="/static/css/base.css">
    <link rel="stylesheet" href="/static/css/blog.css">
    <link rel="stylesheet" href="/static/css/bootstrap.min.css">
    <script type="application/javascript" src="/static/js/jquery.min.js"></script>
    <script type="application/javascript" src="/static/js/bootstrap.min.js"></script>
</head>
    <body>
        <nav class="navbar navbar-inverse">
            <div class="container">
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#navbar" aria-expanded="false" aria-controls="navbar">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" href="{% url 'home' %}">阿星的博客</a>
                </div>
                <div id="navbar" class="collapse navbar-collapse">
                    <ul class="nav navbar-nav">
                        <li><a href="{% url 'home' %}">首页</a></li>
                        <li class="active"><a href="{% url 'blog_list' %}">博客列表</a></li>
                        <li><a href="#about">About</a></li>

                    </ul>
                </div><!--/.nav-collapse -->
            </div>
        </nav>

        <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-10">
                <div class="panel panel-default">
                    <div class="panel-heading">
                        <h3 class="panel-title" style="text-align: center;font-size: 40px">{{ blog.title }}</h3>
                    </div>
                    <div class="panel-body">
                        {{ blog.body|safe }}
                    </div>
                </div>
            </div>
            <div class="hidden-xs col-sm-2">
                {{toc|safe}}
            </div>
        </div>
    </div>

    <footer>
        <div class="author-date">
            <p>Copyright © 2018 <a href="https://github.com/wongjyusing">Sing</a>. Powered by Django2.1</p>
            <p>粤ICP备18079962号</p></div>
    </footer>

    </body>
</html>
```  
## 静态文件  
关于静态文件，就不复制了，大家到[百度网盘](https://pan.baidu.com/s/1PM4q9R-6S2yR3O_KyiUobQ)或[GitHub](https://github.com/wongjyusing/django_project/tree/master/%E6%A8%A1%E6%9D%BF%E8%AF%AD%E5%8F%A5%E7%BB%83%E4%B9%A0/mysite/static)下载吧。  
下载后替换掉**static**目录的内容即可。  
运行项目后，看一下效果吧。  
这里采取的是**Bootstrap3**框架。这是一款CSS框架。  
虽然现在有Bootstrap4，但由于python的markdown模块暂时不支持**html5**的格式。  
所以采取**Bootstrap3**。  
## 总结  
其实用Django搭建博客项目。大多数时间，都是和html、css、js打交道。  
后端代码量不是很多。  
我们大部分工作都是在写一个**容器**（我们的模板文件）。  
写好我们的容器后，装载我们的内容。  
现在大家可以尝试把**创建时间**、**作者**、**修改时间**等信息添加到页面上。  
以blog_detail.html为例。  
大家可以把`<title>博客列表</title>`的部分，换成文章的标题。  
改成`<title>{{ blog.title }}</title>`即可。  

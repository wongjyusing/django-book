## 模板拓展语法  
大家有没有发现，在上一篇文章中，home.html、blog_list.html、blog_detail.html。  
它们在内容中，是有很多一模一样的代码。  
在三个页面上，除了页面中部的内容，其他地方几乎是大同小异。  
假如，现在要修改一下，导航栏部分。  
修改的时候，就要修改三个文件。如果有更多文件呢？  
这个时候就需要**模板拓展语法**来减少我们的代码量和提高代码可维护性。  
## 基本模板base.html
首先，让我们在templates目录下生成一个新的base.html文件。  
复制home.html的内容到base.html中。
现在以**home.html**为例。   
注意下面代码中的注释，**找出三个文件中不相同的地方**。
```html
<!DOCTYPE html>
{# 这是base.html #}
<html lang="en" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>首页</title>{# 不同的地方 #}

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

                        <li class="active"><a href="{% url 'home' %}">首页</a></li> {# 不同的地方 有一个选中的效果 #}

                        <li><a href="{% url 'blog_list' %}">博客列表</a></li>{# 不同的地方 有一个选中的效果 #}


                        <li><a href="#about">About</a></li>{# 不同的地方 有一个选中的效果 #}

                    </ul>
                </div><!--/.nav-collapse -->
            </div>
        </nav>

        <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-10">
                发挥你的想象力{# 不同的地方 内容不一样 #}
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
当找出上方代码中不同的地方后，就开始写我们的拓展语法。  
现在我们可以，以home.html和base.html作为练习。  
例如，title标签里的内容。把里面的**首页**替换成:  
`{% block title %}{% endblock %}`  
大家可以先把下面的内容分别复制到 home.html和base.html后再进行讲解。  
### base.html
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{% block title %}{% endblock %}</title>
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
                        <li {% block home_activate %}{% endblock %}><a href="{% url 'home' %}">首页</a></li>
                        <li {% block blog_activate %}{% endblock %}><a href="{% url 'blog_list' %}">博客列表</a></li>
                        <li {% block about_activate %}{% endblock %}><a href="#about">About</a></li>

                    </ul>
                </div><!--/.nav-collapse -->
            </div>
        </nav>

        <div class="container">
        <div class="row">
            <div class="col-xs-12 col-sm-10">
                {% block content %}{% endblock %}
            </div>
            <div class="hidden-xs col-sm-2">
                {% block right_tags %}{% endblock %}
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
### home.html
```html
{# 继承的模板文件 #}
{% extends 'base.html' %}   

{# 页面头部标题部分 #}
{% block title %}   
    首页
{% endblock %}

{# 导航栏选中效果 #}
{% block home_activate %}   
    class="active"
{% endblock %}


{# 页面左半部分 #}
{% block content %}     
    发挥你的想象力
{% endblock %}

{# 页面右半部分 #}
{% block right_tags %}  
    占位，留待后续内容
{% endblock %}
```
### 讲解  
之前我们说home.html、blog_list.html、blog_detail.html相对于views.py中函数对应的的**容器**。  
***
而现在，base.html是想对于**home.html、blog_list.html、blog_detail.html**的容器。   
也可以说base.html是用于装载这个三个文件的内容的容器。  
以home.html为例。  
被`{% block title %}{% endblock %}`包裹住的内容。  
会被填写到base.html中对应的`{% block title %}{% endblock %}`中。  
其他地方同理。  
其中，**title**，这个名字来源就像是我们平时写python代码中的**变量**。  
是由我们根据内容而起的名字。最好起一些**见名知义**的变量名。   
现在看一下，blog_detail.html的代码，如下：
```html
{% extends 'base.html' %}   {# 继承的模板文件 #}

{% block title %}
    {{ blog.title }}
{% endblock %}

{% block blog_activate %}
    class="active"
{% endblock %}


{% block content %}
<div class="panel panel-default">
    <div class="panel-heading">
        <h3 class="panel-title" style="text-align: center;font-size: 40px">{{ blog.title }}</h3>
    </div>
    <div class="panel-body">
        {{ blog.body|safe }}
    </div>
</div>
{% endblock %}

{% block right_tags %}
    {{ toc|safe }}
{% endblock %}
```  
blog_list.html就留给大家练习一下。  
当我们使用拓展模板后，代码的可维护性就提高了很多了。

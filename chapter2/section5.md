## Django模板语句  
现在我们要学新的语法，jinjia2语法。  
先学三句吧。  
```html
{{ xxxx.oooo }}   {# 变量语句 #}

{% for xxxx in xxxxs %}    {# 开头除了用for 还可以用if 等控制语句 #}
    {{ xxxx }}                  {# 这里是使用变量语句 #}  
{% endfor %}               {# 如果开头用了if 结尾要改成endif #}
                           {# 大多数控制语句都需要end和语句形式结尾 #}
                           {# 也有例外，例如url，还有些语句等到后面在进行讲解 #}

{# 我就不难猜了吧，我是注释 #}
```  
## 运行项目  
其实讲了那么久，不把项目运行起来，好像有点说不过去吧。  
相信大家也看着烦了。  
先运行项目吧。  
在运行项目前，做一些必要操作。  
在项目中导入我们的blog应用。  
打开settings.py。  
找到INSTALLED_APPS，添加如下内容：
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'blog',
]
```
### 创建模板文件
在根目录下终端依次输入以下的命令  
`touch templates/blog_list.html`    
`touch templates/blog_detail.html`  
### 生成数据库迁移文件。  
`python manage.py makemigrations`  
### 迁移 迁移数据库文件  
`python manage.py migrate`  
### 创建超级管理员  
注意，记住帐号和密码，即将用到  
`python manage.py createsuperuser`  
### 启动项目  
请记住下面的这条命令  
以后经常用到。  
`python manage.py runserver`  
好了，项目启动了。用浏览器打开[http://127.0.0.1:8000/](http://127.0.0.1:8000/)。  
有没有发现一片空白啊？？  
这个因为我们还没有填写内容到后台。  
打开[http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/),用你刚才设定的帐号密码登录。  
然后写一篇博文。不会markdown语法的可以复制下面的markdown内容。  
```markdown
# 龙虾伊面真好吃
## 龙虾伊面真好吃
### 龙虾伊面真好吃
#### 龙虾伊面真好吃
##### 龙虾伊面真好吃
###### 龙虾伊面真好吃
[我是超链接](http://127.0.0.1:8000)  
```  
网页的后缀填写`111`吧，方便我后面的讲解。  
其实，现在`http://127.0.0.1:8000/`仍然为空。  
因为，我们的模板页面还没有写。  
## 模板语句初体验  
### 博客列表  
打开templates目录下的blog_list.html  
写入以下内容：
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
### 文章详情页
```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
    <head>
        <meta charset="utf-8">
        <title>{{ blog.title }}</title>
    </head>
    <body>
        <a href="{% url 'blog_list' %}">博客列表</a>
        {{ blog.body_markdown|safe }}
    </body>
</html>
```  
填写完后，只要你们之前没有停止项目。  
打开`http://127.0.0.1:8000`看看效果。  
很**样衰**对吧？？  
样衰是正常的，因为我们还没有真正使用html、css和javascipt。  
但在讲css和javascipt之前，先讲我们看到的**内容**是怎么来的。   

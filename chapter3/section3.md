## templatetags  
把终端操作目录保持在根目录下，依次执行下方的代码？？  
`mkdir toolbox/templatetags`  
`touch toolbox/templatetags/__init__.py`  
`touch toolbox/templatetags/tool_tags.py`  
这时我们的toolbox目录结构如下：
```bash
├── admin.py               # 后台管理文件
├── apps.py                # 应用配置文件
├── __init__.py            # 声明该目录是python的一个包
├── migrations             # 迁移文件目录，
│   ├── 0001_initial.py
│   ├── 0002_auto_20181004_2305.py
│   ├── __init__.py
│   └── __pycache__
│       ├── 0001_initial.cpython-36.pyc
│       ├── 0002_auto_20181004_2305.cpython-36.pyc
│       └── __init__.cpython-36.pyc
├── models.py              # 模型文件
├── __pycache__            # 运行代码后生成的组件，不需要理会
│   ├── admin.cpython-36.pyc
│   ├── __init__.cpython-36.pyc
│   └── models.cpython-36.pyc
├── templatetags            # 自定义模板标签目录
│   ├── __init__.py         # 声明该目录是python的一个包
│   └── tool_tags.py        # 自定义模板标签文件  
├── tests.py                # 测试文件
└── views.py                # 视图文件
```  
### 要点  
自定义模板标签的目录名必须为**templatetags**。  
里面包含一个\__init__.py。  
然后才是我们的自定义模板标签文件。这个文件，我们命名为**tool_tags.py**，来方便和其他文件加以区分。  
里面的内容如下：
```python
# toolbox/templaatetags/tool_tags.py
from django import template     # 从Django中导入模板方法

from ..models import HomeAndAbout,MySiteData,ToolLinks

register = template.Library()   # 注册   模板的库   Library 图书馆，可以理解为放书进去，

@register.simple_tag
def test(): # 测试
    return 'hello world'

# 返回所有的个人链接
@register.simple_tag
def get_toollinks():

    return ToolLinks.objects.all()

## 利用列表索引和函数传参的方式，返回Home或About的内容
@register.simple_tag
def get_home_or_about(num):
    return HomeAndAbout.objects.all()[num]
```  
怎么使用我们的自定义模板标签呢？？  
这里以home.html为例：
```html
{% extends 'base.html' %}   {# 继承的模板文件 #}

{% load tool_tags %}   {# 导入我们刚才定义的自定义模板标签文件 #}

{% block title %}
    首页
{% endblock %}

{% block home_activate %}
    class="active"
{% endblock %}


{% block content %}
    {% get_home_or_about 1 as home %}  {# 第一个是函数名 #}
                                       {# 第二个是需要传入的参数参数 #}
                                       {# 把函数实例化为home #}
    <p>{{ home.title }}</p>

    {{ home.body }}

{% endblock %}

{% block right_tags %}
    {% get_toollinks as links %}   {# 实例化函数为links #}

    {% for each in links %}
        <a href="{{ each.link }}">{{ each.name }}</a>
    {% endfor %}

{% endblock %}
```  

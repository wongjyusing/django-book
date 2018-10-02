## 路由urls.py  
注意，我们现在项目中有两个urls.py文件。  
一个在blog目录下，一个在mysite目录下。  
blog下的是**子路由**，mysite下的是**总路由**。  
现在，我们先写分路由。  
### 子路由  
打开blog目录下的urls.py写入下面的代码
```python
# 从django的路由方法导入 路径、正则路径方法
from django.urls import path,re_path
# 从当前目录下导入views.py的内容
from . import views

urlpatterns = [
    # 下面方法中第一个参数是路径
    # 总路由也是为空，所以我们的博客列表就是首页
    # 当我们在浏览器打开 http://127.0.0.1:8000
    # 就会发送一个请求给django
    # django就会从路由器中寻找是否有这路径的，没有就会返回404
    # 有就寻找该路径对应的方法，也就是下面的第二个参数，
    # 第三个参数，相当于是变量名，也就是说 blog_list = path('', views.blog_list)
    # 后面使用模板时会用到，现在知道一下就好了
    path('', views.blog_list,name='blog_list'),

    # re_path不难理解是正则匹配路径
    # 大家可以回头看一下views.py中的blog_detail函数
    # 它除了request参数还有一个slug参数
    # 解释一下这里的正则表达式吧：
    # 这是正则表达式匹配路径，/(?P<slug>[\w-]+)/
    # 以/开头 以/结束  括号()是括起来的的表达式作为一个分组
    # [\w-]是匹配任意字母数字和减号
    # (?P<slug>[\w-]+)就是把slug进行分组，匹配里面的[\w-]
    re_path('detail/(?P<slug>[\w-]+)/', views.blog_detail, name='detail'),
]
```  
### 总路由  
打开mysite目录下的urls.py
写入下面的代码：
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

# 从django的路由方法导入 路径、包括方法
from django.urls import path,include

# 路由列表
urlpatterns = [
    # 根路径 从blog中导入urls.py文件，
    # 这里的意思就是包括了blog中urls.py的内容
    path('',include('blog.urls')),
    # 下面的这个路径是后台管理路径，后面我们写博文都在这个网址写
    # http://127.0.0.1:8000/admin
    path('admin/', admin.site.urls),
]
```

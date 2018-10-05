## 什么是自定义模板标签？  
这个问题转化一下，**为什么需要自定义模板标签**？  
本来，这个内容要放到后面我们给博文增加标签、分类的时候再讲的。  
现在提前讲一下，当预习吧，看不懂也没关系。后续的内容还会讲解。  
***  
结合现在这个项目来讲，提一些需求吧。  
我们现在页面的**右半部分**，是空的，没有内容。  
现在我要在里面放置我的GitHub、FaceBook、微博、qq、邮箱等信息。  
不知道，大家有没有发现，我们的blog_detail.html文件的右半部分是和其他页面有所区别的？？  
blog_detail.html的右半部分，不能放上面的信息。  
怎么办呢？？  
分别写一段代码放到**blog_list.html和home.html**中？？  
我想说的是，不需要。  
我们只需要新建一个**应用**，再利用自定义模板标签即可。  
怎么用？？  
## 新应用toolbox  
把项目停止，在终端输入：  
`python manage.py startapp toolbox`  
现在我们就多了一个名为toolbox的应用。  
这个应用的作用是用于放置我们**通用的内容**。  
像上面的需求一样的内容。  
怎么写呢？？  
### models.py  
打开toolbox目录下的models.py，写入以下的内容。  
```python
from django.db import models

# 个人空间工具链接
class ToolLinks(models.Model):
    name = models.CharField(verbose_name="链接文本",max_length=50)
    link = models.CharField(verbose_name="链接",max_length=200,help_text='请填写http或https开头的完整形式地址')
    img_link = models.URLField('图片地址', help_text='请填写http或https开头的完整形式地址',blank=True)
    class Meta:
        verbose_name = '个人空间'
        verbose_name_plural = verbose_name
        ordering = ['-id']

    # 使对象在后台显示更友善
    def __str__(self):
        return self.name
# 首页和关于的页面内容
class HomeAndAbout(models.Model):
    title = models.CharField(verbose_name='标题',max_length=50)
    body = models.TextField(verbose_name='文章')
    author = models.CharField(verbose_name='作者',default='sing',max_length=50)
    created_time = models.DateTimeField(verbose_name='创建时间',auto_now_add=True)
    update_time = models.DateTimeField(verbose_name='修改时间',auto_now=True)



    class Meta:
        verbose_name = '首页和关于的页面内容'
        verbose_name_plural = verbose_name
        ordering = ['-created_time']

    # 使对象在后台显示更友善
    def __str__(self):
        return self.title



# 网站信息填写
class MySiteData(models.Model):
    site_logo = models.CharField(verbose_name="网站logo",max_length=50)
    introduction = models.TextField(verbose_name='网站简介')
    approval_number = models.CharField(max_length=64,verbose_name='网站审批号',default='暂无审批号')

    class Meta:
        verbose_name = '网站信息'
        verbose_name_plural = verbose_name
        ordering = ['-id']

    # 使对象在后台显示更友善
    def __str__(self):
        return self.site_logo
```  
### admin.py
注册我们刚写的模型  
```python
from django.contrib import admin
from .models import HomeAndAbout,MySiteData,ToolLinks
# Register your models here.
# 装饰器方法 注册 Blog类方法
@admin.register(HomeAndAbout)
# 生成一个类，继承admin 的模型后台方法
class HomeAndAboutAdmin(admin.ModelAdmin):
    # 定义一个元组
    list_display = ('title', 'id', 'author', 'created_time', 'update_time')



@admin.register(MySiteData)
# 生成一个类，继承admin 的模型后台方法
class MySiteDataAdmin(admin.ModelAdmin):
    # 定义一个元组
    list_display = ('site_logo', 'introduction')


@admin.register(ToolLinks)
# 生成一个类，继承admin 的模型后台方法
class ToolLinksAdmin(admin.ModelAdmin):
    # 定义一个元组
    list_display = ('name', 'link')
```  
### settings.py
在项目中添加我们的toolbox
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    'blog',
    'toolbox',
]
```  
依次执行下面的命令：
```bash
python manage.py makemigrations

python manage.py migrate

python manage.py runserver
```  
运行项目后，在后台填写一些内容。  
下一小节，将介绍如何编写自定义模板标签。

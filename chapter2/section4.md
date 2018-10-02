## 后台管理文件  
这个文件的作用，在后台定义我们显示内容的格式。  
先不介绍吧，后面等我们运行项目并写入内容的时候就可以看到效果了。  
打开blog目录下的admin.py  
写入下面的代码：
```python
# 从django的贡献代码中导入 admin方法（装饰器）
from django.contrib import admin

# 从当前目录下的models.py导入Blog类方法
from .models import Blog

# Register your models here.
# 在这里注册你的模型

# 装饰器方法 注册 Blog类方法
@admin.register(Blog)
# 生成一个类，继承admin 的模型后台方法
class BlogAdmin(admin.ModelAdmin):
    # 定义一个元组
    list_display = ('id', 'title','slug', 'author', 'created_time', 'update_time')
```

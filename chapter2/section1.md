## 模型文件models.py     
这个文件的内容是由**前端**决定的。  
结合项目来讲，我们现在在做一个**个人博客网站**。  
首先，我们的网站要呈现什么内容呢？？？  
文章标题、文章内容、作者、创建时间、修改时间、阅读数、评论、文章分类、文章标签、友情链接、个人空间、留言板等等。  
内容很多对吧？？  
现在呢，我们把上面的功能分配和切割一下，就不多了。  
blog这个应用就只写：文章标题、文章内容、作者、创建时间、修改时间、文章分类、文章标签。  
其它的内容，因为其拓展性，另外做成其他的应用。  
以**阅读数**为例，它可以拓展成今天来访人数、总来访人数、本周来访人数、阁下是今天第xx位来访等等。  
评论呢，可以拓展成文章评论、留言板。（后端代码一样，前端呈现的地方和代码不一样）。  
***
但是，今天我们只做一个**简单的博客**进行练习。  
所以，在models.py写入以下的内容。   
```python
# mysite/blog/models.py
from django.db import models
import markdown

# 由于很多地方需要用到markdown转化后的内容，所以生成基类
# 生成一个基类，让其它类拥有该方法
# 这是用来把markdown格式的文本转化成html格式的方法
class BlogBase(models.Model):
    def body_markdown(self):
        mark = markdown.Markdown(extensions=[
            'markdown.extensions.extra',
            'markdown.extensions.codehilite',
            'markdown.extensions.toc',
        ])
        self.body = mark.convert(self.body)
        self.toc = mark.toc
        return self.body
'''
讲解一下，下面的models.xxxx方法
models.CharField 这个字段是用来表示短语字段，必须带有最大长度的参数 max_length
models.TextField 这个字段是文本格式，也就是文章
models.DateTimeField 这个字段是用来获取时间的
models.SlugField 这个字段有点难翻译，由于Django是新闻方面公司放出来的一个开源项目
                属于新闻方面的术语。我们平时看新闻，控制新闻顺序的就是用slug了
                它是把新闻标题的文章转换为两到四个字或者单词来确定播放顺序的。
                香港澳门那边好像都是用这种。
                我们这里把它用作网址的后缀。
                里面的unique=True的意思是唯一的，不可重复的

'''

# 继承基类的方法
class Blog(BlogBase):
    title = models.CharField(verbose_name="标题",max_length=50)
    body = models.TextField(verbose_name="内容")
    author = models.CharField(verbose_name="作者",max_length=50,default='sing')
    created_time = models.DateTimeField(auto_now_add=True,verbose_name='创建时间')
    update_time = models.DateTimeField(auto_now=True,verbose_name='修改时间')
    slug = models.SlugField(verbose_name="索引后缀",unique=True)

    class Meta:
        verbose_name = '文章'
        verbose_name_plural = verbose_name
        ordering = ['-created_time']

    # 使对象在后台显示更友善
    def __str__(self):
        return self.title

    # 分页
    def get_pre(self):# 上一页
        return Blog.objects.filter(id__lt=self.id).order_by('-id').first()

    def get_next(self):#下一页
        return Blog.objects.filter(id__gt=self.id).order_by('id').first()
```

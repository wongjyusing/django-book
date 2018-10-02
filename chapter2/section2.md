## 视图文件views.py  
这个文件的作用，是对网站页面的内容处理。  
结合代码来讲吧。  
```python
# 从django的快速方法中导入 渲染、获取对象（获取到就返回对象、获取不到就返回404）
from django.shortcuts import render,get_object_or_404

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

    # 在Blog也就是我们的数据库中寻找有没有slug字段和传进来的slug字段匹配的
    # 没有，则返回404,
    # 有则返回该对象的内容
    context['blog'] = get_object_or_404(Blog, slug=slug)

    return render(request,'blog_detail.html',context)
```  
这里简单来说就是写个函数。  
当接收到传来的**请求**后返回内容。  
请求是从哪里来呢？？  
这个涉及到下一小节，路由了。

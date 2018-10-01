## 安装Django2.1

首先，请激活你的虚拟环境。  
不会激活的看上一章节。  
激活后，输入`pip install django==2.1`  
注意：不需要`sudo pip install django==2.1`或`pip3 install django==2.1`

## 创建项目

找一个地方准备存放你的项目。  
例如，我现在在做这个教程的话。我放在了桌面。  
由于我需要根据进度做出好几个环节。所以我在桌面上创建了一个名为**django_project**的目录来存放所有项目，这个目录我上传到[GitHub](https://github.com/wongjyusing/django_project)了。大家去浏览一下。  
如果你想跟着我的步伐去敲代码进行练习的话。  
就在终端依次输入：  
`mkdir /home/your_username/Desktop/django_project`

`cd /home/your_username/Desktop/django_project`

注意：把上面的**your_username**替换成你在Linux的用户名。  
如果不是，那就在你要创建django项目的地方创建。记得把终端切换过去哦。

创建django项目命令：  
`django-admin startproject mysite`  
然后输入`ls`  
然后可以发现当前目录下多了一个名为**mysite**。  

进入该项目可以发现，项目结构如下：

```tree
.
├── manage.py             # 管理、主要文件 启动和配置都需要运行该文件
└── mysite                # 项目配置目录
    ├── __init__.py       # 声明该目录是一个python包
    ├── settings.py       # 项目的设置配置文件
    ├── urls.py           # 总路由
    └── wsgi.py           # 规范接口文件,部署到服务器会用到

1 directory, 5 files
```

项目就创建完成了，网上的很多教程都会在创建项目完成后讲在运行项目后，看一个**Django的欢迎界面**  
这里就不展示了。这个很无聊。  
这里我们先完成一些基础的配置。直接搭建一个小型博客再运行项目。

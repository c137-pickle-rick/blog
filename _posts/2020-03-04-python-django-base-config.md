---
title: "Django 项目基础配置"
categories:
  - Python
tags:
  - django
  - config
last_modified_at: 2020-03-04T19:08:03+08:00
---
  
## 步骤

#### 创建项目

`django-admin startproject django_project`

#### 创建应用

`django-admin startapp demo` 或者 `python manage.py startapp demo`

打开demo下的apps.py，在DemoConfig类中加一条属性，这样在后面使用admin站点管理时应用就显示为该名

`verbose_name = '示例应用'`

#### 配置项目

打开django_project下的settings.py，在INSTALLED_APPS列表中添加刚才的配置类

`demo.apps.DemoConfig`

向下翻，把语言和时区也改了：

`LANGUAGE_CODE = 'zh-Hans'`

`TIME_ZONE = 'Asia/Shanghai'`

在项目根目录分别创建一个templates和statics文件夹，用于后面放模板文件和静态文件

然后在settings.py中找到TEMPLATES列表，DIRS键的对应的列表中添加templates文件夹路径

`os.path.join(BASE_DIR, 'templates')`

然后在最下面加一行，添加静态文件存放路径

`STATICFILES_DIRS = [os.path.join(BASE_DIR, 'statics')]`

#### 视图函数

随便写两个演示

```python
def test1(request: HttpRequest) -> HttpResponse:
    returun HttpResponse("<h1>test1</h1>")

def test2(request: HttpRequest) -> HttpResponse:
    
    context = {'k1': 'v1', 'k2': 'v2'}
    # 这里会使用templates下的test.html页面模板 所以先创建一个test.html文件放里面
    return render(request, 'test.html', context)
```

#### 配置路由

打开django_project下的urls.py，urlpatterns列表中添加一条指向demo应用下urls.py的匹配规则

`path('demo/', include('demo.urls'))`

在demo包下也创建一个urls.py，里面写上代码指向视图函数

```python
urlpatterns = [
    path('test1/', views.test1),
    path('test2/', views.test2),
]
```

#### 启动服务

`python manage.py runserver 8000`

通过http://localhost:8000/demo/test1和http://localhost:8000/demo/test2可以访问相应的views

#### 数据库

在demo下models.py中建表

```python
# 班级表
class ClassInfo(models.Model):
    # id字段会自动创建
    # 创建一个name字段 字符串20
    name = models.CharField(max_length=20)  # 书名
    objects = models.Manager()

    def __str__(self):
        return self.name

# 学生表
class StudentInfo(models.Model):
    name = models.CharField(max_length=12)  # 书中人物名
    gender = models.BooleanField()  # 性别
    # 创建外键和ClassInfo表相关联
   	stu_class = models.ForeignKey(ClassInfo, on_delete=models.CASCADE) 
    objects = models.Manager()

    def __str__(self):
        return self.name
```

在demo下的admin.py中注册表

```python
admin.site.register(ClassInfo)
admin.site.register(StudentInfo)
```

命令行中

创建迁移文件	`python manage.py makemigrations`

执行迁移	 `python manage.py migrate`

#### 站点管理

使用命令创建admin账户

`python manage.py createsuperuser`

启动服务

`python manage.py runserver 8000`

浏览器输入http://locahost:8000/admin/

登录输入刚才注册的账户和密码

就可以进入管理界面了，可以增删数据库等

#### 重新配置为MYSQL数据库

先进入mysql服务器创建一个demo数据库

然后，配置settings.py：

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',	# mysql引擎
        'HOST': 'localhost',	# 主机	
        'PORT': 3306,		# 端口号
        'USER': 'root',		# 用户名
        'PASSWORD': '123456',	# 密码
        'NAME': 'demo',		# 数据库名
    }
```

接着，删除migrations目录下的迁移文件：

然后重新执行命令

`python manage.py makemigrations`

`python manage.py migrate`

可以去数据库查看是否有新增的表
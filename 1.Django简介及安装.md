# 一、Django简介及安装
## （一）Django介绍
- Django 是一个由 Python 编写的一个开放源代码的 Web 应用框架。
- 使用 Django，只要很少的代码，Python 的程序开发人员就可以轻松地完成一个正式网站所需要的大部分内容，并进一步开发出全功能的 Web 服务 Django 本身基于 MVC 模型，即 Model（模型）+ View（视图）+ Controller（控制器）设计模式，MVC 模式使后续对程序的修改和扩展简化，并且使程序某一部分的重复利用成为可能。

## （二）MVC设计模式
1.概念：
- MVC设计模式是一种软件设计典范，用一种业务逻辑，使数据、界面显示分离的方法组织代码，将业务逻辑聚集到一个部件里面，在改进和个性化定制界面与用户交互的同时，不需要重新编写业务逻辑。
- Model：用于封装与应用程序的业务逻辑相关的数据及对数据的处理方法，是web应用程序中用于处理应用程序的数据逻辑部分，Model通常只提供功能性的接口，通过这些接口可以获取Model所有功能。
- View：负责数据的显示和呈现，View是用户的直接输出。
- Controlller：负责从用户端手机用户的输入，可以看成提供View的反向功能，主要处理用户交互。
- 核心思想：解耦

2.优点：
- 降低个模块之间的耦合性，方便变更，容易重构代码，最大程度实现了代码的重用。

## （三）MTV设计模式
1.概念：
- 本质上与MVC没什么差别，也是各组件之间为了保持松耦合关系，只是定义上有些许不同。
- Model：负责业务对象与数据库（ORM）的对象。
- View：负责业务逻辑，并在适当的时候调用Model 和Template
- Template：负责把页面展示给用户。

注意：Django中还有一个url分发器（也可以叫做路由），主要用来将一个个URL页面的请求分发给不同的View进行处理，View再调用相应的Model 和Template。

## （四）Django安装
### 1.虚拟环境 virtualenv

1）安装虚拟环境：pip3 install virtualenv

2）创建虚拟环境：mkvirtualenv  <虚拟环境名称>  

注意：创建【环境名称】文件夹，放置所有的虚拟环境。
```
virtualenv 环境名称 --python=python3.6
virtualenv 环境名称 --python='C:\python\python3.6.exe'
virtualenv 环境名称 --python=python2.7
```

3）删除虚拟环境：rmvirtualenv <虚拟环境名称>  

4）进入某个虚拟环境：workon <虚拟环境名称>  

5）退出虚拟环境：deactivate

### 2.安装Django

1）pip3 安装
```
pip3 install django==1.11.7 （如果不指定版本，则会安装最新版）
```

2）测试Django 是否安装成功，在cmd中输入python，输入以下命令：
```
>>> import django
>>> django.VERSION

```

### 3.创建一个Django项目

1）在命令行中输入：
```
django-admin startproject xxxx
```

2）如果还要增加应用，在进入到虚拟环境之后，输入：
```
python manage.py startapp yyyy
```

3）启动一个项目时，可以使用命令：
```
python manage.py runserver
```

4）使用pycharm打开项目时，必须打开包含manage.py文件的上一级目录，然后需要配置虚拟环境目录：

![jianjie1](https://github.com/tete1987/picture_resource/blob/master/django/%E7%AE%80%E4%BB%8B1.png)




### 4.连接数据库

1）可以先链接Django自带的数据库sqlite


![jianjie2](https://github.com/tete1987/picture_resource/blob/master/django/%E7%AE%80%E4%BB%8B2.png)

![jianjie3](https://github.com/tete1987/picture_resource/blob/master/django/%E7%AE%80%E4%BB%8B3.png)

Test Connection 正常即可点击确定。

2）做数据迁移（完成数据库连接之后）
```
python manage.py migrate

```

![jianjie4](https://github.com/tete1987/picture_resource/blob/master/django/%E7%AE%80%E4%BB%8B4.png)


![jianjie5](https://github.com/tete1987/picture_resource/blob/master/django/%E7%AE%80%E4%BB%8B5.png)




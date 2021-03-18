# 六、admin后台管理类操作数据库
## （一）后台管理
1.创建后台管理员，在终端中输入：
```python
python manage.py createsuperuser
```
自定义管理员账号和密码即可。

2.修改页面语言——打开settings.py 文件，修改如下：
```python
LANGUAGE_CODE = 'zh-Hans'

TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_L10N = True

USE_TZ = True
```

3.使用管理员登录后台之后，人员的姓名显示为object，如图：

![admin1](https://github.com/tete1987/picture_resource/blob/master/django/admin1.png)

4.将后台姓名显示方式修改为每个人员的姓名——打开stu应用中的model.py 文件，如果是python3 增加__str__ 方法，如果是python2 增加 __unicode__方法，如下：
```python
from django.db import models


# Create your models here.

class Student(models.Model):
    sname = models.CharField(max_length=30, unique=True)
    spwd = models.CharField(max_length=30)

    class Meta:
        db_table = 't_stu'

    def __str__(self):
        return self.sname
```

## （二）查询所有人员的信息

1.先在项目的urls.py 文件中定义路由名称
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('register/', include('stu.urls')),
    path('student/', include('stu.urls')),
]

```
2.在stu应用中的urls中添加对应的子路由：
```python
from django.urls import path

from . import views
urlpatterns = [
    path('', views.index_view),
    path('show/', views.show_view),
]

```
3.在views.py文件中创建show_view方法：
```python
def show_view(request):
    #1.查询 t_stu表中的所有数据
    stus = Student.objects.all()

    return render(request,'show.html',{'students':stus})
```
注：
- Student.objects.all() 就是查询表中所有的值，all返回的是QuerySet对象，程序并没有真的在数据库中执行SQL语句查询数据，但支持迭代，使用for循环可以获取数据。所以需要在show.html中编写for循环。
- render中的第三个值是自定义的字典，key：value的组合。数值来源于数据库，所以将上面的获取的数据存到stus变量中，放入value 的位置，students 来源于show.html中编写for循环。
- 小技巧：可以在pycharm的Python Console中做测试。
- 
![admin2](https://github.com/tete1987/picture_resource/blob/master/django/admin2.png)

 
4.编写show.html：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <table border="1" cellspacing="0" width="500px">
        <tr>
            <th>编号</th>
            <th>姓名</th>
            <th>密码</th>
        </tr>
        {% for stu in students %}
         <tr>
            <td>{{ forloop.revcounter0 }}</td>
            <td>{{ stu.sname }}</td>
            <td>{{ stu.spwd }}</td>
        </tr>

        {% endfor %}


    </table>

</body>
</html>
```

注：
- 在Django模板标签中,{% for %}的主要作用就是用来迭代序列中的各个元素与Python中for语句的使用类似，可以迭代列表，元组，字典等。
- forloop的使用：在{% for %}循环的内部，可以访问一个名为forloop的模板变量，通过这个变量我可以获知循环的进程。
- forloop.counter0 是从0开始计数，forloop.revcounter0 是倒序。

## （三）绑定后台
在admin.py 文件中输入：
```
admin.register(Movie)

```

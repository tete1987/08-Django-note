# 七、登录功能（连接MySql）
## （一）安装mysql
1.下载mysql安装包，进行解压

2.在想要安装的目录中添加一个 my.ini文件，内容如下：（basedir 是安装目录，datadir是数据存储目录，根据情况设置即可）
```
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[mysqld]
# 设置3306端口
port = 3306
# 设置mysql的安装目录
basedir=C:\mysql-8.0.11
# 设置mysql数据库的数据的存放目录
datadir=C:\mysql-8.0.11\sqldata
# 允许最大连接数
max_connections=20
# 服务端使用的字符集默认为8比特编码的latin1字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
```

3.在安装目录的bin目录下打开cmd，输入以下命令：
```
#1.安装数据库
mysql install

#2.初始化data目录（会生成一个初始密码）
mysqld --initialize --console

#3.启动服务
net start mysql

#4.登录mysql（使用初始化密码）
mysql -u root -p

#5.切换数据库
use mysql

#6.修改root密码
alter user 'root'@'localhost' identified with mysql_native_password by '123456‘；

#7.切换数据库
use mysql

#8.退出
exit
```

# （二）连接数据库
1.先新建数据库，自定义一个名称，选择“utf-8--UTF-8-Unicode”类型，点击确定

![mysql1](https://github.com/tete1987/picture_resource/blob/master/django/mysql1.png)

2.在django的settings里进行配置：
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'django',
        'HOST': '127.0.0.1',
        'PORT': 3306,
        'USER':'root',
        'PASSWORD':'123456'
    }
}
```

3.映射到mysql数据库：

1）创建迁移文件（如果之前已经迁移过，就不用创建了）
```
python manage.py makemigrations stu
```

2）迁移数据库
```
python manage.py migrate
```

3）如果有报错的话，可先导入安装pymysql
```
pip install pymysql
```

然后在django的项目的inti.py文件中输入以下代码：
```
import pymysql
pymysql.install_as_MySQLdb()
```

再执行迁移命令就可以了。

4）可以手工将现有数据增加到新数据库中。

5）还需要在pycharm中安装mysql-client。

![mysql2](https://github.com/tete1987/picture_resource/blob/master/django/mysql2.png)


# （三）登录功能编写
1.登录功能的路由就写在student路由下，所以项目的urls.py 文件可以不用修改。

2.打开stu目录下的urls.py 文件增加login的路由：
```python
from django.urls import path

from . import views
urlpatterns = [
    path('', views.index_view),
    path('show/', views.show_view),
    path('login/', views.login_view),

]
```

3.在views.py 文件中，增加login_view方法：
```python
def login_view(request):
    if request.method == "GET":
        return render(request,'login.html')
    else:
        # 1.获取请求参数
        uname = request.POST.get('uname')
        pwd = request.POST.get('pwd')

        # 2.查询数据库
        if uname and pwd:

            stu =Student.objects.filter(sname=uname,spwd=pwd).count()
            if stu == 1:
                return HttpResponse('登录成功！')

    return HttpResponse('登录失败！')
```
注：
- 使用filter 是带有条件性的查询，后面加count就是统计个数，只要查询出一条数据，就说明有对应的登录账号。filter后面的括弧中，如果两个条件是“且”的关系，则使用逗号进行连接。

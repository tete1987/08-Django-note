# 十一、Models之创建单表
## （一）常用字段
1.django所有的数据模型都集成子models.Model，常用字段类型：
- CharField.max_length ：输入框（max_length控制最大位数）
- TextField：没有长度限制的字符串（文本域）
- DateField ：日期
- DateTimeField ：日期+时间
- BooleanField：真假（对应值在数据库中以0和1 存储）
- NullBooleanField Null：真假（当该字段可接受null值时可使用该类型）
- Integer：整数，包含负数
- PositiveIntegerField：正整数
- DecimalField.max_digits：浮点数(max_digits必须加上这个参数，控制浮点位数)，decimal_places：小数点后保留几位（数据库中的float没有decimal精度高）
- ImageField：图片，依赖于Pillow（处理图片）upload_to = 'upload' 指定文件上传到目录
- FileField：ImageField 继承FileField
- AutoField：自增
- ForeignKey 1:n  ：11对多外键
- ManyToManyField n:n ：多对多
- EmailField：邮箱
- UUIDField：重复的概率非常低，基本可以忽略，全世界都不一样的标识，uuid的产生和服务器的环境有关（cpu，网关）唯一性的表示，用户模块，订单号
- 不同的字段在后台对应不同的html 的组件

ImageField 依赖于Pillow 组件（python库）。

更多字段可查看官网：https://docs.djangoproject.com/zh-hans/3.1/

## （二）常用属性
- unique：表示这个字段唯一
- default：默认的意思（如果不写的话就使用默认的值）
- null=True：允许字段为null（允许数据库为null），数据库层面的
- blank=True：表单阶段的，admin后台的
- auto_now：针对时间的，自动调整当前（当修改条目的时候，这个时间会自动更新），每次修改都会更新（修改，保存的时候才会生效）
- auto_now_add ：针对时间的，只添加一次（创建的时间）

## （三）示例
1.在Models.py文件中创建一个模型类：
```python
#帖子模型类
class Post(models.Model):
    pid = models.AutoField(primary_key = True)
    title = models.CharField(max_length=100,unique=True)
    content = models.TextField()
    create_time = models.DateTimeField(auto_now_add=True) #只有插入数据时才会以当前时间为准
    modified_time = models.DateTimeField(auto_now=True)
    email = models.EmailField()
    isdelete = models.BooleanField(default=False)
    access_count = models.PositiveBigIntegerField()
    price = models.DecimalField(max_digits=5,decimal_places=2)

    def __str__(self):
        return "Post:%s,%s"%(self.title,self.access_count)

    class Meta:
        db_table = 't_post'

```

2.创建数据库迁移文件，并进行迁移
```
python manage.py makemigrations

python manage.py migrate
```

3.完成迁移后将模块加入到后台管理系统中

1）先创建后台管理员：
```
python manage.py createsuperuser
```

2）将模型类写入到admin.py文件中（为了显示在后台管理模块中）
```python
from django.contrib import admin
from .models import *
# Register your models here.

admin.site.register(Post)

```
4.打开后台管理页面，查看功能：

![danbiao0](https://github.com/tete1987/picture_resource/blob/master/django/danbiao0.png)

## （四）修改模型类
1.在Post模型类的原基础上增加一个上传文件的字段：
```python
#帖子模型类
class Post(models.Model):
    pid = models.AutoField(primary_key = True)
    title = models.CharField(max_length=100,unique=True)
    content = models.TextField()
    create_time = models.DateTimeField(auto_now_add=True) #只有插入数据时才会以当前时间为准
    modified_time = models.DateTimeField(auto_now=True)
    email = models.EmailField()
    isdelete = models.BooleanField(default=False)
    access_count = models.PositiveBigIntegerField()
    price = models.DecimalField(max_digits=5,decimal_places=2)
    file = models.ImageField(upload_to='upload/images/')

    def __str__(self):
        return "Post:%s,%s"%(self.title,self.access_count)

    class Meta:
        db_table = 't_post'
```

2.在做迁移时会报错，提示已经存在。这时可以先删除之前的迁移文件：

![danbiao1](https://github.com/tete1987/picture_resource/blob/master/django/danbiao1.png)

3.然后再到数据库中，找到django_migrations表中，找到该模块的迁移数据，进行删除：

![danbiao2](https://github.com/tete1987/picture_resource/blob/master/django/danbiao2.png)


4.在数据库中 ，将t_post表删除（线上肯定不能删除）
5.再做一次迁移时，会报不能使用ImageField，因为没有安装Pillow。根据提示安装Pillow即可：
```
python -m pip install Pillow
```

6.重新创建迁移文件，进行迁移即可：
```
python manage.py makemigrations

python manage.py migrate
```

7.做一条数据，查看效果

![danbiao3](https://github.com/tete1987/picture_resource/blob/master/django/danbiao3.png)


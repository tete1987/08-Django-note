# 十二、Models之创建多表-1对1关系表
## （一）多表关系举例
#### 1.一对一：学生和学生证
```
student = models.OneToOneField(Student,primary_key = True,on_delete = models.CASCADE)
```

注：CASCADE 是及联删除。

#### 2.一对多：班级和学生
```
class = models.ForeignKey(Class,related_name='student')
```

#### 3.多对多：学生和课程
```
course = models.ManyToManyField(Course)
```


## （二）一对一表的创建
1.在models.py文件中创建模型类
```python
class Student(models.Model):
    sno = models.AutoField(primary_key = True)
    sname = models.CharField(max_length=30)

    def __str__(self):
        return "Student:%s"%self.sname

class Scard(models.Model):
    stu = models.OneToOneField(Student,primary_key=True,on_delete=models.CASCADE)
    major = models.CharField(max_length=30)

    def __str__(self):
        return "Scard:%s"%self.stu.sname
```

注：两张表，Student 和Scard，Scard表的外键是Student表中的ID号

2.然后可以在console中做调试：
```python
from movie.models import *

#在数据库中创建数据
students = Student(sname='zhangsan')
students.save()
scard = Scard(stu=students,major='计算机')
scard.save()

#进行查询
#1.正向查询（通过Student表查询Scard）
Student.objects.first().scard

#2.逆向查询（通过Scard表查询Student）
Scard.objects.first().stu

```

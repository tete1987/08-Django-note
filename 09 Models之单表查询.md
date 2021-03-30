# 九、Models之单表查询
## （一）ORM对象关系映射
1.显示ORM底层生成SQL
```python
from movie.models import *

def showsql():
    from django.db import connection
    print(connection.queries[-1]['sql'])
```

## （二）查询单个对象

所有的继承自models.Model的类，都会有一个叫objects（管理器）

1.查询一个（有且只能有一个）（少于一个或者多余一个都报错）

以Movie对象为例：
```python
Movie.object.get(mid=147)


底层的showsql()结果：

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mid` = 147 LIMIT 21
```

2.获取第一个
```python
Movie.object.first()

底层的showsql()结果：
SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` ASC LIMIT 1
```

3.获得最后一个
```python
Movie.object.last()

底层的showsql()结果：
SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` DESC LIMIT 1
```

4.获得记录的总数
```
Movie.object.count()

底层的showsql()结果：
SELECT COUNT(*) AS `__count` FROM `movie`
```

## （三）查询多个对象
1.获得所有记录
```python
Movie.objects.all()

注：在django的1.11.6 中默认只获得21条记录，暂时不需要的数据不显示，懒加载。
SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` LIMIT 21
```

2.切片（不支持负数所以）
```python
Movie.objects.all()[20:50]

注：
底层直接使用了limit字句，可以自动的分页。
django的orm性能非常强大，能节省很多工作。
offset：偏移（sql语句中的）
SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` LIMIT 21 OFFSET 20
```

3.过滤
```python
1）单个条件过滤：
Movie.objecets.filter(mname=‘麻辣学院’)


2）模糊查询
sql语句中是 like %（多个字符）  _（一个字符），例如：
select * from movie where mname like '%爱情_'


①以“爱情”结尾：
select * from movie where mname like ’%爱情‘

对应：
Movie.objects.filter(mname__endswith='爱情')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE BINARY '%爱情' LIMIT 21


②以爱情开头：
select * from movie where mname like '爱情%'

对应：
Movie.objects.filter(mname__startswith='爱情')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE BINARY '爱情%' LIMIT 21


③包含爱情：
select * from movie where mname like '%爱情%'

对应
Movie.objects.filter(mname__contains='爱情')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE BINARY '%爱情%' 
LIMIT 21



注：其中底层的sql语句中包含 binary，说明区分大小写。
```
3）精确查询
```python
Movie.objects.filter(mname__exact='麻辣学院')
或
Movie.objects.filter(mname='麻辣学院')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mname` = '麻辣学院' LIMIT 21
```

4）忽略大小写
```python
Movie.objects.filter(mname__istartswith='h')


忽略大小写之后，底层的sql中不会包含binary
SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE 'H%' LIMIT 21
```

5）查询某个字段是否为null
```python
Movie.objects.filter(mname__isnull=True)

```
6）多条件查询
```python
Movie.objects.filter(mname__icontains='H' ,mid=7497)
或
Movie.objects.filter(mname__icontains='H').filter(mid=7497)

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE (`movie`.`mname` LIKE '%H%' AND `movie`.
`mid` = 7497) LIMIT 21
```

4.部分查询
```python
Movie.objects.values('mname','mid').filter(mname__contains='爱情')
返回字典。如果返回值中有中文，则显示Unicode编码


SELECT `movie`.`mname`, `movie`.`mid` FROM `movie` WHERE `movie`.`mname` 
LIKE BINARY '%爱情%' LIMIT 21
```

5.排除一分部分
```python
Movie.objects.filter(mname__contains='爱情').exclude(mname__startswith='爱情')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE (`movie`.`mname` LIKE BINARY '%爱情%' 
AND NOT (`movie`.`mname` LIKE BINARY '爱情%')) LIMIT 21

```

6.排序
```python
1）正序：Movie.objects.order_by('mid')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` ASC LIMIT 21


2）降序：Movie.objects.order_by('-mid')

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` DESC LIMIT 21

```

7.日期查询
```python
1）查询大于某个时间的记录（后面带入时间即可）
Movie.objects.filter(mid__gt=2000)

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mid` > 2000 LIMIT 21



2）查询小于某个时间的记录
Movie.objects.filter(mid__lt=2000)

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mid` < 2000 LIMIT 21


3）小于等于 或大于等于
Movie.objects.filter(mid__gte=2000)

Movie.objects.filter(mid__lte=2000)


4）in ：两个值之间是或的关系
Movie.objects.filter(mid__in=(149,160))

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, 
`movie`.`mlink` FROM `movie` WHERE `movie`.`mid` IN (139, 160) LIMIT 21


结果出来是mid=149 和mid=160 的值。
```

5）range：两个值的区间
```python
Movie.objects.filter(mid__range=(149,160))

SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`,
 `movie`.`mlink` FROM `movie` WHERE `movie`.`mid` 
 BETWEEN 149 AND 160 LIMIT 21

```

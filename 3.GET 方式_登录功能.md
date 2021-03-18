# 三、_GET 方式_登录功能
## （一）修改代码
1.将login.html中的method方式更改为 get模式：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/student/login/" method="get">
        <p>
        <label>用户名：</label><input type="text" name="uname">
        </p>
        <p>
        <label>密&emsp;码：</label><input type="password" name="pwd">
        </p>
        <p>
            &emsp;&emsp;&emsp;<label></label><input type="submit" name="登录">
        </p>
    </form>

</body>
</html >
```

2.修改 stu 应用中的 urls.py ，增加login路由：
```python
from django.urls import path

from . import views
urlpatterns = [
    path('', views.index_view),
    path('login/', views.login_view),
] 
```
注：urlpatterns = [ ] 是固定写法，不能修改。 

3.在views.py文件中增加登录功能的处理方式（先使用代码写死的方式，不连接数据库）：
```python
def index_view(request):
    return render(request, 'login.html')


# 处理登录功能
def login_view(request):
    # 接收请求参数
    uname = request.GET.get('uname', '')
    pwd = request.GET.get('pwd', '')

    # 判断
    if uname == 'tete' and pwd == '123':
        return HttpResponse('登录成功！')

    return HttpResponse('登录失败！')

```
注：reques.GET 的方式必须与login.html中写的方式一致，且GET必须要大写。

4.GET请求的编写方式：

1）直接在地址栏中进行回车，是get请求

2）在login.html中将method 改为get方式：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/student/login/" method="get">
        {% csrf_token %}
        <p>
            <label>用户名：</label><input type="text" name="uname">
        </p>
        <p>
            <label>密&emsp;码：</label><input type="password" name="pwd">
        </p>
        <p>
            &emsp;&emsp;&emsp;<label></label><input type="submit" value="登录">
        </p>
    </form>

</body>

```
3）编写一个超链接，也是get请求：
```
<a href="/student/login/?uname=lisi&pwd=123">显示登录首页</a>
```

4）使用js方式编写的方式也是get请求
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <script>
        function test() {
            window.location.href="/student/login/?uname=lisi&pwd=123";
        }
    </script>

</head>
<body>
    <input type="button" onclick="test()" value="GET请求">

    <a href="/student/login/?uname=lisi&pwd=123">显示登录首页</a>

    <form action="/student/login/" method="get">
        {% csrf_token %}
        <p>
            <label>用户名：</label><input type="text" name="uname">
        </p>
        <p>
            <label>密&emsp;码：</label><input type="password" name="pwd">
        </p>
        <p>
            &emsp;&emsp;&emsp;<label></label><input type="submit" value="登录">
        </p>
    </form>

</body>
```

GET请求有四种方式，但POST请求只有一种，就是在method中改为post方式：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/register/" method="post">
          {% csrf_token %}
        <p>
            <label for="username">用户名：</label><input type="text" name="uname" id="username"/>
        </p>
        <p>
            <label for="password">密&emsp;码：</label><input type="password" name="pwd"  id="password"/>
        </p>
        <p>
            &emsp;&emsp;&emsp;<input type="submit" value="登录" />
        </p>

    </form>
</body>
</html>
```

总结GET请求的方式：

1.<form method="get">

2.浏览器地址栏直接访问。

3.<a href="/student/>超链接</a>

4.window.location.href="/student/";

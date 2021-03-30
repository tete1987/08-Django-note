# 四、_POST 方式_登录功能
## （一）代码修改
1.将login.html中的method方式更改为 get模式：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/student/login/" method="post">
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
```

2.修改views.py 文件中的login_view 方法，将GET 更改为POST方式
```python
def index_view(request):
    return render(request, 'login.html')


# 处理登录功能
def login_view(request):
    # 接收请求参数
    uname = request.POST.get('uname', '')
    pwd = request.POST.get('pwd', '')

    # 判断
    if uname == 'tete' and pwd == '123':
        return HttpResponse('登录成功！')

    return HttpResponse('登录失败！')

```
3.重启之后在页面中进行登录：
![post1](https://github.com/tete1987/picture_resource/blob/master/django/post1.png)


会发生403错误。原因是Django的csrf中间件起作用（安全机制），禁止访问：
```
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

4.解决办法有两种：

1）将中间件中的csrf进行注释（不推荐）

2）在login.html文件中加上{% csrf_token %}
（python的方法，不是html的方法）：
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/student/login/" method="post">
        {% csrf_token %}
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
</html>
```

### GET请求和POST请求区别：

1.POST请求的请求参数在请求实体内容中，GET请求的请求参数存放在URL中。

2.POST请求比GET请求安全？（都不安全）

3.GET请求的URL参数长度有限（不超过2k），POST没有长度限制。

4.GET请求一般做查询（有缓存），POST请求一般做添加/修改/删除（无缓存）

5.Django服务器GET/POST请求为什么接收参数方式都一样？

因为他们都是QueryDict对象(django.http.request)

### HTTP特性：
1.HTTP1.1版本后支持长连接。

2.单向性协议（必须先有请求后又响应）

3.无状态的协议：
- Cookie：客户端相关
- Session：服务端相关

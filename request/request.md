## Request

Python 内置了 requests 模块，该模块主要用来发 送 HTTP 请求，requests 模块比 urllib 模块更简洁。

## requests 方法

requests 方法如下表：

| 方法                             | 描述                            |
| :------------------------------- | :------------------------------ |
| delete(*url*, *args*)            | 发送 DELETE 请求到指定 url      |
| get(*url*, *params, args*)       | 发送 GET 请求到指定 url         |
| head(*url*, *args*)              | 发送 HEAD 请求到指定 url        |
| patch(*url*, *data, args*)       | 发送 PATCH 请求到指定 url       |
| post(*url*, *data, json, args*)  | 发送 POST 请求到指定 url        |
| put(*url*, *data, args*)         | 发送 PUT 请求到指定 url         |
| request(*method*, *url*, *args*) | 向指定的 url 发送指定的请求方法 |

### 尝鲜

```python
import requests

# 发送请求
x = requests.get('https://www.baidu.com/')

# 返回网页内容
print(x.text)
```

每次调用 requests 请求之后，会返回一个 response 对象，该对象包含了具体的响应信息。

响应信息如下：

| 属性或方法            | 说明                                                         |
| :-------------------- | :----------------------------------------------------------- |
| apparent_encoding     | 编码方式                                                     |
| close()               | 关闭与服务器的连接                                           |
| content               | 返回响应的内容，以字节为单位                                 |
| cookies               | 返回一个 CookieJar 对象，包含了从服务器发回的 cookie         |
| elapsed               | 返回一个 timedelta 对象，包含了从发送请求到响应到达之间经过的时间量，可以用于测试响应速度。比如 r.elapsed.microseconds 表示响应到达需要多少微秒。 |
| encoding              | 解码 r.text 的编码方式                                       |
| headers               | 返回响应头，字典格式                                         |
| history               | 返回包含请求历史的响应对象列表（url）                        |
| is_permanent_redirect | 如果响应是永久重定向的 url，则返回 True，否则返回 False      |
| is_redirect           | 如果响应被重定向，则返回 True，否则返回 False                |
| iter_content()        | 迭代响应                                                     |
| iter_lines()          | 迭代响应的行                                                 |
| json()                | 返回结果的 JSON 对象 (结果需要以 JSON 格式编写的，否则会引发错误) |
| links                 | 返回响应的解析头链接                                         |
| next                  | 返回重定向链中下一个请求的 PreparedRequest 对象              |
| ok                    | 检查 "status_code" 的值，如果小于400，则返回 True，如果不小于 400，则返回 False |
| raise_for_status()    | 如果发生错误，方法返回一个 HTTPError 对象                    |
| reason                | 响应状态的描述，比如 "Not Found" 或 "OK"                     |
| request               | 返回请求此响应的请求对象                                     |
| status_code           | 返回 http 的状态码，比如 404 和 200（200 是 OK，404 是 Not Found） |
| text                  | 返回响应的内容，unicode 类型数据                             |
| url                   | 返回响应的 URL                                               |

```python
# 发送请求
x = requests.get('https://www.baidu.com/')

# 返回网页内容
print(x.text)
# 返回 http 的状态码
print(x.status_code)

# 响应状态的描述
print(x.reason)

# 返回编码
print(x.apparent_encoding)
```



### 方法解析

在我们日常的请求中，一个http请求包含了很多的参数

![image-20220928141045210](C:\Users\ZZ0DFI672\AppData\Roaming\Typora\typora-user-images\image-20220928141045210.png)

而requests也很方便的帮我封装这里参数

```python
def request(method, url, **kwargs):
```

上面是requests真是的方法，共计有三个参数

- method ：请求的方法 ，可以是 ``GET``, ``OPTIONS``, ``HEAD``, ``POST``, ``PUT``, ``PATCH``, or ``DELETE``.中的任意一种
- url：请求的地址
-  **kwargs ：所有的附加参数，在之前的python基础中我们学过，当函数的参数以 ** \** 开头，表示可以传递以字典类型的参数 ，常用的参数比如 ：cookies，headers，json，data，params，files等等

示例

```python
def sm_headers():
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36"
    }
    sm_headers = requests.get('https://www.baidu.com/', headers=headers)
    print(sm_headers.status_code)
```

post

```python
def sm_post():
    datas = {
        "id": 1,
        "name": "haiji",
        "weChat": "372787553"
    }
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/105.0.0.0 Safari/537.36"
    }
    sm_headers = requests.post('http://localhost:8080/json/', headers=headers, data=datas)
    print(sm_headers.status_code)
```
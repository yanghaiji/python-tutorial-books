## 实战小案例

在前面我们已经学列`python`已经`pytest`相关的技术知识，接下来我们将对之前学习的知识进行一个小实战，对百度的接口进行自动化测试

### 案例 一

> 调用百度首页，判断返回的响应码是否为200

```python
def test_baidu_home():
    """
    测试访问百度首页
    这里需要用到request 包
    :return:
    """
    # 发送请求
    x = requests.get('https://www.baidu.com/')

    # 返回网页内容
    print(x.text)
    # 返回 http 的状态码
    assert x.status_code == 200

```

结果

```
cachedir: .pytest_cache
metadata: {'Python': '3.8.9', 'Platform': 'Windows-10-10.0.22000-SP0', 'Packages': {'pytest': '7.0.1', 'py': '1.11.0', 'pluggy': '1.0.0'}, 'Plugins': {'allure-pytest': '2.9.45', 'assume': '2.4.3', 'base-url': '2.0.0', 'dependency': '0.5.1', 'forked': '1.4.0', 'html': '3.1.1', 'metadata': '1.11.0', 'ordering': '0.6', 'playwright': '0.3.0', 'rerunfailures': '10.2', 'xdist': '2.5.0'}, 'JAVA_HOME': 'C:\\java_tools\\adopt-openjdk-1.8.0_265', 'Base URL': ''}
rootdir: C:\source_code\python_work\python-tutorial\pytests\interface
plugins: allure-pytest-2.9.45, assume-2.4.3, base-url-2.0.0, dependency-0.5.1, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, playwright-0.3.0, rerunfailures-10.2, xdist-2.5.0
collecting ... collected 1 item

itf_demo.py::test_baidu_home <!DOCTYPE html>
<!--STATUS OK--><html> <head> ....... </html>

PASSED

============================== 1 passed in 0.29s ==============================
```

### 案例 二

> 调用百度搜索接口，进行关键字搜索，并判断其返回值是否包含搜索的关键字

```python
def test_baidu_search():
    """
    测试访问百度首页搜素pytest项目的内容
    这里需要用到request 包
    :return:
    """
    # 发送请求
    x = requests.get('https://www.baidu.com/s?ie=utf-8&wd=ptytest')

    # 返回 http 的状态码
    assert x.status_code == 200
    # 返回网页内容 是否包含 pytest
    assert str(x.text).find('pytest')
```

结果

```
cachedir: .pytest_cache
metadata: {'Python': '3.8.9', 'Platform': 'Windows-10-10.0.22000-SP0', 'Packages': {'pytest': '7.0.1', 'py': '1.11.0', 'pluggy': '1.0.0'}, 'Plugins': {'allure-pytest': '2.9.45', 'assume': '2.4.3', 'base-url': '2.0.0', 'dependency': '0.5.1', 'forked': '1.4.0', 'html': '3.1.1', 'metadata': '1.11.0', 'ordering': '0.6', 'playwright': '0.3.0', 'rerunfailures': '10.2', 'xdist': '2.5.0'}, 'JAVA_HOME': 'C:\\java_tools\\adopt-openjdk-1.8.0_265', 'Base URL': ''}
rootdir: C:\source_code\python_work\python-tutorial\pytests\interface
plugins: allure-pytest-2.9.45, assume-2.4.3, base-url-2.0.0, dependency-0.5.1, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, playwright-0.3.0, rerunfailures-10.2, xdist-2.5.0
collecting ... collected 1 item

itf_demo.py::test_baidu_search PASSED

============================== 1 passed in 0.47s ==============================

```

### 案例三

> 调用百度搜索接口，进行关键字搜索，并判断其返回值是否包含搜索的关键字
>
> 此过程为动态的入参，实现数据驱动测试

```python
@pytest.mark.parametrize('search', ['pytest', 'python'])
def test_baidu_search_parametrize(search):
    """
    测试访问百度首页搜素pytest项目的内容
    这里需要用到request包
    :return:
    """
    # 发送请求
    x = requests.get('https://www.baidu.com/s?ie=utf-8&wd=' + search)

    # 返回 http 的状态码
    assert x.status_code == 200
    # 返回网页内容 是否包含 pytest
    assert str(x.text).find(search)
```

结果

```
cachedir: .pytest_cache
metadata: {'Python': '3.8.9', 'Platform': 'Windows-10-10.0.22000-SP0', 'Packages': {'pytest': '7.0.1', 'py': '1.11.0', 'pluggy': '1.0.0'}, 'Plugins': {'allure-pytest': '2.9.45', 'assume': '2.4.3', 'base-url': '2.0.0', 'dependency': '0.5.1', 'forked': '1.4.0', 'html': '3.1.1', 'metadata': '1.11.0', 'ordering': '0.6', 'playwright': '0.3.0', 'rerunfailures': '10.2', 'xdist': '2.5.0'}, 'JAVA_HOME': 'C:\\java_tools\\adopt-openjdk-1.8.0_265', 'Base URL': ''}
rootdir: C:\source_code\python_work\python-tutorial\pytests\interface
plugins: allure-pytest-2.9.45, assume-2.4.3, base-url-2.0.0, dependency-0.5.1, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, playwright-0.3.0, rerunfailures-10.2, xdist-2.5.0
collecting ... collected 2 items

itf_demo.py::test_baidu_search_parametrize[pytest] PASSED
itf_demo.py::test_baidu_search_parametrize[python] PASSED

============================== 2 passed in 0.80s ==============================
```


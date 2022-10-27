## pytest的conftest.py配置文件

## conftest.py是什么？

conftest.py是fixture函数的一个集合，可以理解为公共的提取出来放在一个文件里，然后供其它模块调用。不同于普通被调用的模块，conftest.py使用时不需要导入，Pytest会自动查找。

## conftest.py使用场景

如果我们有很多个前置函数，写在各个py文件中是不很乱？再或者说，我们很多个py文件想要使用同一个前置函数该怎么办？这也就是conftest.py的作用

## conftest.py使用原则

conftest.py这个文件名是固定的，不可以更改。

conftest.py与运行用例在同一个包下，并且该包中有__init__.py文件使用的时候

在编写不需要导入conftest.py，会自动寻找。

## conftest.py文件特点：

**1、可以跨.py文件调用，有多个.py文件调用时，可让conftest.py只调用了一次fixture，或调用多次fixture**

**2、conftest.py与运行的用例要在同一个pakage下，并且有__init__.py文件**

**3、不需要import导入 conftest.py，pytest用例会自动识别该文件，放到项目的根目录下就可以全局目录调用了，如果放到某个package下，那就在该package内有效，可有多个conftest.py**

**4、conftest.py配置脚本名称是固定的，不能改名称**

**5、conftest.py文件不能被其他文件导入**

**6、所有同目录测试文件运行前都会执行conftest.py文件**

## conftest应用场景

1、每个接口需共用到的token

2、每个接口需共用到的测试用例数据

3、每个接口需共用到的配置信息

## conftest用法结合fixture使用：

可以参考之前的

### fixture的作用范围

fixture里面有个scope参数可以控制fixture的作用范围：session>module>class>function

```
-function：每一个函数或方法都会调用，function默认模式@pytest.fixture(scope='function')或 @pytest.fixture()

-class：每一个类调用一次，一个类中可以有多个方法

-module：每一个.py文件调用一次，一个模块中又有多个function和class

-session：多个文件调用一次，可以跨.py文件调用
```

### conftest结合fixture的使用

```
conftest中fixture的scope参数为session，所有测试.py文件执行前执行一次

conftest中fixture的scope参数为module，每一个测试.py文件执行前都会执行一次conftest文件中的fixture

conftest中fixture的scope参数为class，每一个测试文件中的测试类执行前都会执行一次conftest文件中的fixture

conftest中fixture的scope参数为function，所有文件的测试用例执行前都会执行一次conftest文件中的fixture
```

## 代码示例：

**conftest.py使用举例：**

conftest.py文件（scope=“session”）：

```
import pytest
@pytest.fixture(scope="session")
def login():
    print("输入账号密码")
    yield
    print("清理数据完成")
```



case文件：

```
import pytest
class TestLogin1():
    def test_1(self, login):
        print("用例1")

    def test_2(self):
        print("用例2")

    def test_3(self, login):
        print("用例3")
if __name__ == '__main__':
    pytest.main()
```


## 安装和入门

pytest 是一个能够简化测试系统构建、方便测试规模扩展的框架，它让测试变得更具表现力和可读性。只需要几分钟的时间，你就可以开始一个简单的单元测试或者复杂的功能测试。

## 安装 pytest

1. 在命令行执行以下命令：

   ```bash
   $ pip install pytest==7.0.1
   ```

   当然这里可以不指定版本号，直接下载最新版本的即可

2. 检查版本：

   ```bash
   $ pytest --version
   pytest 7.0.1
   ```

## 创建你的第一个测试用例

```python
import pytest


def func(x):
    return x + 1


def test_answer():
    assert func(3) == 5


if __name__ == '__main__':
    pytest.main()
```

> 大家可以用于 pytest test_sample.py执行，不过需要进入到test_sample的文件目录内

```
==================== test session starts ====================
platform win32 -- Python 3.8.9, pytest-7.0.1, pluggy-1.0.0
rootdir: C:\python_work\python-tutorial\pytests
plugins: allure-pytest-2.9.45, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, rerunfailures-10.2, xdist-2.5.0
collected 1 item                                                                                                                                    
test_sample.py F                                       
========================== FAILURES ========================== [100%]
________________________ test_answer _________________________

    def test_answer():
>       assert func(3) == 5
E       assert 4 == 5
E        +  where 4 = func(3)

test_sample.py:19: AssertionError
================== short test summary info ===================
FAILED test_sample.py::test_answer - assert 4 == 5
===================== 1 failed in 0.18s ======================

```

`[100%]`表示执行所有测试用例的总体进度。因为`func(3)`不等于`5`，所以最后 pytest 会显示一个表示测试失败的报告。

Note

> 在上面的例子中，我们直接使用`assert`来验证测试用例中的预期结果，这是因为 pytest 提供了高级的断言自省功能，可以智能的为我们展示失败处的中间信息（`where 4 = func(3)`），因此我们就无需使用`assertEqual`、`assertNotEqual`之类的方法。

## 执行多个测试用例

pytest 会执行当前及其子文件夹中，所有命名符合`test_*.py`或者`*_test.py`规则的文件中的所有的测试用例；

## 触发一个指定异常的断言

使用`raises`可以验证某些代码是否抛出一个指定的异常：

```python

import pytest


def f():
    # 请求退出解释器
    raise SystemExit(1)


def test_mytest():
    with pytest.raises(SystemExit):
        f()


if __name__ == '__main__':
    pytest.main(["test_sysexit.py"])
```



```
=================== test session starts ===================
platform win32 -- Python 3.8.9, pytest-7.0.1, pluggy-1.0.0
rootdir: C:\python_work\python-tutorial\pytests
plugins: allure-pytest-2.9.45, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, rerunfailures-10.2, xdist-2.5.0
collected 1 item

sample\test_sysexit.py .                                                                        [100%]

==================== 1 passed in 0.03s ==================
```



## 在一个类中组织多个测试用例

```python
class TestClass:
    # 判断其是否存在包含的关系
    def test_one(self):
        x = "this"
        assert "h" in x

    def test_two(self):
        x = "hello"
        assert hasattr(x, "check")
```

我们无需让测试类继承自任何基类，但是要确保类名的前缀为`Test`，否则将忽略其中的测试用例。通过传入文件路径就可以执行这个测试类：

```
pytest sample/test_class.py
====================================== test session starts ======================================
platform win32 -- Python 3.8.9, pytest-7.0.1, pluggy-1.0.0
rootdir: C:\python_work\python-tutorial\pytests
plugins: allure-pytest-2.9.45, forked-1.4.0, html-3.1.1, metadata-1.11.0, ordering-0.6, rerunfailures-10.2, xdist-2.5.0
collected 2 items

sample\test_class.py .F                                                                    [100%]

=========================================== FAILURES ============================================
______________________________________ TestClass.test_two _______________________________________

self = <pytests.sample.test_class.TestClass object at 0x0000022E806BCAC0>

    def test_two(self):
        x = "hello"
>       assert hasattr(x, "check")
E       AssertionError: assert False
E        +  where False = hasattr('hello', 'check')

sample\test_class.py:21: AssertionError
==================================== short test summary info ====================================
FAILED sample/test_class.py::TestClass::test_two - AssertionError: assert False
================================== 1 failed, 1 passed in 0.12s ==================================
```

从输出的结果中我们可以看到：

- `test_one`测试成功，用`.`表示；`test_two`测试失败，用`F`表示；
- 并且我们清楚的看到`test_two`失败处的中间值的信息：`where False = hasattr('hello', 'check')`

测试类要符合特定的规则，pytest 才能发现它：

- 测试类的命名要符合`Test*`规则；

- 测试类中不能有 \__init__()

  方法，否则 pytest 无法采集到其中的测试用例；

  - `PytestCollectionWarning: cannot collect test class 'TestClass' because it has a __init__ constructor.`

在类中组织多个测试用例的好处体现以下几个方面：

- 结构化测试的组织；
- 仅在指定的类中共享`fixture`；
- 应用在类上的`marker`将隐式的应用于其中所有的测试用例上；

需要注意的一点是，测试类中的每个用例都拥有该类的唯一实例。这是因为如果让所有测试用例共享同一个类实例，将不利于测试的隔离。
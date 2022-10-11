## 使用和调用

## 通过调用pytest `python -m pytest`

您可以从命令行通过python解释器调用测试：

```
python -m pytest [...]
```

这几乎等同于调用命令行脚本 `pytest [...]` 直接，除了通过 `python` 还将当前目录添加到 `sys.path` .

## 可能的出口代码

运行 `pytest` 可能导致六种不同的退出代码：

- 退出代码0 ：所有测试都已收集并成功通过
- 退出代码1 ：测试已收集并运行，但有些测试失败
- 退出代码2 ：测试执行被用户中断
- 退出代码3 ：执行测试时发生内部错误
- 退出代码4 ：pytest命令行使用错误
- 退出代码5 ：未收集任何测试

他们的代表是 [`pytest.ExitCode`](https://www.osgeo.cn/pytest/reference.html#pytest.ExitCode) 枚举。作为公共API的一部分的出口代码可以通过以下方式直接导入和访问：

```
from pytest import ExitCode
```

注解

如果您想在某些情况下自定义退出代码，特别是在没有收集测试的情况下，请考虑使用 [pytest-custom_exit_code](https://github.com/yashtodi94/pytest-custom_exit_code) 插件。

## 获取有关版本、选项名称、环境变量的帮助

```
pytest --version   # shows where pytest was imported from
pytest --fixtures  # show available builtin function arguments
pytest -h | --help # show help on command line and config file options
```

完整的命令行标志可以在 [reference](https://www.osgeo.cn/pytest/reference.html#command-line-flags) .

## 第一次（或n次）故障后停止

在第一（n）次失败后停止测试过程：

```
pytest -x           # stop after first failure
pytest --maxfail=2  # stop after two failures
```



## 指定测试/选择测试

pytest支持几种方法来运行和从命令行中选择测试。

**在模块中运行测试**

```
pytest test_mod.py
```

**在目录中运行测试**

```
pytest testing/
```

**按关键字表达式运行测试**

```
pytest -k "MyClass and not method"
```

这将运行包含与给定名称匹配的名称的测试 *字符串表达式* （不区分大小写），它可以包括使用文件名、类名和函数名作为变量的Python运算符。上面的例子将运行 `TestMyClass.test_something` 但不是 `TestMyClass.test_method_simple` .

每个收集的测试都被分配一个唯一的 `nodeid` 它由模块文件名和诸如类名、函数名和参数化参数等说明符组成，用 `::` 字符。

在模块内运行特定测试：

```
pytest test_mod.py::test_func
```

在命令行中指定测试方法的另一个示例：

```
pytest test_mod.py::TestClass::test_method
```

**Run tests by marker expressions**

```
pytest -m slow
```

## 详细总结报告

这个 `-r` 标记可用于在测试会话结束时显示“简短测试摘要信息”，使大型测试套件中的所有故障、跳过、xfails等的清晰图像变得容易。

它默认为 `fE` 列出失败和错误。

```python
import pytest


@pytest.fixture
def error_fixture():
    assert 0


def test_ok():
    print("ok")


def test_fail():
    assert 0


def test_error(error_fixture):
    pass


def test_skip():
    pytest.skip("skipping this test")


def test_xfail():
    pytest.xfail("xfailing this test")


@pytest.mark.xfail(reason="always xfail")
def test_xpass():
    pass
```

```
usage\test_example.py .FEsxX                                                               [100%]

============================================ ERRORS =============================================
_________________________________ ERROR at setup of test_error __________________________________

    @pytest.fixture
    def error_fixture():
>       assert 0
E       assert 0

usage\test_example.py:17: AssertionError
=========================================== FAILURES ============================================
___________________________________________ test_fail ___________________________________________

    def test_fail():
>       assert 0
E       assert 0

usage\test_example.py:25: AssertionError
==================================== short test summary info ====================================
SKIPPED [1] usage\test_example.py:33: skipping this test
XFAIL usage/test_example.py::test_xfail
  reason: xfailing this test
XPASS usage/test_example.py::test_xpass always xfail
ERROR usage/test_example.py::test_error - assert 0
FAILED usage/test_example.py::test_fail - assert 0
============= 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.16s =============
```

这个 `-r` 选项接受其后面的字符数，使用 `a` 上面的意思是“除通行证外的所有通行证”。

以下是可用字符的完整列表：

> - `f` -失败
> - `E` -误差
> - `s` 跳过
> - `x` -失败
> - `X` -XPASS
> - `p` 通过
> - `P` -通过输出

用于（取消）选择组的特殊字符：

> - `a` - all except `pP`
> - `A` -所有
> - `N` -无，这不能用来显示任何内容（因为 `fE` 是默认设置）

可以使用多个字符，例如，要只查看失败和跳过的测试，可以执行：

```python
if __name__ == '__main__':
    pytest.main(["-rfs", "test_example.py"])
```

或者

```powershell
pytest -rfs usage/test_example.py
```

使用 `p` 列出通过的测试，同时 `P` 添加一个额外的“通过”部分，其中包含那些通过但已捕获输出的测试

```
pytest -rpP usage/test_example.py
```
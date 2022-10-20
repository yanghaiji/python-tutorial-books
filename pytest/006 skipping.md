## 跳过和xfail：处理无法成功的测试

您可以标记不能在某些平台上运行或预期会失败的测试函数，以便pytest可以相应地处理它们，并在保留测试套件的同时提供测试会话的摘要 *绿色* .

A **skip** 意味着您希望只有在满足某些条件时测试才能通过，否则pytest应该跳过运行测试。常见的例子是跳过非Windows平台上的仅限Windows的测试，或者跳过依赖于当前不可用的外部资源（例如数据库）的测试。

安 **Xfail** 意味着您希望某个测试由于某种原因而失败。一个常见的例子是对尚未实现的特性或尚未修复的错误的测试。当测试通过时，尽管预期会失败（标记为 `pytest.mark.xfail` 这是一个 **XPASS** 并将在测试总结中报告。

`pytest` 计数和列表 *skip* 和 *X失效* 单独测试。默认情况下，不会显示有关跳过的/xfailed测试的详细信息，以避免混淆输出。你可以使用 `-r` 查看与测试进度中显示的“短”字母对应的详细信息的选项：

```
pytest -rxXs  # show extra info on xfailed, xpassed, and skipped tests
```

有关 `-r` 可以通过运行找到选项 `pytest -h` .也可以参考上一篇文章[Pytest 常用的使用和调用方式](usage.md)

## 跳过测试函数

跳过测试函数的最简单方法是用 `skip` 可以传递可选 `reason` ：

```python
@pytest.mark.skip(reason="no way of currently testing this")
def test_the_unknown():
    print("被跳过的测试")


if __name__ == '__main__':
    pytest.main(['-rxXS', 'skip_xfail.py'])
```

或者，也可以通过调用 `pytest.skip(reason)` 功能：

```python
def test_function():
    if not 1 == 2:
        pytest.skip("此表达式不成立，将跳过")


if __name__ == '__main__':
    pytest.main(['-rxXS', 'skip_xfail.py'])
```

当无法在导入期间评估跳过条件时，命令式方法非常有用。

也可以跳过整个模块，使用 `pytest.skip(reason, allow_module_level=True)` 在模块级别：

```
import sys
import pytest

if not sys.platform.startswith("win"):
    pytest.skip("skipping windows-only tests", allow_module_level=True)
```

### `skipif`

如果您希望有条件地跳过某些内容，则可以使用 `skipif` 相反。以下是在python3.6之前的解释器上运行时将测试函数标记为跳过的示例：

```python
@pytest.mark.skipif(sys.version_info < (3, 7), reason="requires python3.7 or higher")
def test_function_skipif():
    print("当你安装的python 版本小于3.7时，将提示你需要升级")


if __name__ == '__main__':
    print(sys.version_info)
    pytest.main(['-rxXS', 'skip_xfail.py'])
```

如果条件评估为 `True` 在收集期间，将跳过测试函数，使用时在摘要中显示指定的原因 `-rs` .

你可以分享 `skipif` 模块之间的标记。考虑这个测试模块：

```
# content of test_mymodule.py
import mymodule

minversion = pytest.mark.skipif(
    mymodule.__versioninfo__ < (1, 1), reason="at least mymodule-1.1 required"
)


@minversion
def test_function():
    ...
```

您可以导入标记并在另一个测试模块中重用它：

```
# test_myothermodule.py
from test_mymodule import minversion


@minversion
def test_anotherfunction():
    ...
```

对于较大的测试套件，最好有一个文件定义标记，然后在整个测试套件中一致地应用这些标记。

或者，您可以使用 [condition strings](https://www.osgeo.cn/pytest/historical-notes.html#string-conditions) 而不是布尔值，但是它们不容易在模块之间共享，因此主要是出于向后兼容性的原因才支持它们。

**参考文献** ： [pytest.mark.skipif](https://www.osgeo.cn/pytest/reference.html#pytest-mark-skipif-ref)

### 跳过类或模块的所有测试函数

你可以使用 `skipif` 类上的标记（作为任何其他标记）：

```python
@pytest.mark.skipif(sys.platform == "win32", reason="does not run on windows")
class TestPosixCalls:
    def test_function_class(self):
        "will not be setup or run under 'win32' platform"


if __name__ == '__main__':
    print(sys.version_info)
    pytest.main(['-rxXS', 'skip_xfail.py'])
```

如果条件是 `True` ，此标记将为该类的每个测试方法生成跳过结果。

### 跳过缺少的导入依赖项

您可以使用 [pytest.importorskip](https://www.osgeo.cn/pytest/reference.html#pytest-importorskip-ref) 在模块级，在测试或测试设置功能内。

```
docutils = pytest.importorskip("docutils")
```

如果 `docutils` 无法在此处导入，这将导致跳过测试结果。也可以根据库的版本号跳过：

```
docutils = pytest.importorskip("docutils", minversion="0.3")
```

版本将从指定的模块中读取 `__version__` 属性。

### 总结

以下是如何在不同情况下跳过模块中的测试的快速指南：

1. 无条件跳过模块中的所有测试：

> ```
> pytestmark = pytest.mark.skip("all tests still WIP")
> ```

1. 根据某些条件跳过模块中的所有测试：

> ```
> pytestmark = pytest.mark.skipif(sys.platform == "win32", reason="tests for linux only")
> ```

1. 如果缺少某些导入，则跳过模块中的所有测试：

> ```
> pexpect = pytest.importorskip("pexpect")
> ```



## xfail：将测试功能标记为预期失败

你可以使用 `xfail` 标记以指示预期测试失败：

```python
@pytest.mark.xfail
def test_function_xfailed():
    print(1 / 0)


@pytest.mark.xfail
def test_function_xpassed():
    print(1 / 1)


if __name__ == '__main__':
    print(sys.version_info)
    pytest.main(['-rxXS', 'skip_xfail.py'])
```

此测试将运行，但失败时不会报告回溯。相反，终端报告会将其列在“预期失败”中 (`XFAIL` ）或“意外通过” (`XPASS` 部分。

### `condition` 参数

如果测试只在特定条件下失败，则可以将该条件作为第一个参数通过：

```python
@pytest.mark.xfail(sys.platform == "win32", reason="bug in a 3rd party library")
def test_function():
    ...
```

请注意，您还必须传递一个原因（请参阅 [pytest.mark.xfail](https://www.osgeo.cn/pytest/reference.html#pytest-mark-xfail-ref) ）

### `reason` 参数

可以使用 `reason` 参数：

```
@pytest.mark.xfail(reason="known parser issue")
def test_function():
    ...
```

### `raises` 参数

如果要更具体地说明测试失败的原因，可以在 `raises` 参数。

```
@pytest.mark.xfail(raises=RuntimeError)
def test_function():
    ...
```

如果测试失败，则报告为常规失败，除非 `raises` .

### `run` 参数

如果测试应标记为xfail并报告为xfail，但不应执行，则使用 `run` 参数AS `False` ：

```
@pytest.mark.xfail(run=False)
def test_function():
    ...
```

这对于Xfailing测试特别有用，这些测试会使解释器崩溃，应该稍后进行调查。



### `strict` 参数

两个 `XFAIL` 和 `XPASS` 默认情况下不要让测试套件失败。您可以通过设置 `strict` 仅关键字参数到 `True` ：

```
@pytest.mark.xfail(strict=True)
def test_function():
    ...
```

这将使 `XPASS` 此测试的（“意外通过”）结果将使测试套件失败。

您可以更改 `strict` 参数使用 `xfail_strict` ini选项：

```
[pytest]
xfail_strict=true
```

### 忽略x失败

通过在命令行上指定：

```
pytest --runxfail
```

您可以强制运行和报告 `xfail` 将测试标记为完全没有标记。这也导致 [`pytest.xfail()`](https://www.osgeo.cn/pytest/reference.html#pytest.xfail) 不产生效果。

### 实例

下面是一个简单的测试文件，有几种用法：

```python
import pytest

xfail = pytest.mark.xfail


@xfail
def test_hello():
    assert 0


@xfail(run=False)
def test_hello2():
    assert 0


@xfail("hasattr(os, 'sep')")
def test_hello3():
    assert 0


@xfail(reason="bug 110")
def test_hello4():
    assert 0


@xfail('pytest.__version__[0] != "17"')
def test_hello5():
    assert 0


def test_hello6():
    pytest.xfail("reason")


@xfail(raises=IndexError)
def test_hello7():
    x = []
    x[1] = 1
```

使用“报告xfail”选项运行它可以得到以下输出：

```
collected 7 items

skip_xfail02.py xxxxxxx                                                  [100%]

=========================== short test summary info ===========================
XFAIL skip_xfail02.py::test_hello
XFAIL skip_xfail02.py::test_hello2
  reason: [NOTRUN] 
XFAIL skip_xfail02.py::test_hello3
  condition: hasattr(os, 'sep')
XFAIL skip_xfail02.py::test_hello4
  bug 110
XFAIL skip_xfail02.py::test_hello5
  condition: pytest.__version__[0] != "17"
XFAIL skip_xfail02.py::test_hello6
  reason: reason
XFAIL skip_xfail02.py::test_hello7
============================= 7 xfailed in 0.18s ==============================
```

## 跳过/xfail，参数化

当使用参数化时，可以对单个测试实例应用诸如skip和xfail之类的标记：

```python
import sys
import pytest


@pytest.mark.parametrize(
    ("n", "expected"),
    [
        (1, 2),
        pytest.param(1, 0, marks=pytest.mark.xfail),
        pytest.param(1, 3, marks=pytest.mark.xfail(reason="some bug")),
        (2, 3),
        (3, 4),
        (4, 5),
        pytest.param(
            10, 11, marks=pytest.mark.skipif(sys.version_info >= (3, 0), reason="py2k")
        ),
    ],
)
def test_increment(n, expected):
    assert n + 1 == expected
```
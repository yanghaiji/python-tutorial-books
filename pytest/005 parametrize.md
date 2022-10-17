## 参数化工具和测试功能

Pytest可以在多个级别上实现测试参数化：

- [`pytest.fixture()`](https://www.osgeo.cn/pytest/reference.html#pytest.fixture) 允许一个 [parametrize fixture functions](https://www.osgeo.cn/pytest/fixture.html#fixture-parametrize) .

- [@pytest.mark.parametrize](https://www.osgeo.cn/pytest/parametrize.html#pytest-mark-parametrize) 允许在测试函数或类中定义多组参数和设备。
- [pytest_generate_tests](https://www.osgeo.cn/pytest/parametrize.html#pytest-generate-tests) 允许定义自定义参数化方案或扩展。

## `@pytest.mark.parametrize` 

下面是一个测试函数的典型示例，它执行检查某个输入是否会导致预期的输出：

```python
import pytest


@pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

这里， `@parametrize` decorator定义了三个不同的 `(test_input,expected)` 使 `test_eval` 函数将依次运行三次：

```
collected 3 items

parametrize.py ..F                                                       [100%]

================================== FAILURES ===================================
______________________________ test_eval[6*9-42] ______________________________

test_input = '6*9', expected = 42

    @pytest.mark.parametrize("test_input,expected", [("3+5", 8), ("2+4", 6), ("6*9", 42)])
    def test_eval(test_input, expected):
>       assert eval(test_input) == expected
E       AssertionError: assert 54 == 42
E        +  where 54 = eval('6*9')

parametrize.py:17: AssertionError
========================= 1 failed, 2 passed in 0.11s =========================
```

注解

参数值按原样传递给测试（没有任何副本）。

例如，如果您传递一个list或dict作为参数值，并且测试用例代码对其进行了变异，那么这些变化将反映在后续的测试用例调用中。情况下，pytest会对Unicode字符串中用于参数化的任何非ASCII字符进行转义，因为它有几个缺点。但是，如果您希望在参数化中使用Unicode字符串并在终端中看到它们（非转义），请在 `pytest.ini` ：

```
[pytest]
disable_test_id_escaping_and_forfeit_all_rights_to_community_support = True
```

但是请记住，这可能会导致不必要的副作用，甚至错误，这取决于所使用的操作系统和当前安装的插件。

正如本例中设计的，只有一对输入/输出值未通过简单的测试功能。和通常的测试函数参数一样，您可以看到 `input` 和 `output` 回溯中的值。

注意，也可以在类或模块上使用参数化标记（请参见 [用属性标记测试函数](https://www.osgeo.cn/pytest/mark.html#mark) ）它将使用参数集调用多个函数。

也可以在参数化中标记单个测试实例，例如使用内置 `mark.xfail` ：

```python
import pytest


@pytest.mark.parametrize(
    "test_input,expected",
    [("3+5", 8), ("2+4", 6), pytest.param("6*9", 42, marks=pytest.mark.xfail)],
)
def test_eval2(test_input, expected):
    assert eval(test_input) == expected
```

让我们来运行：

```
collected 3 items

parametrize.py ..x                                                       [100%]

======================== 2 passed, 1 xfailed in 0.10s =========================
```

以前导致失败的一个参数集现在显示为“xfailed”（预期失败）测试。

如果提供给 `parametrize` 结果是一个空列表-例如，如果它们是由某个函数动态生成的-pytest的行为由 [`empty_parameter_set_mark`](https://www.osgeo.cn/pytest/reference.html#confval-empty_parameter_set_mark) 选择权。

要获取多个参数化参数的所有组合，可以将 `parametrize` 装饰者：

```python
@pytest.mark.parametrize("x", [0, 1])
@pytest.mark.parametrize("y", [2, 3])
def test_foo(x, y):
    print("------------")
    print("x = ", x)
    print("y = ", y)
```



```
collected 4 items

------------

x =  0
y =  2
.------------
x =  1
y =  2
.------------
x =  0
y =  3
.------------
x =  1
y =  3

============================== 4 passed in 0.03s ==============================
```

## 基本的 `pytest_generate_tests` 例子

有时，您可能希望实现自己的参数化方案，或者实现一些动态机制来确定夹具的参数或范围。为此，您可以使用 `pytest_generate_tests` 在收集测试函数时调用的钩子。通过传入 `metafunc` 对象可以检查请求的测试上下文，最重要的是，可以调用 `metafunc.parametrize()` 使参数化。

例如，假设我们想运行一个测试，并通过一个新的 `pytest` 命令行选项。让我们先编写一个简单的测试来接受 `stringinput` fixture函数参数：

```
# content of test_strings.py


def test_valid_string(stringinput):
    assert stringinput.isalpha()
```

现在我们添加一个 `conftest.py` 包含添加命令行选项和测试函数参数化的文件：

```
# content of conftest.py


def pytest_addoption(parser):
    parser.addoption(
        "--stringinput",
        action="append",
        default=[],
        help="list of stringinputs to pass to test functions",
    )


def pytest_generate_tests(metafunc):
    if "stringinput" in metafunc.fixturenames:
        metafunc.parametrize("stringinput", metafunc.config.getoption("stringinput"))
```

如果现在我们通过两个StringInput值，则测试将运行两次：

```
$ pytest -q --stringinput="hello" --stringinput="world" test_strings.py
..                                                                   [100%]
2 passed in 0.12s
```

让我们还使用将导致测试失败的StringInput运行：

```
$ pytest -q --stringinput="!" test_strings.py
F                                                                    [100%]
================================= FAILURES =================================
___________________________ test_valid_string[!] ___________________________

stringinput = '!'

    def test_valid_string(stringinput):
>       assert stringinput.isalpha()
E       AssertionError: assert False
E        +  where False = <built-in method isalpha of str object at 0xdeadbeef>()
E        +    where <built-in method isalpha of str object at 0xdeadbeef> = '!'.isalpha

test_strings.py:4: AssertionError
========================= short test summary info ==========================
FAILED test_strings.py::test_valid_string[!] - AssertionError: assert False
1 failed in 0.12s
```

如预期，我们的测试功能失败。

如果不指定StringInput，将跳过它，因为 `metafunc.parametrize()` 将使用空参数列表调用：

```
$ pytest -q -rs test_strings.py
s                                                                    [100%]
========================= short test summary info ==========================
SKIPPED [1] test_strings.py: got empty parameter set ['stringinput'], function test_valid_string at $REGENDOC_TMPDIR/test_strings.py:2
1 skipped in 0.12s
```

调用时请注意 `metafunc.parametrize` 使用不同参数集多次，这些集之间的所有参数名称都不能重复，否则将引发错误。

## 更多例子

如需更多示例，您可能需要查看 [more parametrization examples](https://www.osgeo.cn/pytest/example/parametrize.html#paramexamples) .
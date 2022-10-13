# 编写断言

## 使用`assert`编写断言

pytest 允许你使用`python`标准的`assert`表达式写断言；

例如：

```python
# src/chapter-3/test_assert1.py

def func(x):
    return x + 1


def test_function():
    assert func(2) == 4
```

如果这个断言失败，你会看到`func(2)`实际的返回值`+ where 3 = func(2)`：

```bash
$ pipenv run pytest -q src/chapter-3/test_assert1.py
F                                                                              [100%]
====================================== FAILURES ======================================
___________________________________ test_function ____________________________________

    def test_function():
>       assert func(2) == 4
E       assert 3 == 4
E        +  where 3 = func(2)

src/chapter-3/test_assert1.py:6: AssertionError
============================== short test summary info ===============================
FAILED src/chapter-3/test_assert1.py::test_function - assert 3 == 4
1 failed in 0.15s
```

`pytest`支持显示常见的`python`子表达式的值，包括：调用、属性、比较、二进制和一元运算符等，这允许你在没有模版代码参考的情况下，可以使用的`python`的数据结构，而无须担心自省丢失的问题。

同时，你也可以为断言指定了一条说明信息，用于失败时的情况说明：

```bash
assert a % 2 == 0, "value was odd, should be even"
```

## 编写触发期望异常的断言

你可以使用`pytest.raises()`作为上下文管理器，来编写一个触发期望异常的断言。

例如，我们有如下测试用例：

```python
import pytest


def myfunc():
    raise ValueError("Exception 123 raised")


def test_match():
    with pytest.raises(ValueError):
        myfunc()
```

当用例没有返回`ValueError`或者没有异常返回时，断言判断失败；

如果你希望同时访问异常的属性，可以这样：

```python
import pytest


def myfunc():
    raise ValueError("Exception 123 raised")


def test_match():
    with pytest.raises(ValueError) as excinfo:
        myfunc()
    assert '123' in str(excinfo.value)
```

其中，`excinfo`是[ExceptionInfo](https://docs.pytest.org/en/6.1.1/reference.html#exceptioninfo)的一个实例，它封装了异常的信息；常用的属性包括：`.type`、`.value`和`.traceback`；

Note

在上下文管理器的作用域中，raises代码必须是最后一行，否则，其后面的代码将不会执行；所以，如果上述例子改成：

```bash
def test_match():
  with pytest.raises(ValueError) as excinfo:
      myfunc()
      assert '456' in str(excinfo.value)
```

则测试将永远成功，因为`assert '456' in str(excinfo.value)`并不会执行；

我们也可以给`pytest.raises()`传递一个关键字参数`match`，来测试异常的字符串表示`str(excinfo.value)`是否符合给定的正则表达式（和`unittest`中的`TestCase.assertRaisesRegexp`方法类似）：

```python
import pytest


def myfunc():
    raise ValueError("Exception 123 raised")


def test_match():
    with pytest.raises((ValueError, RuntimeError), match=r'.* 123 .*'):
        myfunc()
```

pytest 实际调用的是`re.search()`方法来做上述检查。并且，`pytest.raises()`也支持检查多个期望异常（以元组的形式传递参数），我们只需要触发其中任意一个。

`pytest.raises`还有另外的一种使用形式。

首先，我们来看一下它在源码中的定义：

```python
# _pytest/python_api.py

def raises(  # noqa: F811
    expected_exception: Union["Type[_E]", Tuple["Type[_E]", ...]],
    *args: Any,
    **kwargs: Any
) -> Union["RaisesContext[_E]", _pytest._code.ExceptionInfo[_E]]:
```

它接收一个位置参数`expected_exception`，一组可变参数`args`，一组关键字参数`kwargs`；

接着看方法的主体内容：

```python
# _pytest/python_api.py

    if isinstance(expected_exception, type):
        excepted_exceptions = (expected_exception,)  # type: Tuple[Type[_E], ...]
    else:
        excepted_exceptions = expected_exception
    for exc in excepted_exceptions:
        if not isinstance(exc, type) or not issubclass(exc, BaseException):  # type: ignore[unreachable]
            msg = "expected exception must be a BaseException type, not {}"  # type: ignore[unreachable]
            not_a = exc.__name__ if isinstance(exc, type) else type(exc).__name__
            raise TypeError(msg.format(not_a))

    message = "DID NOT RAISE {}".format(expected_exception)

    if not args:
        match = kwargs.pop("match", None)  # type: Optional[Union[str, Pattern[str]]]
        if kwargs:
            msg = "Unexpected keyword arguments passed to pytest.raises: "
            msg += ", ".join(sorted(kwargs))
            msg += "\nUse context-manager form instead?"
            raise TypeError(msg)
        return RaisesContext(expected_exception, message, match)
    else:
        func = args[0]
        if not callable(func):
            raise TypeError(
                "{!r} object (type: {}) must be callable".format(func, type(func))
            )
        try:
            func(*args[1:], **kwargs)
        except expected_exception as e:
            # We just caught the exception - there is a traceback.
            assert e.__traceback__ is not None
            return _pytest._code.ExceptionInfo.from_exc_info(
                (type(e), e, e.__traceback__)
            )
    fail(message)
```

我们可以看到，如果没有传入可变参数`args`，那么关键字参数`kwargs`只能包含`match`关键字，否则会上报`TypeError`异常。

```python
        match = kwargs.pop("match", None)  # type: Optional[Union[str, Pattern[str]]]
        if kwargs:
            msg = "Unexpected keyword arguments passed to pytest.raises: "
            msg += ", ".join(sorted(kwargs))
            msg += "\nUse context-manager form instead?"
            raise TypeError(msg)
```

如果传入可变参数`args`，那么它的第一个参数必须是一个可调用的对象，否则会报`TypeError`异常；

同时，它会把剩余的`args`参数和所有`kwargs`参数传递给这个可调用对象，然后检查这个对象执行之后是否触发指定异常。

```python
        func = args[0]
        if not callable(func):
            raise TypeError(
                "{!r} object (type: {}) must be callable".format(func, type(func))
            )
        try:
            func(*args[1:], **kwargs)
        except expected_exception as e:
            ...
```

所以我们有了一种新的写法：

```python
def f():
    raise ValueError("123")

pytest.raises(ValueError, f)
```

也可以传入一个`lambda`表达式：

```python
pytest.raises(ZeroDivisionError, lambda x: 1/x, 0)

# 或者

pytest.raises(ZeroDivisionError, lambda x: 1/x, x=0)
```

这个时候如果你再传递`match`参数，是不生效的，因为它只有在`if not args:`的时候生效；

`pytest.mark.xfail()`也可以接收一个`raises`参数，来判断用例是否因为一个具体的异常而导致失败：

```python
@pytest.mark.xfail(raises=IndexError)
def test_f():
    f()
```

如果`f()`触发一个`IndexError`异常，则用例标记为`xfailed`；如果没有，则正常执行`f()`；

Note

- 如果`test_f`测试成功，用例的结果是`xpassed`，而不是`passed`；
- `pytest.raises`适用于检查由代码故意引发的异常；而`@pytest.mark.xfail()`更适合用于记录一些未修复的 Bug；



## 编写触发期望告警的断言

我们也可以利用[pytest.warns](https://docs.pytest.org/en/6.1.1/warnings.html#warns)来编写触发期望告警的断言，它的用法和上面的`pytest.raises`非常相似。

## 特殊数据结构比较时的优化

我们来看这个下面这个测试用例：

```python
# src/chapter-3/test_special_compare.py

def test_set_comparison():
    set1 = set('1308')
    set2 = set('8035')
    assert set1 == set2


def test_long_str_comparison():
    str1 = 'show me codes'
    str2 = 'show me money'
    assert str1 == str2


def test_dict_comparison():
    dict1 = {
        'x': 1,
        'y': 2,
    }
    dict2 = {
        'x': 1,
        'y': 1,
    }
    assert dict1 == dict2
```

我们比较了三种数据结构：集合、字符串和字典，下面我们来执行测试：

```bash
$ pipenv run pytest -q src/chapter-3/test_special_compare.py
FFF                                                                            [100%]
====================================== FAILURES ======================================
________________________________ test_set_comparison _________________________________

    def test_set_comparison():
        set1 = set("1308")
        set2 = set("8035")
>       assert set1 == set2
E       AssertionError: assert {'0', '1', '3', '8'} == {'0', '3', '5', '8'}
E         Extra items in the left set:
E         '1'
E         Extra items in the right set:
E         '5'
E         Full diff:
E         - {'8', '3', '5', '0'}
E         + {'3', '8', '1', '0'}

src/chapter-3/test_special_compare.py:4: AssertionError
______________________________ test_long_str_comparison ______________________________

    def test_long_str_comparison():
        str1 = "show me codes"
        str2 = "show me money"
>       assert str1 == str2
E       AssertionError: assert 'show me codes' == 'show me money'
E         - show me money
E         ?         ^ ^ ^
E         + show me codes
E         ?         ^ ^ ^

src/chapter-3/test_special_compare.py:10: AssertionError
________________________________ test_dict_comparison ________________________________

    def test_dict_comparison():
        dict1 = {
            "x": 1,
            "y": 2,
        }
        dict2 = {
            "x": 1,
            "y": 1,
        }
>       assert dict1 == dict2
E       AssertionError: assert {'x': 1, 'y': 2} == {'x': 1, 'y': 1}
E         Omitting 1 identical items, use -vv to show
E         Differing items:
E         {'y': 2} != {'y': 1}
E         Full diff:
E         - {'x': 1, 'y': 1}
E         ?               ^
E         + {'x': 1, 'y': 2}...
E
E         ...Full output truncated (2 lines hidden), use '-vv' to show

src/chapter-3/test_special_compare.py:22: AssertionError
============================== short test summary info ===============================
FAILED src/chapter-3/test_special_compare.py::test_set_comparison - AssertionError:...
FAILED src/chapter-3/test_special_compare.py::test_long_str_comparison - AssertionE...
FAILED src/chapter-3/test_special_compare.py::test_dict_comparison - AssertionError...
3 failed in 0.13s
```

针对一些特殊的数据结构间的比较，pytest 对结果的显示做了一些优化：

- 集合、列表等：标记出第一个不同的元素；
- 字符串：标记出不同的部分；
- 字典：标记出不同的条目；

## 为失败断言添加自定义的说明

我们来看下面这个测试用例：

```python
# src/chapter-3/test_foo_compare.py

class Foo:
    def __init__(self, val):
        self.val = val

    def __eq__(self, other):
        return self.val == other.val


def test_foo_compare():
    f1 = Foo(1)
    f2 = Foo(2)
    assert f1 == f2
```

我们定义了一个`Foo`对象，也复写了它的`__eq__()`方法，但当我们执行这个用例时：

```bash
$ pipenv run pytest -q src/chapter-3/test_foo_compare.py
F                                                                              [100%]
====================================== FAILURES ======================================
__________________________________ test_foo_compare __________________________________

    def test_foo_compare():
        f1 = Foo(1)
        f2 = Foo(2)
>       assert f1 == f2
E       assert <test_foo_compare.Foo object at 0x1076bf250> == <test_foo_compare.Foo object at 0x1076bf6a0>

src/chapter-3/test_foo_compare.py:12: AssertionError
============================== short test summary info ===============================
FAILED src/chapter-3/test_foo_compare.py::test_foo_compare - assert <test_foo_compa...
1 failed in 0.12s
```

我们并不能直观的从中看出来失败的原因：`assert  == `。

在这种情况下，我们有两种优化的方法：

- 复写`Foo`的`__repr__()`方法：

  ```python
  def __repr__(self):
      return str(self.val)
  ```

  我们再执行用例：

  ```bash
  $ pipenv run pytest -q src/chapter-3/test_foo_compare.py
  F                                                                              [100%]
  ====================================== FAILURES ======================================
  __________________________________ test_foo_compare __________________________________
  
      def test_foo_compare():
          f1 = Foo(1)
          f2 = Foo(2)
  >       assert f1 == f2
  E       assert 1 == 2
  
  src/chapter-3/test_foo_compare.py:15: AssertionError
  ============================== short test summary info ===============================
  FAILED src/chapter-3/test_foo_compare.py::test_foo_compare - assert 1 == 2
  ```

  这时，我们能看到失败的原因是因为`1 == 2`不成立；

  Note

  至于__str__()和__repr__()的区别，可以参考StackFlow上的这个问题中的回答：https://stackoverflow.com/questions/1436703/difference-between-str-and-repr

  

- 使用`pytest_assertrepr_compare`这个钩子方法添加自定义的失败说明：

  ```python
  # src/chapter-3/conftest.py
  
  from test_foo_compare import Foo
  
  
  def pytest_assertrepr_compare(op, left, right):
      if isinstance(left, Foo) and isinstance(right, Foo) and op == "==":
          return [
              "比较两个Foo实例:",  # 顶头写概要
              "   值: {} != {}".format(left.val, right.val),  # 除了第一个行，其余都可以缩进
          ]
  ```

  我们再次执行用例：

  ```bas
  $ pipenv run pytest -q src/chapter-3/test_foo_compare.py
  F                                                                              [100%]
  ====================================== FAILURES ======================================
  __________________________________ test_foo_compare __________________________________
  
      def test_foo_compare():
          f1 = Foo(1)
          f2 = Foo(2)
  >       assert f1 == f2
  E       assert 比较两个Foo实例:
  E            值: 1 != 2
  
  src/chapter-3/test_foo_compare.py:15: AssertionError
  ============================== short test summary info ===============================
  FAILED src/chapter-3/test_foo_compare.py::test_foo_compare - assert 比较两个Foo实例:
  1 failed in 0.11s
  ```

  我们会看到一个更友好的失败说明；

## 关于断言自省的细节

当断言失败时，pytest 为我们提供了非常人性化的失败说明，中间往往夹杂着相应变量的自省信息，这个我们称为断言的自省；

那么，pytest 是如何做到这样的：

- pytest 发现测试模块，并引入他们，与此同时，pytest 会复写断言语句，添加自省信息；但是，不是测试模块的断言语句并不会被复写；

### 复写缓存文件

pytest 会把被复写的模块存储到本地作为缓存使用，你可以通过在测试用例的根文件夹中的`conftest.py`里添加如下配置来禁止这种行为；

```python
import sys

sys.dont_write_bytecode = True
```

但是，它并不会妨碍你享受断言自省的好处，只是不会在本地存储`.pyc`文件了。

### 关闭断言自省

你可以通过一下两种方法：

- 在模块的`docstring`中添加`PYTEST_DONT_REWRITE`字符串；
- 执行 pytest 时，添加`--assert=plain`选项；

我们来看一下关闭后的效果：

```bash
pipenv run pytest -q --assert=plain src/chapter-3/test_foo_compare.py
F                                                                              [100%]
====================================== FAILURES ======================================
__________________________________ test_foo_compare __________________________________

    def test_foo_compare():
        f1 = Foo(1)
        f2 = Foo(2)
>       assert f1 == f2
E       AssertionError

src/chapter-3/test_foo_compare.py:15: AssertionError
============================== short test summary info ===============================
FAILED src/chapter-3/test_foo_compare.py::test_foo_compare - AssertionError
1 failed in 0.12s
```

断言失败时的信息就非常的不完整了，我们几乎看不出任何有用的调试信息；
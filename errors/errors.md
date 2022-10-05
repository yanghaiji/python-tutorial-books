## 错误和异常

到目前为止，我们还没有提到错误消息，但是如果你已经尝试过那些例子，你可能已经看过了一些错误消息。 目前（至少）有两种可区分的错误：*语法错误* 和 *异常*。

但是现在大家基本上都在用 `IDEA`的工具，这些工具在语法上都会有提示。

```python
while True print('Hello world')
```

### 异常

即使语句或表达式在语法上是正确的，但在尝试执行时，它仍可能会引发错误。 在执行时检测到的错误被称为 *异常*。

这个异常可能会导致程序出现严重的业务错乱。

```python
def conversion(total):
    num = int(total)
    return "@" * num
```

比如上方的代码，我们的语句或表达式在语法上是正确的。但是这里存在一个潜在的问题就是传输的参数未知，我们无法确定使用着传入的参数。

```python
print(conversion(2))
print(conversion("t"))
```

当我们通过以上的方式调用时，就会发生一下的异常

```
Traceback (most recent call last):
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 22, in <module>
    print(conversion("t"))
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 17, in conversion
    num = int(total)
ValueError: invalid literal for int() with base 10: 't'
```

错误信息的最后一行告诉我们程序遇到了什么类型的错误。异常有不同的类型，而其类型名称将会作为错误信息的一部分中打印出来：上述示例中的异常类型是 `ValueError`。作为异常类型打印的字符串是发生的内置异常的名称。对于所有内置异常都是如此，但对于用户定义的异常则不一定如此（虽然这是一个有用的规范）。标准的异常类型是内置的标识符（而不是保留关键字）。

这一行的剩下的部分根据异常类型及其原因提供详细信息。

错误信息的前一部分以堆栈回溯的形式显示发生异常时的上下文。通常它包含列出源代码行的堆栈回溯；但是它不会显示从标准输入中读取的行。

### 处理异常

我们可以对上面的函数进行改造，我们还是允许用户随意传入值的，但是出现异常时我们进行处理，并在控制台进行输出！

```python
def conversion(total):
    try:
        num = int(total)
        print("@" * num)
    except ValueError as e:
        print("程序处理发生异常，请检查您的参数！")
```

>测试输出：
>
>@@
>程序处理发生异常，请检查您的参数！

[`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句的工作原理如下：

- 首先，执行 *try 子句* （[`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 和 [`except`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#except) 关键字之间的（多行）语句）。
- 如果没有异常发生，则跳过 *except 子句* 并完成 [`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句的执行。
- 如果在执行 try 子句时发生了异常，则跳过该子句中剩下的部分。 然后，如果异常的类型和 [`except`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#except) 关键字后面的异常匹配，则执行 except 子句，然后继续执行 [`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句之后的代码。
- 如果发生的异常和 except 子句中指定的异常不匹配，则将其传递到外部的 [`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句中；如果没有找到处理程序，则它是一个 *未处理异常*，执行将停止并显示如上所示的消息。

一个 [`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句可能有多个 except 子句，以指定不同异常的处理程序。 最多会执行一个处理程序。 处理程序只处理相应的 try 子句中发生的异常，而不处理同一 `try` 语句内其他处理程序中的异常。 一个 except 子句可以将多个异常命名为带括号的元组，例如:

```python
 except (RuntimeError, TypeError, NameError):
    pass
```

如果发生的异常和 [`except`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#except) 子句中的类是同一个类或者是它的基类，则异常和 except 子句中的类是兼容的（但反过来则不成立 --- 列出派生类的 except 子句与基类不兼容）

[`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) ... [`except`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#except) 语句有一个可选的 *else 子句*，在使用时必须放在所有的 except 子句后面。对于在 try 子句不引发异常时必须执行的代码来说很有用。 例如:

```python
def err_else(total):
    try:
        num = int(total)
        print("@" * num)
    except ValueError as e:
        print("程序处理发生异常，请检查您的参数！")
    else:
        print("@")
        
err_else("t")        
```

> 输出
>
> 程序处理发生异常，请检查您的参数！

大家可以看到，当程序发生异常时，else的语句并没有执行，只有成功调用时才会执行，看到这里，可能有读者觉得奇怪：既然只有当 try 块没有异常时才会执行 else 块，那么直接把 else 块的代码放在 try 块的代码的后面不就行了？

实际上大部分语言的异常处理都没有 else 块，它们确实是将 else 块的代码直接放在 try 块的代码的后面的，因为对于大部分场景而言，直接将 else 块的代码放在 try 块的代码的后面即可。

但 Python 的异常处理使用 else 块绝不是多余的语法，当 try 块没有异常，而 else 块有异常时，就能体现出 else 块的作用了。例如如下程序：

```python
def err_else_e(total):
    num = 100 / total
    print("-" * num)


def err_else_conversion(total):
    try:
        print(total)
    except ValueError as e:
        print("程序处理发生异常，请检查您的参数！")
    else:
        err_else_e(total)


err_else_conversion(0)

```
控制台输出：

```
0
Traceback (most recent call last):
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 65, in <module>
    err_else_conversion(0)
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 62, in err_else_conversion
    err_else_e(total)
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 52, in err_else_e
    num = 100 / total
ZeroDivisionError: division by zero
```

如果希望某段代码的异常能被后面的 except 块捕获，那么就应该将这段代码放在 try 块的代码之后；如果希望某段代码的异常能向外传播（不被 except 块捕获），那么就应该将这段代码放在 else 块中。

### 抛出异常

[`raise`](https://docs.python.org/zh-cn/3.8/reference/simple_stmts.html#raise) 语句允许程序员强制发生指定的异常。例如:

```python
def error_raise():
    try:
        10 / 0
    except ZeroDivisionError as e:
        raise

error_raise()
```

```
Traceback (most recent call last):
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 75, in <module>
    error_raise()
  File "C:/source_code/python_work/python-tutorial/errors/errors.py", line 70, in error_raise
    10 / 0
ZeroDivisionError: division by zero
```

[`raise`](https://docs.python.org/zh-cn/3.8/reference/simple_stmts.html#raise) 唯一的参数就是要抛出的异常。这个参数必须是一个异常实例或者是一个异常类（派生自 [`Exception`](https://docs.python.org/zh-cn/3.8/library/exceptions.html#Exception) 的类）。如果传递的是一个异常类，它将通过调用没有参数的构造函数来隐式实例化:

### 用户自定义异常

程序可以通过创建新的异常类来命名它们自己的异常（有关Python 类的更多信息，请参阅 [类](https://docs.python.org/zh-cn/3.8/tutorial/classes.html#tut-classes)）。异常通常应该直接或间接地从 [`Exception`](https://docs.python.org/zh-cn/3.8/library/exceptions.html#Exception) 类派生。

可以定义异常类，它可以执行任何其他类可以执行的任何操作，但通常保持简单，只提供一些属性，这些属性允许处理程序为异常提取有关错误的信息。 在创建可能引发多个不同错误的模块时，通常的做法是为该模块定义的异常创建基类，并为不同错误条件创建特定异常类的子类:

```python
class Error(Exception):
    """Base class for exceptions in this module."""
    pass

class InputError(Error):
    """Exception raised for errors in the input.

    Attributes:
        expression -- input expression in which the error occurred
        message -- explanation of the error
    """

    def __init__(self, expression, message):
        self.expression = expression
        self.message = message

class TransitionError(Error):
    """Raised when an operation attempts a state transition that's not
    allowed.

    Attributes:
        previous -- state at beginning of transition
        next -- attempted new state
        message -- explanation of why the specific transition is not allowed
    """

    def __init__(self, previous, next, message):
        self.previous = previous
        self.next = next
        self.message = message
```

大多数异常都定义为名称以“Error”结尾，类似于标准异常的命名。

许多标准模块定义了它们自己的异常，以报告它们定义的函数中可能出现的错误。

### 定义清理操作

[`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句有另一个可选子句，用于定义必须在所有情况下执行的清理操作。例如:

```python
def error_finally():
    try:
        10 / 0
    except ZeroDivisionError as e:
        raise
    finally:
        print("此代码无论是否发生异常都会被执行")
```

如果存在 [`finally`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#finally) 子句，则 `finally` 子句将作为 [`try`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#try) 语句结束前的最后一项任务被执行。 `finally` 子句不论 `try` 语句是否产生了异常都会被执行。 以下几点讨论了当异常发生时一些更复杂的情况：

- 如果在执行 `try` 子句期间发生了异常，该异常可由一个 [`except`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#except) 子句进行处理。 如果异常没有被某个 `except` 子句所处理，则该异常会在 `finally` 子句执行之后被重新引发。
- 异常也可能在 `except` 或 `else` 子句执行期间发生。 同样地，该异常会在 `finally` 子句执行之后被重新引发。
- 如果在执行 `try` 语句时遇到一个 [`break`](https://docs.python.org/zh-cn/3.8/reference/simple_stmts.html#break), [`continue`](https://docs.python.org/zh-cn/3.8/reference/simple_stmts.html#continue) 或 [`return`](https://docs.python.org/zh-cn/3.8/reference/simple_stmts.html#return) 语句，则 `finally` 子句将在执行 `break`, `continue` 或 `return` 语句之前被执行。
- 如果 `finally` 子句中包含一个 `return` 语句，则返回值将来自 `finally` 子句的某个 `return` 语句的返回值，而非来自 `try` 子句的 `return` 语句的返回值

###  预定义的清理操作

某些对象定义了在不再需要该对象时要执行的标准清理操作，无论使用该对象的操作是成功还是失败，清理操作都会被执行。 请查看下面的示例，它尝试打开一个文件并把其内容打印到屏幕上。:

```python
for line in open("myfile.txt"):
    print(line, end="")
```

这个代码的问题在于，它在这部分代码执行完后，会使文件在一段不确定的时间内处于打开状态。这在简单脚本中不是问题，但对于较大的应用程序来说可能是个问题。 [`with`](https://docs.python.org/zh-cn/3.8/reference/compound_stmts.html#with) 语句允许像文件这样的对象能够以一种确保它们得到及时和正确的清理的方式使用。:

```python
with open("myfile.txt") as f:
    for line in f:
        print(line, end="")
```

执行完语句后，即使在处理行时遇到问题，文件 *f* 也始终会被关闭。和文件一样，提供预定义清理操作的对象将在其文档中指出这一点。
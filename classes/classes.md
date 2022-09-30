## 类

类提供了一种组合数据和功能的方法。 创建一个新类意味着创建一个新的对象 *类型*，从而允许创建一个该类型的新 *实例* 。 每个类的实例可以拥有保存自己状态的属性。 一个类的实例也可以有改变自己状态的（定义在类中的）方法。

和其他编程语言相比，Python 用非常少的新语法和语义将类加入到语言中。它是 C++ 和 Modula-3 中类机制的结合。Python 的类提供了面向对象编程的所有标准特性：类继承机制允许多个基类，派生类可以覆盖它基类的任何方法，一个方法可以调用基类中相同名称的的方法。对象可以包含任意数量和类型的数据。和模块一样，类也拥有 Python 天然的动态特性：它们在运行时创建，可以在创建后修改。

​																																								--摘选自官方解释

### 类定义语法

最简单的类定义看起来像这样:

```
class ClassName:
    <statement-1>
    .
    .
    .
    <statement-N>
```

在实践中，类定义内的语句通常都是函数定义，但也允许有其他语句，有时还很有用 --- 我们会稍后再回来说明这个问题。 在类内部的函数定义通常具有一种特别形式的参数列表，这是方法调用的约定规范所指明的 --- 这个问题也将在稍后再说明。

当进入类定义时，将创建一个新的命名空间，并将其用作局部作用域 --- 因此，所有对局部变量的赋值都是在这个新命名空间之内。 特别的，函数定义会绑定到这里的新函数名称。

当（从结尾处）正常离开类定义时，将创建一个 *类对象*。 这基本上是一个包围在类定义所创建命名空间内容周围的包装器；我们将在下一节了解有关类对象的更多信息。 原始的（在进入类定义之前起作用的）局部作用域将重新生效，类对象将在这里被绑定到类定义头所给出的类名称 (在这个示例中为 `ClassName`)。

类对象支持两种操作：属性引用和实例化。

*属性引用* 使用 Python 中所有属性引用所使用的标准语法: `obj.name`。 有效的属性名称是类对象被创建时存在于类命名空间中的所有名称。 因此，如果类定义是这样的:

```
class MyClass:
    """A simple example class"""
    i = 12345

    def f(self):
        return 'hello world'
```

那么 `MyClass.i` 和 `MyClass.f` 就是有效的属性引用，将分别返回一个整数和一个函数对象。 类属性也可以被赋值，因此可以通过赋值来更改 `MyClass.i` 的值。 `__doc__` 也是一个有效的属性，将返回所属类的文档字符串: `"A simple example class"`。

类的 *实例化* 使用函数表示法。 可以把类对象视为是返回该类的一个新实例的不带参数的函数。 举例来说（假设使用上述的类）:

```
x = MyClass()
```

创建类的新 *实例* 并将此对象分配给局部变量 `x`。

实例化操作（“调用”类对象）会创建一个空对象。 许多类喜欢创建带有特定初始状态的自定义实例。 为此类定义可能包含一个名为 [`__init__()`](https://docs.python.org/zh-cn/3.8/reference/datamodel.html#object.__init__) 的特殊方法，就像这样:

```
def __init__(self):
    self.data = []
```

当一个类定义了 [`__init__()`](https://docs.python.org/zh-cn/3.8/reference/datamodel.html#object.__init__) 方法时，类的实例化操作会自动为新创建的类实例发起调用 [`__init__()`](https://docs.python.org/zh-cn/3.8/reference/datamodel.html#object.__init__)。 因此在这个示例中，可以通过以下语句获得一个经初始化的新实例:

```
x = MyClass()
```

当然，[`__init__()`](https://docs.python.org/zh-cn/3.8/reference/datamodel.html#object.__init__) 方法还可以有额外参数以实现更高灵活性。 在这种情况下，提供给类实例化运算符的参数将被传递给 [`__init__()`](https://docs.python.org/zh-cn/3.8/reference/datamodel.html#object.__init__)。 例如，:

```
class Complex:
    def __init__(self, realpart, imagpart):
        self.r = realpart
        self.i = imagpart


x = Complex(3.0, -4.5)
print(x.i)
print(x.r)
```

### 继承和多态

​																																			-- [摘选自编程爱好者的栖息地](https://turingplanet.org/2019/09/21/%E7%B1%BB-class/)

### 继承

在面向对象编程中，当我们已经创建了一个类，而又想再创建一个与之相似的类，比如添加几个方法，或者修改原来的方法，这时我们不必从头开始，可以从原来的类派生出一个新的类，我们把原来的类称为父类或基类，而派生出的类称为子类，子类继承了父类的所有数据和方法。

让我们看一个简单的例子，首先我们定义一个 Animal 类：

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def hi(self):
        print('Hello, I am %s.' % self.name)
```

现在，我们想创建一个 Dog 类，比如：

```python
class Dog():
    def __init__(self, name):
        self.name = name
    def hi(self):
        print('WangWang.., I am %s. ' % self.name)
```

可以看到，Dog 类和 Animal 类几乎是一样的，只是 `greet` 方法不一样，我们完全没必要创建一个新的类，可以直接创建子类（child class）来继承父类Animal：

```
class Dog(Animal):
    def hi(self):
        print('WangWang.., I am %s. ' % self.name)
```
Dog 类是从 Animal 类继承而来的，Dog 类自动获得了 Animal 类的所有数据和方法，而且还可以对从父类继承来的方法进行修改，调用的方式是一样的：
```python

animal = Animal('animal')
animal.hi()
dog = Dog('dog')
dog.hi()
```

我们也可以在Dog 类中添加新的方法：

```python
class Dog(Animal):
    def hi(self):
        print('Wang Wang.., I am %s. ' % self.name)

    def run(self):
        print('I am running!')


dog = Dog('dog')
dog.hi()
dog.run()
```

### 多态

多态的概念其实不难理解，它是指对不同类型的参数进行相同的操作，根据对象（或类）类型的不同而表现出不同的行为。继承可以拿到父类的所有数据和方法，子类可以重写父类的方法，也可以新增自己特有的方法。有了继承，才有了多态，这样才能实现为不同的数据类型的实体提供统一的接口。

```python
class Animal():
    def __init__(self, name):
        self.name = name
    def hi(self):
        print(f'Hello, I am {self.name}.')

class Dog(Animal):
    def hi(self):
        print(f'WangWang.., I am {self.name}.')

class Cat(Animal):
    def hi(self):
        print(f'MiaoMiao.., I am {self.name}')

def hello(animal):
    animal.hi()
```

可以看到，`cat` 和 `dog` 是两个不同的对象，对它们调用 `greet` 方法，它们会自动调用实际类型的 `greet` 方法，作出不同的响应：

```python
dog = Dog('dog')
hello(dog)
cat = Cat('cat')
hello(cat)
```

## Iterators

在某些情况下，我们希望实例对象可被用于`for...in`循环，这时我们需要在类中定义`__iter__`和`__next__`方法。其中，`__iter()__`方法返回迭代器对象本身`__next()__`方法返回容器的下一个元素，在没有后续元素时会抛出`StopIteration`异常。（Python 的 `for` 循环实质上是先通过内置函数 `iter()` 获得一个迭代器，然后再不断调用 `next()` 函数实现的。）

```python
class Fib():
    def __init__(self):
        self.a, self.b = 0, 1
    def __iter__(self):
        return self
    def __next__(self):
        self.a, self.b = self.b, self.a + self.b
        return self.a

fib = Fib()
for i in fib:
    if i > 10: 
         break
    print(i)# 1, 1, 2, 3, 5, 8
```

## 访问限制 underscore

在某些情况下，我们希望限制用户访问对象的属性或方法，也就是希望它是私有的，对外隐蔽。比如，对于上面的例子，我们希望 `name` 属性在外部不能被访问，我们可以在属性或方法的名称前面加上两个下划线，即 `__`，以下是对之前例子的改动：

```python
class Animal():
    def __init__(self, name):
        self.__name = name
    def hi(self):
        print(f'Hello, I am self.__name.')

animal = Animal('a1')
animal.__name # error
```

需要注意的是，在 Python 中，以双下划线开头，并且以双下划线结尾（即 `__xxx__`）的变量是特殊变量，特殊变量是可以直接访问的。所以，不要用 `__name__` 这样的变量名。另外，如果变量名前面只有一个下划线_，表示此变量不要随便访问，虽然它可以直接被访问。
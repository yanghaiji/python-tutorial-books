## 条件语句

Python条件语句是通过一条或多条语句的执行结果（True或者False）来决定执行的代码块。

就和常常说的话术很相似，如果‘你有💴’，你就会做这些事情，否则‘你只能选择其他的事情做’

Python程序语言指定任何非0和非空（null）值为true，0 或者 null为false。

Python 编程中 if 语句用于控制程序的执行，基本形式为：

```
if 判断条件：
    执行语句……
else：
    执行语句……
```

这句话，放在程序里翻译如下：

```python
m = 10000000

if m > 1000000:
    print("你的资金量非常充足，你可以做些你喜欢的事情！")
else:
    print("你的资金量不是很充足，加油吧打工人！！！")
```

if 语句的判断条件可以用>（大于）、<(小于)、==（等于）、>=（大于等于）、<=（小于等于）来表示其关系。

当判断条件为多个值时，可以使用以下形式：

```
if 判断条件1:
    执行语句1……
elif 判断条件2:
    执行语句2……
elif 判断条件3:
    执行语句3……
else:
    执行语句4……
```



```python
age = 28
if 7 < age < 18:
    print("你还未成年")
elif 18 < age < 35:
    print("你已经成年")
elif 35 < age < 55:
    print("你已步入中年")
elif age > 55:
    print("你已步入老年")
else:
    print("你的年龄还太小")
```
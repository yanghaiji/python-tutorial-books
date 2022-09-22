## for 循环语句

Python for循环可以遍历任何序列的项目，如一个列表或者一个字符串。

**语法：**

for循环的语法格式如下：

```
for iterating_var in sequence:
   statements(s)
```

### for 循环

```python
seq = [1, 2, 3, 4, 5, 6, 7, 8, 9]

for val in seq:
    print("当前元素为 %s" % val)
```

这里还可以换一种写法

```python
for val in range(len(seq)):
    print("当前元素为 %s,其下标为 %s" % (seq[val], val))
```

第一种方式，是直接获取列表内的元素，第二种是通过获取下标，然后通过下标去访问列表的元素

### 循环使用 else 语句

在 python 中，for … else 表示这样的意思，for 中的语句和普通的没有区别，else 中的语句会在循环正常执行完（即 for 不是通过 break 跳出而中断的）的情况下执行，while … else 也是一样。

```python
for num in range(10, 20):  # 迭代 10 到 20 之间的数字
    for i in range(2, num):  # 根据因子迭代
        if num % i == 0:  # 确定第一个因子
            j = num / i  # 计算第二个因子
            print('%d 等于 %d * %d' % (num, i, j))
            break  # 跳出当前循环
    else:  # 循环的 else 部分
        print('%d 是一个质数' % num)
```

> 10 等于 2 * 5
> 11 是一个质数
> 12 等于 2 * 6
> 13 是一个质数
> 14 等于 2 * 7
> 15 等于 3 * 5
> 16 等于 2 * 8
> 17 是一个质数
> 18 等于 2 * 9
> 19 是一个质数

### for 遍历字典

```python
# 同时获取 key value
for k, v in dicts.items():
    print("dicts key = %s , value = %s" % (k, v))
# 获取所有的 key
for key in dicts.keys():
    print("dicts key = %s" % key)
# 获取所有的 value
for value in dicts.values():
    print("dicts value = %s" % value)
```

> dicts key = id , value = 1
> dicts key = name , value = haiji
> dicts key = pwd , value = 123456789
> 
> ------------------------------
> 
> dicts key = id
> dicts key = name
> dicts key = pwd
> 
> ------------------------------
> dicts value = 1
> dicts value = haiji
> dicts value = 123456789
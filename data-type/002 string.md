## 字符串 类型

除了数字，Python 也可以操作字符串。字符串有多种形式，可以使用单引号（`'...'`），双引号（`"..."`）都可以获得同样的结果

### 创建一个String

```python
    str1: str = "hello world"
    str2: str = 'hello python'
    print("str1 的值为：%s" % str1)
    print("str2 的值为：%s" % str2)
```

字符串是我们写程序经常会用到的，python也为我们提供了很多方法，接下来我们将学些一下`str`常用的方法

### 字符串过长时的处理

```python
# 当字符串过长时
str3 = "4s5dyunlkmvo;udznv b,hbvaliygwj khjdeuibnjkm" \
       "suyvaj czlkj z,vkj ,j jdshbfabewkafyaiesatwet"
# 字符串过长时，且想保留字符串原有的格式时
str4 ="""这是一个很长的字符串，
我想在实用时，保留其原有的格式！
"""
print("str3 的值为：%s" % str3)
print("str4 的值为：%s" % str4)
```

输出结果

````python
str3 的值为：4s5dyunlkmvo;udznv b,hbvaliygwj khjdeuibnjkmsuyvaj czlkj z,vkj ,j jdshbfabewkafyaiesatwet
str4 的值为：这是一个很长的字符串，
    我想在实用时，保留其原有的格式！
````

### 转义符的处理

如果你不希望前置了 `\` 的字符转义成特殊字符，可以使用 *原始字符串* 方式，在引号前添加 `r` 即可:

```python
# 当字符串内有 \ / 且我们需要保留原有的格式时,
# 如果不添加 \ 则会将\n解析成回车
str6 = "C:\python\\name"
print("str6 的值为：%s" % str6)
str7 = r"C:\python\name"
print("str7 的值为：%s" % str7)
```

### 字符串连接

```python
 x = '%s, %s!' % (imperative, expletive)
 x = '{}, {}!'.format(imperative, expletive)
 x = 'name: %s; score: %d' % (name, n)
 x = 'name: {}; score: {}'.format(name, n)
```

### 数据切片

```python
# 数据切片 这里的数据下标是从0开始
str10 = "0123456789"
print("str10[3] 的值为：%s" % str10[3])
# [3:8] 的表达式遵循左闭又开的原则
print("str10[3] 的值为：%s" % str10[3:8])
```

Python 中的字符串不能被修改，它们是 [immutable](https://docs.python.org/zh-cn/3.8/glossary.html#term-immutable) 的。因此，向字符串的某个索引位置赋值会产生一个错误:

```python
str10[0] = 's'
print(str10)
```

```
Traceback (most recent call last):
  File "C:/source_code/python_work/python-tutorial/data_type/string.py", line 48, in <module>
    str10[0] = 's'
TypeError: 'str' object does not support item assignment
```

### replace

```python
str_rep = "Python"
str_rep = str_rep.replace('o', 'O')
print("str_rep 的值为：%s" % str_rep)
```

### index

index 可以传入三个参数，第一个参数为需要检测的str，第二参数为检测的起始，第三个参数为终止位置，其中后两个参数为非必传字段

```python
index_num = lower.index("o")
index_num2 = lower.index("o", 2)
index_num3 = lower.index("o", 2,len(lower))
print("index_num 的值为：%s" % index_num)
print("index_num2 的值为：%s" % index_num2)
print("index_num3 的值为：%s" % index_num3)
```












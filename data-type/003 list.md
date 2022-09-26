## 列表（List）

序列是Python中最基本的数据结构。序列中的每个元素都分配一个数字 - 它的位置，或索引，第一个索引是0，第二个索引是1，依此类推。

Python有6个序列的内置类型，但最常见的是列表和元组。

序列都可以进行的操作包括索引，切片，加，乘，检查成员。

此外，Python已经内置确定序列的长度以及确定最大和最小的元素的方法。

列表是最常用的Python数据类型，它可以作为一个方括号内的逗号分隔值出现。

列表的数据项不需要具有相同的类型

创建一个列表，只要把逗号分隔的不同的数据项使用方括号括起来即可。如下所示：

```python
# 创建一个列表
list1 = [1, 2, 3, 4, 5, 6, 7]
```

值得注意的是`python` 列表内是不限制类型的，这是python的一个特点

```python
list1 = [0, 1, 2, 3, 4, 5, 6, 7, "python", True, 20.1]
```

### 根据索引查询

```python
# 根据下标查询
print("list1[1]= %s" % list1[1])
```

### 循环遍历

```python
a = 0
for i in list1:
    print("list1[ %s ]= %s" % (a, i))
    a += 1
```

### 切片

这str一样，左闭右开的原则

```python
print("list1[1:3]= %s" % list1[1:3])
```

###  数据添加

```python
list1.append("append")
```

这里我们也可以使用insert 方法，根据下标进行新增

```python
list1.insert(len(list1), "insert")
```

### 数据修改

根据下标修改指定位置的元素

```python
list1[11] = "app"
```

### 删除数据

```python
list1.remove(20.1)
```

### 反转

```python
list1.reverse()
```

### 排序

```python
list2 = [0, 29, 2, 3, 4, 2, 6, 7]
list2.sort()
for i in list2:
    print("list2[ %s ]= %s" % (a, i))
    a += 1
```
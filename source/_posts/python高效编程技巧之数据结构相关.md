---
title: python高效编程技巧之数据结构相关
date: 2018-01-22 14:50:03
tags: python高效编程技巧
categories: python
---
> 本博客在于记录python高效编程之数据结构相关的编程技巧。

#### 如何在列表、字典、集合中根据条件删选数据
##### 列表筛选元素
> 筛选数据，最最常用的就是for循环及迭代，而相关的高级用法就是filter函数，列表解析、字典解析以及集合解析。

举个栗子：生成一个随机的列表，过滤掉其中小于0的数字
```python
from random import randint

a = [randint(-10, 10) for i in range(10)]
print(a)

f = []
for i in a:
    if i > 0:
        f.append(i)
print(f)

b = filter(lambda x: x > 0, a)  # filter 函数过过滤
print(list(b))

c = [i for i in a if i > 0]  # 列表解析过滤
print(c)
```
> 其中使用到了迭代、filter函数和列表解析，推荐使用filter函数和列表解析。
filter函数接收的第一个参数为函数，这里使用了lambda匿名函数（推荐使用匿名函数），第二个参数传入可迭代对象。

---
现在我们来对迭代，filter和列表解析来进行一下运行时间的比较：
```python
from random import randint
import time

def count_running_time(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        rep = func(*args, **kwargs)
        time.sleep(2)# 时间执行太短，加入干扰
        print('{0}函数运行了{1}秒'.format(func.__name__, time.time()-start))
        return rep
    return wrapper


def random_arr(m1, m2):
    return [randint(m1, m2) for _ in range(10)] # 生成随机列表

@count_running_time
def my_filter(m1):
    return list(filter(lambda x: x > 0, m1)) # filter 函数

@count_running_time
def my_listanalysis(m1):
    return [i for i in m1 if i > 0] # 列表解析

if __name__ == '__main__':

    a = random_arr(-10, 10)
    print(a)

    b = my_filter(a)
    print(b)

    c = my_listanalysis(a)
    print(c)
```
打印的结果如下：
```python
[5, 10, 4, 8, 1, -2, 8, -10, -6, -1]
my_filter函数运行了2.0003464221954346秒
[5, 10, 4, 8, 1, 8]
my_listanalysis函数运行了2.0003223419189453秒
[5, 10, 4, 8, 1, 8]
```
我们可以看到列表解析的速度相对快一些，推荐使用列表解析的方式。

---
##### 字典筛选元素
```python
from random import randint

a = {x: randint(60, 100) for x in range(1, 8)} # 随机生成60到100分7个人
print(a)

b = {key: value for key, value in a.items() if value > 90}
print(b)
```

---
##### 筛选集合元素
```python
data = [randint(-10, 10) for _ in range(10)]
s = set(data)
filter_s = {x for x in s if x % 3 == 0} # 筛选集合中能被3整除的数
print(filter_s)
```
----
#### 如何为元组中的每个元素进行命名，提高程序的可读性
> 这样一来我们使用index来访问的时候就可以避免大量的索引值

##### 手动添加索引值
举个栗子：
```python
NAME = 0
AGE = 1
SEX = 2
EMAIL = 3
# 或者使用下面的
NAME, AGE, SEX, EMAIL = range(4)

student = ('zhangcong', 18, 'male', '123@qq.com')
print(student[NAME])
print(student[AGE])
print(student[SEX])
print(student[EMAIL])
```
student元组对象在进行初始化的时候一定要依次按照顺序

---
##### 使用标准库中的namedtuple
```python
student = namedtuple('Student', ['name', 'age', 'sex', 'email'])
s = student('zhangcong', 18, 'male', '123@qq.com')
s2 = student(name='123', age=20, sex='', email='')
print(s)
print(s.name)
print(s2)
print(s2.name)

print(isinstance(s, tuple))  # s是一个元组
```
使用namedtuple()，第一个参数传入元组的对象名，第二个参数传入索引值列表，student初始化时有两种方式，一种是按照顺序初始化，一种是按照索引值进行初始化。

---
#### 统计序列中元素出现的频率，字符串中字符出现的频率
##### 常规迭代方式
例如：
```python
from random import randint

data = [randint(1, 30)for x in range(30)] # 随机生成30个1到30的数字的列表
print(data)
'''
根据列表，生成以列表值为key，value为0的字典，对data进行迭代，没遇见一次元素，a字典中的value+1，
最终统计数字出现的次数 key:数字，value:出现的次数
'''
a = dict.fromkeys(data, 0)
for x in data:
    a[x] += 1
print(a)
```
---
##### 使用标准库中的Counter对象统计前三项
```python
from random import randint
from collections import Counter

data = [randint(1, 30)for x in range(30)]
print(data)

a = dict.fromkeys(data, 0)
for x in data:# 迭代data 每遇见一次元素 a字典中的value+1
    a[x] += 1
print(a)

a1 = Counter(a)
most_number = a1.most_common(3) # dict_a前三项
print(most_number)

a2 = Counter(data)
most_number = a2.most_common(3) # list_data前三项
print(most_number_data)
```
可以看出使用most_common方式可以快速的得出前三项的值（参数为3），创建Counter对象时传入的参数可以是list，也可以是dict，亦可以时set集合。

---
##### 统计字符出现的次数
```python
from collections import Counter
import re

text = open(r'C:\Users\renrenlabzc\Desktop\***.txt').read()
keyword_list = re.split('\W+', text) # 根据正则，将字符串以不是标点符号的字符分割为list
m = Counter(keyword_list)
n = m.most_common(3)
print(n) # 打印出现次数前三的单词
```

---
#### 对列表及字典排序
> 使用内置的sorted内置函数进行排序，会结合使用到zip函数。

##### 列表排序
```python
from random import randint

data = [randint(60, 100) for i in range(10)]
print(data)

a = sorted(data, reverse=True) # reverse=True 降序 = False升序
print(a)
```
---
##### 字典进行排序
```python
from random import randint

dict_a = {key: randint(60, 100) for key in 'abcdefg'} # 随机生成adc....学生的成绩dict
print(dict_a)

a = sorted(dict_a，reverse=True)
print(a)   #直接传入字典的话，用到了字典key的可迭代性，此时的value是不存在的，这样是无法进行排序的

'''
此时我们寻找办法，利用zip函数将字典的key，value进行对调，然后在对key进行排序，
zip函数将字典的key，value分别封装成了一个元组，所有的元组组合成为一个列表，将此列表传入sorted函数
'''
a = zip(dict_a.values(), dict_a.keys())
print(a)
m = sorted(a, reverse=True)# 降序排列
print(m)

'''
如果不使用zip函数将字典进行重新构造，我们可以使用匿名函数的方式，
sorted(dict_a.items(), key=lambda x: x[1], reverse=True)，sorted函数的默认参数为：第一个参数传入要进行排序的可迭代对象，key=参数
可以传入匿名函数进行处理，reverse参数决定升序还是降序排列。
'''
a = sorted(dict_a.items(), key=lambda x: x[1], reverse=True) # key=需要传入一个函数，x[1]表示比较value，x[0]表示比较key，只有对比x[1]也就是value才可以进行排序
print(a)
```















---
title: 最新python面试题
date: 2018-01-05 13:15:25
tags: 面试题
categories: 面试题
---


## Python面试题
> 关键在于了解Python的语言特性，结合具体的例子来理解Python的语言特性

#### python的函数参数传递
看两个例子：

```
a = 1
def fun(a):
		a = 2
fun(a)
print a  # 1
```
```
a = []
def fun(a):
    	a.append(1)
fun(a)
print a  # [1]
```

所有的变量都可以理解是内存中一个对象的“引用”，或者，也可以看成是类似于c中void*的感觉。
上面的例子我们通过``id``来查看引用``a``的地址，来进行比较理解:
```
a = 1
def fun(a):
		print "func_in",id(a)   # func_in 41322472
    	a = 2
    	print "re-point",id(a), id(2)   # re-point 41322448 41322448
print "func_out",id(a), id(1)  # func_out 41322472 41322472
fun(a)
print a  # 1
```

注：具体的id的值在不同的电脑上运行时可能有所不同，并且在Python中对较小的数字都进行了缓存。
可以看到，在执行完``a = 2``之后，a引用中保存的值，及内存地址发生了变化，由原来的``1``对象的地址变成了``2``这个实体对象的内存地址。
而在第2个例子``a``引用保存的内存地址就不会发生变化：
```
a = []
def fun(a):
		print "func_in",id(a)  # func_in 53629256
		 a.append(1)
print "func_out",id(a)     # func_out 53629256
fun(a)
print a  # [1]
```

在这里需要记住的是类型是属于对象的，而不是变量的。而对象有两种，“可更改”（mutable）和“不可更改的对象”（imnutable）。在Python中，string，tuples和numbers是不可更改的对象，而list，dict，set等则是可以修改的对象。（这就是这个问题的重点）

当一个引用传递给函数的时候，函数自动的复制一份引用，这个函数里的引用和外边的引用就没有半毛钱的关系了。所以第一个例子里函数把引用指向了一个不可变的对象，当函数返回的时候，外面的引用没半毛钱的感觉，而第二个例子不一样，函数内部引用指向的是可变对象，对它的操作就和定位了指针地址一样，在内存里直接的进行修改。

---
如果还不明白的话，这里有更好的解释： http://stackoverflow.com/questions/986006/how-do-i-pass-a-variable-by-reference
#### Python中的元类
这个就非常的不常用了，但是像在ORM这种复杂的结构中还是会需要的，详情请看：:http://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python
#### @ staticmethod和@ classmethod
Python中其实有3个方法，及静态方法（staticmethod），类方法（classmethod）和实例方法，如下：
```
def foo(x):
		print"executing foo(%s)" % (x)
class A(object):
    	def foo(self, x):
        print("executing foo(%s,%s)" % (self, x))
   	 @classmethod
    	def class_foo(cls, x):
      		  print("executing class_foo(%s,%s)" % (cls, x))
    	@staticmethod
    	def static_foo(x):
       		 print("executing static_foo(%s)" % x)
a = A()
```

这里先理解下函数参数里的self和cls。这个self和cls是对类或者实例的绑定，对于一般的函数来说我们可以这么的调用``foo(x)``，这个就是最常用的函数，它的工作跟任何的东西（类，实例）无关。对于实例方法，我们知道在类里每次定义方法的时候都需要绑定这个实例，就是``foo(self,x)``，为什么要这么做呢？因为实例方法的调用离不开实例，我们需要把实例自己传递给函数，调用的时候是这样的``a.foo(x)``（其实是``foo(a,x)``）。类方法一样，只不过它传递的是类而不是实例，``A.class_foo(x)``。注意这里的self和cls可以替换成别的参数，但是Python中的约定是这俩，还是不要改的好。
对于静态方法其实和普通的方法一样，不需要对谁进行绑定，唯一的区别就是调用的时候需要使用``a.static_foo(x)``或者``A.static_foo(x)``来进行调用。
下面是一张表格，来比对各种方法的可以的调用形式

|\|实例方法|类方法|静态方法|
| :------------: | :------------: | :------------: | :------------: |
| a = A()         | a.foo(x)        | a.class_foo(x)  | a.static_foo(x)  |
| A | 不可用    | A.class_foo(x)  | A.static_foo(x)  |

更度多的关于这个问题：
1.http://stackoverflow.com/questions/136097/what-is-the-difference-between-staticmethod-and-classmethod-in-python
2.https://realpython.com/blog/python/instance-class-and-static-methods-demystified/
#### 类变量和实例变量
##### 类变量：
 >是可在类的所有实例之间共享的值（也就是说，它们不是单独分配给每个实例的）。例如下例中，num_of_instance就是类变量，用于跟踪存在着多少个Test的实例。

##### 实例变量：
 >实例化之后，每个实例单独拥有的变量
 
 ```
 class Test(object):
 		um_of_instance = 0
    	def __init__(self, name):
        		self.name = name
        		Test.num_of_instance += 1
if __name__ == '__main__':
    print est.num_of_instance  # 0
    t1 = Test('jack')
    print est.num_of_instance  # 1
    t2 = Test('lucy')
    print t1.name, t1.num_of_instance  # jack 2
    print t2.name.num_of_instance  # lucy 2
 ```
   >补充的例子
   
 ```
 class Person:
    	name="aaa"
p1=Person()
p2=Person()
p1.name="bbb"
print p1.name  # bbb
print p2.name  # aaa
print Person.name  # aaa
```
这里的``p1.name='bbb'``是实例调用了类变量，这其实和上面的第一个问题一样，就是函数传参的问题，``p1.name``一开始是指向的类变量``name='aaa'``，但是在实例的作用域里把类变量的引用改变了，就变成了一个实例变量，self.name不再引用Person的类变量name了。
可以看看下面的例子
```
class Person:
   	 name=[]
p1=Person()
p2=Person()
p1.name.append(1)
print p1.name  # [1]
print p2.name  # [1]
print Person.name  # [1]
```
参考：http://stackoverflow.com/questions/6470428/catch-multiple-exceptions-in-one-line-except-block
#### Python的自省
这个也是Python彪悍的特性。
自省就是面向对象的语言所写的程序在运行时，所能知道对象的类型。简单一句就是运行时能够捕获到对象的类型。比如type()，dir()（返回当前模块的属性列表)，getattr(返回对象的属性)，setattr()（动态添加属性）这两者参考set，get方法，hasattr()（判断是否包含哪个属性），isinstance()（判断对象是否属于哪个类型）
```
a = [1,2,3]
b = {'a':1,'b':2,'c':3}
c = True
print type(a),type(b),type(c) # <type 'list'> <type 'dict'> <type 'bool'>
print isinstance(a,list)  # True
```
#### 字典推导式
可能你见过列表推导式，却没有见过字典推导式，在2.7中才加入的：
```
d = {key: value for (key, value) in iterable}
```
#### Python中单下划线和双下划线
```
>>> class MyClass():
...     def __init__(self):
...             self.__superprivate = "Hello"
...             self._semiprivate = ", world!"
...
>>> mc = MyClass()
>>> print mc.__superprivate
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: myClass instance has no attribute '__superprivate'
>>> print mc._semiprivate
, world!
>>> print mc.__dict__
{'_MyClass__superprivate': 'Hello', '_semiprivate': ', world!'}
```
``__foo__``：一种约定，Python内部的名字，用来区别其他用户自定义的命名，以防冲突么就是例如：``__init__(),__del__(),__calll__()``这些的特殊的方法。
``_foo``：一种约定，用来指定变量私有，程序员用来指定私有变量的一种方式，不能用``from module impore*``导入，其他的方面和公有一样访问。
``__foo``：这个有真正的意义：解析器用``_classname__foo``来代替这个名字，以区别和其他类相同的命名，它无法直接像公有成员一样随便访问，通过``对象名._类名__xxx``这样的方式访问。
详情见：http://stackoverflow.com/questions/1301346/the-meaning-of-a-single-and-a-double-underscore-before-an-object-name-in-python
或者：http://www.zhihu.com/question/19754941

---
title: 最新python面试题之python特性
date: 2018-01-05 13:15:25
tags: python面试题
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

如果还不明白的话，这里有更好的解释： http://stackoverflow.com/questions/986006/how-do-i-pass-a-variable-by-reference

---
#### Python中的元类
这个就非常的不常用了，但是像在ORM这种复杂的结构中还是会需要的，详情请看：:http://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python

---
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

---
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

---
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

---
#### 字典推导式
可能你见过列表推导式，却没有见过字典推导式，在2.7中才加入的：
```
d = {key: value for (key, value) in iterable}
```
---
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

---
#### 字符串格式化：%和.format
.format在许多发面看起来会更加的便利，对于%最烦人的是它无法同时传递一个变量和元组。你可能会想下面的代码不会有什么问题：
```
"hi there %s" % name
```
但是，如果name恰好是(1,2,3)，那么它将会跑出一个TypeError异常，为了保证它是正常的，你必须这样做：
```
"hi there %s" % (name,)   # 提供一个单元素的数组而不是一个参数
```
但是有点丑..format就没有这些问题.你给的第二个问题也是这样,.format好看多了.
你为什么不用它？
- 不知道它（在读这个之前）
- 为了和Python2.5兼容（譬如logging库建议使用``%``）


http://stackoverflow.com/questions/5082452/python-string-formatting-vs-format

---
#### 迭代器和生成器
这个是stackoverflow里python排名第一的问题,值得一看: http://stackoverflow.com/questions/231767/what-does-the-yield-keyword-do-in-python
这是中文版: http://taizilongxu.gitbooks.io/stackoverflow-about-python/content/1/README.html
这里有个关于生成器的创建问题面试官有考： 问： 将列表生成式中[]改成() 之后数据结构是否改变？ 答案：是，从列表变为生成器
```
>>> L = [x*x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x*x for x in range(10))
>>> g
<generator object <genexpr> at 0x0000028F8B774200>
```
通过列表生成式，可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含百万元素的列表，不仅是占用很大的内存空间，如：我们只需要访问前面的几个元素，后面大部分元素所占的空间都是浪费的。因此，没有必要创建完整的列表（节省大量内存空间）。在Python中，我们可以采用生成器：边循环，边计算的机制—>generator

---
#### \*args and \**kwargs
用\*args和\**kwargs只是为了方便并没有强制使用它们。
当你不确定你的函数里将要传递多少参数时你可以用\**args.例如,它可以传递任意数量的参数:
```
>>> def print_everything(*args):
        for count, thing in enumerate(args):
...         print '{0}. {1}'.format(count, thing)
...
>>> print_everything('apple', 'banana', 'cabbage')
0. apple
1. banana
2. cabbage
```
相似的,**kwargs允许你使用没有事先定义的参数名:
```
>>> def table_things(**kwargs):
...     for name, value in kwargs.items():
...         print '{0} = {1}'.format(name, value)
...
>>> table_things(apple = 'fruit', cabbage = 'vegetable')
cabbage = vegetable
apple = fruit
```
你也可以混着用.命名参数首先获得参数值然后所有的其他参数都传递给\*args和\**kwargs.命名参数在列表的最前端.例如:
```
def table_things(titlestring, **kwargs)
```
```
*args和**kwargs可以同时在函数的定义中,但是*args必须在**kwargs前面.
```
当调用函数时你也可以用\*和\**语法.例如:
```
>>> def print_three_things(a, b, c):
...     print 'a = {0}, b = {1}, c = {2}'.format(a,b,c)
...
>>> mylist = ['aardvark', 'baboon', 'cat']
>>> print_three_things(*mylist)

a = aardvark, b = baboon, c = cat
```
就像你看到的一样,它可以传递列表(或者元组)的每一项并把它们解包.注意必须与它们在函数里的参数相吻合.当然,你也可以在函数定义或者函数调用时用\*.
http://stackoverflow.com/questions/3394835/args-and-kwargs

---
#### 面向切面编程AOP和装饰器
这个AOP一听起来有点懵，下面做下解释；Java的spring框架中有OOP编程，而AOP可以看成是OOP的补充。
>面向切面编程（AOP是Aspect Oriented Program的缩写），我们知道，面向对象的特点是继承
多态和封装。而封装就是将不同的功能分散到不同的对象中去，这种设计往往在软件设计中叫做职责分配。实际上就是说让不同的类设计不同的方法。这样代码就分散到一个个的类中去了。这样做的好处就是降低了代码的复杂程度，使类可以重用。
但是人们发现，在分散代码的同时，也增加了代码的重复性。什么意思呢？就是比如说，我们两个类中，可能都需要在每个方法上做日志。按面向对象的设计方法，我们就必须在两个类的方法中都加入写日志的内容。即使写日志的代码都相同；但就是因为面向对象的设计方法让类与类之间是无法联系的，而不能将这些代码统一起来。
也许会有人说，那好办呐，我们可以将写日志的这段代码写在一个独立类的方法里，然后在这两个类中调用。但是这样一来，这两个类跟我们上面提到的独立的类就有耦合了，他的改变会影响到这两个类。（这里就是耦合的弊端）有什么解决办法呢？能让我们在需要的时候随意的插入功能代码？
**这种在运行时，动态的将特定的功能代码将入到（切入到）类的指定的方法、指定位置上的编程思想就是面向切面的编程**
一般而言，我们管切入到指定类的指定方法的代码片段称之为切面，而切入到哪些类，哪些方法则叫做切入点。有了AOP，我们就可以将几个类共有的功能代码，抽取到一个切面中，等到需要的时候再切入到对象中去，从而改变其原有的行为。
这样看来，AOP其实是OOP的补充而已。OOP从横向的区分出一个个类来，而AOP则从纵向上像对象中加入特定的代码。有了AOP，OOP就变的立体了。如果加上时间的纬度，AOP使OOP有原来的二位变为了三维，有平面变成了立体。从技术上说AOP基本上是通过代码机制实现的。
AOP在编程历史上可以说是里程碑式的，对OOP是一种补充。

而在Python中面向切面编程便是Python中的语法糖装饰器。装饰器是一个很著名的设计模式，经常被用于有切面需求的场景，较为经典的有日志的插入，性能的测试、事物的处理等。装饰器是解决这类问题的绝佳的设计，有了装饰器，我们就可以抽离出大量函数中与函数功能本身无关的雷同的代码并可以继续的重用。概括的讲装饰器的作用就是为已经存在的对象添加额外的功能。
这个问题比较大，推荐：http://stackoverflow.com/questions/739654/how-can-i-make-a-chain-of-function-decorators-in-python
中文：http://taizilongxu.gitbooks.io/stackoverflow-about-python/content/3/README.html

---
#### 鸭子类型
“当你看到一张鸟走起来像鸭子，游起泳来像鸭子、叫起来也像鸭子，那么可以称这只鸟为鸭子”。
>我们并不关心对象是什么类型（到底是不是鸭子），我们只关心它的行为。
鸭子类型的使用，经常出现在动态语言中，非常的灵活。
比如在Python中，有很多file-like的东西，比如StringIO，GzipFile，socket。他们有很多相同的方法，我们把它们当做文件使用。
又比如：list.extend()，我们不关心它的参数是不是list，只要它是可迭代的，所以它的参数可以是list/tuple/dict/字符串/生成器等。
这样使得Python不像java那样专门去弄一大推的设计模式。

---
#### Python中的重载
>Python中其实不需要做函数的重载

函数的重载主要是为了解决两个问题。
- 可变参数类型
- 可变参数个数

另外，一个基本的设计原则是，仅仅当两个函数除了参数类型个参数个数不同以外，其功能是完全的相同的，此时才需要用到函数的重载，如果两个函数的功能本来就是不同的，那么就不应当使用重载，而应当使用一个名字不同的函数。
好吧，那么对于情况1，函数功能相同，但是参数的类型不同，Python如何的处理？答案是根本不需要处理，因为Python可以接收任何类型的参数，如果函数的功能相同，那么不同的参数类型在Python中很可能是相同的代码，则没有必要做成两个函数。
那么对于情况2，函数功能相同，但是函数的参数个数不同，Python如何处理？大家都知道，那就是缺省参数。对于那些缺少的参数将其设定为缺省参数即可解决问题。因为你假设函数的功能相同，那么那些缺少的参数最终还是需要用到的。
好了，鉴于情况1更情况2都有了解决方案，Python自然就不需要进行函数的重载了。

---
#### 新式类和旧式类
>提前说明一下，新式类的查找顺序是广度优先，而旧式类的查找顺序是深度优先

这里有一篇文藏很好的介绍了新式类的特性：http://www.cnblogs.com/btchenguang/archive/2012/09/17/2689146.html
新式类很在在2.2中就已经出现了，所以旧式类则完全沦为了兼容的问题，Python3里的类全部都是新式类。这里有一个MRO问题可以了解一下（新式类是广度优先，旧式类是深度优先）
>一个旧式类的深度优先的例子

```
class A():
    def foo1(self):
        print "A"
class B(A):
    def foo2(self):
        pass
class C(A):
    def foo1(self):
        print "C"
class D(B, C):
    pass

d = D()
d.foo1()

# A
```
按照经典的查找顺序，从做到右深度优先的原则，在访问``d.foo1()``的时候，D这个类是没有具体的实现的，那么往上查找，先找到B，B里面也没有，深度优先访问到A，找到了``foo1()``所以这时候调用的是A的foo1()，从而导致C重写的foo1()被绕过。

---
#### __new__和__init__的区别
>``__new__返回个一个cls的静态实例``，只有当返回cls的静态实例后``__init__``才会被调用
实际上就是一个调用先后的问题，一般不用重写``__new__``。

- ``__new__``是一个静态方法，而``__init__``是一个实例方法。
- ``__new__``方法返回一个创建的实例，而``__init__``什么都不返回。
- 只有在``__new__``返回一个cls的实例时后面的``__init__``才被调用。
- 当创建一个新的实例时调用``__new__``，初始化一个实例时用``__init__``。

[stackoverflow](https://stackoverflow.com/questions/674304/why-is-init-always-called-after-new)
ps：``__metaclass__``是创建类是起作用，所以我们分别使用``__metaclass__``，``__new__``和``__init__``来分别在在类创建，实例创建和实例初始化的时候做一些小的手脚。

---
#### 单例模式
>单例模式是一种软件设计模式。在它的核心结构中只包含一个被称为单例类的特殊类。通过单例的模式保证系统中一个类之后一个实例而且该实例易于被外界访问。从而方便对实例的个数控制并节省系统得资源。如果希望在系统中的某个类的对象只能存在一个，单例模式是最好的解决方案。
``__new__``在``__init__``之前被调用，用于生成实例对象。利用这个方法和类的属性的特点可是实现设计模式的单例模式。单例模式是指创建唯一的对象，单例模式的类只能实例。

一下介绍几种方法：
- 使用``__new__``方法结合共享属性

```
class Singleton(object):
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            orig = super(Singleton, cls)
            cls._instance = orig.__new__(cls, *args, **kw)
        return cls._instance

class MyClass(Singleton):
    a = 1
```
- 共享属性

创建实例时把所有的``__dict__``指向同一个字典，这样他们就具有了相同的属性和方法，具有了相同的属性和方法Python中就不需要进行重载，它们本省就是同一个实例。
```
class Borg(object):
    _state = {}
    def __new__(cls, *args, **kw):
        ob = super(Borg, cls).__new__(cls, *args, **kw)
        ob.__dict__ = cls._state
        return ob

class MyClass2(Borg):
    a = 1
```
- 装饰器版本的共享属性

```
def singleton(cls, *args, **kw):
    instances = {}
    def getinstance():
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return getinstance

@singleton
class MyClass:
  ...
```
- import方法

作为Python的模块是天然的单例模式，当然要选择合适的应用场景。
```
# mysingleton.py
class My_Singleton(object):
    def foo(self):
        pass

my_singleton = My_Singleton()

# to use
from mysingleton import my_singleton

my_singleton.foo()
```

---
#### Python中的作用域
>Python中，一个变量的作用域总是在代码中被赋值的地方所决定的。

当Python遇到一个变量的话他会按照这样的顺序进行搜索：
本地作用域（Local）->当前作用域被嵌入的本地作用域（Enclosing locals）->全局/模块作用域->内置作用域（Built-in）

---
#### GIL线程全局锁
>GIL是针对Python下的多线程来讲的

线程全局锁（Global Interpreter Lock），即Python为了保证线程安全而采取的独立线程运行的限制，说白了就是一个核只能在同一时间运行一个线程。对于IO密集型任务，Python的所线程可以起到作用，但对于CPU密集型任务，Python的多线程几乎占不到任何的优势，还有可能会因为资源的争夺而变慢。
详细见[Python最难的问题](http://www.oschina.net/translate/pythons-hardest-problem)
解决办法就是多进程和下面的协程（协程也只是单CPU，但是能减小切换代价提升性能）。

---
#### 协程
>简单点说协程是进程和线程的升级版

进程和线程都面临着内核态和用户态的切换问题而耗费许多的切换的时间，而协程就是用户自己控制切换的时机，不再需要陷入系统得内核态。
Python里最常见的yiled就是协程的思想！

---
#### 闭包
>闭包（closure）是函数式编程的重要的语法结构。闭包也是一种组织代码的结构，它同样提高了代码的可重复使用性。

当一个内嵌函数引用其外部作用域的变量，我们就会得到一个闭包，总结一下创建一个闭包必须满足以下几点：
- 必须要有一个内嵌的函数。
- 内嵌函数必须引用外部函数中的变量。
- 外部函数的返回值必须是内嵌函数。

感觉闭包还是有难度的，几句话是说不明白的，还是查查相关的资料吧。
重点是函数运行之后并不会被撤销，就像上面的单例模式中的装饰器方法中的instance字典一样，当函数运行完之后，instance并不销毁，而是继续保存在内存空间里。这个功能类似类里的类变量，只不过迁移到了函数上。
闭包就像个空心球一样，你知道外面和里面，但你不知道中间是什么样的。

---
#### lambda函数
>其实就是一个匿名的函数，为什么引入lambda，是为了函数式编程不用再去重新定义小功能的函数

推荐阅读：[知乎](https://www.zhihu.com/question/20125256)

---
#### Python函数式编程
>Python中函数式编程主要用到的高阶函数有filter，map，reduce和列表及字典解析，将它们嵌套lambda使用。

推荐阅读： [酷壳](https://coolshell.cn/articles/10822.html)
Python中函数式编程的支持：
- fliter函数

filter函数的功能相当于过滤器。调用一个布尔函数``bool_func``来遍历每个可迭代集合中的每一个对象，返回一个使`bool_seq`返回值为true的元素序列。
```
>>>a = [1,2,3,4,5,6,7]
>>>b = filter(lambda x: x > 5, a)
>>>print b
>>>[6,7]
```
- map函数

map函数是对一个序列的每个项依次执行定义好的函数，下面是对序列的每一项都乘以2：
```
>>> a = map(lambda x:x*2,[1,2,3])
>>> list(a)
[2, 4, 6]
```
- reduce函数

reduce函数是对一个序列的每个项迭代调用函数。下面是求3的阶乘：
```
>>> reduce(lambda x,y:x*y,range(1,4))
6
```

---
#### Python里的拷贝
>Python里的拷贝分浅拷贝和深拷贝，浅拷贝不追踪对象的时间维度节点，深拷贝始终追踪对象的时间维度节点。

在这里引用copy()，deepcopy()的区别
```
import copy
a = [1, 2, 3, 4, ['a', 'b']]  #原始对象

b = a  #赋值，传对象的引用
c = copy.copy(a)  #对象拷贝，浅拷贝
d = copy.deepcopy(a)  #对象拷贝，深拷贝

a.append(5)  #修改对象a
a[4].append('c')  #修改对象a中的['a', 'b']数组对象

print 'a = ', a
print 'b = ', b
print 'c = ', c
print 'd = ', d

输出结果：
a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
c =  [1, 2, 3, 4, ['a', 'b', 'c']]
d =  [1, 2, 3, 4, ['a', 'b']]
```

---
#### Python垃圾回收机制
>  Python GC主要使用引用计数（reference counting）来追踪和回收垃圾。在引用计数的基础上。通过“标记”和“清除”（mark and sweep）解决容器对象可能产生的循环引用的问题，通过“分代回收”（generation collection）以空间换时间的方法提高垃圾回收的效率。

- 引用计数

PyObject是每个对象必有的内容。其中`ob_refcnt`就是作为引用计数。当一个对象有新的引用时，它的`ob_refcnt`就会增加，当引用它的对象删除时，它的`ob_refcnt`就会减少，当引用的计数为0时，该对象的生命就结束了。

优点：
1、简单。
2、实时性。

缺点：
1、维护引用计数消耗资源。
2、循环引用。

- 标记-清除机制

基本的思路是先按需分配，等到没有空闲内存的时候从寄存器和程序栈上的引用出发，遍历以对象为节点，以引用为边构成的图，把所有可以访问到的对象打上标记，然后清扫一遍内存空间，把所有没有标记的对象释放。

- 分代技术

分代回收的整体思想是：将系统中的所有内存块根据其存活的时间划分为不同的集合，每个集合就成为了一个“代”，垃圾的收集频率随着“代”的存活的时间的增大而减小，存活的时间通常利用经过几次垃圾回收来度量。

Python默认定义了三代对象集合，索引数越大，对象的存活时间就越长。

举例：当某些内存块M经过了3次垃圾收集的清洗后如果还存活时，我们就将内存块M划到一个集合A中去，而新分配的内存都划分到集合B中去。当垃圾收集的工作开始时，大多数的情况就只针对B集合进行垃圾回收，而对集合A进行垃圾回收要间隔相当长的一段时间后才进行，这就使得垃圾收集机制需要处理的内存少了，回收工作本身占用的系统资源减少，效率自然就提高了。这这个过程中，集合B中的某些内存块由于存活时间较长而被转移到集合A中，当然，集合A中实际上也会存在一些的垃圾，这些垃圾的回收会因为这种分代的机制而被延迟回收。

---
#### Python的List
推荐阅读：http://www.jianshu.com/p/J4U6rR

---
#### Python的is
is是对比地址，==是对比值。

---
#### read，readline和readlines
> read 读取整个文件
readline 读取下一行，使用生成器的方法
readlines 读取整个文件到一个迭代器以供我们遍历

---
#### Python2和3的区别
推荐阅读：Python 2.7.x 与 Python 3.x 的主要差异

---
#### super init
super() lets you avoid referring to the base class explicitly, which can be nice. But the main advantage comes with multiple inheritance, where all sorts of fun stuff can happen. See the standard docs on super if you haven't already.

Note that the syntax changed in Python 3.0: you can just say super().__init__() instead of super(ChildB, self).__init__() which IMO is quite a bit nicer.

http://stackoverflow.com/questions/576169/understanding-python-super-with-init-methods

[Python2.7中的super方法浅见](http://blog.csdn.net/mrlevo520/article/details/51712440)

---
#### range and xrange
都是在循环的时候使用，xrange内存的性能更好。for i in range(0, 20): for i in xrange(0, 20): What is the difference between range and xrange functions in Python 2.X? range creates a list, so if you do range(1, 10000000) it creates a list in memory with 9999999 elements. xrange is a sequence object that evaluates lazily.

http://stackoverflow.com/questions/94935/what-is-the-difference-between-range-and-xrange-functions-in-python-2-x


#  :snake:python语法部分

- [1 @staticmethod和@classmethod](# 1-@staticmethod和@classmethod) 
- [2  类变量和实例变量](#2-类变量和实例变量) 
- [3 Python自省](#3-Python自省)
- [4 列表、字典、集合推导式和生成器表达式](#4-列表、字典、集合推导式和生成器表达式)
- [5 Python中单下划线和双下划线](#5-Python中单下划线和双下划线)
- [6 迭代器和生成器](#6-迭代器和生成器)
- [7 面向切片编程AOP和装饰器](#7-面向切片编程AOP和装饰器) 
- [8 Property属性描述符](#8-Property属性描述符) /
- [9 鸭子类型 ](#9-鸭子类型 )
- [10 新式类和旧式类](#10-新式类和旧式类)
- [11 Python的一切皆对象 ](#11-Python的一切皆对象 )
- [12 new和init关键字的区别](#12-new和init关键字的区别)
- [13 元类](#13-元类)
- [14 单例模式 ](#14-单例模式 )
- [15 闭包 ](#15-闭包 )
- [16 深浅拷贝](#16-深浅拷贝  )
- [17 垃圾回收](#17-垃圾回收 )
- [18 进程线程协程](#18-进程线程协程)

## 1 @staticmethod和@classmethod

三种方法在内存中都归属于类，区别在于调用方式不同 

- 实例方法：由对象调用；至少一个self参数；执行实例方法 时，自动将调用该方法的对象赋值给self；
- 类方法@classmethod：由类调用； 至少一个cls参数；执行类方法时，自动将调用该方法的类赋值给cls；
- 静态方法@staticmethod：由类调用；无默认参数  

```
def func1(x):
    print(f"in func1 :{x}")


class A:
    dd = 18

    def func1(self, x):
        print(f"in ins_func1 ： {x}")

    @staticmethod
    def static_func1(x):
        print(f"in static_func1 ： {x}")

    @classmethod
    def cls_func1(cls, x):
        print(f"in cls_func1 ： {x}")
        print(cls.dd)
        print(A.dd)


a = A()
x = "mr_menand"

# 普通方法和实列方法
func1(x)
a.func1(x)

# 静态方法
a.static_func1(x)
A.static_func1(x)

# 类方法
A.cls_func1(x)
```



## 2 类变量和实例变量

- 实例变量属于对象 ,实例化之后，每个实例单独拥有的变量
- 类变量属于类,是可在类的所有实例之间共享的值（也就是说，它们不是单独分配给每个实例的） 

```
class Test(object):
    num_of_instance = 0

    def __init__(self, name):
        self.name = name
        Test.num_of_instance += 1


if __name__ == '__main__':
    print(Test.num_of_instance) # 0
    
    test1 = Test("jacky")
    print(test1.name, test1.num_of_instance) # jacky 1

    test2 = Test("love")
    print(test1.name, test1.num_of_instance) # jacky 1 
    print(test2.name, test2.num_of_instance) # jacky 1

```

先找类有没有实例变量num_of_instance,没有就向上找类变量  

> 补充例子

```
class Person:
    name = "aaa"


p1 = Person()
p2 = Person()

print(p2.__dict__)  # {}
p2.age = 18 # 类的实列属性
print(p2.age)  # 18  
print(p2.__dict__)  # {'age': 18}

p2.name = "bbb"
print(p2.name)  # bbb
print(p2.__dict__)  # {'age': 18, 'name': 'bbb'}
print(p1.name)  # aaa
print(Person.name)  # aaa
```

这里`p1.name="bbb"`是实例调用了类变量,`p1.name`一开始是指向的类变量`name="aaa"`,但是在实例的作用域里把类变量的引用改变了,就变成了一个实例变量,self.name不再引用Person的类变量name了. 



不要用空列表维护类变量 (TODO // 有一个经典的问题)

```
class Person:
    name = []


p1 = Person()
p2 = Person()

p1.name.append(1)
print(p1.name)
print(p2.name)
print(Person.name)

p2.name.append(2)
print(Person.name)
```

## 3 Python自省

这个也是python彪悍的特性.

自省就是面向对象的语言所写的程序在运行时,所能知道对象的类型.简单一句就是运行时能够获得对象的类型.

**Python中比较常见的自省（introspection）机制(函数用法)有： obj.\__dict__ ,  dir()，type(), hasattr(), isinstance()，通过这些函数，我们能够在程序运行时得知对象的类型，判断对象是否存在某个属性，访问对象的属性。**



isinstance()函数来判断一个对象是否是一个已知的类型。

isinstance 会考虑继承关系，type不会

如果要判断两个类型是否相同推荐使用 isinstance()  

```
class A:
    name = "mr_menand"


class B(A):
    pass


b = B()

print(isinstance(b, B))  # True
print(isinstance(b, A))  # True
print(type(b) is B)  # True
print(type(b) is A)  # False

print(b.__dict__)  # {}
print(dir(b))  # [..., '__weakref__', 'name']
print(hasattr(b, "name"))  # True

```

## 4 列表、字典、集合推导式和生成器表达式

- 列表  data = [randint(-10,10) for _ in range(10)]
  - 过滤器：filter(lambda x: x>0, data)
  - 列表推导：[x for x in data if x>0]
- 字典   data = {x:randint(60,100) for x in range(1,21)}
  - 字典推导： {k:v  for k ,v in data.items if v > 90}
- 集合 data = set([randint(-10,10) for _ in range(10)]) 
  - 集合推导 {x for x in data if x%3 ==0} 
- 生成器表达式背后遵守了迭代器协议，可以逐个地产出元素，节省内存 
  - （x for x in data if x>0）

```
import time
from random import randint

start = time.perf_counter()
data = [randint(-100, 100) for _ in range(1000000)]
list_data = [x for x in data if x > 0]
gen_data = (x for x in data if x > 0)
dd = []
for i in list_data:
    dd.append(i)

print(f" total time consume : {time.perf_counter() - start} S ") 
# total time consume : 0.6398516000000001 S 
mm = []
start = time.perf_counter()
for i in gen_data:
    mm.append(i)
print(f" total time consume : {time.perf_counter() - start} S ")
#  total time consume : 0.07002510000000006 S 
```


## 5 Python中单下划线和双下划线  

- `_dd`    : 程序员用来指定私有变量的一种方式，不能用'from module import *'导入.

- `__dd` :这个有真正的意义:解析器用`_classname__foo`来代替这个名字,以区别和其他类相同的命名,它无法直接像公有成员一样随便访问,通过对象名._类名__xxx这样的方式可以访问.

- `__dd__` :表示python里特殊方法专用的标识，如`__new__,__init__`等常见的魔法方法 

  ```
  class A:
      def __init__(self, name, age, height):
          self.__name = name
          self._age = age
          self.height = height
  
  
  mr_menand = A("mr_menand", 25, 170)
  
  print(mr_menand.__dict__)
  print(mr_menand.height)
  print(mr_menand._age)
  print(mr_menand.__name)
  ```

  

## 6 迭代器和生成器

```
from random import randint
from typing import Iterable, Iterator

l = [randint(0, 10) for _ in range(10)]
s = "abcdefg"
print(isinstance(l, Iterator), isinstance(l, Iterable))  # False True
print(isinstance(s, Iterator), isinstance(s, Iterable))  # False True
# ls 都是可迭代对象

print(isinstance(iter(l), Iterator), isinstance(iter(l), Iterable))  # True True
# iter(l) 与 l.__iter__()  都是迭代器对象（__next__）
```

迭代对象实现`__iter__`魔法函数，迭代器实现`__next__`魔法函数

生成器：边循环，边计算的机制—>generator

```
class PrimeNumbers:
    def __init__(self, start, end):
        self.start = start
        self.end = end

    def is_prime_num(self, k):
        if k < 2:
            return False
        for i in range(2, k):
            if k % i == 0:
                return False

        return True

    def __iter__(self):
        for k in range(self.start, self.end + 1):
            if self.is_prime_num(k):
                yield k


for x in PrimeNumbers(1, 100):
    print(x)
```

将该类的 `__iter__` 方法实现成生成器函数，每次yield 返回一个素数 

生成器对象实现迭代器对象和可迭代对象 



## 7 面向切片编程AOP和装饰器

AOP（Aspect Oriented Programming），面向切面编程

AOP为了更加简化代码，防止代码侵入，降低代码耦合，将多个类都可能使用的具有相同功能的公共行为提取出来，多个类可以共同使用这个公共行为。

AOP思想的主要功能是日志记录，性能统计，安全控制，事务处理，异常处理等。

Python中的装饰器充分体现了AOP的思想



定义装饰器函数，用它来生成一个原函数添加了新功能的函数，替代原函数

@装饰器名func    
def dd()  
等价于  dd = func(dd)   
dd()本质上等于被装饰函数dd先作为参数赋值给func后，装饰器闭包函数里面执行dd()  

```
from functools import lru_cache

def memo(func):
    cache = {}

    def wrap(*args):
        if args not in cache:
            cache[args] = func(*args)
        return cache[args]

    return wrap


@memo
def fibonacci(n):
    if n <= 1:
        return 1
    return fibonacci(n - 1) + fibonacci(n - 2)

start = time.perf_counter_ns()
print(f"fib consume time : {time.perf_counter_ns()-start} ns , result : {fibonacci(50)}") 
# fib consume time : 300 ns , result : 453973694165307953197296969697410619233826
```

memo和lru_cahe花费的时间差不多  

分析下lru_cached()的源码 ：

```
 def wrapper(*args, **kwds):
            # Size limited caching that tracks accesses by recency
            nonlocal root, hits, misses, full
            key = make_key(args, kwds, typed)
            with lock:
                link = cache_get(key)
                if link is not None:
                    # Move the link to the front of the circular queue
                    link_prev, link_next, _key, result = link
                    link_prev[NEXT] = link_next
                    link_next[PREV] = link_prev
                    last = root[PREV]
                    last[NEXT] = root[PREV] = link
                    link[PREV] = last
                    link[NEXT] = root
                    hits += 1
                    return result
                misses += 1
            result = user_function(*args, **kwds)
```

**`lru_cache`的实现，依赖于`Python`的闭包，以及`LRU`算法。**用了双链表和哈表表

python实现LRU_CACHE  [参考](https://zhuanlan.zhihu.com/p/7655322)

[TODO // 算法部分自己实现一遍，一般为必考点 ] 



## 8 Property属性描述符

 @Property（把类中的方法变成*属性*）来代替属性重构（取代get和set描述符方法），为类创建可管理属性  

装饰器方式 即：在方法上应用装饰器 

- @property --> func 将方法伪装成属性 
  @func.setter --> func 对伪装的属性进行赋值的时候调用这个方法 一般情况下用来做修改 
  @func.deleter --> func 在执行del 对象.func的时候调用这个方法 一般情况下用来做删除 

```
class Student:

    def __init__(self, name, age, sex):
        self._name = name
        self._age = age
        self._sex = sex

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, item):
        self._age = item

    @age.deleter
    def age(self):
        del self._age
        print("del done !")


stu = Student("mr_menand", 25, "male")
print(stu.age)  # 25
stu.age = 188
print(stu.age)  # 188
del stu.age  # del done
print(stu.__dict__)  # {'_name': 'mr_menand', '_sex': 'male'}
```

Property的类属性方法：property(getname,[setname,delname]),用法比较少

另外说下`__getattr__和__setattr__`两个属性描述符：

```
class Student:

    def __init__(self, name, age, sex):
        self._name = name
        self._age = age
        self._sex = sex

    def __getattr__(self, item):
        return ("invoke __getattr__ ", item)


stu = Student("mr_menand", 25, "male")
print(stu.__dict__) # {'_name': 'mr_menand', '_age': 25, '_sex': 'male'}
print(stu._name) # mr_menand 不会调用getattr
print(stu._height) # ('invoke __getattr__ ', '_height')  _height没找到，调用getattr 

stu._height = 170 # obeject 已经实现__setattr__
print(stu.__dict__) # {'_name': 'mr_menand', '_age': 25, '_sex': 'male', '_height': 170}
```

对实例属性赋值，被赋值的属性和值会存入实例属性字典__dict__中

而`__getattribute__`无论找不找到都会执行 



## 9 鸭子类型 

> 提下多态和鸭子类型的区别 

多态是在Java ，C++中概念，对象需要定义类型，左父右子，用子类生成父类对象，调用子类重写的方法表现的多态性  

鸭子类型是动态类型的一种风格。“鸭子测试”可以这样表述：“当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。**关注的不是对象的类型本身，而是它是如何使用的。类与类之间不用共同继承一个父类，只需要将它们做得像一种事物即可**  

```
# extend方法的参数是接收一个可迭代的对象，而list、tuple、和set对象都实现了__iter__(self)（定义当迭代容器中的元素的行为），这就是所谓的，它不关注对象的类型，而是关注对象具有的行为(方法)。

list = [1,2,3]
tuple = (4,5,6)
set = {7,8,9}

list.extend(tuple) # [1,2,3,4,5,6]
list.extend(set) # [1,2,3,4,5,6,7,8,9]

# extend 还可以接受所有自定义类型实现了__iter__(self)的魔法函数
```

魔法函数充分扩展了鸭子类型这种松耦合的方式 



## 10 新式类和旧式类 

新样式类继承自*object*或另一个新类，旧式类没有。**Python 3仅具有新型类，统一class和type的概念**。

> 我理解成：Python一切皆对象的原因，下一条解释  

python3的 新式类的MRO（Method Resolution Order方法解析顺序)采用C3算法，旧时类的算法为DFS

C3算法对于多继承的箭形继承和菱形继承分别用DFS和BFS 

```
class D:
    pass

class E:
    pass

class C(E):
    pass

class B(D):
    pass

class A(B, C):
    pass

print(A.__mro__)  # (<class '__main__.A'>, <class '__main__.B'>, <class '__main__.D'>, <class '__main__.C'>, <class '__main__.E'>, <class 'object'>) 
# 箭形基础 DFS　　

```

```
#新式类
class D:
    pass

class E:
    pass

class C(D):
    pass

class B(D):
    pass

class A(B, C):
    pass

print(A.__mro__) # (<class '__main__.A'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.D'>, <class 'object'>) 
# 菱形继承  BFS
```

> 这里再拓展一下Super的调用顺序

**super的调用顺序和 mro的顺序一致** 

```
class D:
    def __init__(self):
        print("D")


class B(D):
    def __init__(self):
        print("B")
        super().__init__()


class C(D):
    def __init__(self):
        print("C")
        super().__init__()


class A(B, C):
    def __init__(self):
        print("A")
        super(A, self).__init__()



print(A.__mro__) # (<class '__main__.A'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.D'>, <class 'object'>)  菱形继承 BFS 
d = A()  # A B C D 
```

## 11 Python的一切皆对象 

Python中一切皆对象，可以表现函数和类也是对象，属于Python的一等公民 

1. 赋值给一个变量 
1. 可以添加到集合对象中
1. 可以作为参数传递给函数 
1. 可以当作参数的返回值   

##### **1.  type、object 和 class的关系** 

type关键字的作用： 1. 创建类的类 （元类）    2. 检查对象类型  

obj(对象)  --> class -- > type

```
a = [5,2,0]
type(a) # list 
type(list) # type 
list.__bases__ # (object,)

type(type) # type 
type.__bases__ # (object,) 
type.__class__ #  type

type(object) # type 
object.__bases__  # ()
object.__class__ # type
```

type也是一个类，同时type也是一个对象 

object 是最顶层基类 ,object是type的实例, 

type继承object ,type是自身的实例 

**一切皆对象是因为 ： type 是所有对象的实例，object是所有对象顶层基类，而object也是type的实例，type也继承object，形成一个循环**  

![type_class_object](E:/Py-Transaction/images/type_class_object.png)



##### **2.  从Python源码分析一切皆对象的原因**

```
/ Include/object.h 
#define _PyObject_HEAD_EXTRA            
    struct _object *_ob_next;           
    struct _object *_ob_prev;

typedef struct _object {
    _PyObject_HEAD_EXTRA    // 双向链表 垃圾回收 需要用到
    Py_ssize_t ob_refcnt;   // 引用计数
    struct _typeobject *ob_type;    // 指向类型对象的指针，决定了对象的类型
} PyObject;
```

Python 中的所有对象都拥有一些相同的内容，而这些内容就定义在**PyObject**中 

详细可以参看[python的源码分析]()



## 12 new和init关键字的区别

> `__new__`其实就是析构函数，而`__init__`为初始化方法

1. `__new__`是一个静态方法,而`__init__`是一个实例方法.
2. `__new__`方法会返回一个创建的实例,而`__init__`什么都不返回.
3. 只有在`__new__`返回一个cls的实例时后面的`__init__`才能被调用.
4. 当创建一个新实例时调用`__new__`,初始化一个实例时用`__init__`.

```
class Student:

    def __new__(cls, *args, **kwargs):
        print("in new")
        # return super().__new__(cls)
    def __init__(self, name):
        print("in init")
        self._name = name

# new类是用来控制对象的生成过程，在对象生成之前
# 如果new方法不返回对象，则不会调用init函数
stu = Student("mr_menand") # in new  
```



## 13 元类

这篇文章说的很清楚，[python中的元类和abc.ABCMeta实现的虚类](https://www.jianshu.com/p/06c960020322)

- Python中一切皆对象，类也是个对象 

- 元类就是用来创建类的“东西” ，元类就是类的类

  ```
  MyClass = MetaClass() # 使用元类创建出一个对象，这个对象称为“类”
  my_object = MyClass() # 使用“类”来创建出实例对象，定义的有`__call__`魔法方法
  ```

- type就是Python在背后用来创建所有类的元类 ,Python的所有东西都是对象，都是通过type创建的 

- 元类的主要目的就是为了当创建类时能够自动地改变类

  - 拦截类的创建
  - 修改类
  - 返回修改之后的类 

- abc.ABCMeta
  ABCMeta就是让你的类变成一个纯虚类，子类必须实现某个方法，这个方法在父类中用@abc.abstractmethod 抽象方法修

**1. 元类的实现方式**

详情请看:http://stackoverflow.com/questions/100003/what-is-a-metaclass-in-python

**2. 元类实现ORM**

ORM 是 python编程语言后端web框架 Django的核心思想，“Object Relational Mapping”，即对象-关系映射，简称ORM



## 14 单例模式 

> 单例模式至只包含一个被称为单例类的特殊类 

#### **1. 使用`__new__`关键字** 

`__new__()`在`__init__()`之前被调用，用于生成实例对象。利用这个方法和类的属性的特点可以实现设计模式的单例模式

```
class Singleton:
    _instance = None

    def __new__(cls, *args, **kwargs):
        print("in new instance ")
        if cls._instance is None:
            cls._instance = super().__new__(cls, *args, **kwargs)
        return cls._instance

    def __init__(self):
        print("in init")

A = Singleton()  # in new ;in init
B = Singleton()  # in new ;in init 
print(id(A), id(B), id(A) is id(B), id(A) == id(B))  # 2072001252080 2072001252080 False Truea 

# 另一个版本，不用None,因为None本身就是一个单例实现，全局只有一个实例 

class Singleton(object):
    def __new__(cls, *args, **kw):
        if not hasattr(cls, '_instance'):
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kw)
        return cls._instance

```

这种写法有两种问题：

- ​	单例模式对应类实例化时无法传入参数

  ```
  class Singleton:
      _instance = None
  
      def __new__(cls, *args, **kwargs):
          print("in new instance ")
          if cls._instance is None:
              cls._instance = super().__new__(cls, *args, **kwargs)
          return cls._instance
  
      def __init__(self, x, y):
          print("in init")
          self._x = x
          self._y = y
  ```

  会出现问题`TypeError: object.__new__() takes exactly one argument (the type to instantiate) `

- 多个线程实例化Singleton类时,可能会出现创建多个实例的情况，因为很有可能多个线程同时判断cls._instance is None，从而进入初始化实例的代码中 

​    [Python单例模式最佳实践](https://juejin.im/post/6844904081182425102) :这篇文章有讲到通过**同步锁**和**装饰器**来解决上述两个问题

#### **2.  Monostate 单态模式**

> 其实用的就是类变量的全局唯一性 

创建实例时把所有实例的`__dict__`指向同一个字典,这样它们具有相同的属性和方法. 

基于所有的对象共享相同的状态，__dict__存储一个类的所有对象 

```
class Singleton:
    _shared_state = {}

    def __new__(cls, *args, **kwargs):
        obj = super(Singleton, cls).__new__(cls, *args, **kwargs)
        obj.__dict__ = cls._shared_state
        return obj


A = Singleton()
B = Singleton()
print(id(A), id(B), id(A) is id(B), id(A) == id(B))  # 2156059364080 2156059363792 False False

A.x = 66
print("object state 'A': ", A.__dict__)  # object state 'A':  {'x': 66}
B.x = 888
print("object state 'A': ", A.__dict__)  # object state 'A':  {'x': 888}
print("object state 'B': ", B.__dict__)  # object state 'B':  {'x': 888}
```

可以发现A，B的id（）内存的地址不一同，值也不同



#### **3.  装饰器模型**

AOP编程

```
def singleton(cls):
    _instance = {}

    def _singleton(*args, **kwargs):
        print("in wrapper ")
        if cls not in _instance:
            _instance[cls] = cls(*args, **kwargs)
        return _instance[cls]

    return _singleton


@singleton
class MySingleton:
    def __init__(self):
        print("in init")


A = MySingleton()  # in wrapper  ;in init ; in wrapper
B = MySingleton()
print(id(A), id(B), id(A) is id(B), id(A) == id(B))  # 2651816456880 2651816456880 False True
```



**4.  单例与元类**

类的定义由它的元类决定，所以当我们用类A创建一个类时， Python 通过A=type(name,base,dict)创建 

**元类**对类的创建和对象实例化有更多的控制权，所以可以创建单例 。 

为了控制类的创建和初始化，元类将覆盖`__new__`和`__init__`方法。

```
class MetaSingleton(type):
    _instance = {}

    def __call__(cls, *args, **kwargs):
        print("in call")
        if cls not in cls._instance:
            cls._instance[cls] = super(MetaSingleton, cls).__call__(*args, **kwargs)

        return cls._instance[cls]


class MySingleton(metaclass=MetaSingleton):

    def __init__(self):
        print("in init ")


A = MySingleton()  # in call  ;in init ; in call
B = MySingleton()
print(id(A), id(B), id(A) is id(B), id(A) == id(B))  # 2175795627440 2175795627440 False Tru
```



## 15 闭包 

闭包(closure)是函数式编程的重要的语法结构。

当一个内嵌函数引用其外部作作用域的变量,我们就会得到一个闭包. 总结一下,创建一个闭包必须满足以下几点:

1. 必须有一个内嵌函数
2. 内嵌函数必须引用外部函数中的变量
3. 外部函数的返回值必须是内嵌函数

> 我们可以理解为：对象的方法obj()，类似类对象定义`__call__`函数，obj = func()  --> obj() 这种的一种方式。

```
x = 300


def func(y):
    x = 200

    def inner():
        print("in closure")
        nonlocal x
        x += 1
        print(f"{x + y}")

    return inner


obj = func(100)
print(obj.__closure__)  # (<cell at 0x00000219B2BFF2B0: int object at 0x00007FFE5B6C2F80>, <cell at 0x00000219B2C80A00: int object at 0x00007FFE5B6C2300>)
obj()  # in closure ; 301
```

外函数结束的时候发现内部函数将会用到自己的临时变量，这两个临时变量就不会释放，会绑定给这个内部函数 

**闭包中内函数通过`nonlocal`修改外函数局部变量**



## 16 深浅拷贝

  - 浅拷贝是对于一个对象的顶层拷贝，拷贝了引用，并没有拷贝内容 
  - 深拷贝是对于一个对象所有层次的拷贝(递归) 

```
import copy

a = [1, 2, 3, 4, ['a', 'b']]  # 原始对象

b = a  # 赋值，传对象的引用
c = copy.copy(a)  # 对象拷贝，浅拷贝
d = copy.deepcopy(a)  # 对象拷贝，深拷贝

a.append(5)  # 修改对象a
a[4].append('c')  # 修改对象a中的['a', 'b']数组对象

print("a : ", a) # a :  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
print("b : ", b) # b :  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
print("c : ", c) # c :  [1, 2, 3, 4, ['a', 'b', 'c']]
print("d : ", d) # d :  [1, 2, 3, 4, ['a', 'b']]
```

可以发现：浅复制，只会拷贝父对象， 不会拷贝父对象中的子对象，a的父子对象变c也变，但是a的父对象变，c没有变  



## 17 垃圾回收 
Python GC主要使用引用计数（reference counting）来跟踪和回收垃圾。在引用计数的基础上，通过“标记-清除”（mark and sweep）解决容器对象可能产生的循环引用问题，通过“分代回收”（generation collection）以空间换时间的方法提高垃圾回收效率 

标记--清除方法主要是垃圾检测节点，其简要过程如下：

- `寻找根对象(root object)的集合，所谓的root object就是一些全局引用和函数栈的引用。这些引用所用的对象是不可被删除的，而这个root object集合也是垃圾检测动作的起点`
- `从root object集合出发，沿着root object集合中的每一个引用，如果能到达某个对象A，则称A是可达的(reachable)，可达的对象也不可被删除。这个阶段就是垃圾检测阶段`
- `当垃圾检测阶段结束后，所有的对象分为了可达的(reachable)和不可达的(unreachable)。而所有可达对象都必须予以保留，而不可达对象所占用的内存将被回收。` 

而这样的链表为什么有三条呢？试想一下，gc发动的成本也是很高的，如果在gc的洗礼下还能活下来的对象，说明其暂时是较稳的，没有必要每次都对其进行检测。所以会将零代链表中比较稳定的对象移动到一代链表中，同理二代链表也是同理。当清理零代链表达到10次的时候，会清理一次一代链表，清理一代链表达到10次的时候会清理一次二代链表。这样的技术在Python中也被成为分代技术。 



## 18 进程线程协程 

多进程：并行，适合CPU密集型 , 多进程切换耗时大、创建开销大 

多进程：并发，适合IO密集型，共享全局变量、抢占式、资源竞争 、GIL, I/O bound达到极限，CPU占用率低

协程的方式，调度来自用户，用户可以在函数中yield一个状态,切换到另一个函数。 以异步+非阻塞的方式，适合高I/O  



**协程Coroutine**

- 比线程更小占用更小执行单元，自带CPU上下文

-  在一个线程中的某个函数，可以在任何地方保存当前函数的一些临时变量等信息，然后切换到另外一个函数中执行，注意不是通过调用函数的方式做到的，并且切换的次数以及什么时候再切换到原来的函数都由开发者自己确定

  - 线程切换从系统层面远不止保存和恢复 CPU上下文这么简单。 操作系统为了程序运行的高效性每个线程都有自己缓存Cache等等数据，操作系统还会帮你做这些数据的恢复操作 
  - 协程的切换只是单纯的操作CPU的上下文 



如果一个对象可以在 await 语句中使用，那么它就是 **可等待** 对象。许多 asyncio API 都被设计为接受可等待对象。

*可等待* 对象有三种主要类型: **协程**, **任务** 和 **Future**.

- event_loop 事件循环：程序开启一个无限的循环，程序员会把一些函数注册到事件循环上。当满足事件发生的时候，调用相应的协程函数 
- coroutine 协程：协程对象，指一个使用async关键字定义的函数，它的调用不会立即执行函数，而是会返回一个协程对象。协程对象需要注册到事件循环，由事件循环调用。
- task 任务：一个协程对象就是一个原生可以挂起的函数，任务则是对协程进一步封装，其中包含任务的各种状态。所谓task对象是Future类的子类。保存了协程运行后的状态，用于未来获取协程的结果。
- future 未来对象： 代表将来执行或没有执行的任务的结果。它和task上没有本质的区别.

```
import asyncio
import time
from concurrent import futures

import aiohttp
import requests

URL = 'https://www.nihaowua.com/'


def fetch_sync():
    start = time.perf_counter()
    response = requests.get(URL)
    # time.sleep(randint(1, 3))
    time.sleep(1)
    # print(f"time consume : {time.perf_counter() - start} S ")
    return response


async def aiohttp_get(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            await asyncio.sleep(1)
            return response

async def fetch_async():
    start = time.perf_counter()
    # response = requests.get(URL)
    response = await aiohttp_get(URL)

    # await asyncio.sleep(randint(1,3))
    # print(f"time consume : {time.perf_counter() - start} S ")
    await asyncio.sleep(0.1)
    return response


async def asynchronous():
    start = time.perf_counter()
    tasks = [fetch_async() for _ in range(10)]

    await asyncio.gather(*tasks)

    # for _ ,future in enumerate(asyncio.as_completed(tasks)):
    #     ret = await  future
    #     print(ret)

    # done, pendding = await  asyncio.wait(tasks,return_when=FIRST_COMPLETED)
    # print(done.pop().result())

    # done,_ = await  asyncio.wait(tasks)
    # for task in done:
    #     print(task.result())

    print(f"Asynchronous total time consume : {time.perf_counter() - start} S ")


def synchronous():
    start = time.perf_counter()
    for _ in range(10):
        fetch_sync()
    print(f"Synchronous total time consume : {time.perf_counter() - start} S ")


def process_way():
    start = time.perf_counter()
    with futures.ProcessPoolExecutor(max_workers=10) as executor:
        futs = [executor.submit(fetch_sync) for _ in range(10)]

    print(f"Process total time consume : {time.perf_counter() - start} S ")


def thread_way():
    start = time.perf_counter()
    with futures.ThreadPoolExecutor(max_workers=10) as executor:
        futs = [executor.submit(fetch_sync) for _ in range(10)]

    print(f"Thread total time consume : {time.perf_counter() - start} S ")


if __name__ == '__main__':
    print("Process fetch :")
    process_way()

    print("Thread fetch :")
    thread_way()

    print("Synchronous fetch :")
    synchronous()

    print("---" * 8)
    print('Asynchronous fetch:')
    asyncio.run(asynchronous())

# Process fetch :
# Process total time consume : 4.2361524 S 
# Thread fetch :
# Thread total time consume : 1.6362946999999997 S 
# Synchronous fetch :
# Synchronous total time consume : 15.447584000000003 S 
# ------------------------
# Asynchronous fetch:
# Asynchronous total time consume : 1.3940881000000012 S 
```

模拟I/O下载过程（受网络影响），对比了同步、多进程、多线程和协程四种不同的I/O处理方式。 
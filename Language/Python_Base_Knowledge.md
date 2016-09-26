# Python 必备知识点
<!-- toc -->

## Python Resource
- [The Python 2.7 Standard Library](https://docs.python.org/2/library/index.html)
- [Python多继承](https://segmentfault.com/a/1190000004018476)
- [linux下python调试 pdb](http://blog.csdn.net/linda1000/article/details/11031771)
- [Pycharm对linux上python代码进行远程调试](http://blog.csdn.net/eric_sunah/article/details/20538283)
- [Python 2.7.x 与 Python 3.x 的主要差异](http://chenqx.github.io/2014/11/10/Key-differences-between-Python-2-7-x-and-Python-3-x/)

## Python基础

> 到今天，Python的框架已经确立，Python语言以对象为核心组织代码(Everything is object)，支持多种编程范式(multi-paradigm)，采用动态类型(dynamic typing)，自动进行内存回收(garbage collection)。Python支持解释运行(interpret)，并能调用C库进行拓展。Python有强大的标准库 (battery included)。由于标准库的体系已经稳定，所以Python的生态系统开始拓展到第三方包。这些包，如Django, web.py, wxpython, numpy, matplotlib,PIL，将Python升级成了物种丰富的热带雨林。

### 序列
```
sequence(序列)是一组有顺序的元素的集合(tuple and list)
sequence范围 [下限:上限:步长]
字符串是一种特殊的tuple
tuple元素不可变，list元素可变
```

### 基本数据类型
```
基本数据类型(标准类型): 数字, str, tuple, list, dict, set, frozenset
不可变&可哈希: 数字, str, tuple, frozenset
可变&不可哈希: list, dict, set
dict: key必须是不可变&可哈希, value可以是任意对象
集合运算: a|b a&b a-b a^b(只属于a和只属于b的元素集合)
集合判断: a==b a!=b a<b(a是否是b真子集) a<=b(子集) a>b a>=b
```

| 数据类型 | 存储模型|更新模型|访问模型|顺序模型|
|------|------|------|-----------|-------|
|数字|标量|不可变|直接访问| - |
|str|标量|不可变|顺序访问|有序|
|tuple|容器|不可变|顺序访问|有序|
|list|容器|可变|顺序访问|有序|
|dict|容器|可变|映射访问|无序|
|set|容器|可变|循环遍历, in, not in|无序|
|frozenset|容器|不可变|循环遍历, in, not in|无序|

### 类与对象

```Python
class Boy(object):
    laugh = 'hahahaha'   #类属性
    def __init__(self):  #在建立对象时自动执行
    	 self.age = 20    #对象属性
    	 print self.laugh #通过self调用类属性
    	 
Jim = Boy()
print dir(Jim)  #dir()用来查询一个类或者对象所有属性
print help(Jim) #help()用来查询类或对象所属类的说明文档
```

通过对象可以修改类属性值。但这是危险的。  
类属性被所有同一类及其子类的对象共享。类属性值的改变会影响所有的对象。


## Python进阶
### 文件

```python
f = open(文件名，模式)  # "r"只读 "w"写入
f.read(N)     # 读取N bytes的数据
f.readline()  # 读取一行
f.readlines() # 读取所有行，储存在列表中，每个元素是一行
f.write(str) 
f.close()
```
### 模块

```
1. 在Python中，一个.py文件就构成一个模块
2. import a as b
   from a import * #a中以_打头的对象(__打头更不行)不会被导入
   from a import obj #减小所引用模块的内存占用
3. Python会在以下路径中搜索它想要寻找的模块：程序所在的文件夹 
   -> 标准库的安装路径 -> 操作系统环境变量PYTHONPATH所包含的路径
4. 文件夹 + __init__.py + 其它模块(包) = 构成一个模块包, 就可以 import dir.a
5. print hasattr(obj, attr_name) #True or Flase
   print obj.__class__ #对象所属的类
   print obj.__class__.__name__ #类名
   print cls.__base__ #父类
6. #coding=utf8 or #-*- coding: UTF-8 -*-
   print("你好吗？")
```
test.py

```
if __name__ == '__main__': #如果是import test,该__name__等于"test"
    print "Hello"
```

### 包裹关键字传递

```python
def func(*name):
    print type(name) #tuple
    print name

func(1,4,6)

def func(**dict):
    print type(dict) #dict
    print dict

func(a=1,b=9)

#在定义或者调用参数时，参数的几种传递方式可以混合。
#但在过程中要小心前后顺序。
#基本原则是: 先位置，再关键字，再包裹位置，再包裹关键字
```
### 解包裹

```python
def func(a,b,c):
    print a,b,c

args = (1,3,4)
func(*args)
dict = {'a':1,'b':2,'c':3}
func(**dict)
```
### 循环

```
range() -> list of int  
enumerate() -> list of (index, value)  
zip() -> list of list_a, list_b, ..., list_n

循环对象是这样一个对象，它包含有一个next()方法(__next__()方法，在python 3x中)， 
这个方法的目的是进行到下一个结果，而在结束一系列结果之后，举出StopIteration错误。

从技术上来说，循环对象和for循环调用之间还有一个中间层，就是要将循环对象转换成迭代器(iterator)。
这一转换是通过使用iter()函数实现的。
但从逻辑层面上，常常可以忽略这一层，所以循环对象和迭代器常常相互指代对方。

生成器(generator)的主要目的是构成一个用户自定义的循环对象，在Python 3.X中，map()的返回值就是一个循环对象。
生成器的编写方法和函数定义类似，只是在return的地方改为yield。
生成器自身又构成一个循环器，每次循环使用一个yield返回的值。

生成器表达式: g = (x for x in range(4))
表推导: l = [x**2 for x in range(10)]

Python允许你快速定义单行的最小函数，类似与C语言中的宏，这些叫做lambda函数:
g = lambda s: s+1
print g(1) #2
print (lambda x: x * 2)(3) #6

将函数对象作为参数的函数有：
map(function, sequence) :                      def fun(s): return s*10
filter(function, sequence) :                   def fun(s): return s if s != 1 else None
reduce(function, sequence[, starting_value]) : def fun(a, b): return a + b
```
### **[迭代器和生成器](http://python.jobbole.com/81881/)**

### **[异常处理](http://www.runoob.com/python/python-exceptions.html)**

```python
try:
    ...
    raise exception
    ...
except exception1:
    ...
except exception2:
    ...
except:
    ...
else:
    ...
finally:
    ...
```

## Python补充

### Python内置函数清单

```
基本数据类型 type()
反过头来看看 dir() help() len()
词典 len()
文本文件的输入输出 open()
循环设计 range() enumerate() zip()
循环对象 iter()
函数对象 map() filter() reduce()
```

```
数学运算

abs(-5)                          # 取绝对值，也就是5
round(2.6)                       # 四舍五入取整，也就是3.0
pow(2, 3)                        # 相当于2**3，如果是pow(2, 3, 5)，相当于2**3 % 5
cmp(2.3, 3.2)                    # 比较两个数的大小
divmod(9,2)                      # 返回除法结果和余数
max([1,5,2,9])                   # 求最大值
min([9,2,-4,2])                  # 求最小值
sum([2,-1,9,12])                 # 求和

类型转换

int("5")                         # 转换为整数 integer
float(2)                         # 转换为浮点数 float
long("23")                       # 转换为长整数 long integer
str(2.3)                         # 转换为字符串 string
complex(3, 9)                    # 返回复数 3 + 9i
ord("A")                         # "A"字符对应的数值
chr(65)                          # 数值65对应的字符
unichr(65)                       # 数值65对应的unicode字符
bool(0)                          # 转换为相应的真假值，在Python中，0相当于False
# Python中,下列对象都相当于False: [] () {} 0 None 0.0 ''

bin(56)                          # 返回一个字符串，表示56的二进制数
hex(56)                          # 返回一个字符串，表示56的十六进制数
oct(56)                          # 返回一个字符串，表示56的八进制数
list((1,2,3))                    # 转换为表 list
tuple([2,3,4])                   # 转换为定值表 tuple
slice(5,2,-1)                    # 构建下标对象 slice
dict(a=1,b="hello",c=[1,2,3])    # 构建词典 dictionary
# print l[5:2:-1] 等价于 print l[slice(5,2,-1)]

序列操作

all([True, 1, "hello!"])         # 是否所有的元素都相当于True值
any(["", 0, False, [], None])    # 是否有任意一个元素相当于True值
sorted([1,5,3])                  # 返回正序的序列，也就是[1,3,5]
reversed([1,5,3])                # 返回反序的序列，也就是[3,5,1]
```

```
类，对象，属性

hasattr(me, "test")               # 检查me对象是否有test属性
getattr(me, "test")               # 返回test属性
setattr(me, "test", new_test)     # 将test属性设置为new_test
delattr(me, "test")               # 删除test属性
isinstance(me, Me)                # me对象是否为Me类生成的对象 (一个instance)
issubclass(Me, object)            # Me类是否为object类的子类

编译，执行

repr(me)                          # 返回对象的字符串表达
compile("print('Hello')",'test.py','exec')  # 编译字符串成为code对象
eval("1 + 1")                     # 解释字符串表达式。参数也可以是compile()返回的code对象
exec("print('Hello')")            # 解释并执行字符串，print('Hello')。参数也可以是compile()返回的code对象

其他

input("Please input:")            # 等待输入
globals()                         # 返回全局命名空间，比如全局变量名，全局函数名
locals()                          # 返回局部命名空间
```

### 字符串格式化

```
模板
a = "I'm %s. I'm %d year old" % ('Vamei', 99)
a = "I'm %+10x age" % 10
a = "I'm %(name)s. I'm %(age)d year old" % {'name':'Vamei', 'age':99}

格式符为真实值预留位置，并控制显示的格式。
格式符可以包含有一个类型码，用以控制显示的类型，如下:
%s    字符串 (采用str()的显示)
%r    字符串 (采用repr()的显示)
%c    单个字符
%b %o %d %i %x  分别为二、八、十、十、十六进制整数
%e    指数 (基底写为e)
%E    指数 (基底写为E)
%f %F 浮点数
%g    指数(e)或浮点数 (根据显示长度)
%G    指数(E)或浮点数 (根据显示长度)
%%    字符"%"

可以用如下的方式，对格式进行进一步的控制：

%[(name)][flags][width].[precision]typecode

(name)为命名
width表示显示宽度
precision表示小数点后精度
flags可以有+,-,' '或0
+表示右对齐(默认右对齐);
-表示左对齐;
' '为一个空格,表示在正数的左侧填充一个空格,从而与负数对齐;
0表示使用0填充.
```

## Python深入
### multi-paradigm

```
Python还是一个多范式语言(multi-paradigm),你不仅可以使用面向对象的方式来编写程序，
还可以用面向过程的方式来编写相同功能的程序(还有函数式、声明式等，我们暂不深入)。
Python的多范式依赖于Python对象中的特殊方法(special method), 特殊方法名的前后各有两个下划线。
特殊方法又被成为魔法方法(magic method)，定义了许多Python语法和表达方式，
```

### 上下文管理器 with...as...

任何定义了\_\_enter\_\_()和\_\_exit__()方法的对象都可以用于上下文管理器。  
文件对象f是内置对象，所以f自动带有这两个特殊方法，不需要自定义。

```python
class VOW(object):
    def __init__(self, text):
        self.text = text
    def __enter__(self):
        self.text = "I say: " + self.text    # add prefix
        return self                          # note: return an object
    def __exit__(self,exc_type,exc_value,traceback):
        self.text = self.text + "!"          # add suffix
        
# __exit__()中有四个参数,当程序块中出现异常(exception),
# __exit__()的参数中exc_type, exc_value, traceback用于描述异常。
# 我们可以根据这三个参数进行相应的处理, 如果正常运行结束，这三个参数都是None。
        
with VOW("I'm fine") as myvow:
    print(myvow.text) #I say: I'm fine
    
print(myvow.text) #I say: I'm fine!

#####################################################
# 上下文管理器(with EXPR as VAR)大致相当于如下流程 :

VAR = EXPR
VAR = VAR.__enter__()
try:
    BLOCK
finally:
    VAR.__exit__()
```

### 属性&特性

```python
# 对象的属性可能来自于其类定义，叫做类属性(class attribute),
# 对象的属性还可能是该对象实例定义的，叫做对象属性(object attribute)。
# 对象的属性储存在对象的__dict__属性中: jim.__dict__['age']=3 等价于 jim.age=5
# __dict__分层存储属性,每一层的__dict__只存储该层新增的属性,子类不需要重复存储父类中的属性。

# 特性(property)是特殊的属性, 特性使用内置函数property()来创建。
# property()最多可以加载四个参数。前三个参数为函数，分别用于处理查询特性、修改特性、删除特性。
# 最后一个参数为特性的文档，可以为一个字符串，起说明作用。

class num(object):
    def __init__(self, value):
        self.value = value
    def getNeg(self):
        return -self.value
    def setNeg(self, value):
        self.value = -value
    def delNeg(self):
        print("value also deleted")
        del self.value
    neg = property(getNeg, setNeg, delNeg, "I'm negative")
    
    def __getattr__(self, name):
    	# 每个特性需要有自己的处理函数，而__getattr__可以将所有的
    	# 即时生成属性放在同一个函数中处理。
        if name == 'attr_name':
            if self.value > 1.0: return True
            else: return False
        else: raise AttributeError(name)
    
x = num(1.1)
print x.neg #get
x.neg = -22 #set
del x.neg   #del

# 我们可以用__getattr__(self, name)来查询即时生成的属性。
# 当我们查询一个属性时，如果通过__dict__方法无法找到该属性，
# 那么Python会调用对象的__getattr__方法，来即时生成该属性。

# Python中还有一个__getattribute__特殊方法，用于查询任意属性。
# __getattr__只能用来查询不在__dict__系统中的属性。
# __setattr__(self, name, value)和__delattr__(self, name)可用于修改和删除属性。
# 它们的应用面更广，可用于任意属性。

```
### 闭包

> 闭包(closure)是函数式编程的重要的语法结构。函数式编程是一种编程范式 (而面向过程编程和面向对象编程也都是编程范式)。

> 一个函数和它的环境变量合在一起，就构成了一个闭包(closure)。在Python中，所谓的闭包是一个包含有环境变量取值的函数对象。环境变量取值被保存在函数对象的__closure__属性中,比如下面的代码：

```python
def line_conf():
    b = 15
    def line(x):
        return 2*x+b
    return line       # return a function object

b = 5
my_line = line_conf()
print(my_line.__closure__)
print(my_line.__closure__[0].cell_contents)

# __closure__里包含了一个元组(tuple)。这个元组中的每个元素是cell类型的对象。
# 我们看到第一个cell包含的就是整数15，也就是我们创建闭包时的环境变量b的取值。
```

### [装饰器(decorator)](http://www.cnblogs.com/vamei/archive/2013/02/16/2820212.html)

> 装饰器可以对一个函数、方法或者类进行加工

```python
# 修饰函数

def decorator(F):
    def new_F(a, b):
        print("input", a, b)
        return F(a, b)
    return new_F

@decorator
def square_sum(a, b):
    return a**2 + b**2
    
# 含参的装饰器

# a new wrapper layer
def pre_str(pre=''):
    # old decorator
    def decorator(F):
        def new_F(a, b):
            print(pre + "input", a, b)
            return F(a, b)
        return new_F
    return decorator

@pre_str('^_^')
def square_sum(a, b):
    return a**2 + b**2
    
# 装饰类
# Python 2.6以后, 装饰器可以接收一个类, 并返回一个类, 从而起到加工类的效果

def decorator(aClass):
    class newClass:
        def __init__(self, age):
            self.total_display   = 0
            self.wrapped         = aClass(age)
        def display(self):
            self.total_display += 1
            print("total display", self.total_display)
            self.wrapped.display()
    return newClass

@decorator
class Bird:
    def __init__(self, age):
        self.age = age
    def display(self):
        print("My age is",self.age)    
```

### [linux下python调试](http://blog.csdn.net/linda1000/article/details/11031771) 
#### pdb  
python -m pdb myscript.py # 这样调用myscript.py的话断点就是程序的执行第一行之前  
在Python交互环境中启用调试  
```
>>> import pdb
>>> import mymodule
>>> pdb.run(‘mymodule.test()’)
```
比较常用的用法就是在程序中间插入pdb，相对于在一般IDE里面打上断点然后启动debug  
```python
if __name__ == "__main__":  
    a = 1  
    import pdb  
    pdb.set_trace() # 运行后，到了pdb.set_trace()就会定下来，就可以看到提示符(Pdb)了
    b = a+2  
    print (b)  
```
```
(Pdb) command
一些常用指令：
h(elp) [comman]  #打印可用指令及帮助信息
r(eturn)  #运行代码直到下一个断点或当前函数返回
b(reak) [[filename:]lineno | function[, condition]]  #指定文件某行或函数体来设置断点
l(ist) [first[, last]]  #查看指定代码段
n(ext)  #执行下一行
s(tep) #执行下一行，若为函数则进入函数体
p  #打印某个变量
a(rgs)  #打印当前函数的参数
w(here)  #打印堆栈信息
d(own)  #移至下层堆栈
u(p)  #移至上层堆栈
j(ump)  #跳转到指定行
c(continue)  #继续执行
disable [bpnumber [bpnumber]] #失效断点
enable[bpnumber [bpnumber]]  #启用断点
cl(ear) [filename:lineno | bpnumber [bpnumber]] #删除断点
q(uit)/exit  #中止调试并退出
```

#### pudb
安装:pip install pudb  
本地调试: 在需要加断点的地方直接加入代码`import pudb; pu.db`, 或者使用pudb直接运行脚本，例如：pudb my-script.py  
远程调试:  
```
在需要加断点的地方直接加入以下代码
from pudb.remote import set_trace;set_trace(term_size=(153, 39))
运行到上述代码，程序会暂停并显示：pudb:6899: Please telnet into 127.0.0.1 6899
在另外一个命令行窗口，运行：
telnet 127.0.0.1 6899
使用pudb 16.1版本，centos 7下面验证过本地调试openstack Mitaka 版本l3 agent的主线程和工作线程，
在ubuntu 14.04下面可以调试主线程，工作线程需要用远程调试。
```

Openstack多线程调试设置:  
```
Python中的并发，在Python中，并发有三种，分别是：
进程：Python中一般使用multiprocessing/subprocess来实现
线程：threading/thread是Python中用来实现多线程的模块
协程(Coroutines)：Python中用于处理协程的模块比较多，有eventlet、Twisted、Tulip、asyncio等

eventlet是一个用来处理和网络相关的python库函数，而且可以通过协程来实现并发，
在eventlet里，把“协程”叫做greenthread(绿色线程)。
所谓并发，就是开启了多个greenthread，并且对这些greenthread进行管理，以实现非阻塞式的I/O

在neutrone的源代码common/eventlet_utils.py中，
 if os.name == 'nt':
	eventlet.monkey_patch(os=False, thread=False)
else:
	eventlet.monkey_patch()
这种设置中，直接使用pudb，断点是不会生效的。
必须改成：eventlet.monkey_patch(all=False, socket=True, time=True, thread=True)
```


### [Python的内存管理](http://www.cnblogs.com/vamei/p/3232088.html)

> Python作为一种动态类型的语言，其对象和引用分离。这与曾经的面向过程语言有很大的区别。为了有效的释放内存，Python内置了垃圾回收的支持。Python采取了一种相对简单的垃圾回收机制，即引用计数，并因此需要解决孤立引用环的问题。

- 垃圾回收: 垃圾回收时，Python不能进行其它的任务。频繁的垃圾回收将大大降低Python的工作效率。如果内存中的对象不多，就没有必要总启动垃圾回收。所以，Python只会在特定条件下，自动启动垃圾回收。当Python运行时，会记录其中分配对象(object allocation)和取消分配对象(object deallocation)的次数。当两者的差值高于某个阈值时，垃圾回收才会启动。
- 分代回收: 这一策略的基本假设是，存活时间越久的对象，越不可能在后面的程序中变成垃圾。我们的程序往往会产生大量的对象，许多对象很快产生和消失，但也有一些对象长期被使用。出于信任和效率，对于这样一些“长寿”对象，我们相信它们的用处，所以减少在垃圾回收中扫描它们的频率。
- 孤立的引用环: 为了回收这样的引用环，Python复制每个对象的引用计数，可以记为gc\_ref。假设，每个对象i，该计数为gc\_ref\_i。Python会遍历所有的对象i。对于每个对象i引用的对象j，将相应的gc\_ref\_j减1。在结束遍历后，gc\_ref不为0的对象，和这些对象引用的对象，以及继续更下游引用的对象，需要被保留。而其它的对象则被垃圾回收。


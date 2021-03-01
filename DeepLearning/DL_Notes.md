# Python

## 基础

### List

Python内置的一种数据类型是列表：list。list是一种有序的集合，可以随时添加和删除其中的元素。

比如，列出班里所有同学的名字，就可以用一个list表示：

```python
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates
['Michael', 'Bob', 'Tracy']
```

### tuple

另一种有序列表叫元组：tuple。tuple和list非常类似，但是**tuple一旦初始化就不能修改**，比如同样是列出同学的名字：

```
>>> classmates = ('Michael', 'Bob', 'Tracy')
```

**一个元素的tuple**

要定义一个只有1个元素的tuple，如果你这么定义：

```
>>> t = (1)
>>> t
1
```

定义的不是tuple，是`1`这个数！这是因为括号`()`既可以表示tuple，又可以表示数学公式中的小括号，这就产生了歧义，因此，Python规定，这种情况下，按小括号进行计算，计算结果自然是`1`。

所以，只有1个元素的tuple定义时必须加一个逗号`,`，来消除歧义：

```
>>> t = (1,)
>>> t
(1,)
```

Python在显示只有1个元素的tuple时，也会加一个逗号`,`，以免你误解成数学计算意义上的括号。



## 函数

在Python中，定义一个函数要使用`def`语句，依次写出函数名、括号、括号中的参数和冒号`:`，然后，在缩进块中编写函数体，函数的返回值用`return`语句返回。

~~~python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
~~~



### 空函数

如果想定义一个什么事也不做的空函数，可以用`pass`语句：

```
def nop():
    pass
```

`pass`语句什么都不做，那有什么用？实际上`pass`可以用来作为占位符，比如现在还没想好怎么写函数的代码，就可以先放一个`pass`，让代码能运行起来。

`pass`还可以用在其他语句里，比如：

```
if age >= 18:
    pass
```

缺少了`pass`，代码运行就会有语法错误。

### 返回多个值

函数可以返回多个值吗？答案是肯定的。

比如在游戏中经常需要从一个点移动到另一个点，给出坐标、位移和角度，就可以计算出新的坐标：

```
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```

`import math`语句表示导入`math`包，并允许后续代码引用`math`包里的`sin`、`cos`等函数。

然后，我们就可以同时获得返回值：

```
>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
151.96152422706632 70.0
```

但其实这只是一种假象，Python函数返回的仍然是单一值：

```
>>> r = move(100, 100, 60, math.pi / 6)
>>> print(r)
(151.96152422706632, 70.0)
```

原来返回值是一个tuple！但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便。

### 递归函数

递归函数的优点是定义简单，逻辑清晰。理论上，所有的递归函数都可以写成循环的方式，但循环的逻辑不如递归清晰。

例子：

~~~python
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
~~~

如果我们计算`fact(5)`，可以根据函数定义看到计算过程如下：

```ascii
===> fact(5)
===> 5 * fact(4)
===> 5 * (4 * fact(3))
===> 5 * (4 * (3 * fact(2)))
===> 5 * (4 * (3 * (2 * fact(1))))
===> 5 * (4 * (3 * (2 * 1)))
===> 5 * (4 * (3 * 2))
===> 5 * (4 * 6)
===> 5 * 24
===> 120
```

**使用递归函数需要注意防止栈溢出**。在计算机中，**函数调用是通过栈（stack）这种数据结构实现的**，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。

解决递归调用栈溢出的方法是通过**尾递归**优化，事实上尾递归和循环的效果是一样的，所以，把循环看成是一种特殊的尾递归函数也是可以的。

**尾递归**是指，在函数返回的时候，调用自身本身，**并且，return语句不能包含表达式**。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。

```python
def fact(n):
    return fact_iter(n, 1)

def fact_iter(num, product):
    if num == 1:
        return product
    return fact_iter(num - 1, num * product)
```

可以看到，`return fact_iter(num - 1, num * product)`仅返回递归函数本身，`num - 1`和`num * product`在函数调用前就会被计算，不影响函数调用。

`fact(5)`对应的`fact_iter(5, 1)`的调用如下：

```
===> fact_iter(5, 1)
===> fact_iter(4, 5)
===> fact_iter(3, 20)
===> fact_iter(2, 60)
===> fact_iter(1, 120)
===> 120
```



尾递归调用时，如果做了优化，栈不会增长，因此，无论多少次调用也不会导致栈溢出。

遗憾的是，大多数编程语言没有针对尾递归做优化，Python解释器也没有做优化，所以，即使把上面的`fact(n)`函数改成尾递归方式，也会导致栈溢出。

### 小结

使用递归函数的优点是逻辑简单清晰，缺点是过深的调用会导致栈溢出。

针对尾递归优化的语言可以通过尾递归防止栈溢出。尾递归事实上和循环是等价的，没有循环语句的编程语言只能通过尾递归实现循环。

Python标准的解释器没有针对尾递归做优化，任何递归函数都存在栈溢出的问题。



## 高级特性

### 切片

Python提供了切片（Slice）操作符，能大大简化这种操作。

对应上面的问题，取前3个元素，用一行代码就可以完成切片：

```
>>> L[0:3]
['Michael', 'Sarah', 'Tracy']
```

`L[0:3]`表示，从索引`0`开始取，直到索引`3`为止，但不包括索引`3`。即索引`0`，`1`，`2`，正好是3个元素。

如果第一个索引是`0`，还可以省略：

```
>>> L[:3]
['Michael', 'Sarah', 'Tracy']
```

也可以从索引1开始，取出2个元素出来：

```
>>> L[1:3]
['Sarah', 'Tracy']
```

类似的，既然Python支持`L[-1]`取倒数第一个元素，那么它同样支持倒数切片，试试：

```
>>> L[-2:]
['Bob', 'Jack']
>>> L[-2:-1]
['Bob']
```

记住倒数第一个元素的索引是`-1`。

### 列表生成式

列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。

举个例子，要生成list `[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`可以用`list(range(1, 11))`：

```python
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

但如果要生成`[1x1, 2x2, 3x3, ..., 10x10]`怎么做？方法一是循环：

```python
>>> L = []
>>> for x in range(1, 11):
...    L.append(x * x)
...
>>> L
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

但是循环太繁琐，而列表生成式则可以用一行语句代替循环生成上面的list：

```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

写列表生成式时，把要生成的元素`x * x`放到前面，后面跟`for`循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。

for循环后面还可以加上if判断，这样我们就可以筛选出仅偶数的平方：

```python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

还可以使用两层循环，可以生成全排列：

```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
```

三层和三层以上的循环就很少用到了。

运用列表生成式，可以写出非常简洁的代码。例如，列出当前目录下的所有文件和目录名，可以通过一行代码实现：

```python
>>> import os # 导入os模块，模块的概念后面讲到
>>> [d for d in os.listdir('.')] # os.listdir可以列出文件和目录
['.emacs.d', '.ssh', '.Trash', 'Adlm', 'Applications', 'Desktop', 'Documents', 'Downloads', 'Library', 'Movies', 'Music', 'Pictures', 'Public', 'VirtualBox VMs', 'Workspace', 'XCode']
```

### 生成器

如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。

要创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的`[]`改成`()`，就创建了一个generator：

```
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```

创建`L`和`g`的区别仅在于最外层的`[]`和`()`，`L`是一个list，而`g`是一个generator。

我们可以直接打印出list的每一个元素，但我们怎么打印出generator的每一个元素呢？

如果要一个一个打印出来，可以通过`next()`函数获得generator的下一个返回值：

```
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
>>> next(g)
9
>>> next(g)
16
>>> next(g)
25
>>> next(g)
36
>>> next(g)
49
>>> next(g)
64
>>> next(g)
81
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

我们讲过，generator保存的是算法，每次调用`next(g)`，就计算出`g`的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出`StopIteration`的错误。

当然，上面这种不断调用`next(g)`实在是太变态了，正确的方法是使用`for`循环，因为generator也是可迭代对象：

```
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
... 
0
1
4
9
16
25
36
49
64
81
```

所以，我们创建了一个generator后，基本上永远不会调用`next()`，而是通过`for`循环来迭代它，并且不需要关心`StopIteration`的错误。

generator非常强大。如果推算的算法比较复杂，用类似列表生成式的`for`循环无法实现的时候，还可以用函数来实现。

比如，著名的斐波拉契数列（Fibonacci），除第一个和第二个数外，任意一个数都可由前两个数相加得到：

1, 1, 2, 3, 5, 8, 13, 21, 34, ...

斐波拉契数列用列表生成式写不出来，但是，用函数把它打印出来却很容易：

```
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print(b)
        a, b = b, a + b
        n = n + 1
    return 'done'
```

*注意*，赋值语句：

```
a, b = b, a + b
```

相当于：

```
t = (b, a + b) # t是一个tuple
a = t[0]
b = t[1]
```

但不必显式写出临时变量t就可以赋值。

上面的函数可以输出斐波那契数列的前N个数：

```
>>> fib(6)
1
1
2
3
5
8
'done'
```

仔细观察，可以看出，`fib`函数实际上是定义了斐波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似generator。

也就是说，上面的函数和generator仅一步之遥。要把`fib`函数变成generator，只需要把`print(b)`改为`yield b`就可以了

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

这就是定义generator的另一种方法。如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个**generator**：

```
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```

这里，最难理解的就是generator和函数的执行流程不一样。函数是顺序执行，遇到`return`语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

举个简单的例子，定义一个generator，依次返回数字1，3，5：

```python
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)
```

调用该generator时，首先要生成一个generator对象，然后用`next()`函数不断获得下一个返回值：

```
>>> o = odd()
>>> next(o)
step 1
1
>>> next(o)
step 2
3
>>> next(o)
step 3
5
>>> next(o)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

可以看到，`odd`不是普通函数，而是generator，在执行过程中，遇到`yield`就中断，下次又继续执行。执行3次`yield`后，已经没有`yield`可以执行了，所以，第4次调用`next(o)`就报错。

回到`fib`的例子，我们在循环过程中不断调用`yield`，就会不断中断。当然要给循环设置一个条件来退出循环，不然就会产生一个无限数列出来。

### 迭代器

我们已经知道，可以直接作用于`for`循环的数据类型有以下几种：

一类是集合数据类型，如`list`、`tuple`、`dict`、`set`、`str`等；

一类是`generator`，包括生成器和带`yield`的generator function。

这些可以直接作用于`for`循环的对象统称为**可迭代对象**：`Iterable`。

可以使用`isinstance()`判断一个对象是否是`Iterable`对象：

```python
>>> from collections.abc import Iterable
>>> isinstance([], Iterable)
True
>>> isinstance({}, Iterable)
True
>>> isinstance('abc', Iterable)
True
>>> isinstance((x for x in range(10)), Iterable)
True
>>> isinstance(100, Iterable)
False
```

而生成器不但可以作用于`for`循环，还可以被`next()`函数不断调用并返回下一个值，直到最后抛出`StopIteration`错误表示无法继续返回下一个值了。

可以被`next()`函数调用并不断返回下一个值的对象称为**迭代器**：`Iterator`。

可以使用`isinstance()`判断一个对象是否是`Iterator`对象：

```python
>>> from collections.abc import Iterator
>>> isinstance((x for x in range(10)), Iterator)
True
>>> isinstance([], Iterator)
False
>>> isinstance({}, Iterator)
False
>>> isinstance('abc', Iterator)
False
```

生成器都是`Iterator`对象，但`list`、`dict`、`str`虽然是`Iterable`，却不是`Iterator`。

把`list`、`dict`、`str`等`Iterable`变成`Iterator`可以使用`iter()`函数：

```python
>>> isinstance(iter([]), Iterator)
True
>>> isinstance(iter('abc'), Iterator)
True
```

你可能会问，为什么`list`、`dict`、`str`等数据类型不是`Iterator`？

这是因为Python的`Iterator`对象表示的是一个数据流，Iterator对象可以被`next()`函数调用并不断返回下一个数据，直到没有数据时抛出`StopIteration`错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过`next()`函数实现按需计算下一个数据，所以`Iterator`的计算是惰性的，只有在需要返回下一个数据时它才会计算。

`Iterator`甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。



## 函数式编程

函数是Python内建支持的一种封装，我们通过把大段代码拆成函数，通过一层一层的函数调用，就可以把复杂任务分解成简单的任务，这种分解可以称之为面向过程的程序设计。函数就是面向过程的程序设计的基本单元。

而函数式编程（请注意多了一个“式”字）——Functional Programming，虽然也可以归结到面向过程的程序设计，但其思想更接近数学计算。

我们首先要搞明白计算机（Computer）和计算（Compute）的概念。

在计算机的层次上，CPU执行的是加减乘除的指令代码，以及各种条件判断和跳转指令，所以，汇编语言是最贴近计算机的语言。

而计算则指数学意义上的计算，越是抽象的计算，离计算机硬件越远。

对应到编程语言，就是越低级的语言，越贴近计算机，抽象程度低，执行效率高，比如C语言；越高级的语言，越贴近计算，抽象程度高，执行效率低，比如Lisp语言。

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。

函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

Python对函数式编程提供部分支持。由于Python允许使用变量，因此，Python不是纯函数式编程语言。



### 高阶函数

函数本身也可以赋值给变量，即：变量可以指向函数。

**变量可以指向函数**

**函数名也是变量**

**传入函数**

既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

一个最简单的高阶函数：

```python
def add(x, y, f):
    return f(x) + f(y)
```



#### map/reduce

Python内建了`map()`和`reduce()`函数。

如果你读过Google的那篇大名鼎鼎的论文“[MapReduce: Simplified Data Processing on Large Clusters](http://research.google.com/archive/mapreduce.html)”，你就能大概明白map/reduce的概念。

我们先看map。`map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。

举例说明，比如我们有一个函数f(x)=x^2，要把这个函数作用在一个list `[1, 2, 3, 4, 5, 6, 7, 8, 9]`上，就可以用`map()`实现如下：

```ascii
          f(x) = x * x

                  │
                  │
  ┌───┬───┬───┬───┼───┬───┬───┬───┐
  │   │   │   │   │   │   │   │   │
  ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼

[ 1   2   3   4   5   6   7   8   9 ]

  │   │   │   │   │   │   │   │   │
  │   │   │   │   │   │   │   │   │
  ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼   ▼

[ 1   4   9  16  25  36  49  64  81 ]
```

现在，我们用Python代码实现：

```
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

`map()`传入的第一个参数是`f`，即函数对象本身。由于结果`r`是一个`Iterator`，`Iterator`是惰性序列，因此通过`list()`函数让它把整个序列都计算出来并返回一个list。

你可能会想，不需要`map()`函数，写一个循环，也可以计算出结果：

```
L = []
for n in [1, 2, 3, 4, 5, 6, 7, 8, 9]:
    L.append(f(n))
print(L)
```

的确可以，但是，从上面的循环代码，能一眼看明白“把f(x)作用在list的每一个元素并把结果生成一个新的list”吗？

所以，`map()`作为高阶函数，事实上它把运算规则抽象了，因此，我们不但可以计算简单的f(x)=x2，还可以计算任意复杂的函数，比如，把这个list所有数字转为字符串：

```
>>> list(map(str, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
['1', '2', '3', '4', '5', '6', '7', '8', '9']
```

只需要一行代码。

**reduce**

再看`reduce`的用法。`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算，其效果就是：

```
reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
```

比方说对一个序列求和，就可以用`reduce`实现：

```
>>> from functools import reduce
>>> def add(x, y):
...     return x + y
...
>>> reduce(add, [1, 3, 5, 7, 9])
25
```

当然求和运算可以直接用Python内建函数`sum()`，没必要动用`reduce`。

但是如果要把序列`[1, 3, 5, 7, 9]`变换成整数`13579`，`reduce`就可以派上用场：

```
>>> from functools import reduce
>>> def fn(x, y):
...     return x * 10 + y
...
>>> reduce(fn, [1, 3, 5, 7, 9])
13579
```

这个例子本身没多大用处，但是，如果考虑到字符串`str`也是一个序列，对上面的例子稍加改动，配合`map()`，我们就可以写出把`str`转换为`int`的函数：

```
>>> from functools import reduce
>>> def fn(x, y):
...     return x * 10 + y
...
>>> def char2num(s):
...     digits = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
...     return digits[s]
...
>>> reduce(fn, map(char2num, '13579'))
13579
```

整理成一个`str2int`的函数就是：

```
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def str2int(s):
    def fn(x, y):
        return x * 10 + y
    def char2num(s):
        return DIGITS[s]
    return reduce(fn, map(char2num, s))
```

还可以用lambda函数进一步简化成：

```
from functools import reduce

DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}

def char2num(s):
    return DIGITS[s]

def str2int(s):
    return reduce(lambda x, y: x * 10 + y, map(char2num, s))
```

也就是说，假设Python没有提供`int()`函数，你完全可以自己写一个把字符串转化为整数的函数，而且只需要几行代码！

lambda函数的用法在后面介绍。



```
from functools import reduce

def str2float(s):
    str_list = s.split('.')
    def fn(x, y):
        return int(x) * 10 + int(y)
    part1 = reduce(fn, str_list[0])
    part2 = reduce(fn, str_list[1]) / (10 ** len(str_list[1]))
    return float(part1+part2)
```

#### filter

Python内建的`filter()`函数用于过滤序列。

和`map()`类似，`filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。

例如，在一个list中，删掉偶数，只保留奇数，可以这么写：

```
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
# 结果: [1, 5, 9, 15]
```

Python strip() 方法用于移除字符串头尾指定的字符（默认为空格或换行符）或字符序列。

~~~
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
str = "00000003210Runoob01230000000"; 
print str.strip( '0' );  # 去除首尾字符 0
 
str2 = "   Runoob      ";   # 去除首尾空格
print str2.strip();
~~~

输出结果：

~~~
3210Runoob0123
Runoob
~~~



把一个序列中的空字符串删掉，可以这么写：

```
def not_empty(s):
    return s and s.strip()

list(filter(not_empty, ['A', '', 'B', None, 'C', '  ']))
# 结果: ['A', 'B', 'C']
```

可见用`filter()`这个高阶函数，关键在于正确实现一个“筛选”函数。

注意到`filter()`函数返回的是一个`Iterator`，也就是一个惰性序列，所以要强迫`filter()`完成计算结果，需要用`list()`函数获得所有结果并返回list。



### 匿名函数

当我们在传入函数时，有些时候，不需要显式地定义函数，直接传入匿名函数更方便。

在Python中，对匿名函数提供了有限支持。还是以`map()`函数为例，计算f(x)=x2时，除了定义一个`f(x)`的函数外，还可以直接传入匿名函数：

```
>>> list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```

通过对比可以看出，匿名函数`lambda x: x * x`实际上就是：

```
def f(x):
    return x * x
```

关键字`lambda`表示匿名函数，冒号前面的`x`表示函数参数。

匿名函数有个限制，就是只能有一个表达式，不用写`return`，返回值就是该表达式的结果。

### 装饰器

由于函数也是一个对象，而且函数对象可以被赋值给变量，所以，通过变量也能调用该函数。

```
>>> def now():
...     print('2015-3-25')
...
>>> f = now
>>> f()
2015-3-25
```

函数对象有一个`__name__`属性，可以拿到函数的名字：

```
>>> now.__name__
'now'
>>> f.__name__
'now'
```

现在，假设我们要增强`now()`函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改`now()`函数的定义，这种在代码运行期间动态增加功能的方式，称之为“**装饰器**”（Decorator）。

本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：

```
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

观察上面的`log`，因为它是一个decorator，所以接受一个函数作为参数，并返回一个函数。我们要借助Python的@语法，把decorator置于函数的定义处：

```
@log
def now():
    print('2015-3-25')
```

### Argparse

argparse模块是 Python 标准库中推荐的命令行解析模块。

[`argparse`](https://docs.python.org/zh-cn/3.6/library/argparse.html#module-argparse) 模块可以让人轻松编写用户友好的命令行接口。程序定义它需要的参数，然后 [`argparse`](https://docs.python.org/zh-cn/3.6/library/argparse.html#module-argparse) 将弄清如何从 [`sys.argv`](https://docs.python.org/zh-cn/3.6/library/sys.html#sys.argv) 解析出那些参数。 [`argparse`](https://docs.python.org/zh-cn/3.6/library/argparse.html#module-argparse) 模块还会自动生成帮助和使用手册，并在用户给程序传入无效参数时报出错误信息。

~~~
注解 还有另外两个模块可以完成同样的任务，称为 getopt (对应于 C 语言中的 getopt() 函数) 和被弃用的 optparse。还要注意 argparse 是基于 optparse 的，因此用法与其非常相似。
~~~









# MMSeg配置

问题1：pip安装报错

~~~
root@7a422d92f36a:~# pip3 install mmcv-full==latest+torch1.5.0+cu102 -f https://download.openmmlab.com/mmcv/dist/index.html
Traceback (most recent call last):
  File "/usr/bin/pip3", line 11, in <module>
    sys.exit(main())
  File "/usr/lib/python3/dist-packages/pip/__init__.py", line 215, in main
    locale.setlocale(locale.LC_ALL, '')
  File "/usr/lib/python3.6/locale.py", line 598, in setlocale
    return _setlocale(category, locale)
locale.Error: unsupported locale setting
~~~

Github[问题答案](https://github.com/bakwc/JamSpell/issues/17)，具体操作

~~~
apt-get install locales
locale-gen en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
~~~



~~~
pip3 install mmcv-full==1.2.5+torch1.5.0+cu102 -f https://download.openmmlab.com/mmcv/dist/index.html
~~~



~~~
1.2.5+torch1.5.0+cu102
~~~





~~~
python demo/image_demo.py ${IMAGE_FILE} ${CONFIG_FILE} ${CHECKPOINT_FILE} [--device ${DEVICE_NAME}] [--palette-thr ${PALETTE}]
~~~

**运行image_demo.py**

~~~
python3 demo/image_demo.py demo/demo.png configs/pspnet/pspnet_r50-d8_512x1024_40k_cityscapes.py checkpoints/pspnet_r50-d8_512x1024_40k_cityscapes_20200605_003338-2966598c.pth --device cpu --palette cityscapes
~~~

运行**test.py**

~~~python
python3 tools/test.py configs/pspnet/pspnet_r50-d8_512x1024_40k_cityscapes.py checkpoints/pspnet_r50d8_512x1024_40k_cityscapes_20200605_0033382966598c.pth --show
~~~



# ML

## 正则化 Regularization

**正则化主要用于避免过拟合的产生和减少网络误差。**



正则化一般具有如下形式：

![[公式]](https://www.zhihu.com/equation?tex=J%28w%2Cb%29%3D+%5Cfrac%7B1%7D%7Bm%7D+%5Csum_%7Bi%3D1%7D%5E%7Bm%7DL%28f%28x%29%2Cy%29%2B%5Clambda+R%28f%29)

其中，第 1 项是**经验风险**，第 2 项是**正则项**， ![[公式]](https://www.zhihu.com/equation?tex=%CE%BB%E2%89%A50) 为调整两者之间关系的系数。

第 1 项的经验风险较小的模型可能较复杂（有多个非零参数），这时第 2 项的模型复杂度会较大。

常见的有正则项有 **L1 正则** 和 **L2 正则** ，其中 **L2 正则** 的控制过拟合的效果比 **L1 正则** 的好。

**正则化的作用是选择经验风险与模型复杂度同时较小的模型**。 ![[公式]](https://www.zhihu.com/equation?tex=%5E%7B%5B3%5D%7D)

常见的有正则项有 **L1 正则** 和 **L2 正则** 以及 **Dropout** ，其中 **L2 正则** 的控制过拟合的效果比 **L1 正则** 的好。



### 范数

为什么叫 L1 正则，有 L1、L2 正则 那么有没有 L3、L4 之类的呢？

首先我们补一补课， ![[公式]](https://www.zhihu.com/equation?tex=L_%7Bp%7D) 正则的 L 是指 ![[公式]](https://www.zhihu.com/equation?tex=L_%7Bp%7D) 范数，其定义为：

![[公式]](https://www.zhihu.com/equation?tex=L_%7B0%7D) 范数： ![[公式]](https://www.zhihu.com/equation?tex=%5Cleft+%5C%7C+w+%5Cright+%5C%7C_%7B0%7D+%3D+%5C%23%28i%29%5C+with+%5C+x_%7Bi%7D+%5Cneq+0) *（非零元素的个数）*

![[公式]](https://www.zhihu.com/equation?tex=L_%7B1%7D) 范数： ![[公式]](https://www.zhihu.com/equation?tex=%5Cleft+%5C%7C+w+%5Cright+%5C%7C_%7B1%7D+%3D+%5Csum_%7Bi+%3D+1%7D%5E%7Bd%7D%5Clvert+x_i%5Crvert) *（每个元素绝对值之和）*

![[公式]](https://www.zhihu.com/equation?tex=L_%7B2%7D) 范数： ![[公式]](https://www.zhihu.com/equation?tex=%5Cleft+%5C%7C+w+%5Cright+%5C%7C_%7B2%7D+%3D+%5CBigl%28%5Csum_%7Bi+%3D+1%7D%5E%7Bd%7D+x_i%5E2%5CBigr%29%5E%7B1%2F2%7D) *（欧氏距离）*

![[公式]](https://www.zhihu.com/equation?tex=L_%7Bp%7D) 范数： ![[公式]](https://www.zhihu.com/equation?tex=%5Cleft+%5C%7C+w+%5Cright+%5C%7C_%7Bp%7D+%3D+%5CBigl%28%5Csum_%7Bi+%3D+1%7D%5E%7Bd%7D+x_i%5Ep%5CBigr%29%5E%7B1%2Fp%7D)

在机器学习中，若使用了 ![[公式]](https://www.zhihu.com/equation?tex=%5ClVert+w%5CrVert_p) 作为正则项，我们则说该机器学习任务**引入了** ![[公式]](https://www.zhihu.com/equation?tex=L_%7Bp%7D) **正则项**。



![img](https://pic3.zhimg.com/80/v2-af9e02d00d9e37cfa0920583cab6d5b6_720w.jpg)



## 归一化 Normalization

归一化一般是将数据映射到指定的范围，用于去除不同维度数据的量纲以及量纲单位。

常见的映射范围有 [0, 1] 和 [-1, 1] ，最常见的归一化方法就是 **Min-Max 归一化**：

### Min-Max 归一化

![[公式]](https://www.zhihu.com/equation?tex=x_%7Bnew%7D%3D%5Cfrac%7Bx-x_%7Bmin%7D%7D%7Bx_%7Bmax%7D-x_%7Bmin%7D%7D)

举个例子，我们判断一个人的身体状况是否健康，那么我们会采集人体的很多指标，比如说：身高、体重、红细胞数量、白细胞数量等。

一个人身高 180cm，体重 70kg，白细胞计数 ![[公式]](https://www.zhihu.com/equation?tex=7.50%C3%9710%5E%7B9%7D%2FL) ，etc.

衡量两个人的状况时，白细胞计数就会起到主导作用从而**遮盖住其他的特征**，归一化后就不会有这样的问题。



## 标准化 Normalization

**在这里我们需要强调一下英文翻译的问题，在 Udacity 字幕组中对此进行了探讨：**

> 归一化和标准化的英文翻译是一致的，但是根据其用途（或公式）的不同去理解（或翻译）

下面我们将探讨最常见的标准化方法： **Z-Score 标准化**。

### Z-Score 标准化

![[公式]](https://www.zhihu.com/equation?tex=x_%7Bnew%7D%3D%5Cfrac%7Bx-%5Cmu+%7D%7B%5Csigma+%7D)

其中 ![[公式]](https://www.zhihu.com/equation?tex=%5Cmu) 是样本数据的**均值（mean）**， ![[公式]](https://www.zhihu.com/equation?tex=%5Csigma) 是样本数据的**标准差（std）**。

![img](https://pic3.zhimg.com/80/v2-ee0280ea470db277509e95efce1991f6_720w.jpg)



上图则是一个散点序列的标准化过程：原图->减去均值->除以标准差。

显而易见，变成了一个**均值为 0 ，方差为 1 的分布**，下图通过 Cost 函数让我们更好的理解标准化的作用。

![img](https://pic3.zhimg.com/80/v2-7f49cde4e78c482421e17721d2e0fc5e_720w.jpg)



机器学习的目标无非就是不断优化损失函数，使其值最小。在上图中， ![[公式]](https://www.zhihu.com/equation?tex=J%28w%2Cb%29) 就是我们要优化的目标函数

我们不难看出，**标准化后可以更加容易地得出最优参数** ![[公式]](https://www.zhihu.com/equation?tex=w) **和** ![[公式]](https://www.zhihu.com/equation?tex=b) **以及计算出** ![[公式]](https://www.zhihu.com/equation?tex=J%28w%2Cb%29) **的最小值，从而达到加速收敛的效果**





# DL





## PyTorch

### torch.randn()

**标准正态分布**

**torch.randn(\*sizes, out=None)** → Tensor

返回一个张量，包含了从标准正态分布（均值为0，方差为1，即高斯白噪声）中抽取的一组随机数。张量的形状由参数sizes定义。

**参数:**

- sizes (int...) - 整数序列，定义了输出张量的形状
- out (Tensor, optinal) - 结果张量

**例子：**

~~~python
torch.randn(2, 3)
0.5419 0.1594 -0.0413
-2.7937 0.9534 0.4561
[torch.FloatTensor of size 2x3]
~~~



### 梯度下降法

通过改变x的值，使f(x)的值最大化或最小化，f(x)——》**目标函数或准则**，当要对其进行最小化时，称为 **代价函数、损失函数或误差函数**。

- SGD: stochastic gradient descent 随机梯度下降法

![](https://pic1.zhimg.com/80/v2-b3f14a09ad27df9c66a3af208060f5d7_720w.jpg?source=1940ef5c)

- 批量梯度下降

  在每次更新时用1个样本，可以看到多了随机两个字，随机也就是说我们用样本中的一个例子来近似我所有的样本，来调整*θ*，因而随机梯度下降是会带来一定的问题，因为计算得到的并不是准确的一个梯度，**对于最优化问题，凸问题，**虽然不是每次迭代得到的损失函数都向着全局最优方向， 但是大的整体的方向是向全局最优解的，最终的结果往往是在全局最优解附近。但是相比于批量梯度，这样的方法更快，更快收敛，虽然不是全局最优，但很多时候是我们可以接受的，所以这个方法用的也比上面的多。

![](https://pic4.zhimg.com/v2-5809743fd06c4ff804753d29e4b83935_r.jpg?source=1940ef5c)

- mini-batch梯度下降

![](https://pic4.zhimg.com/80/v2-96181aa7bc39a7e5fb54cfdf2c42a7b9_720w.jpg?source=1940ef5c)





hook in PyTorch

第一个是register_hook，是针对Variable对象的，后面的两个：register_backward_hook和register_forward_hook是针对nn.Module这个对象的。



- 为什么用hook

  打个比方，有这么个函数， ![[公式]](https://www.zhihu.com/equation?tex=x%5Cin+%5Cmathbb%7BR%7D%5E2) ， ![[公式]](https://www.zhihu.com/equation?tex=y%3Dx%2B2) ， ![[公式]](https://www.zhihu.com/equation?tex=z+%3D+%5Cfrac%7B1%7D%7B2%7D%28y_1%5E2%2By_2%5E2%29) 你想通过梯度下降法求最小值。在PyTorch里面很容易实现，你只需要：

  ~~~python
  import torch
  from torch.autograd import Variable
  
  x = Variable(torch.randn(2, 1), requires_grad=True)
  y = x+2
  z = torch.mean(torch.pow(y, 2))
  lr = 1e-3 		#learning rate
  z.backward()    #x.grad里面存储了后向传播梯度下降的结果，梯度结果存储在z的tensor的 .grad属性中
  x.data -= lr*x.grad.data
  ~~~

- 但问题是，如果我想要求中间变量 ![[公式]](https://www.zhihu.com/equation?tex=y)的梯度，系统会返回错误。

  事实上，如果你输入：

  ```text
  type(y.grad)
  ```

  系统会告诉你：NoneType，因为pytorch开发时设计的是，对于中间变量，一旦它们完成了自身反传的使命，就会被释放掉。

- 因此，hook就派上用场了。简而言之，register_hook的作用是，当反传时，除了完成原有的反传，额外多完成一些任务。你可以定义一个中间变量的hook，将它的grad值打印出来，当然你也可以定义一个全局列表，将每次的grad值添加到里面去。

```python3
import torch
from torch.autograd import Variable

grad_list = []

def print_grad(grad):
    grad_list.append(grad)

x = Variable(torch.randn(2, 1), requires_grad=True)
y = x+2
z = torch.mean(torch.pow(y, 2))
lr = 1e-3
y.register_hook(print_grad)
z.backward()
x.data -= lr*x.grad.data
```

需要注意的是，register_hook函数接收的是一个函数，这个函数有如下的形式：

```text
hook(grad) -> Variable or None
```

也就是说，这个函数是拥有改变梯度值的威力的！



至于register_forward_hook和register_backward_hook的用法和这个大同小异。只不过对象从Variable改成了你自己定义的nn.Module。

当你训练一个网络，想要提取中间层的参数、或者特征图的时候，使用hook就能派上用场了。

### torch.autograd

`torch.autograd`是 PyTorch 的自动差分引擎，可为神经网络训练提供支持。autograd如何为神经网络训练提供帮助

#### variable

tensor是硬币的话，那Variable就是钱包，它记录着里面的钱的多少，和钱的流向

![img](https://pic4.zhimg.com/80/v2-ab0e724afb75f6ba74ebb99f27e3a2b7_720w.jpg)

variable是tensor的外包装，data属性存储着tensor数据，grad属性存储关于该变量的导数，creator是代表该变量的创造者。

autograd.Variable 是包的中央类, 它包裹着Tensor, 支持几乎所有Tensor的操作,并附加额外的属性, 在进行操作以后, 通过调用.backward()来计算梯度, 通过.data来访问原始raw data (tensor), 并将变量梯度累加到.grad

autograd.Variable是torch.autograd中很重要的类。它用来包装Tensor，将Tensor转换为Variable之后，可以装载梯度信息。

关于Variable的参数之一“requires_grad”和特性之一“grad_fn”有要注意的地方，都和该变量是否是人自己创建的有关：

1. requires_grad有两个值：True和False，True代表此变量处需要计算梯度，False代表不需要。变量的“requires_grad”值是Variable的一个参数，在建立Variable的时候就已经设定好，默认是False。
2. **grad_fn**的值可以得知该变量是否是一个计算结果，也就是说该变量是不是一个函数的输出值。若是，则grad_fn返回一个与该函数相关的对象，否则是None。
3. 

Variable 与 Function互连并建立一个非循环图，编码完整的计算历史。 每个变量都有一个 **.grad_fn** 属性，它引用了一个已经创建了 Variable 的操作,如加减乘除等（除了用户创建的变量代替creator is None 即第一个运算节点, .grad_fn为空)

![img](https://pic2.zhimg.com/80/v2-0a938a33a77b14171cb17f2bbafc0ba1_720w.jpg)

### DAG(directed acyclic graph)  有向无环图





### class torch.nn.Module

**torch.nn.Module**是所有网络的基类



~~~
optimizer = optim.SGD(net.parameters(), lr=0.001, momentum=0.9)
~~~

momentum——》学习衰减率

## Tips

### batchsize

中文翻译为批大小（批尺寸）。在深度学习中，一般采用SGD训练，即每次训练在训练集中取batchsize个样本训练



### iteration

中文翻译为迭代，1个iteration等于使用batchsize个样本训练一次；一个迭代 = 一个正向通过+一个反向通过



### epoch

迭代次数，1个epoch等于使用训练集中的全部样本训练一次；一个epoch = 所有训练样本的一个正向传递和一个反向传递

### eg:

训练集有1000个样本，batchsize=10，那么：训练完整个样本集需要：100次iteration，1次epoch。



## BN

batch normalize

### ICS

internal covatiate shift

在深层网络训练的过程中，由于网络中参数变化而引起内部结点数据分布发生变化的这一过程被称作Internal Covariate Shift。

这句话该怎么理解呢？我们同样以1.1中的图为例，我们定义每一层的线性变换为 ![[公式]](https://www.zhihu.com/equation?tex=Z%5E%7B%5Bl%5D%7D%3DW%5E%7B%5Bl%5D%7D%5Ctimes+input%2Bb%5E%7B%5Bl%5D%7D)，其中 ![[公式]](https://www.zhihu.com/equation?tex=l+) 代表层数；非线性变换为 ![[公式]](https://www.zhihu.com/equation?tex=A%5E%7B%5Bl%5D%7D%3Dg%5E%7B%5Bl%5D%7D%28Z%5E%7B%5Bl%5D%7D%29) ，其中 ![[公式]](https://www.zhihu.com/equation?tex=g%5E%7B%5Bl%5D%7D%28%5Ccdot%29) 为第 ![[公式]](https://www.zhihu.com/equation?tex=l) 层的激活函数。

随着梯度下降的进行，每一层的参数 ![[公式]](https://www.zhihu.com/equation?tex=W%5E%7B%5Bl%5D%7D) 与 ![[公式]](https://www.zhihu.com/equation?tex=b%5E%7B%5Bl%5D%7D) 都会被更新，那么 ![[公式]](https://www.zhihu.com/equation?tex=Z%5E%7B%5Bl%5D%7D) 的分布也就发生了改变，进而 ![[公式]](https://www.zhihu.com/equation?tex=A%5E%7B%5Bl%5D%7D) 也同样出现分布的改变。而 ![[公式]](https://www.zhihu.com/equation?tex=A%5E%7B%5Bl%5D%7D) 作为第 ![[公式]](https://www.zhihu.com/equation?tex=l%2B1) 层的输入，意味着 ![[公式]](https://www.zhihu.com/equation?tex=l%2B1) 层就需要去不停适应这种数据分布的变化，这一过程就被叫做Internal Covariate Shift。



**ICS带来的问题**

- **1）上层网络需要不停调整来适应输入数据分布的变化，导致网络学习速度的降低**
- **2）网络的训练过程容易陷入梯度饱和区，减缓网络收敛速度**



因此引入 **batch normalization**缓解**ICS**所带来的问题

### 白化 whitening

白化（Whitening）是机器学习里面常用的一种规范化数据分布的方法，主要是PCA白化与ZCA白化。白化是对输入数据分布进行变换，进而达到以下两个目的：

- **使得输入特征分布具有相同的均值与方差。**其中PCA白化保证了所有特征分布均值为0，方差为1；而ZCA白化则保证了所有特征分布均值为0，方差相同；
- **去除特征之间的相关性。**

通过白化操作，我们可以减缓ICS的问题，进而固定了每一层网络输入分布，加速网络训练过程的收敛（LeCun et al.,1998b；Wiesler&Ney,2011）。

#### Batch Normalization提出

既然白化可以解决这个问题，为什么我们还要提出别的解决办法？当然是现有的方法具有一定的缺陷，白化主要有以下两个问题：

- **白化过程计算成本太高，**并且在每一轮训练中的每一层我们都需要做如此高成本计算的白化操作；
- **白化过程由于改变了网络每一层的分布**，因而改变了网络层中本身数据的表达能力。底层网络学习到的参数信息会被白化操作丢失掉。

既然有了上面两个问题，那我们的解决思路就很简单，一方面，我们提出的normalization方法要能够简化计算过程；另一方面又需要经过规范化处理后让数据尽可能保留原始的表达能力。于是就有了简化+改进版的白化——Batch Normalization。

### batch normalization

#### BN思路

既然白化计算过程比较复杂，那我们就简化一点，比如我们可以尝试单独对每个特征进行normalizaiton就可以了，让每个特征都有均值为0，方差为1的分布就OK。

另一个问题，既然白化操作减弱了网络中每一层输入数据表达能力，那我就再加个线性变换操作，让这些数据再能够尽可能恢复本身的表达能力就好了。

因此，基于上面两个解决问题的思路，作者提出了Batch Normalization，下一部分来具体讲解这个算法步骤。

#### 算法

在深度学习中，由于采用full batch的训练方式对内存要求较大，且每一轮训练时间过长；我们一般都会采用对数据做划分，用mini-batch对网络进行训练。因此，Batch Normalization也就在mini-batch的基础上进行计算。







# Linux

## linux ln命令

ln -s 源文件 目标文件
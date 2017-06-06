# python函数参数传递

定义函数的时候，我们把参数的名字和位置确定下来，函数的接口定义就完成了。对于函数的调用者来说，只需要知道如何传递正确的参数，以及函数将返回什么样的值就够了，函数内部的复杂逻辑被封装起来，调用者无需了解。

Python的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数外，还可以使用默认参数、可变参数和关键字参数，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。

## 位置参数

Python中在调用函数时，需要给定和形参相同个数的实参并按顺序一一对应。

```
def fun(name, age, gender):
  print ('Name:',name,'，Age:',age,'，Gender:',gender)
  print ()

fun('Jack', 20, 'man') # call
#当我们调用fun函数时，必须传入有且仅有的3个参数,参数要按照顺序对应。
```

现在，如果我们要计算x3怎么办？可以再定义一个`power3`函数，但是如果要计算x4、x5……怎么办？我们不可能定义无限多个函数。

你也许想到了，可以把`power(x)`修改为`power(x, n)`，用来计算xn：

```
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
print (power(3,3))
```

## 默认参数

一是必选参数在前，默认参数在后，否则Python的解释器会报错（思考一下为什么默认参数不能放在必选参数前面）；

二是如何设置默认参数。

把年龄和城市设为默认参数：

```
def enroll(name, gender, age=6, city='Beijing'):
     print ('name:', name)
     print ('gender:', gender)
     print ('age:', age)
     print ('city:', city)
print (enroll('dd','nan'))
```

定义默认参数要牢记一点：默认参数必须指向不变对象！使用默认参数有什么好处？最大的好处是能降低调用函数的难度。

默认参数很有用，但使用不当，也会掉坑里。默认参数有个最大的坑，演示如下：

先定义一个函数，传入一个list，添加一个`END`再返回：

```
def add_end(L=[]):
    L.append('END')
    return L
print (add_end([1,2,3]))
print (add_end())
print (add_end())

#结果出乎意料
[1, 2, 3, 'END']
['END']
['END', 'END']
```

Python函数在定义的时候，默认参数`L`的值就被计算出来了，即`[]`，因为默认参数`L`也是一个变量，它指向对象`[]`，每次调用该函数，如果改变了`L`的内容，则下次调用时，默认参数的内容就变了，不再是函数定义时的`[]`了。

所以，定义默认参数要牢记一点：默认参数必须指向不变对象！

要修改上面的例子，我们可以用`None`这个不变对象来实现：

```
def add_end(L=None):
    if L is None:
        L = []
    L.append('END')
    return L
print (add_end())
print (add_end())

```



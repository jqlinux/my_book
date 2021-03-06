# python-json详解


##一、介绍

* JSON全称是Javascript Object Notation，很明显，是源于Javascript。此处暂可不深究这方面，知道这点即可。
* JSON是一种字符串，有一定特定的语法格式的字符串；
* JSON之所以定义这样的语法格式，目的在于方便数据的交换。即，一些数据，通过JSON这种格式，从一个地方，尤其是网络上，发送，传递到另外一个地方，然后使得接受者，也很容易理解相关的数据。

##二、语法

*  对象，即一个变量名，一个值，对应的写法是：{name:value}
*  列表，有多个元素是，写法是：[collection, collection]
*  余下的，按照正常逻辑理解即可，比如字符串是两个双引号""括起来的，数字是0到9等等。

##三、举例

```
{
     "firstName": "John",
     "lastName": "Smith",
     "male": true,
     "age": 25,
     "address": 
     {
         "streetAddress": "21 2nd Street",
         "city": "New York",
         "state": "NY",
         "postalCode": "10021"
     },
     "phoneNumber": 
     [
         {
           "type": "home",
           "number": "212 555-1234"
         },
         {
           "type": "fax",
           "number": "646 555-4567"
         }
     ]
 }
```
通过此例子，也就算很形象的知道了，JSON算是一个结构很清晰的，用于表示数据的一种格式。

##四、Python JSON

### Demjson

在使用 Python 编码或解码 JSON 数据前，我们需要先安装 JSON 模块。本教程我们会下载 [Demjson](http://deron.meranda.us/python/demjson/) 并安装：

```
$ tar xvfz demjson-1.6.tar.gz
$ cd demjson-1.6
$ python setup.py install

```

JSON 函数

|函数|	描述|
|-|-|
|encode	|将 Python 对象编码成 JSON 字符串|
|decode	|将已编码的 JSON 字符串解码为 Python 对象|


* 1、encode

Python encode() 函数用于将 Python 对象编码成 JSON 字符串。

语法

demjson.encode(self, obj, nest_level=0)

实例

以下实例将数组编码为 JSON 格式数据：

```
#!/usr/bin/python
import demjson

data = [ { 'a' : 1, 'b' : 2, 'c' : 3, 'd' : 4, 'e' : 5 } ]

json = demjson.encode(data)
print json
```


以上代码执行结果为：

```
[{"a":1,"b":2,"c":3,"d":4,"e":5}] 
```

*  2、decode

Python 可以使用 demjson.decode() 函数解码 JSON 数据。该函数返回 Python 字段的数据类型。

语法

```demjson.decode(self, txt)```

实例

以下实例展示了Python 如何解码 JSON 对象：

```
#!/usr/bin/python
import demjson

json = '{"a":1,"b":2,"c":3,"d":4,"e":5}';

text = demjson.decode(json)
print  text

```

以上代码执行结果为：

```{u'a': 1, u'c': 3, u'b': 2, u'e': 5, u'd': 4}```


###  python 自带JSON模块

* 从python原始类型向json类型的转换过程，具体的转换如下：

```
>>> import json
>>> json.dumps(['foo', {'bar': ('baz', None, 1.0, 2)}])
'["foo", {"bar": ["baz", null, 1.0, 2]}]'
 
>>> f = open('demo.txt','w')
>>> json.dump(range(100), f)
 
# 打开 demo.txt 可以看到
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99]
```

* 从 json 类型向 python 的转换过程，具体的转换如下：

```
>>> import json
>>> json.loads('{"a": 1, "b": 2, "c": 3}') # string to python objects
{u'a': 1, u'c': 3, u'b': 2}
 
 
# 1.txt 中的内容
[{"a": [1, 2, 3, 4, 5]}, {"b": [1, 2, 3]}, {"c": ["English", "Chinese"]}]
 
>>> f = open('1.txt')
>>> json.load(f)
[{u'a': [1, 2, 3, 4, 5]}, {u'b': [1, 2, 3]}, {u'c': [u'English', u'Chinese']}]
```



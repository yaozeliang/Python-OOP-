



## 使用attrs解放双手

大家好，这一期我想和大家分享一个OOP编程的高效神器：attrs库

首先我们来介绍下 attrs 这个库，其官方的介绍如下：

> attrs 是这样的一个 Python 工具包，它能将你从繁综复杂的实现上解脱出来，享受编写 Python 类的快乐。它的目标就是在不减慢你编程速度的前提下，帮助你来编写简洁而又正确的代码。

因此用了它，定义和实现 Python 类变得更加简洁和高效

首先明确一点，我们现在是装了 attrs 和 cattrs 这两个库，但是实际导入的时候是使用 attr 和 cattr 这两个包，是不带 s 的。

在 attr 这个库里面有两个比较常用的组件叫做 attrs 和 attr，前者是主要用来修饰一个自定义类的，后者是定义类里面的一个字段的。下面是一个小例子


```python

from attr import attrs,attrib

@attrs
class Person:
    name = attrib(type = str,default="")
    age = attrib(type = int,default=0)
    sex = attrib(type = str,default="")

if __name__ == '__main__':
    first_person = Person("John",18,"M")
    print(first_person)

Out:Person(name='John', age=18, sex='M')


```


## 主要作用
可以发现，Person这个类 三个属性都只写了一次，同时还指定了各个字段的类型和默认值，另外也不需要再定义 **init** 方法和 **repr** 方法了，非常简洁


实际上，主要是 attrs 这个修饰符起了作用，然后根据定义的 attrib 属性自动帮我们实现了 **init**、**repr**、**eq**、**ne**、**lt**、**le**、**gt**、**ge**、**hash** 这几个方法

## 深入了解

现在来用实例看一下：


```python
first_person = Person("John",18,"M")
second_person = Person("Nancy",16,"F")

print('Equal:', first_person == second_person)  #False
print('Not Equal(ne):', first_person != second_person) #True
print('Less Than(lt):', first_person.age < second_person.age) #False
print('Less or Equal(le):', first_person.age <= second_person.age) #False
print('Greater Than(gt):', first_person.age > second_person.age) #True
print('Greater or Equal(ge):', first_person.age >= second_person.age) #True
```


## 属性定义

对于 attrib 的定义，可以传入各种参数，不同的参数对于这个类的定义有非常大的影响。

下面来详细了解一下每个属性的具体参数和用法。

首先我们用 attrs 里面的 fields 方法可以查看一下

```python
from attr import attrs, attrib,fields
print(fields(Person))

(Attribute(name='name', default='', validator=None, repr=True, cmp=True, hash=None, init=True, metadata=mappingproxy({}), type=<class 'str'>, converter=None, kw_only=False), Attribute(name='age', default=0, validator=None, repr=True, cmp=True, hash=None, init=True, metadata=mappingproxy({}), type=<class 'int'>, converter=None, kw_only=False), Attribute(name='sex', default='', validator=None, repr=True, cmp=True, hash=None, init=True, metadata=mappingproxy({}), type=<class 'str'>, converter=None, kw_only=False))

```

参数| 解释  
-------- | -----  
name| 属性的名字，是一个字符串类型
default| 属性的默认值，如果没有传入初始化数据，那么就会使用默认值，如果没有默认值定义，那么就是 NOTHING，即没有默认值
 validator| 验证器，检查传入的参数是否合法
 init|是否参与初始化，如果为 False，那么这个参数不能当做类的初始化参数，默认是 True。 
 type| 类型，比如 int、str 等各种类型，默认为 None
 metadata| 元数据，只读性的附加数据
 converter| 转换器，进行一些值的处理和转换器，增加容错性
  kw_only| 是否为强制关键字参数，默认为 False

### 初始化
如果一个类的某些属性不想参与初始化，比如想直接设置一个初始值，一直固定不变，我们可以将属性的 init 参数设置为 False，看一个实例：
```python 3
from attr import attrs,attrib

@attrs
class Person:
    name = attrib(type = str)
    age = attrib(init=False)
    sex = attrib(type = str)

first = Person("John","M") # Person(name='John', age=NOTHING, sex='M')

second = Person("Mike",89,"M")
#TypeError: __init__() takes 3 positional arguments but 4 were given
```
可以发现，first没有问题，但是second会报错，因为age没有参与初始化，只剩了self，age，sex

### 强制关键字
强制关键字是 Python 里面的一个特性，在传入的时候必须使用关键字的名字来传入

设置了强制关键字参数的属性必须要放在后面，其后面不能再有非强制关键字参数的属性

我们还是拿一样的例子，这回把sex的参数
```python
from attr import attrs,attrib

@attrs
class Person:
    name = attrib(type = str)
    age = attrib(type = str)
    sex = attrib(kw_only=True)
    
first = Person("John",18,sex="M")
#Person(name='John', age=18, sex='M')

```
如果初始化first时使用Person("John",18,"M")则会报错

### 验证器
有时候在设置一个属性的时候必须要满足某个条件，比如性别必须要是男或者女，否则就不合法。对于这种情况，我们就需要有条件来控制某些属性不能为非法值。
```python 
from attr import attrs, attrib

def is_valid_gender(instance, attribute, value):
    if value not in ('M', 'F'):
        raise ValueError(f'gender {value} is not valid')
        
@attrs
class Person:
    name = attrib(type = str)
    age = attrib(type = str)
    sex = attrib(validator=is_valid_gender)
```
在这里我们定义了一个验证器 Validator 方法，叫做 is_valid_gender，其中 gender 定义的时候传入了一个参数 validator，其值就是我们定义的 Validator 方法：

 - instance：类对象
 - attribute：属性名
 - value：属性值
 
下面做了两个实验，一个就是正常传入 "M"，另一个写错了，写的是 "X":

```python

first = Person("John",18,"M")  
# Person(name='John', age=18, sex='M')

second = Person("Ann",29,"X")  
ValueError: gender X is not valid
```
second报错了，因为其值不是正常的性别，所以程序直接报错终止
注意在 Validator 里面返回 True 或 False 是没用的，错误的值还会被照常复制。所以，一定要在 Validator 里面 raise 某个错误。

另外 attrs 库里面还给我们内置了好多 Validator，比如判断类型，这里如果规定age必须为 int 类型：
```python
age  =attrib(validator=validators.instance_of(int))
```

另外 validator 参数还支持多个 Validator，比如我们要设置既要是数字，又要小于 100，那么可以把几个 Validator 放到一个列表里面并传入：

```python
from attr import attrs, attrib,validators

def is_valid_gender(instance, attribute, value):
    if value not in ('M', 'F'):
        raise ValueError(f'gender {value} is not valid')
        
def is_less_than_100(instance, attribute, value):
    if value > 100:
        raise ValueError(f'age {value} must less than 100')
        
@attrs
class Person:
    name = attrib(type = str)
    age = attrib(validator=[validators.instance_of(int), is_less_than_100])
    sex = attrib(validator=[validators.instance_of(str),is_valid_gender])


```




### 转换器
很多时候我们会不小心传入一些形式不太标准的结果，比如本来是 int 类型的 100，我们传入了字符串类型的 100，那这时候直接抛错应该不好吧，所以我们可以设置一些转换器来增强容错机制，比如将字符串自动转为数字：
```python
from attr import attrs, attrib,validators

def to_int(value):
    try:
        return int(value)
    except:
        return None
@attrs
class Person:
    name = attrib(type = str)
    age = attrib(converter=to_int)
    sex = attrib(validator=validators.instance_of(str))

last_person = Person("xiaobai","35","M")    
print(last_person)

Out: Person(name='xiaobai', age=35, sex='M')
```


## 总结

这次我记录了attrs 库的用法，是参考了别人的文章，至此Python OOP 编程的个人学习经历都已经记录下来了

我的其他原创文章已经放到了Github上，如果感兴趣的朋友可以去看看，链接如下：

 - [Python 精品练习题100道][1]
 - [Python 实用技巧汇总][2]
 - [Python Pandas教程][3]
 
 
  [1]: https://github.com/yaozeliang/Python-100-exercises-notebook
  [2]: https://github.com/yaozeliang/python_tricks_share
  [3]: https://github.com/yaozeliang/pandas_share







## Python面向对象编程学习备忘录

### OOP编程是什么
大家好，作为小白，最近学习了很多Python OOP编程的知识，因为脑容量有限，特此一一按照学习顺序记录下来，如果哪里有错误，还请大神尽快指出，以免误导他人。。。

首先让我们简单了解一下何为面向对象编程：

> 把一组数据结构和处理它们的方法组成对象（object），把相同行为的对象归纳为类（class），通过类的封装（encapsulation）隐藏内部细节，通过继承（inheritance）实现类的特化（specialization）和泛化（generalization），通过多态（polymorphism）实现基于对象类型的动态分派。

这样一说貌似有些复杂，简单来看的话可以参考下面的解释：

![](https://user-gold-cdn.xitu.io/2019/7/15/16bf16bbee74d83c?w=1348&h=970&f=png&s=407651)

### 常见概念一览

概念| 解释
-------- | -----
**类(Class)** | 用来描述具有相同的属性和方法的对象的集合。它定义了该集合中每个对象所共有的属性和方法。对象是类的实例
**类变量**| 类变量在整个实例化的对象中是公用的。类变量定义在类中且在函数体之外。类变量通常不作为实例变量使用
**数据成员**| 类变量或者实例变量, 用于处理类及其实例对象的相关的数据
   **方法重写**| 如果从父类继承的方法不能满足子类的需求，可以对其进行改写，这个过程叫方法的覆盖（override），也称为方法的重写
**局部变量**   | 定义在方法中的变量，只作用于当前实例的类
**实例变量**   | 在类的声明中，属性是用变量来表示的。这种变量就称为实例变量，是在类声明的内部但是在类的其他成员方法之外声明的
 **继承**  | 即一个派生类（derived class）继承基类（base class）的字段和方法。继承也允许把一个派生类的对象作为一个基类对象对待。例如，有这样一个设计：一个Dog类型的对象派生自Animal类，这是模拟"是一个（is-a）"关系（例图，Dog是一个Animal）
  **实例化** | 创建一个类的实例，类的具体对象
  **方法** | 类中定义的函数
 **对象**  | 通过类定义的数据结构实例。对象包括两个数据成员（类变量和实例变量）和方法

    
### 定义一个类
下面让我们简单定义一个汽车类：
```python
class Car:
    def __init__(self, color, model, year):

        self.color = color
        self.model = model
        self.year = year
```
这里我们创建了一个汽车类Car，它有三个公共属性，分别是color（颜色），model（型号），year（生产年份）

### 创建实例对象，访问属性

现在让我们新建一个对象my_car:
```python
my_car = Car("yellow", "beetle", 1967)
```
查看一下my_car的属性

```python
print(f" My {my_car.color} car {my_car.model} is made in {my_car.year}")
# My yellow car beetle is made in 1967
```
### 添加新属性
我们想要给my_car添加一个新属性wheels
```python
my_car.wheels = 5
print(f"Wheels: {my_car.wheels}")
# Wheels: 5
```
使用dir(my_car)可以让我们确认一下属性是否存在：
```python
dir(my_car)

Out:
['__class__',
 '__delattr__',
 '__dict__',
 '__dir__',
 '__doc__',
 '__eq__',
 '__format__',
 '__ge__',
 '__getattribute__',
 '__gt__',
 '__hash__',
 '__init__',
 '__init_subclass__',
 '__le__',
 '__lt__',
 '__module__',
 '__ne__',
 '__new__',
 '__reduce__',
 '__reduce_ex__',
 '__repr__',
 '__setattr__',
 '__sizeof__',
 '__str__',
 '__subclasshook__',
 '__weakref__',
 'color',
 'model',
 'wheels',     <====已经添加成功啦
 'year']

```
### 类变量，修改类变量的值

在Python中，我们在类外声明一个类变量，下面让我们修改一下Car类：

```python
class Car:
    wheels = 0
    def __init__(self, color, model, year):
        self.color = color
        self.model = model
        self.year = year
```
这样的话，我们在调用wheels这个变量时，可以通过实例，或者直接调用Car.wheels：
```python
my_car = Car("yellow", "beetle", 1967)
print(f"My car is {my_car.color}")
print(f"It has {Car.wheels} wheels")
print(f"It has {my_car.wheels} wheels")

Out:
My car is yellow
It has 0 wheels
It has 0 wheels

```
这里需要注意一下，如果想要通过my_car.wheels =xxx来修改wheels的值，不会真正修改类变量wheels的值，我们来看一个具体的例子：

```python
my_car = Car("yellow", "Beetle", "1966")
my_other_car = Car("red", "corvette", "1999")

print(f"My car is {my_car.color}")
print(f"It has {my_car.wheels} wheels")


print(f"My other car is {my_other_car.color}")
print(f"It has {my_other_car.wheels} wheels")

Out：
My car is yellow
It has 0 wheels
My other car is red
It has 0 wheels
```
我们首先创建两个实例my_car 和my_other_car ，默认的wheels=0，下面我们首先直接通过Car这个类来修改类变量的值：

```python
# Change the class variable value

Car.wheels = 4

print(f"My car has {my_car.wheels} wheels")
print(f"My other car has {my_other_car.wheels} wheels")

Out：
My car has 4 wheels
My other car has 4 wheels

```
可以看到这样修改的话，Car类拥有的所有实例中的wheels值会被全部修改，如果我们通过my_other_car 来修改呢？


```python
# Change the instance variable value for my_car

my_car.wheels = 5
print(f"My car has {my_car.wheels} wheels")
print(f"My other car has {my_other_car.wheels} wheels")

Out:
My car has 5 wheels
My other car has 4 wheels

```
现在大家可以发现区别了，仅仅是修改了my_car中wheels的值，对类本身不会造成影响

### 私有和公有属性

在Python中的所有属性都是public，可能有c++和java的同学觉得神奇，其实python最初规定了一种特殊的命名方式来区分public还是private，那就是下划线_

我还是拿一样的例子说明：

```python
class Car:
    wheels = 0
    def __init__(self, color, model, year):
        self.color = color
        self.model = model
        self.year = year
        self._cupholders = 6

my_car = Car("yellow", "Beetle", "1969")
print(f"It was built in {my_car.year}")

Out：
It was built in 1969
```
这里Car类中的杯托 _cupholders就是“私有“属性，为什么我这里加上了引号，是因为Python只是名义上规定这种写法，但是在实际访问上没啥卵用，依然可以直接用._cupholders来访问：

```python
my_car.year = 1966
print(f"It was built in {my_car.year}")
print(f"It has {my_car._cupholders} cupholders.")

Out:
It was built in 1966
It has 6 cupholders.

```

后来Python决定使用双下划线__来替换单下划线，这样可以最大程度避免“意外访问“，然而还是没有卵用，再来展示一下新方案：
```python
class Car:
    wheels = 0
    def __init__(self, color, model, year):
        self.color = color
        self.model = model
        self.year = year
        self.__cupholders = 6

```
其实某种程度上，这回效果还是很明显的，如果我们还像刚才一样尝试调用my_car.cupholders 会报错：

```
my_car = Car("yellow", "Beetle", "1969")
print(f"It was built in {my_car.year}")
print(f"It has {my_car.__cupholders} cupholders.")


Out:
It was built in 1969

---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-108-1efe56f0c054> in <module>
 1 my_car = Car("yellow", "Beetle", "1969")
 2 print(f"It was built in {my_car.year}")
----> 3  print(f"It has {my_car.__cupholders} cupholders.")

AttributeError: 'Car' object has no attribute '__cupholders'
```
这个错误很有意思，为什么会说cupholders这个变量不存在呢 ? 因为当Python看到__ 时，会自动在cupholders前面补上一个下划线_和所属类名，也就是说，这里我们尝试用my_car.__cupholders 来调用时，Python默认的正确写法是
my_car._Car__cupholders，现在再试一下：

```
print(f"It has {my_car._Car__cupholders} cupholders")
Out: It has 6 cupholders
```

看见没，依然没拦住。。。。
不过我个人认为这种规定公有私有变量的方式也是好处多多，这里就仁者见仁，智者见智了~

### 访问权限管理

就像刚刚提到的，Python所有的东西都是公有的，我们可以随意的新增，修改，甚至删除变量：

```
my_car = Car("yellow", "beetle", 1969)
print(f"My car was built in {my_car.year}")

my_car.year = 2003
print(f"It was built in {my_car.year}")

del my_car.year
print(f"It was built in {my_car.year}")

Out：
My car was built in 1969
It was built in 2003

---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-110-46914b0bae82> in <module>
 6 
 7 del my_car.year
----> 8  print(f"It was built in {my_car.year}")

AttributeError: 'Car' object has no attribute 'year'

```
那我们如何才能控制属性的访问权限呢？Python给出的答案是装饰器 @property，这个类似于Java中的setter和getter，现在我们试试：

```python
class Car:
    def __init__(self, color, model, year):
        self.color = color
        self.model = model
        self.year = year
        self._voltage = 12

    @property
    def voltage(self):
        return self._voltage

    @voltage.setter
    def voltage(self, volts):
        print("Warning: this can cause problems!")
        self._voltage = volts

    @voltage.deleter
    def voltage(self):
        print("Warning: the radio will stop working!")
        del self._voltage
```

我们新增了voltage（电压）这个属性，并用property来控制外部的访问权限，这里我们定义了三个方法，利用setter方法可以改变voltage的值，利用getter方法来访问，利用deleter方法实现删除，接下来让我们新建实例来看看propert是如何工作的：

```python
my_car = Car("yellow", "beetle", 1969)
print(f"My car uses {my_car.voltage} volts")

my_car.voltage = 6
print(f"My car now uses {my_car.voltage} volts")

del my_car.voltage

Out:
My car uses 12 volts
Warning: this can cause problems!
My car now uses 6 volts
Warning: the radio will stop working!
```
可以发现，我们这里直接使用.voltage 而不是._voltage，这样就告诉python去使用property装饰的方法，我们可以通过使用@.setter and @.deleter 使属性变为read-only（只读），从而保护voltage不会被随意修改和删除

### 总结

今天主要总结了OOP编程中的类，对象，属性，公有私有属性，访问权限这些基础概念，下一篇文章会进一步深入，如果本文有哪些语言使用不当，希望大家可以指出，让我们一起进步! 

我之前的一些文章已经放到了Github上，如果感兴趣的朋友可以去看看，链接如下：

 - [Python 精品练习题100道][1]
 - [Python 实用技巧汇总][2]
 - [Python Pandas教程][3]
 
 
  [1]: https://github.com/yaozeliang/Python-100-exercises-notebook
  [2]: https://github.com/yaozeliang/python_tricks_share
  [3]: https://github.com/yaozeliang/pandas_share

 


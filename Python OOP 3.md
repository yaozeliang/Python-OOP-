
## 类的方法概览

首先回顾一下Python OOP常见的三种方法：
 - instance method  实例/接口方法
 - class method      类方法
 - static method    静态方法

标准书写方式如下：

```python
class MyClass:
    def method(self):
        return 'instance method called', self

    @classmethod
    def classmethod(cls):
        return 'class method called', cls

    @staticmethod
    def staticmethod():
        return 'static method called'
```

我们最常用的其实就是普通的接口方法，其他两个需要用类似装饰器的写法来标注。

类方法接受一个cls作为参数，它是指向MyClass本身的，并不是MyClass所创建的实例。

静态方法不接受self或者cls作为参数，所以不会修改类本身或者实例

## 类方法示例
看一下如何使用类方法，新建一个Pizza类，主要参数为原料ingredients

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    def __repr__(self):
        return f'Pizza({self.ingredients !r})'
```

新建一个实例测试一下：

```python
Pizza(['cheese', 'tomatoes'])
Out: Pizza(['cheese', 'tomatoes'])
```
测试成功。
现在问题来了，既然是Pizza类，会有不同口味的Pizza，他们的配方都是固定的，那么如何便捷的生成不同口味的Pizza呢，答案就是classmethod

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    def __repr__(self):
        return f'Pizza({self.ingredients!r})'

    @classmethod
    def margherita(cls):
        return cls(['mozzarella', 'tomatoes'])

    @classmethod
    def prosciutto(cls):
        return cls(['mozzarella', 'tomatoes', 'ham'])


```
类方法可以根据需求事先预定义生成的实例，减少了代码量，这里我们根据margherita和prosciutto两种口味pizza的原料提前准备好了，cls就是代表类本身，这样如果我们再生成一些实例时，会方便很多：

```python
#生成一个margherita口味的pizza
m = Pizza.margherita()
m
Out：Pizza(['mozzarella', 'tomatoes'])
```

```python
#生成一个prosciutto口味的pizza
p = Pizza.prosciutto()
p
Out：Pizza(['mozzarella', 'tomatoes', 'ham'])

```
## 静态方法示例

那么什么时候用静态方法呢? 比如还用这个例子，我想计算pizza的面积：


```python
import math

class Pizza:
    def __init__(self, radius, ingredients):
        self.radius = radius
        self.ingredients = ingredients

    def __repr__(self):
        return (f'Pizza({self.radius!r}, '
                f'{self.ingredients!r})')

    def area(self):
        return self.circle_area(self.radius)

    @staticmethod
    def circle_area(r):
        return r ** 2 * math.pi

```
这种情况下使用一个静态方法是一个好的选择，通过area这个普通的接口方法，可以调用circle_area来计算面积，封装性更好：

```python
p = Pizza(4, ['mozzarella', 'tomatoes'])
p.area() 

Out: 50.26548245743669
```


## 总结

这次简单总结了类中的三种方法，通过Pizza类的实例方便理解，如果大家有想和我沟通交流的，欢迎留言，或者点击以下链接访问我的网站：

 - [我的Github 静态博客][1]


谢谢阅读



  [1]: https://yaozeliang.github.io/resume/






##  Dataclasses

大家好，这一期我主要记录一下我的Dataclasses的学习过程。

上一期简单回顾了attrs的用法，这一期来看更简洁的自带写类神器：dataclasses

官方文档链接：[ Data Classes](https://docs.python.org/3/library/dataclasses.html)
下面直接来看例子：

##  创建Dataclass


```python
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float
    lat: float

```
可以发现，主要起作用的是装饰符@dataclass  ，需要注意，如果想要使用dataclass，需要Python 3.7或更高版本
使用dataclass的好处是可以节省书写__init()__等一些常用的实例方法

这里创建一个Position类，用来显示一个地点的位置
- name：地点的名字
- lon：经度
- lat：纬度

新建一个实例来看看:

```python
>>> pos = Position('Oslo', 10.8, 59.9)
>>> print(pos)
Position(name='Oslo', lon=10.8, lat=59.9)
>>> pos.lat
59.9
>>> print(f'{pos.name} is at {pos.lat}°N, {pos.lon}°E')
Oslo is at 59.9°N, 10.8°E
```

除了这种方法，还要一种类似创建namedtuple的方式也可以：

```python

from dataclasses import make_dataclass

Position = make_dataclass('Position', ['name', 'lat', 'lon'])
```



## 默认值
让我们看看如何给类的属性添加默认值：

```python
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0
```
效果和普通的类设定初始值的效果是一样的：

```python
>>> Position('Null Island')
Position(name='Null Island', lon=0.0, lat=0.0)
>>> Position('Greenwich', lat=51.8)
Position(name='Greenwich', lon=0.0, lat=51.8)
>>> Position('Vancouver', -123.1, 49.3)
Position(name='Vancouver', lon=-123.1, lat=49.3)

```
## 输入提示

大家可以发现我们的Positon类规定了三个属性的类型：
 - name：str
 - lon：float
 -  lat：float

现在如果想要开放限制，允许任意的数据类型，可以这么做:
```python
from dataclasses import dataclass
from typing import Any

@dataclass
class WithoutExplicitTypes:
    name: Any
    value: Any = 42
```
这样运行的时候不会报错，哪怕瞎传参：
```python
>>> Position(3.14, 'pi day', 2018)
Position(name=3.14, lon='pi day', lat=2018)
```

## 添加一个方法

现在我们想要计算两个地点的距离，可以参考如下公式：
![The haversine formula](https://user-gold-cdn.xitu.io/2019/7/20/16c0eaf6cdf4316f?w=551&h=38&f=png&s=2067)

根据这个公式为类添加一个`.distance_to()`方法
```python
from dataclasses import dataclass
from math import asin, cos, radians, sin, sqrt

@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0

    def distance_to(self, other):
        r = 6371  # Earth radius in kilometers
        lam_1, lam_2 = radians(self.lon), radians(other.lon)
        phi_1, phi_2 = radians(self.lat), radians(other.lat)
        h = (sin((phi_2 - phi_1) / 2)**2
             + cos(phi_1) * cos(phi_2) * sin((lam_2 - lam_1) / 2)**2)
        return 2 * r * asin(sqrt(h))
```
实验一下：
```python
>>> oslo = Position('Oslo', 10.8, 59.9)
>>> vancouver = Position('Vancouver', -123.1, 49.3)
>>> oslo.distance_to(vancouver)
7181.7841229421165
```

## 更加灵活的应用

目前为止我们已经看到了dataclass的基础用法，现在我们看看根据实际需要，有哪些其他灵活的应用方式。

现在创建两个类，纸牌类和牌库类，纸牌类的属性包括花色和数字，牌库是List类型，包含纸牌类的一些实例

```python
from dataclasses import dataclass
from typing import List

@dataclass
class PlayingCard:
    rank: str
    suit: str

@dataclass
class Deck:
    cards: List[PlayingCard]

```
现在向牌库添加红桃Q和黑桃A的操作可以这样:

```python
>>> queen_of_hearts = PlayingCard('Q', 'Hearts')
>>> ace_of_spades = PlayingCard('A', 'Spades')
>>> two_cards = Deck([queen_of_hearts, ace_of_spades])
Deck(cards=[PlayingCard(rank='Q', suit='Hearts'),
 PlayingCard(rank='A', suit='Spades')])
```
现在我们可以创建一套完整的扑克牌牌库，注意这里使用了符号表示花色，建议在实际环境中改换为字符串：
```python 3
RANKS = '2 3 4 5 6 7 8 9 10 J Q K A'.split()
SUITS = '♣ ♢ ♡ ♠'.split()

def make_french_deck():
    return [PlayingCard(r, s) for s in SUITS for r in RANKS]

>>> make_french_deck()
[PlayingCard(rank='2', suit='♣'), PlayingCard(rank='3', suit='♣'), ...
 PlayingCard(rank='K', suit='♠'), PlayingCard(rank='A', suit='♠')]
```

理论上，我们可以把这个方法作为Deck类的初始变量，但是根本不行，因为它可变：
```python 3
from dataclasses import dataclass
from typing import List

@dataclass
class Deck:  # Will NOT work
    cards: List[PlayingCard] = make_french_deck()
```
面对这种情况，dataclass的解决方案是使用`default_factory`函数作为field的参数来指明：
```python 3
from dataclasses import dataclass, field
from typing import List

@dataclass
class Deck:
    cards: List[PlayingCard] = field(default_factory=make_french_deck)
```
现在就没有任何问题了：
```python 3
>>> Deck()
Deck(cards=[PlayingCard(rank='2', suit='♣'), PlayingCard(rank='3', suit='♣'), ...
 PlayingCard(rank='K', suit='♠'), PlayingCard(rank='A', suit='♠')])
```

## field
现在简单总结一下dataclass中使用field涉及到的关键参数：

 -  `default`: Default value of the field
-   `default_factory`: Function that returns the initial value of the field
-   `init`: Use field in  `.__init__()`  method? (Default is  `True`.)
-   `repr`: Use field in  `repr`  of the object? (Default is  `True`.)
-   `compare`: Include the field in comparisons? (Default is  `True`.)
-   `hash`: Include the field when calculating  `hash()`? (Default is to use the same as for  `compare`.)
-   `metadata`: A mapping with information about the field

最后这个metadata有点像前端h5中的那个，就是可以为类的一个属性添加一个额外的描述信息：
```python
from dataclasses import dataclass, field

@dataclass
class Position:
    name: str
    lon: float = field(default=0.0, metadata={'unit': 'degrees'})
    lat: float = field(default=0.0, metadata={'unit': 'degrees'})


```
可以发现，传递的是一个dict，现在可以使用fields来查看一个属性的附加信息了：
```python
>>> from dataclasses import fields
>>> fields(Position)
(Field(name='name',type=<class 'str'>,...,metadata={}),
 Field(name='lon',type=<class 'float'>,...,metadata={'unit': 'degrees'}),
 Field(name='lat',type=<class 'float'>,...,metadata={'unit': 'degrees'}))
>>> lat_unit = fields(Position)[2].metadata['unit']
>>> lat_unit
'degrees'
```

##  描述类（str,repr）
这里指的就是常用的repr(obj)和str(obj)
先看一下刚才的Deck()描述：

```python
>>> Deck()
Deck(cards=[PlayingCard(rank='2', suit='♣'), PlayingCard(rank='3', suit='♣'), PlayingCard(rank='4', suit='♣'), PlayingCard(rank='5', suit='♣')...

```
有些过长了，让我们用传统的str或者repr来表达，首先从纸牌类PlayingCard开始：
```python 
from dataclasses import dataclass

@dataclass
class PlayingCard:
    rank: str
    suit: str

    def __str__(self):
        return f'{self.suit}{self.rank}'
```

```python 
>>> ace_of_spades = PlayingCard('A', '♠')
>>> ace_of_spades
PlayingCard(rank='A', suit='♠')
>>> print(ace_of_spades)
♠A
```
现在看上去好多了，现在再自定义下牌库类Deck的描述：
```python 
from dataclasses import dataclass, field
from typing import List

@dataclass
class Deck:
    cards: List[PlayingCard] = field(default_factory=make_french_deck)

    def __repr__(self):
        cards = ', '.join(f'{c!s}' for c in self.cards)
        return f'{self.__class__.__name__}({cards})'
```
这回看上去舒服多了：
```python 
>>> Deck()
Deck(♣2, ♣3, ♣4, ♣5, ♣6, ♣7, ♣8, ♣9, ♣10, ♣J, ♣Q, ♣K, ♣A,
 ♢2, ♢3, ♢4, ♢5, ♢6, ♢7, ♢8, ♢9, ♢10, ♢J, ♢Q, ♢K, ♢A,
 ♡2, ♡3, ♡4, ♡5, ♡6, ♡7, ♡8, ♡9, ♡10, ♡J, ♡Q, ♡K, ♡A,
 ♠2, ♠3, ♠4, ♠5, ♠6, ♠7, ♠8, ♠9, ♠10, ♠J, ♠Q, ♠K, ♠A)

```

## 不可修改的Dataclass
其实这里和FrozenSet有点像，无非在装饰器中添加了一个参数frozen=True：
```python 
from dataclasses import dataclass

@dataclass(frozen=True)
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0
```

在这样一个frozen data class中，我们不能随意赋值：
```python
>>> pos = Position('Oslo', 10.8, 59.9)
>>> pos.name
'Oslo'
>>> pos.name = 'Stockholm'
dataclasses.FrozenInstanceError: cannot assign to field 'name'

```




## 继承

使用dataclass的继承也比较简单，和普通类的继承没有太大区别：
```python
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float
    lat: float

@dataclass
class Capital(Position):
    country: str

```
```python
>>> Capital('Oslo', 10.8, 59.9, 'Norway')
Capital(name='Oslo', lon=10.8, lat=59.9, country='Norway')
```
这里可以发现，创建Capital类的实例时会自动继承了父类的属性，我们只需要额外加入country这个新属性就可以了

假如父类初始化时有默认值：
```python
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0

@dataclass
class Capital(Position):
    country: str = 'Unknown'
    lat: float = 40.0
```
## 简单优化
可以用我之前提到的slot方法进行优化：
```python
from dataclasses import dataclass

@dataclass
class SimplePosition:
    name: str
    lon: float
    lat: float

@dataclass
class SlotPosition:
    __slots__ = ['name', 'lon', 'lat']
    name: str
    lon: float
    lat: float

```

## 总结

这次我记录了dataclass的基础用法，是参考了别人的文章，如果想要了解更多，还是要到官方文档去看

我的其他原创文章已经放到了Github上，如果感兴趣的朋友可以去看看，链接如下：

 - [Python 精品练习题100道][1]
 - [Python 实用技巧汇总][2]
 - [Python Pandas教程][3]
 
 
  [1]: https://github.com/yaozeliang/Python-100-exercises-notebook
  [2]: https://github.com/yaozeliang/python_tricks_share
  [3]: https://github.com/yaozeliang/pandas_share








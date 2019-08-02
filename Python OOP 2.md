
# __slots__魔法，类的继承，拓展父类方法，复写

## __slots__魔法
大家好，上一期我重点总结了有关类的基本知识，现在简单回顾一下，顺便加上一个创建类时常用的东西：__slots__

首先创建一个名人类：**Celebrity**
```python
 class Celebrity:
     # 限定 Celebrity对象只能绑定name, age,domain属性,加速
    __slots__ = ['name','age',"domain"]
    # Class Attribute
    species = 'human'
    
    # Initializer / Instance Attributes
    def __init__(self, name, age, domain):
        self.name = name
        self.age = age
        self.domain = domain
```
这里注意我们用slots绑定了三个属性给Celebrity，这就是slots的作用：
 - 如果我们需要限定自定义类型的对象只能绑定某些属性，可以通过在类中定义__slots__变量来进行限定。需要注意的是__slots__的限定只对当前类的对象生效，对子类并不起任何作用。
 - 加速

现在可以做个实验，首先我们把slots绑定的domian属性去掉：
```python
class Celebrity:
    
    # Class Attribute
    species = 'human'
    __slots__ = ['name', 'age']
    # Initializer / Instance Attributes
    def __init__(self, name, age,domain):
        self.name = name
        self.age = age
        self.domain = domain
        
female_leader = Celebrity("Miss Dong",65,"electrical appliance")

# Access the instance attributes
print("{} is {}.".format(
    female_leader.name, female_leader.age))

Out:AttributeError: 'Celebrity' object has no attribute 'domain'
```
会发现报错了，即便我们在init方法中有domain属性，但是由于slots中没有，所以Celebrity类下创建的对象都不能有domain



接下来让我们简单回顾一下如何调用类变量：

 
```python
female_leader = Celebrity("Miss Dong", 65,"electrical appliance")
male_leader = Celebrity("Jack Ma", 55,"internet")

# Access the instance attributes
print("{} is {} and {} is {}.".format(
    female_leader.name, female_leader.age, male_leader.name, male_leader.age))

# Is male_leader a human?
if male_leader.species == "human":
    print("{0} is a {1}!".format(male_leader.name, male_leader.species))  

Out:
Miss Dong is 65 and Jack Ma is 55.
Jack Ma is a human!
```
## *args 

其实*args应该和*kargs一起来说，但是今天先重点看一下它在对象中的应用，我们现在给Celebrity类新建3个对象，并且我们想知道年龄最大的是谁 ？ 

这种情况下*args很好用：
```python
a = Celebrity("Miss Dong",65,"electrical appliance")
b = Celebrity("Jack Ma", 55,"internet")
c = Celebrity("Lei Jun", 50,"mobile")

def get_oldest(*args):
    return max(args)

print("The big brother is {} years old.".format(get_oldest(a.age, b.age, c.age)))

Out:
The big brother is 65 years old.

```
当然，其他的应用场景还有很多，不多列举了

## 类的继承



首先，我们在Celebrity类中新增两个方法:
 - description:对生成的大佬简单描述
 - speak: 大佬发言

完成后的结果如下：

```python
class Celebrity:
     

    __slots__ = ['name', 'age',"domain"]
    species = 'human'

    def __init__(self, name, age, domain):
        self.name = name
        self.age = age
        self.domain = domain
        
     # instance method
    def description(self):
        return "{} is {} years old, working in the {} industry".format(self.name, self.age,self.domain)

    # instance method
    def speak(self, sound):
        return "{} says {}".format(self.name, sound)
```

现在新建两个类InternetBoss，MobileBoss，全部继承于
Celebrity类：

```python
    # Child class (inherits from Dog() class)
    class InternetBoss(Celebrity):
        pass

    # Child class (inherits from Dog() class)
    class MobileBoss(Celebrity):
        pass
```
如果我们什么都不做，会自动继承父类的 description和speak方法，我们做个实验，新建li作为InternetBoss的对象：

```python
li = InternetBoss("Robbin",50,"advertisement")
```
调用description和speak方法：

```python
li.description()
li.speak("What's your problem ?")

Out:
Robbin is 50 years old, working in the advertisement industry
Robbin says: What's your problem ?
```
 再尝试一个MobileBoss的对象：
 
```python
lei = MobileBoss("leijun", 50,"mobile")
lei.speak("Are you ok ?")

Out:
leijun says: Are you ok ?
```
都是一样的

### 类的多态, 复写父类的方法

对于类的多态，各种教程说的都太专业了，我的理解仅仅是：

对父类现有方法A，当新增新的子类时，可以根据需要重写A。

在我们现在的例子中，可以复写description方法：

```python
    class InternetBoss(Celebrity):
        def description(self):
            print("I'm Internet Boss !")
         
    class MobileBoss(Celebrity):
        def description(self):
            print("I'm Mobile phone Boss !")
```

这样InternetBoss类和MobileBoss类生成实例后，会调用自己的description方法：

```python
li = InternetBoss("Robbin",50,"advertisement")
lei = MobileBoss("leijun", 50,"mobile")

li.description()
lei.description()

Out:
I'm Internet Boss !
I'm Mobile phone Boss !

```
### isinstance() 和 issubclass()

Python 有两个判断继承的函数：
 - isinstance() 用于检查实例类型
 - issubclass() 用于检查类继承

现在还用我们的例子说明一下，首先，这是我们现有的三个类：
```python
class Celebrity:
    __slots__ = ['name', 'age',"domain"]
    species = 'human'

    def __init__(self, name, age, domain):
        self.name = name
        self.age = age
        self.domain = domain
        
    def description(self):
        print( "{} is {} years old, working in the {} industry".format(self.name, self.age,self.domain))

    def speak(self, sound):
        print("{} says: {}".format(self.name, sound))
        
        
        
class InternetBoss(Celebrity):
    def description(self):
        print("I'm Internet Boss !")
         
class MobileBoss(Celebrity):
    def description(self):
        print("I'm Mobile phone Boss !")
```
然后我们分别用不同的类创建三个实例：
```python
mingzhu = Celebrity("Miss Dong",65,"electrical appliance")
ma= InternetBoss("Pony", 48,"internet")
lei = MobileBoss("leijun", 50,"mobile")
```
现在使用issubclass（）判断InternetBoss和MobileBoss是否继承自Celebrity：
```python
# True
issubclass(InternetBoss,Celebrity) 

# True
issubclass(MobileBoss,Celebrity)
```
使用isinstance（）查看mingzhu到底是谁的实例：
```python
# True
isinstance(mingzhu,Celebrity)

# False
isinstance(mingzhu,InternetBoss)

# False
isinstance(mingzhu,MobileBoss)
```
同理查看ma到底是哪个类的实例：

```python
# True
isinstance(ma,Celebrity)

# True
isinstance(ma,InternetBoss)

# False
isinstance(ma,MobileBoss)


```
因为InternetBoss是Celebrity子类，所以ma同时是Celebrity和InternetBoss的实例。

如果我们混用了issubclass和isinstance，会报错：

```python
issubclass(ma,InternetBoss)

Out:
TypeError: issubclass() arg 1 must be a class
```

### 子类重写构造函数

刚才提到了，如果子类没有写构造函数init（），会自动继承父类的init，但我们通常需要子类有不同的初始函数，这样我们就需要自己复写一下，这里以InternetBoss为例：

```python
class InternetBoss(Celebrity):
    def __init__(self,name, age, domain,hometown):
        super().__init__(name, age, domain)
        self.hometown = hometown
        
    
    def description(self):
        print("I'm Internet Boss !")
        
    def __repr__(self):
        return f"This is {self.name} speaking !"
```
使用了super（）会保留需要的父类初始化参数，再添加自己的就行了，这里的repr我会下次总结，现在再新建实例：





## 总结

这次我记录了slots用法，*args 的一个使用场景，类的继承，复写父类方法，构造函数等基本概念，剩下的慢慢来，我会一点点补充。。。

Ps: 本文的实例名称均为杜撰，请不要对号入座...


我的其他文章已经放到了Github上，如果感兴趣的朋友可以去看看，链接如下：

 - [Python 精品练习题100道][1]
 - [Python 实用技巧汇总][2]
 - [Python Pandas教程][3]
 
 
  [1]: https://github.com/yaozeliang/Python-100-exercises-notebook
  [2]: https://github.com/yaozeliang/python_tricks_share
  [3]: https://github.com/yaozeliang/pandas_share

 


```python
```


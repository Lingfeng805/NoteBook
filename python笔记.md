# 面向对象编程

面向对象编程把对象作为程序的基本单位，一个对象包含了数据和操作数据的函数。

```python
class Student(object):
    
    def __init__(self, name, score):    # self表示创建的实例本身
        self.name = name
        self.score = score
        
    def print_score(self):
        print('%s: %s' % (self.name, self.score))
```

`Student`这个数据类型被视为一个**对象**， 这个对象拥有`name`和`score`这两个**属性**，和`print_score`这个**方法。**

```python
bart = Student('Bart Simpson', 59)    # 创建实例
lisa = Student('Lisa Simpson', 87)
bart.print_score()    # 实例调用‘方法’
lisa.print_score()
```

每个类都有自己的属性(attribute)和方法(method)。

- 类是一种抽象概念，比如上述定义的Student，是指学生这个概念，而实例(instance)则是一个个具体的Student。
- 所以，面向对象的设计思想是抽象出Class，根据Class创建Instance.

## 类和实例

实例是根据类创建出来的一个个具体的“对象”，每个对象都拥有相同的方法，但各自的数据可能不同。

```python
class Student(object):
    pass
```

class后紧跟着是**类名**`Student`,类名通常是大写开头的单词，紧接着是`(object)`,表示该类是从哪个类**继承**下来的，通常如果没有合适的继承类，就使用`object`类。

和普通函数相比，在类中定义的函数只有一点不同，就是第一个参数永远都是实例变量`self`，并且在调用时，不用传递该参数。

**数据封装**：

在Student类中，每个实例都拥有各自的name和score这些数据。可以通过函数访问这些数据，比如打印一个学生的成绩：

```python
def print_score(std):
    print('%S: %s' % (std.name, std.score))
# 在类外可以调用该函数
print_score(bart)
>>>Bart Simpson: 59
```

但是，Student实例本身就拥有这些数据，要访问这些数据，没必要从外面的函数去访问，可以直接在Student类的内部定义访问数据的函数，这样就把“数据”给封装起来了。这些封装数据的函数是和Student类本身是关联起来的，称之为**类的方法**。

```python
class Student(object):
    
    def __init__(self, name, score):    # self表示创建的实例本身
        self.name = name
        self.score = score
        
    def print_score(self):
        print('%s: %s' % (self.name, self.score))
```

- 小结
  - 类是创建实例的模板，而实例是一个个具体的对象，各个实例拥有的数据都相互独立，互不影响；
  - 方法就是与实例绑定的函数，和普通函数不同，方法可以直接访问实例的数据；
  - 通过在实例上调用方法，我们就直接操作了对象内部的数据，但无需知道方法内部的实现细节。



## 访问限制

在Class内部，可以有属性和方法，而外部代码可以通过直接调用实例变量的方法来操作数据，这样就隐藏了内部的复杂逻辑。

```python
bart = Student('Bart Simpson', 59)    # 创建实例
bart.score    # 调用实例bart的变量score
>>>59
bart.score = 99    # 给实例的变量重新赋值
bart.score
>>>99
```

如果要让内部属性不被外部访问，可以把属性的名称前加上两个下划线`__`，以两个下划线开头的实例的变量名，就变成了一个私有属性(private)，只有内部可以访问，外部不能访问。

```python
class Student(object):

    def __init__(self, name, score):
        self.__name = name
        self.__score = score

    def print_score(self):
        print('%s: %s' % (self.__name, self.__score))
```

此时，对于外部代码来说，已经无法从外部访问`实例变量.__name`和`实例变量.__score`了。

这样可以确保外部代码不能随意修改对象内部的状态，通过访问限制的保护，使代码更稳定。

此时，如果外部代码要获取name和score可以给Student类增加`get_name`和`get_score`这样的方法：

```python
class Student(object):
    ...

    def get_name(self):
        return self.__name

    def get_score(self):
        return self.__score
```

若要允许外部修改score，可以给Student类增加``get_score`方法：

```python
class Student(object):
    ...

    def set_score(self, score):
        self.__score = score
```

## 继承和多态

当定义一个class时，可以从某个现有的class继承，新的class称为子类(Subclass)，而被继承的class称为基类、父类或超类(Base class、Super class)。

如，已有一个名为`Animal`的类：

```python
class Animal(object):
    def run(self):
        print('Animal is running...')
```

当需要编写`Dog`和`Cat`类时，可以直接从`Animal`类继承：

```python
class Dog(Animal):
    pass
class Cat(Animal):
    pass
```

- **继承**：子类获得父类的全部功能，因为Animal实现了run（）的方法，因此Dog和Cat作为子类，什么都没做，就自动拥有了run( )方法。

改进，

```python
class Dog(Animal):

    def run(self):
        print('Dog is running...')

class Cat(Animal):

    def run(self):
        print('Cat is running...')

>>>Dog is running...
>>>Cat is running...
```

- 当子类和父类存在相同的`run()`方法时，子类的方法覆盖了父类的方法，在代码运行的时候，总会调用子类的方法，即获得了继承的另一个好处：**多态**。

**静态语言**和**动态语言**：

- 动态类型语言：在运行期进行类型检查的语言，即在编写代码的时候可以`不指定变量的数据类型`，如python；
- 静态类型语言：它的数据类型是在编译期进行检查的，即变量在`使用前要声明变量的数据类型`，好处是把类型检查放在编译期，提前检查可能出现的类型错误，典型代表C/C++和Java。



- 小结：
  - 继承可以把父类的所有功能都直接拿过来，不必重零做起，子类只需要新增自己特有的方法，也可以把父类不合适的方法覆盖重写。



## 获取对象信息

当我们拿到一个**对象**的引用时，如何知道该对象是什么类型、有哪些方法？

【1】**type( )**

```python
type(123)
>>> <class 'int'>
type('str')
>>> <class 'str'>
type(None)
>>> <type(None) 'NoneType'>
```

【2】**isinstance( )**

对于class的继承关系来说，使用`type()`就很不方便。我们要判断class的类型，可以使用`isinstance()`函数。

若继承关系是：

```python
object -> Animal -> Dog -> Husky
```

```python
a = Animal()
d = Dog()
h = Husky()
```

```python
isinstance(h, Husky)
>>> True
isinstance(d, Husky)
>>> False
```

还可以判断一个变量是否是某些类型中的一种，比如下面的代码就可以判断是否是list或者tuple：

```python
isinstance([1, 2, 3], (list, tuple))
>>> True
isinstance((1, 2, 3), (list, tuple))
>>> True
```

*总是优先使用`isinstance()`判断类型，可以将指定类型及其子类“一网打尽”*

【3】**dir( )**

如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法：

```python
dir('ABC')
>>> ['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
```

- `hasattr( )`函数，用于判断对象是否包含对应的属性：

  hasattr(object, name)

  - object：对象
  - name： 字符串，属性名
  - 如果对象有该属性返回True，否则返回False.

```python
class MyObject(object):
    def __init__(self):
        self.x = 9
    def power(self):
        return self.x * self.x

obj = MyObject()
```

```python
hasattr(obj, 'x') # 有属性'x'吗？
>>> True
obj.x
>>> 9
hasattr(obj, 'y') # 有属性'y'吗？
>>> False
setattr(obj, 'y', 19) # 设置一个属性'y'
hasattr(obj, 'y') # 有属性'y'吗？
>>> True
getattr(obj, 'y') # 获取属性'y'
>>> 19
obj.y # 获取属性'y'
>>> 19
```

- 小结

例子：

```python
def readImage(fp):
    if hasattr(fp, 'read'):
        return readData(fp)
    return None
```

假设我们希望从文件流fp中读取图像，我们首先要判断该fp对象是否存在read方法，如果存在，则该对象是一个流，如果不存在，则无法读取。`hasattr()`就派上了用场。

请注意，在Python这类动态语言中，根据鸭子类型，有`read()`方法，不代表该fp对象就是一个文件流，它也可能是网络流，也可能是内存中的一个字节流，但只要`read()`方法返回的是有效的图像数据，就不影响读取图像的功能。

## 实例属性和类属性

因python是动态语言，根据类创建的实例可以任意绑定属性。给实例绑定属性的方法是通过实例变量，或者通过`self`变量。

```python
class Student(object):
    def __init__(self, name):
        self.name = name

s = Student('Bob')
s.score = 90
```

若，`Student`类本身需要绑定一个属性，可以直接在class中定义属性，即**类属性**，归`Student`类所有：

```python
class Student(object):
    name = 'Student'
```

当定义了一个类属性后，虽然这个属性归类所有，但类的所有实例都可以访问到。

- 小结
  - 实例属性属于各个实例所有，互不干扰；
  - 类属性属于类所有，所有实例共享一个属性；
  - 不要对实例属性和类属性使用相同的名字，否则将产生难以发现的错误。



# 面向对象高级编程

## 使用`__slots__`

```python
class Student(object):
    pass

s = Student()
s.name = 'Michael'    # 动态给实例绑定一个属性
print(s.name)
>>>Michael

# 给实例绑定一个方法
def set_age(self,age):    # 定义一个函数作为实例方法
    self.age = age
from types import MethodType
s.set_age = MethodType(set_age, s)    # 给实例绑定一个方法
s.set_age(25)    # 调用实例方法
s.age    # 测试结果
>>>25
# 但是，给一个实例绑定的方法，对另一个实例是不起作用的
s2 = Student()    # 创建新的实例
s2.set_age(25)    # 尝试调用方法
>>>会报错
# 为了给所有实例都绑定方法，可以给class绑定方法
def set_score(self,score):
    self.score = score
Student.set_score = set_score
# 给class绑定方法后，所有实例均可调用
```

通常情况下，上面的`set_score`方法可以直接定义在class中，但动态绑定允许我们在程序运行过程中动态给class加上功能，这在静态语言中很难实现。

如何**限制**实例的属性？比如，只允许对Student实例添加`name`和`age`属性。在定义class的时候，定义一个特殊的`__slots__`变量，来限制该class实例能添加的属性。

```python
class Student(object):
    __slots__ = ('name', 'age')    # 用tuple定义允许绑定的属性名称
    
# 再试试
s = Student()    # 创建新的实例
s.name = 'Michael'    # 绑定属性'name'
s.age = 25    # 绑定属性'age'
s.score = 99    # 绑定属性'score'
>>> Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'Student' object has no attribute 'score'
# 由于'score'没有被放到__slots__中，所以不能绑定score属性，试图绑定score将得到AttributeError的错误。
```

`使用`__slots__`要注意，`__slots__`定义的属性仅对当前类实例起作用，对继承的子类是不起作用的：`

```python
class GraduateStudent(Student):
	pass

g = GraduateStudent()
g.score = 9999
```

## 使用@property

Python内置的`@property`装饰器就是负责把一个方法变成属性调用的：

作用：可以用@property装饰器来创建**只读属性**，@property装饰器会将**方法**转换为相同名字的**只读属性**，可以与所定义的属性配合使用，可以防止属性被修改。

```python
class Student(object):

    @property
    def score(self):
        return self._score

    @score.setter
    def score(self, value):
        if not isinstance(value, int):
            raise ValueError('score must be an integer!')
        if value < 0 or value > 100:
            raise ValueError('score must between 0 ~ 100!')
        self._score = value
```

使用场景：

【1】修饰方法，使方法可以像属性一样访问。

```python
class DataSet(object):
  @property
  def method_with_property(self): ##含有@property
      return 15
  def method_without_property(self): ##不含@property
      return 15

l = DataSet()
print(l.method_with_property) # 加了@property后，可以用调用属性的形式来调用方法,后面不需要加（）。
print(l.method_without_property())  #没有加@property , 必须使用正常的调用方法的形式，即在后面加()
```

```python
class DataSet(object):
  @property
  def method_with_property(self): ##含有@property
      return 15
l = DataSet()
print(l.method_with_property（）) # 加了@property后，可以用调用属性的形式来调用方法,后面不需要加（）。
'''如果使用property进行修饰后，又在调用的时候，方法后面添加了()， 那么就会显示错误信息：TypeError: 'int' object is not callable，也就是说添加@property 后，这个方法就变成了一个属性，如果后面加入了()，那么就是当作函数来调用，而它却不是callable（可调用）的。'''
```

【2】与所定义的属性配合使用，这样可以防止属性被修改

 由于python进行属性的定义时，没办法设置私有属性，因此要通过@property的方法来进行设置。这样可以隐藏属性名，让用户进行使用的时候无法随意修改。

```python
class DataSet(object):
    def __init__(self):
        self._images = 1
        self._labels = 2 #定义属性的名称
    @property
    def images(self): #方法加入@property后，这个方法相当于一个属性，这个属性可以让用户进行使用，而且用户没办法随意修改。
        return self._images 
    @property
    def labels(self):
        return self._labels
l = DataSet()
#用户进行属性调用的时候，直接调用images即可，而不用知道属性名_images，因此用户无法更改属性，从而保护了类的属性。
print(l.images) # 加了@property后，可以用调用属性的形式来调用方法,后面不需要加（）。
```

## 多重继承

在设计类的继承关系时，通常，主线都是单一继承下来的，例如，`Ostrich`继承自`Bird`。但是，如果需要“混入”额外的功能，通过多重继承就可以实现，比如，让`Ostrich`除了继承自`Bird`外，再同时继承`Runnable`。这种设计通常称之为MixIn。

为了更好地看出继承关系，我们把`Runnable`和`Flyable`改为`RunnableMixIn`和`FlyableMixIn`。类似的，你还可以定义出肉食动物`CarnivorousMixIn`和植食动物`HerbivoresMixIn`，让某个动物同时拥有好几个MixIn：

```python
class Dog(Mammal, RunnableMixIn, CarnivorousMixIn):
    pass
```

## 定制类（待编辑）

`__xxx__`这类变量或者函数名，在Python中有特殊用途。

## 使用枚举类

一些具有特殊含义的类，其实例化对象的个数往往是固定的，比如用一个类表示月份，则该类的实例对象最多有12个；再比如用一个类表示季节，则该类的实例化对象最多有4个。

- 对于这些实例化对象个数**固定**的类，可以用Enum枚举类来定义。

```python
from enum import Enum    # 将一个类定义为枚举类，只要令其继承自enum模块中的Enum类即可。
class Color(Enum):
    # 为序列值指定value值
    red = 1    # 枚举类的每个成员由2部分组成，name = value,name属性值为该枚举类的变量名，
    green = 2    # value代表该枚举值的序号（通常从1开始）
    blue = 3
    
    
# 调用枚举成员的3中方式
print(Color.red)
print(Color['red'])
print(Color(1))
# 调取枚举成员中的value和name
print(Color.red.value)
print(Color.red.name)
# 遍历枚举类中所有成员的2种方式
for color in Color:
    print(color)
```

- **枚举类中各个成员的值，不能在类的外部做任何修改**；如 Color.red = 4 该写法是错误的！
- 枚举类中各个成员必须保证name互不相同，但value可以相同；

```python
from enum import Enum
class Color(Enum):
    # 为序列值指定value值
    red = 1
    green = 1
    blue = 3
print(Color['green'])
>>>Color.red   #可以看到，Color 枚举类中 red 和 green 具有相同的值（都是 1），Python 允许这种情况的发生，它会将 green 当做是 red 的别名，因此当访问 green 成员时，最终输出的是 red。
```

- 除了通过继承Enum类的方法创建枚举类，还可以使用Enum( )函数创建枚举类。

```python
from enum import Enum
# 创建一个枚举类
Color = Enum("Color",('red','green','blue'))
# Enum()函数接受2个参数，第一个指定枚举类的类名，第二个指定枚举类中的多个成员。
```



## 使用元类(待编辑）

Python中**一切皆对象**，用class关键字定义的类本身也是一个对象，负责产生该对象的类称之为**元类**。（元类可以称为类的类），内置的元类为type。

```python
class MyTeacher(object):
    school='john'
 
 
    def __init__(self,name,age):
        self.name = name
        self.age =age
 
 
    def say(self):
        print('%s says welcome to the john to learn Python' %self.name)
        
t1 = MyTeacher('zhangsan',18)
print(type(t1))   # 查看对象t1的类是<class '__main__.MyTeacher'>
print(type(MyTeacher))    # 结果为<class 'type'>,证明是调用类type这个元类产生的MyTeacher，即默认的元类为type
```

- 相当于：t1*这个实例*=MyTeacher*这个类*( )
- MyTeacher=元类(...)

【**metaclass**】元类

- 控制类的创建行为
- 当定义了类以后，可以根据这个类创建出实例，所以：先定义类，然后创建实例。
- 当想创建出类，先定义metaclass，然后创建类。
- **先定义metaclass，就可以创建类，最后创建实例**

```python
# metaclass是类的模板，所以必须从`type`类型派生：
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)
```



## 错误、调试和测试

- 【错误处理】

```python
try:
    print('try...')
    r = 10 / 0     # 该段代码会出错，所以下面的print()函数不会执行，直接跳转值except
    print('result:', r)
except ZeroDivisionError as e:
    print('except:', e)
finally:
    print('finally...')
print('END')
```

当我们认为某些代码可能会出错时，就可以用`try`来运行这段代码，如果执行出错，则后续代码不会继续执行，而是直接跳转至错误处理代码，即`except`语句块，执行完`except`后，如果有`finally`语句块，则执行`finally`语句块，至此，执行完毕。

使用`try...except`捕获错误还有一个巨大的好处，就是可以跨越多层调用，比如函数`main()`调用`bar()`，`bar()`调用`foo()`，结果`foo()`出错了，这时，只要`main()`捕获到了，就可以处理：

```python
def foo(s):
    return 10 / int(s)

def bar(s):
    return foo(s) * 2

def main():
    try:
        bar('0')
    except Exception as e:
        print('Error:', e)
    finally:
        print('finally...')
```

- 【记录错误】

如果不捕获错误，自然可以让Python解释器来打印出错误堆栈，但程序也被结束了。既然我们能捕获错误，就可以把错误堆栈打印出来，然后分析错误原因，同时，**让程序继续执行下去**。

Python内置的`logging`模块可以非常容易地记录错误信息：

```python
import logging

def foo(s):
    return 10 / int(s)
def bar(s):
    return foo(s) * 2
def main():
    try:
        bar('0')
    except Exception as e:
        logging.exception(e)

main()
print('END')
```

同样是出错，但程序打印完错误信息后会继续执行，并正常退出：

- 【调试】

**断言**：

```python
def foo(s):
    n = int(s)
    assert n != 0, 'n is zero!'
    return 10 / n

def main():
    foo('0')
# assert的意思是，表达式 n != 0应该是True，否则，根据程序运行的逻辑，后面的代码肯定会出错
# 如果断言失败，assert语句本身就会抛出AsserttionError.
```

**logging**:

和assert比，logging不会抛出错误，而且可以输出到文件。

```python
import logging
logging.basicConfig(level=logging.INFO)
s = '0'
n = int(s)
logging.info('n = %d' % n)
print(10 / n)
```

`logging`的好处，它允许你指定记录信息的级别，有`debug`，`info`，`warning`，`error`等几个级别，当我们指定`level=INFO`时，`logging.debug`就不起作用了。同理，指定`level=WARNING`后，`debug`和`info`就不起作用了。这样一来，你可以放心地输出不同级别的信息，也不用删除，最后统一控制输出哪个级别的信息。

**级别**：CRITICAL > ERROR > WARNING > INFO > DEBUG

默认生成的root logger的lever是logging.WARNING，低于该级别的就不输出了。

debug:打印全部的日志，详细的信息，通常只出现在诊断问题上；

info：打印info，warning，error，critical级别的日志，确认一切按预期运行；

warning：打印warning，error，critical级别的日志，一个迹象表明，一些意想不到的事情发生了，或表明一些问题在不久的将来（例如，磁盘空间低），这个软件还能按预期工作；

error:打印error，critical级别的日志，更严重的问题，软件没能执行一些功能；

critical：打印critical级别，一个严重的错误，这表明程序本身可能无法继续运行。

```python
import logging  # 引入logging模块
logging.basicConfig(level=logging.NOTSET)  # 设置日志级别
logging.debug(u"如果设置了日志级别为NOTSET,那么这里可以采取debug、info的级别的内容也可以显示在控制台上了")
```

- Logging.Formatter：这个类配置了日志的格式，在里面自定义设置日期和时间，输出日志的时候将会按照设置的格式显示内容。
  Logging.Logger：Logger是Logging模块的主体，进行以下三项工作：
  - 为程序提供记录日志的接口
  -  判断日志所处级别，并判断是否要过滤
  - 根据其日志级别将该条日志分发给不同handler

- 常用函数：
  - Logger.setLevel() 设置日志级别
  - Logger.addHandler() 和 Logger.removeHandler() 添加和删除一个Handler
  - Logger.addFilter() 添加一个Filter,过滤作用
  - Logging.Handler：Handler基于日志级别对日志进行分发，如设置为WARNING级别的Handler只会处理
  - WARNING及以上级别的日志。
  - 常用函数有：
  - setLevel() 设置级别
  - setFormatter() 设置Formatter

- 【单元测试】

单元测试是用来对一个模块、一个函数或者一个类来进行正确性检验的测试工作；

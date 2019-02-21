Python允许在类中使用一个特殊的变量`__slots__`对该类的实例限制随意添加属性，例如，限制`Person`类只有`height`和`weight`属性：
```py
class Person(object):
    __slots__ = ('height', 'weight') # 使用tuple定义好允许被绑定的属性名
```
然后进行测试：
```py
a = Person()    # 创建Person类的一个新实例
a.height = 180  # 设定属性‘height’
a.weight = 130  # 设定属性‘weight’
a.age = 66      # 设定属性‘sge’
```
结果：
```
Traceback (most recent call last):
  File "text.py", line 8, in <module>
    a.age = 66
AttributeError: 'Person' object has no attribute 'age'
```
因为`'age'`没有被定义在`__slots__`中，所以当想要设定属性age时会提示AttributeError错误。
另外，`__slots__`定义限制的属性只对当前类的实例起作用，对继承当前类的子类是无法进行限制的，例如：
```py
class YellowMan(Person):
    pass


b = YellowMan()
b.age = 66
print(b.age)
```
结果：
`66`
要限制子类的属性，就需要在子类定义__slots__，那么子类的实例被允许绑定的属性就是父类的__slots__加上自身的__slots__。
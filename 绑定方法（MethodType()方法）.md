Python允许一个类（class)的实例绑定任意的属性和方法，这也正是动态语言的灵活性。
例如绑定一个属性：
```py
class Person():
    pass


a = Person()
a.height = 180    # 为实例a绑定一个属性
print(a.height)
```
结果：
`180`
绑定一个方法：
```py
def set_weight(self, weight):
    self.weight = weight


from types import MethodType
a.set_weight = MethodType(set_weight, a)    # 为实例a绑定一个方法
a.set_weight(130)
a.weight
```
结果：
`130`
当然，给一个实例绑定的方法，对另一个实例是不起作用的，所以为了给所有实例绑定相同的方法，可以直接给类（class）绑定方法：
```py
def set_age(self, age):
    self.age = age


Person.set_age = set_age
```
这样所有的实例都可以进行调用了：
```py
a.set_age(66)
print(a.age)
```
结果：
`66`
```py
b = Person()
b.set_age(77)
print(b.age)
```
结果：
`77`
通常情况下，上面的set_score方法可以直接定义在class中，但动态绑定允许我们在程序运行的过程中动态给class加上功能，这在静态语言中很难实现。
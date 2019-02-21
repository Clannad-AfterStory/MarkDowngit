####__getattr__
当我们调用一个类的属性或方法时，如果不存在就会报错，例如：
```py
class Person(object):
    def __init__(self):
        self.height = 180
```
现在来调用`height`属性是没有任何问题的，但如果一旦调用不存在的属性如`weight`属性，就会进行报错：
```py
a = Person()
print(a.height)
```
结果：
`180`
```py
print(a.weight)
```
结果：
`AttributeError: 'Person' object has no attribute 'weight'`
很明显提示了没有*weight*这个属性导致了错误。
要避免这种错误发生，除了直接在类中加上这个属性外，Python还提供了一个__getattr__方法，动态的返回一个属性：
```py
class Person(object):
    def __init__(self):
        self.height = 180
    
    def __getattr__(self, attr):
        return 130
```
此时来调用不存的的属性如weight时，就会调用到`__getattr__(self, 'weight)`来获得一个属性，并返回一个值：
```py
a = Person()
print(a.weight)
```
结果：
`130`
当然也可以返回一个函数：
```py
class Person(object):
    def __init__(self):
        self.height = 180
    
    def __getattr__(self, attr):
        return lambda x: x + 1
```
此时调用的方式就要变一下了：
```py
print(a.weight(130))
```
结果：
`131`
当然这只是在没有找到属性或方法的情况下才会去调用`__getattr__`，如果我们想要类只响应特定的几个属性或方法，而调用其他属性时仍旧抛出错误，只要设置好条件就可以了：
```py
class Person(object):
    def __init__(self):
        self.height = 180

    def __getattr__(self, attr):
        if attr == 'weight':
            return lambda x: x + 1
        raise AttributeError("'Person' object has no attribute '{}'".format(attr))
```
其实这就是将类中所有不存在的属性和方法动态处理了，可以针对完全动态的情况使用。
例如现在流行的REST API，很多网站调用的URL都很类似：
* http://api.server/user/friends
* http://api.server/user/timeline/list

对于这种URL，我们如果写SDK就可以利用动态的`__getattr`来进行链式调用：
```py
class Chain(object):
    def __init__(self, path=''):
        self.path = path

    def __getattr__(self, attr):
        return Chain('{}/{}'.format(self.path, attr))

    def __str__(self):
        return self.path

    __repr__ = __str__
```
调用：
```py
url = Chain().status.user.timeline.list
print(url)
```
结果：
`/status/user/timeline/list`
这样无论API如何变化，SDK都可以随URL进行动态调用，也不会因为api的增加而改变。
对于有些REST API会把参数放到URL中，例如GitHub的API:
`GET /users/:user/repos`
调用时需把`:user`替换为实际的用户名，此时只要将类写成能进行如下的调用：
`Chain().users('Lee').repos`
就可以了，实际只要在`__getattr__()`中添加满足某个判断条件返回一个函数即可：
```py
class Chain(object):
    def __init__(self, path=''):
        self.path = path

    def __getattr__(self, attr):
        if attr == 'users':
            return lambda x: Chain('{}/{}/{}'.format(self.path, attr, x))
        return Chain('{}/{}'.format(self.path, attr))

    def __str__(self):
        return self.path

    __repr__ = __str__


print(Chain().users('Lee').repos)
```
结果：
`/users/Lee/repos`
#### __call__
定义此方法后将支持某个实例直接进行自身调用：
```py
class Person(object):
    def __init__(self, name):
        self.name = name

    def __call__(self):
        print('My name is {}'.format(self.name))


a = Person('Lee')
a()
```
结果：
`My name is Lee`
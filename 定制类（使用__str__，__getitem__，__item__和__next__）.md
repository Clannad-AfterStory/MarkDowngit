在Python中，形如__xx__形式的变量名或函数名都是有特殊用途的，例如__slots__就是用来限制当前类的属性的，因此可以使用这些投诉作用的函数来自定义类。

#### __str__方法
当我们定义好一个类后，直接输出当前类的一个实例：
```py
class Person(object):
    def __init__(self, name):
        self.name = name


print(Persom('Lee'))
```
结果：
`<__main__.Person object at 0x0000000002471198>`
表示了这个实例是一个Person对象，所在的内存地址就是后面的十六进制数
而如果在类中地定义好__str__()方法，他就会返回你想要自定义显示的结果了，如：
```py
class Person(object):
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return ('Person object: \n(name: {})'.format(self.name))


print(Person('Lee'))
```
结果：
```
Person object:
(name: Lee)
```
这样你就可以直接看出实例内部拥有那些数据了。
如果是在**控制台**运行python并将一个实例赋给一个变量然后直接看变量内容而不用`print`你会发现：
```
>>> a = Person('Lee')
>>> a
<__main__.Person object at 0x0000000002566860>
```
打印出的还是刚才没定义的__str__时的结果，这是因为直接显示变量调用的是__repr__()方法，主要是为了程序员调试用的,而__str__主要是返回给用户看的。
所以只要再定义一个__repr__()就可以了，但一般__repr__和__str__代码都是相同的，所以直接写成下面这样就可以了：
```py
class Person(object):
    def __init__(self, name):
        self.name = name

    def __str__(self):
        return ('Person object: \n(name: {})'.format(self.name))
    __repr__ = __str__
```
#### __getitem__方法
此方法可以使当前定义的的类像字符串和列表一样使用索引下标取出各个元素，例如定义一个Fibonacci（斐波那契数列）类：
```py
class Fibonacci(object):
    def __getitem__(self, n):
        a, b = 1, 1
        for x in range(n):
            a, b = b, a + b     # 计算数列的下一个值，即后面一个数等于前面两个数的和
        return a
```
现在就可以使用下标进行取值了：
```py
a = Fibonacci()
print(a[0],a[1],a[2],a[3],a[4],a[5],a[6])
```
结果：
`1 1 2 3 5 8 13`
但列表还有一种切片方法：
```py
print(list(range(6))[2:5])
```
结果：
`[2, 3, 4]`
而这种方法用在Fibonacci上就会报错，因为其中没有对__getitem__的参数为切片对象（slice)时的处理，所以需要在传入参数时进行对象的判断处理：
```py
class Fibonacci(object):
    def __getitem__(self, n):
        if isinstance(n, int):    # 如果传入参数是整型数
            a, b = 1, 1
            for x in range(n):
                a, b = b, a + b
            return a
        if isinstance(n, slice):    # 如果传入参数是切片
            start = n.start
            stop = n.stop
            if stop is None:
                start = 0
            a, b = 1, 1
            lt = []
            for x in range(stop):
                if x >= start:    # 过滤掉切片起始前的数
                    lt.append(a)
                a, b = b, a + b
            return lt
```
这样就可以进行切片了：
```py
a = Fibonacci()
print(a[2:6])
```
结果：
`[2, 3, 5, 8]`
#### __iter__方法、__next__方法
__iter__方法返回一个迭代对象，for循环不断的调用该迭代对象的__next__方法拿到循环的值，直到达到设定的某个条件是抛出*StopIterration*错误退出循环：
```py
class Fibonacci(object):
    def __init__(self):
        self.a, self.b = 0, 1

    def __iter__(self):
        return self         # 实例自己就是一个迭代对象，所以返回自身
    
    def __next__(self):
        self.a, self.b = self.b, self.a + self.b    # 计算下数列中的下一个值
        if self.a > 66:  # 计算下一个值到特定条件时退出循环
            raise StopIteration()
        return self.a   # 返回下一个值
```
此时就可以用作for循环了：
```py
for x in Fibonacci():
    print(x)
```
结果：
```
1
1
2
3
5
8
13
21
34
55
```

# python函数中的缺省参数

标签（空格分隔）： python

---
## 缺省参数
默认参数是必须放在必选参数后面，给这些参数指定一个默认值的参数。
### 缺省参数是不可变对象时：
```
def func1(name, city="sz"):
    print "name: %s" % name
    print "city: %s" % city

func1("gao")
func1("zhang", "bj")
```
运行结果如下：
```
name: gao
city: sz
name: zhang
city: bj
```
正常情况下运行python方法时如果缺省参数没有传参时，会给参数指定默认参数。

### 缺省参数是可变对象时：
```
def func2(cities=[]):
    cities.append("sz")
    print "cities: %s, address: %s" % (cities, id(cities))

func2()
func2()
func2(["sz"])
func2()
func2(["bj"])
print "address a: %s" % id(a)
func2(a)
print "address b: %s" % id(b)
func2(b)
```

运行代码：
```
cities: ['sz'], address: 4457058104
cities: ['sz', 'sz'], address: 4457058104
cities: ['sz', 'sz'], address: 4457125432
cities: ['sz', 'sz', 'sz'], address: 4457058104
cities: ['bj', 'sz'], address: 4457125432
address a: 4498100024
cities: ['sh', 'sz'], address: 4498100024
address b: 4498167064
cities: ['bj', 'sz'], address: 4498167064
```

可以发现一个很有趣的现象,如果没有给cities这个参数传参的话，无论运行多少次函数，cities的地址都是同一个，如果给cities直接传一个列表，后面运行多少次，使用的也都是同一个地址。

说明在函数定义的时候，缺省参数cities的地址已经确定了，每次调用函数的时候如果是改变该地址的内容的话，下次再调用的时候默认参数的内容就会改变。

如果传入的参数没有指定地址，函数会默认开辟一个地址给该参数，后面再次调用参数也不指定地址的话，函数还会默认使用该地址。

如果传入的参数是指定地址的,函数在修改该可变对象的时候，修改的就是该指定地址的内容。

## 结论
在定义函数的时候，缺省参数尽量不要用可变参数来作为默认值。不然就会遇到上述问题。







## Python 命名空间的本质

### 一、命名空间
Python使用叫做命名空间的东西来记录变量的轨迹。命名空间是一个 **字典**（dictionary） ，它的键就是变量名，它的值就是那些变量的值。

A namespace is a mapping from names to objects. Most namespaces are currently implemented as Python dictionaries。

**在一个 Python 程序中的任何一个地方，都存在几个可用的命名空间。**

1. 每个函数都有着自已的命名空间，叫做**局部命名空间**，它记录了函数的变量，包括函数的参数和局部定义的变量。
2. 每个模块拥有它自已的命名空间，叫做**全局命名空间**，它记录了模块的变量，包括函数、类、其它导入的模块、模块级的变量和常量。
3. 还有就是**内置命名空间**，任何模块均可访问它，它存放着内置的函数和异常

### 二、命名空间查找顺序
当一行代码要使用变量 x 的值时，Python 会到所有可用的名字空间去查找变量，按照如下顺序：

1. 局部命名空间：特指当前函数或类的方法。如果函数定义了一个局部变量 x，或一个参数 x，Python 将使用它，然后停止搜索。
2. 全局命名空间：特指当前的模块。如果模块定义了一个名为 x 的变量，函数或类，Python 将使用它然后停止搜索。
3. 内置命名空间：对每个模块都是全局的。作为最后的尝试，Python 将假设 x 是内置函数或变量。
4. 如果 Python 在这些名字空间找不到 x，它将放弃查找并引发一个 NameError 异常，如，NameError: name 'aa' is not defined。

嵌套函数的情况：

1. 先在当前 (嵌套的或 lambda) 函数的命名空间中搜索
2. 然后是在父函数的命名空间中搜索
3. 接着是模块命名空间中搜索
4. 最后在内置命名空间中搜索

![img](https://cdncontribute.geeksforgeeks.org/wp-content/uploads/types_namespace-1.png)

### 三、命名空间的生命周期
- Namespace（命名空间）的生命周期，取决于scope of object（对象的作用域）
- 如果对象作用域结束，命名空间的生命周期结束
- 因此无法从外部命名空间访问内部命名空间


不同的命空间在不同的时候创建，有不同的生存期

- **内置命名空间**在python解释器启动时创建，会一直保留，不被删除
- **全局命名空间**在模块被导入时创建，一直到解释器退出时退出
- 当函数被调用时创建一个**局部命名空间**，直到函数返回结果或抛出异常时被删除
    - 每一个递归调用的函数都有自己的命名空间


说明：

- python的一个特别之处在于其赋值操作重视在最里层的作用域
- 赋值不会赋值数据——只是将命名绑定到对象
- 删除也是如此：``del y``只是从局部左右域的命名空间中删除命名``y``
- 事实上，所有引入新命名空间的操作都作用于局部作用域

案例一

```python
i = 1
def  func():
    i = i + 1
func()
```

输出结果：

```
UnboundLocalError: local variable 'i' referenced before assignment
```

说明：

- 由于创建命名空间时，python会检查代码并填充局部命名空间
- 在执行代码前，python发现了`i=i+1`，并将`i`放入局部命名空间
- 在函数执行时，python解释器认为`i`在局部命名空间中但是没有值，所以会产生呢个错误

### 四、通过`locals()`和`globals()BIF`访问命名空间 

#### 1、局部命名空间可以用`locals()BIF`来访问

`locals`返回一个名字/值对的`dictiionary`

这个`dictionary`的键时字符串形式的变量名字，`dictionary`的值是变量的实际值

示例：

```python
def func(i,str):
    x = 123456
    print(locals())
func(1,"first")
```

输出结果：

```
{'i': 1, 'str': 'first', 'x': 123456}
```

#### 2、全局（模块级别）命名空间可以通过`globals()BIF`来访问

##### 示例代码：

```python
import copy
str = "global string"
class test():
    y = 321
def func():
    x = 123
dictionary = globals()
print(dictionary)
```

##### 输出结果

```
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x0000026ADFE80348>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, '__file__': 'D:\\JavaProject\\PyTest\\test.py', '__cached__': None, 'copy': <module 'copy' from 'D:\\Python\\install\\lib\\copy.py'>, 'str': 'global string', 'test': <class '__main__.test'>, 'func': <function func at 0x0000026ADFF9F828>, 'dictionary': {...}}
```

总结：

- 模块的命名空间中不仅仅包含模块级别的变量和常量，还包括所有在模块总定义的函数和类
  - 除此之外，它还包括任何被导入到模块中的东西
- 我们看到，内置命名空间也同样被包含在一个模块中，它被称作`__builtins__`

### 3、locals和globals之间的一个重要的区别

locals是只读的globals不是。

##### 示例代码

```python
def func1 (i,info):
    x = 1234
    print(locals())
    locals()["x"] = 6789
    print("x=", x)

y = 54321
func1(1, "first")
globals()["y"] = 9876
print("y=", y)
```

##### 输出结果：

```python
{'i': 1, 'info': 'first', 'x': 1234}
x= 1234
y= 9876
```

##### 说明：

- `locals()`实际上没有返回局部命名空间，他看会的是一个拷贝
  - 所以对它修改，对局部命名空间中的变量没有影响
- ``globals()``返回的是实际的命名空间
  - 所有对`global()`修改会改变命名空间中变量的值


### 五、理解global和nonlocal

#### 1、简述

- global

  global语句用来声明一系列变量，这些变量会引用到当前模块的全局命名空间的变量，如果，如果该变量没有定义，也会在全局空间中添加这个变量。

- nonlocal

  - nonlocal是Python3.2引入的
  - Python2.7中还没有nonlocal语句
  - nonlocal语句用来声明一系列的变量
    - 这个声明会从声明处从里到外的namespace去搜索这个变量，直到模块的全局域（不包括全局域）
    - 找到了则引用这个命名空间的名字和对象
    - 若作赋值操作，则直接改变外层域中的这个名字的绑定
  - nonlocal语句声明的变量不会找在当前的scope的namespace字典中加入一个key-value对，如果在外层域中没有找到，则会报错

  ##### 案例理解

  ```python
  def test():
      def do_local():
          spam = "local spam"
          
      def do_nonlocal():
          nonlocal spam #这里并没有在局部空间do_nonlocal创建一个变量,而是引用了外层的“test spam”
          spam = "nonlocal spam"
          
      def do_global():
          global spam #在全局中声明一个变量
          spam = "global spam" 
          
      spam  = "test spam"
      do_local()
      print("after    do_local:", spam) #输出：after    do_local: test spam
      do_nonlocal()
      print("after    do_nonlocal:", spam) #输出：after    do_nonlocal: nonlocal spam
      do_global()
      print("after    do_global:", spam) #输出：after    do_global: nonlocal spam
      
  test()
  print("global    spam:", spam) #输出：after    do_local: test spam
  ```

  ​

##### 输出结果

```python
after    do_local: test spam
after    do_nonlocal: nonlocal spam
after    do_global: nonlocal spam
global    spam: global spam
```





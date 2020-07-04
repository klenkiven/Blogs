# Python的一些基础整理

# 一、注释

## 单行注释

+ 以`#`号开头，右侧的所有代码，当作说明文字，而不是真正的执行程序，起到辅助说明的作用

+ 单行注释可以自己单独占一行

+ 单行注释可以直接放在一行的最后面

  ```python
  # 这是一条单行注释
  ```

## 多行注释



+ 如果希望编写的注释信息很多，一行无法显示，就需要多行注释

+ 多行注释使用一对 **三个连续的引号**（单双引号都可以）

  ```python
  """
  这些就是
  多行注释
  """
  ```

# 二、数据类型

## 布尔值

布尔值和布尔代数的表示完全一致，一个布尔值只有`True`、`False`两种值，要么是`True`，要么是`False`，在Python中，可以直接用`True`、`False`表示布尔值（请注意大小写），也可以通过布尔运算计算。

布尔运算：与(`and`)、或(`or`)、非(`not`)

+ 这里注意**只能用关键字**进行逻辑运算
+ `True and True` (对)
+ `True && True` (错)

## 整数

Python可以处理**任意大小**的整数。这里和其他C like语言区别一下，python对于整数没有大小要求。也就是不存在int long等等数据类型。

## 浮点数

这个也没说表示大小，表示方法和C和Java表示方法差不多，运算起来也差不多，四个字大同小异。

Python的浮点数也没有大小限制，但是超出一定范围就直接表示为`inf`（无限大）。

## 字符串

字符串是以单引号`'`或双引号`"`括起来的任意文本。字符串里面可以包含**转义字符**。

代码示例：

```python
>>> print('I\'m ok.')
I'm ok.
>>> print('I\'m learning\nPython.')
I'm learning
Python.
>>> print('\\\n\\')
\
\
```

### 使用 `r''` 来减少使用转义字符

如果字符串里面有很多字符都需要转义，就需要加很多`\`，为了简化，Python还允许用`r''`表示`''`内部的字符串默认不转义。

代码示例：

```python
>>> print('\\\t\\')
\       \
>>> print(r'\\\t\\')
\\\t\\
```

### 使用文本块（BTW Java14支持文本块了哦！）

如果字符串内部有很多换行，用`\n`写在一行里不好阅读，为了简化，Python允许用`'''...'''`的格式表示多行内容。

```python
>>> print('''line1
... line2
... line3''')
line1
line2
line3
```

### `r''`和文本块搭配起来更爽哦

```python
>>> print(r'''hello,\n
... world''')
hello,\n
world
```

## 空值

空值是Python里一个特殊的值，用`None`表示。`None`不能理解为`0`，因为`0`是有意义的，而`None`是一个特殊的空值。

## 还有更多

列表(list)、元组(tuple)、字典(dict)、集合(set)

# 三、变量

这种变量本身类型不固定的语言称之为*动态语言*，与之对应的是*静态语言*。静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。

python会自动匹配，变量的类型。

## 算术运算符

Python里面有加减乘除，取余之外，还有几种特殊的需要了解一下：

+ **取整**  `//`   22 // 10 = 2
+ **幂**     `**`    2 ** 3 = 8

当然了还要注意一下，python**没有自增自减**操作的，可以用二元运算符`+= 1`和`-= 1`来代替。

# 四、常量

所谓常量就是不能变的变量，比如常用的数学常数π就是一个常量。在Python中，通常用全部大写的变量名表示常量。

例如，`PI`

```python
PI =  3.14159265359
```

Python根本没有任何机制保证`PI`不会被改变，所以，用全部大写的变量名表示常量只是一个习惯上的用法，如果你一定要改变变量`PI`的值，也没人能拦住你。

## 在Python中整数的除法是精确的

`\`除法是准确的，因为两数相除以后，会得到的是浮点数。

```python
>>> 10 / 3
3.3333333333333335
```

即使是两数相除，得到的也是浮点数（个人觉得相当于是强制转换变量类型了）

```python
>>> 9 / 3
3.0
```

`\\`取整，这种做法显而易见得到的都是整数。永远都是整数。

```python
>>> 10 // 3
3
```

# 五、字符串

## 字符编码（了解即可）

>  字符的编码很麻烦，这里一般只要了解一下就行了呗。

+ ASCII码

  最早**只有127个字符被编码**到计算机里，也就是大小写英文字母、数字和一些符号，这个编码表被称为`ASCII`编码，比如大写字母`A`的编码是`65`，小写字母`z`的编码是`122`。

+ Unicode码

  Unicode把**所有语言都统一到一套编码**里，这样就不会再有乱码问题了。出现了把Unicode编码转化为“可变长编码”的`UTF-8`编码。UTF-8编码把一个Unicode字符根据不同的数字大小编码成1-6个字节，常用的英文字母被编码成1个字节，汉字通常是3个字节，只有很生僻的字符才会被编码成4-6个字节。如果你要传输的文本包含大量英文字符，用UTF-8编码就能节省空间。

## Python的字符串

> 在最新的Python 3版本中，字符串是以Unicode编码的

### 单个字符编码

+ `ord()`函数获取字符的整数表示
+ `chr()`函数把编码转化为对应的字符

```python
>>> ord('A')
65
>>> ord('中')
20013
>>> chr(66)
'B'
>>> chr(25991)
'文'
```

+ 思考：尝试使用`ord()`函数把单个字符排序？

  ```python
  a = 'a'
  b = 'S'
  c = 'A'
  ```

### 十六进制表示字符

如果知道字符的整数编码，还可以用十六进制这么写`str`：

```python
>>> '\u4e2d\u6587'
'中文'
```

### 字符串转`bytes`（编码encode）

由于Python的字符串类型是`str`，在内存中以Unicode表示，一个字符对应**若干个字节**(可能是长短不一的)。如果要在网络上传输，或者保存到磁盘上，需要**传输的是字节流**，就需要把`str`变为以字节为单位的`bytes`。

+ Python对`bytes`类型的数据用**带`b`前缀的单引号或双引号**表示：

  ```python
  x = b'ABC' # ASCII码可以直接这样写
  >>> x = b'中文'
    File "<stdin>", line 1
  SyntaxError: bytes can only contain ASCII literal characters.
  ```

+ 以Unicode表示的`str`通过`encode()`方法可以编码为指定的`bytes`

  ```python
  >>> 'ABC'.encode('ascii')
  b'ABC'
  
  # 在bytes中，无法显示为ASCII字符的字节，用\x##显示。
  >>> '中文'.encode('utf-8')
  b'\xe4\xb8\xad\xe6\x96\x87'
  
  # 很明显ascii码范围有限
  >>> '中文'.encode('ascii')
  Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
  UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)
  ```

  纯英文的`str`可以用`ASCII`编码为`bytes`，内容是一样的，含有中文的`str`可以用`UTF-8`编码为`bytes`。含有中文的`str`无法用`ASCII`编码，因为中文编码的范围超过了`ASCII`编码的范围，Python会报错。

### `bytes`转字符串（解码decode）

如果我们从网络或磁盘上**读取了字节流**，那么读到的数据就是`bytes`。要把`bytes`变为`str`，就需要用`decode()`方法.

```python
>>> b'ABC'.decode('ascii')
'ABC'
>>> b'\xe4\xb8\xad\xe6\x96\x87'.decode('utf-8')
'中文'
```

+ 如果`bytes`中包含无法解码的字节，`decode()`方法会报错

  ```python
  >>> b'\xe4\xb8\xad\xff'.decode('utf-8')
  Traceback (most recent call last):
    ...
  UnicodeDecodeError: 'utf-8' codec can't decode byte 0xff in position 3: invalid start byte
  ```

+ 如果`bytes`中只有一小部分无效的字节，可以传入`errors='ignore'`忽略错误的字节

  ```python
  >>> b'\xe4\xb8\xad\xff'.decode('utf-8', errors='ignore')
  '中'
  ```

### 计算字符串字符数量 & 字节数量

计算字符数量，当然是使用内置的`len()`函数啦！

>  这个函数可以计算一个集合里面的元素数量，比如统计列表，元组等等集合里面的元素数量。

+ 计算字符数量

  ```python
  >>> len('ABC')
  3
  >>> len('中文')
  2
  ```

+ 计算字节数量

  ```python
  >>> len(b'ABC')
  3
  >>> len(b'\xe4\xb8\xad\xe6\x96\x87')
  6
  >>> len('中文'.encode('utf-8'))
  6
  ```

## 字符串格式化

+ 方法一：使用 `%` 来实现，**类似于C语言**的格式化操作

  ```python
  >>> 'Hello, %s' % 'world'
  'Hello, world'
  >>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)
  'Hi, Michael, you have $1000000.'
  ```

+ 方法二：使用 `format()` 函数来实现，用传入的参数依次替换字符串内的占位符`{0}`、`{1}`……（**麻烦**）

  ```python
  >>> 'Hello, {0}, 成绩提升了 {1:.1f}%'.format('小明', 17.125)
  'Hello, 小明, 成绩提升了 17.1%'
  ```

# 六、流程跳转

## 条件判断

这个和C语言差不多，不过 `else if` 缩写为 `elif`

```python
if <条件判断1>:
    <执行1>
elif <条件判断2>:
    <执行2>
elif <条件判断3>:
    <执行3>
else:
    <执行4>
```

但是的话Python一定要注意**缩进的问题**，比如 `if` 下面的执行语句（可能不止一句）一定是相对于 `if` 有一个缩进量的。下面的循环语句也是如此，包括后面的函数等等，都要注意缩进问题。

第二点就是注意，不要把后面的冒号丢了。

## 循环语句

+ `for x in ...`循环

  1. 遍历list或者tuple元素

     ```python
     names = ['Michael', 'Bob', 'Tracy']
     for name in names:
         print(name)
     ```

  2. 计算1-10的整数之和

     ```python
     sum = 0
     for x in [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]:
         sum = sum + x
     print(sum)
     ```

  3. 使用 `range()` 函数简化过程

     ```python
     sum = 0
     for x in range(1, 11):
         sum = sum + x
     print(sum)
     ```

     >  `range()` 函数取的是左闭右开区间

+ `while` 循环

  和C语言大同小异。

  ```python
  # 计算100以内所有奇数之和
  sum = 0
  n = 99
  while n > 0:
      sum = sum + n
      n = n - 2
  print(sum)
  ```

## `break` 和 `continue`

+ break

  在循环中，`break`语句可以提前退出循环。

+ continue

  在循环过程中，也可以通过`continue`语句，跳过当前的这次循环，直接开始下一次循环。

> `break`语句可以在循环过程中直接退出循环，而`continue`语句可以提前结束本轮循环，并直接开始下一轮循环。这两个语句通常都*必须*配合`if`语句使用。

>  *要特别注意*，不要滥用`break`和`continue`语句。`break`和`continue`会造成代码执行逻辑分叉过多，容易出错。
# 3. 数据模型

## 3.1. 对象，值和类型

对象是 Python 的抽象数据。在一个Python程序中，所有数据都通过对象或对象之间的关系表示。（在某种意义上讲，和冯·诺依曼的“存储程序计算机”的模型一致，代码也是由对象所表示的。）

每一个对象都具有一个标识，一个类型和一个值。对象一旦建立，它的表示就永远不能改变了；你可以认为它是在内存中的地址。‘[is](https://docs.python.org/3/reference/expressions.html#is)’ 运算符用来可以比较两个对象的身份；‘[id](https://docs.python.org/3/library/functions.html#id)’ 可以获得一个整数形式的对象标示。

**CPython 的实现细节：**对于CPython，`id（x）` 是 `x` 存储的内存地址。

一个对象的类型决定了这个对象所支持的操作（例如，“有长度吗？”），并且还定义了针对该类型对象的可能值。[type（）](https://docs.python.org/3/library/functions.html#type) 函数返回一个对象的类型（这是一个对象本身）。正如它的身份一样，一个对象的*类型*也是不可以改变的。[[1]](https://docs.python.org/3/reference/datamodel.html#id4)

某些对象的*值*是可以改变的。那些值可以改变的对象被认为是*可变的*；那些值不可以改变的对象一旦创建就被称为不可变的。（属于不可变对象的容器在包括有可变对象的引用时，当可变对象改变了时，其实是可变的，但仍被看作是可变对象，因为它所包含的对相机集合不可变的，所以不可变对象和值不可变是不完全一样的，它更加微妙。）一个对象的可变性是由它的类型来确定的；例如，数值，字符串和元组是不可变的，而字典和列表是可变的。

对象不用显式地释放他们；然而，当这些对象变的不能访问时，它们可能当做垃圾被收集。一种处理方式是延迟垃圾收集或者完全忽略 —— 这是回收机制如何实现的质量问题，只要求尚能访问的对象不被回收。

**CPython 的实现细节：**CPython 当前实现使用一个引用计数机制和一个可选的循环垃圾延时检测机制，只要对象不可用了，就会回收大部分这样的对象，但不能保证回收中包含有循环引用。对于循环回收的详细控制情况，见 [gc](https://docs.python.org/3/library/gc.html#module-gc) 模块参考。其他不同的实现方式在 CPython 中可能会改变。 当对象变得不能实现时，不要依赖对象可以立马终结（所以你需要经常明确地关闭文件）。

注意使用实现的跟踪和调试工具时可能会保留本该回收的对象，也要注意使用语句 [‘try...](https://docs.python.org/3/reference/compound_stmts.html#try)[except’](https://docs.python.org/3/reference/datamodel.html) 也可能保留本该回收的对象。

有些对象包含有“外部”资源，像文件或窗口。可以相信在垃圾回收时这些资源也被释放，但因为垃圾回收不保证一定发生，这样的对象提供了显式的方法释放这些资源，通常是用 close() 方法。特别推荐使用这种方法释放包含外部资源的对象。[‘try...](https://docs.python.org/3/reference/compound_stmts.html#try)[finally’](https://docs.python.org/3/reference/compound_stmts.html#finally) 和 ‘[with](https://docs.python.org/3/reference/compound_stmts.html#with)’ 提供了这样的一个便利途径。  

有些对象包含了其它对象的引用；它们叫做*容器*。容器的例子有元组，列表和字典。引用作为容器值的一部分。大多数情况下，当我们谈及一个容器的值时，我们只是涉及了这个值，而不是所包含的对象；但是，当我们谈及容器对象的可变性的时候，就只是涉及被直接包含的对象的标识了。因此，如果一个不可变对象（如元组）包含了一个可变对象，那么只要这个可变对象的值变了则它的值也就改变了。

类型对对象的大多数行为都有影响。甚至在某种程度上对对象的标识也有重要的影响：对于不可变对象，计算新值的运算符可能实际上返回的是一个已经存在的具有相同类型和值的对象的引用，对于可变对象，只是不允许的。例如，在 `a=1; b=1` 之后，`a` 和 `b` 有可能指向一个具有值为1的对象，这依赖于实现，但 `c=[];d=[]` 之后，	`c` 和 `d` 可以保证是保存了两个不同的，独立的，新创建的空列表。（注意 `c=d=[]` 是把相同的对象赋予了 `c` 和 `d` 。）

##3.2. 标准类型层次

以下是 Python 内建类型的一个列表，扩展模块（ 用C 语言，Java，或者其他语言写成，取决于实现方式）可以定义其他类型。以后版本的 Python 可能会在这个类型层次中增加其他类型（例如：有理数，高效率存储的整数数组，等等），即使通过标准库将会提供这些类型。

有些类型描述中包括了一个列出“特殊属性”的段落。他们提供了一些访问实现的方法而不是作为一般目的使用。这些定义可能会在未来改变。

无 None  
这个类型具有单一值，也只有一个对象有这个值，这个对象可以通过内建名字 `None` 访问它在许多场合不是无值，例如它在那些没有返回值的函数中返回，其值为真值。

未实现 NotImplemented  
这个类型具有单一值，也只有一个对象有这个值，这个对象可以通过内建名字 `NotImplemented` 访问。如果数值方法和许多比较方法的操作数未提供其实现，他们就可能返回这个值。（解释器会试图扩展成其它操作，或者其它退化的擦做，它依赖于运算符）他的真值为真。

更多细节见 [Implementing the arithmetic operations](https://docs.python.org/3/library/numbers.html#implementing-the-arithmetic-operations) 。

省略 Ellipsis  
这个类型具有单一只，也只有一个对象有这个值。这个对象可以通过文字 `...` 或者内建名字 `Ellipsis` 访问。其真值为真。

数值型 [nubmbers.Numbers](https://docs.python.org/3/library/numbers.html#numbers.Number)  
它们由数值型字面值生成，或由算术运算和内置算术函数作为值返回。数值型对象是不可变的；一旦创建其值就不可以改变。Python 的数值型和数学上的关系非常密切，但要受到计算机的数值表达能力的限制。

Python 对整数，浮点数和复数区别对待：

[numbers.Integral](https://docs.python.org/3/library/numbers.html#numbers.Integral)  
描述了数学上的整数集（正数和负数）。

有2种整数类型：  

数值型整数 Integers ([int](https://docs.python.org/3/library/functions.html#int))  
数值型整数的表示范围在语言上是无限制的，只受限于你的内存（虚拟内存）。对于以移位和屏蔽为目的的运算，数值型正数被认为是二进制的形式，如果是负数，那么就被转换成二进制补码形式，符号位向左扩展。

布尔型整数 Booleans ([bool](https://docs.python.org/3/library/functions.html#bool))  
布尔型整数的真值是用假和真来表示的。对于布尔型对象的值只能通过`假`和`真`表示。布尔类型是整数类型的子类型，分别用数值 0 和 1 来表示，但是当返回值的类型是字符时，其分别用 `"False"` 或者 `"True"` 表示。

整数表示法的这个规则是为了使对负数操作时尽量有实际意义。

浮点数 numbers.Real ([float](https://docs.python.org/3/library/functions.html#float))  
本类型表示了机器级的双精度浮点数。是机器的体系结构和C实现的浮点数范围和控制溢出方法屏蔽你不需知道的细节。Python 不支持单精度浮点数，使用它的原因通常是减少CPU负荷和节省内存，但是这一节省被对象在Python中的使用开销所抵消，因此没有必要支持两种浮点数使语言变的复杂。

复数 numbers.Complex ([complex](https://docs.python.org/3/library/functions.html#complex))  
本类型描述了一对机器级的双精度浮点数，在浮点数中的警告内容也适用于复数类型。复数 `z` 的实部和虚部可以通过属性 `z.real` 和 `z.imag` 获得。

有序类型 Sequences  
本类型描述了用非负数索引的有限的有序集合。内建函数 [len()](https://docs.python.org/3/library/functions.html#len) 返回有序类型数据中的项数当有序类型数据的长为 *n* 时，索引号为 0,1，...,*n*-1。有序类型数据 *a* 中的项 *i* ,可以以 `a[i]` 表示。

有序类型也支持片断：`a[i:j]` 可以表示满足 *i* `<=` *k* `<` *j* 的所有项 `a[k]`。在使用这个表达式时，它具有相同的有序类型，这也隐含着索引重新从0开始计。

有些有序类型用第三个“步骤”参数来支持“扩展片断”：`a[i:j:k]` 选择所有项的索引 *x*,其中 `x = i + n * k`，*n* `>=` 0 并且 *i* `<=` *x* `<` *j*。

有序类型按照可变性分为两类：

不可变类型 Immutable sequences  
一个不可变对象一旦建立其值不可能再改变。（如果这个对象包括其它对象的引用，这个被引用的对象可以是可变对象并且其值可以变化；但为该不可变对象所直接引用的对象集合是不能变的。）

以下类型是不可变类型：

字符串 Strings  
字符串的序列值体现了 Unicode 的代码点，字符串中所有代码点的范围是 `U+0000 - U+10FFF`。Python 不支持 char 类型，相反，每个代码的字符串用长度`1`表示一个字符串对象。内建函数 [ord()](https://docs.python.org/3/library/functions.html#ord) 从字符串转换为代码点形成一个整数的范围 `0 - 10FFFF`，[chr()](https://docs.python.org/3/library/functions.html#chr) 将一个范围在 `0 - 10FFFF` 的整数对应长度`1`的字符串对象。[str.encode()](https://docs.python.org/3/library/stdtypes.html#str.encode) 可以使用给定的明文编码将 [str](https://docs.python.org/3/library/stdtypes.html#str) 转换成 [bytes](https://docs.python.org/3/library/functions.html#bytes)， 并且 [bytes.decode()](https://docs.python.org/3/library/stdtypes.html#bytes.decode) 可以实现相反转换。

元组 Tuples  
元组对象的项可以是做任意的 Python 对象。具有两个或多个项的元组是用逗号分隔开表达式列表，只有一项的列表（独元）其项可以后缀一个逗号使其成为满足要求元组要求的表达式（一个表达式本身不能创建一个元组,因为圆括号本身用于表达式的分组）。一个空元组可以以一对空圆括号表示。

字节 Bytes  
字节对象是不可变的数组，每个项由8位字节组成，由整数范围为 0 <= x < 256 表示。字节文字（如 `b'abc'`)和内建函数 [bytes()](https://docs.python.org/3/library/functions.html#bytes) 可以被用来构建字节对象。同时，字节对象可以经由 [decode()](https://docs.python.org/3/library/stdtypes.html#bytes.decode) 方法被解码为字符串。

可变对象 Mutable sequences  
可变对象可以在其创建后改变，其下标表示和片断表示可以作为赋值语句和 [del](https://docs.python.org/3/reference/simple_stmts.html#del) （删除）语句的对象。

目前只有一种可变类型。

列表 Lists  
列表对象的项可以是任意类型的 Python 对象。列表对象是在方括号之间的用逗号分开的表达式表。（注意，形成长度为0或者1的链表不要求特别的写法）。

字节数组 Byte Arrays  
字节数组对象是一个可变的数组，它们是由内建函数 [bytearray()](https://docs.python.org/3/library/functions.html#bytearray) 构造创建。除了是可变的（和无序的），字节数组以其他方式提供相同的界面和功能不变的字节对象。

扩展模块 [array](https://docs.python.org/3/library/array.html#module-array) 提供一个额外的可变序列类型模板， [collections](https://docs.python.org/3/library/collections.html#module-collections) 模块。

集类型 Set types  
集类型对象具有无序性，有限集的独一无二性，和不变性。因此，它们不能被任何下标所搜引，然而，它们可以进行遍历，并有内建函数 [len()](https://docs.python.org/3/library/functions.html#len) 返回遍历数。集的常见应用包括快速加入测试，删除重复虚了，和计算数学操作，如十字路口，并，差异和对称查分。

对于集元素，同样的不变形属性作为申请字典关键字。注意，数字类型遵从数字比较的正常规则：如果两个数字比较后相等（比如`1`和`1.0`），那么会只有一个数字包含在集合里。

目前有两种原始集类型：

集 Sets  
这种代表可变的集合，它们是由内建函数 [set()](https://docs.python.org/3/library/stdtypes.html#set) 构建，并且之后可变用多发方法去修改的，比如 [add()](https://docs.python.org/3/library/stdtypes.html#set.add)。

冻结集 Frozen sets  
这种代表不可变的集合，它们是由内建函数 [frozenset()](https://docs.python.org/3/library/stdtypes.html#frozenset) 构建，由于冻结集的不可变性以及无序性 *[hashable](https://docs.python.org/3/glossary.html#term-hashable)* ，它也可以用来作另外一个集合的元素或者字典的关键字。


映射类型 Mappings  
本类型描述了可以是由任意类型作索引的有限对象集合。下标表示法 `a[k]` 从映射类型对象中选择一个由 `k` 索引项，它们可以用作赋值语句和 [del](https://docs.python.org/3/reference/simple_stmts.html#del) 语句的目标。内建函数 [len()](https://docs.python.org/3/library/functions.html#len) 返回映射对象的项的个数。

当前只有一个内置的映射类型：

字典 Dictionaries  
本类型表达了几乎是任意类型对象都可作索引的有限对象集合。不可接受的键值仅仅是那些包括有列表和字典的值，或那些是通过值比较而不是通过对象标识符比较的类型的值。其原因是字典的高效实现要求键的哈希值保持不变。用于键值的数值型在比较时使用通常的规则：如果两值相等（如：`1`和`0`），那么它们可以互换地索引相同的字典项。

字典是可变的，它们由 ... 语法创建（详见 *[Dictionary displays](https://docs.python.org/3/reference/expressions.html#dict)* ）。

扩展模块 dbm.ndbm 和 dbm.gnu 提供了另外的映射类型的例子， 同 [collections](https://docs.python.org/3/library/collections.html#module-collections) 模块。

可调用类型 Callable types  
这是一个可用的函数调用操作类型（详见 *[Calls](https://docs.python.org/3/reference/expressions.html#calls)*），应用于：

用户自定义函数 User-defined functions  
一个自定义函数对象由函数定义（详见 *[Function definitions](https://docs.python.org/3/reference/compound_stmts.html#function)*）。创建在函数调用时应该与定义时的形式参数相同数目参数。

特殊属性：

_ _ doc _ _ 是函数的文档串，如果无效就是 `None`，不可以被子类继承，可写；  
[_ _ name _ _](https://docs.python.org/3/reference/import.html#__name__) 是函数名称，可写；  
_ _ qualname _ _ 是函数的限定名称，在版本 *3.3.* 中的新属性，可写；  
_ _ module _ _ 定义在函数模块里的定义，如果无效就是 `None`，可写；  
_ _ defaults _ _ 一个元组包含默认参数值，这些参数是默认的，如果没有默认参数值就是 `None`，可写；  
_ _　code _ _ 一个编译后的代码对象，可写；  
_ _ globals _ _ 是一个引用，指向保存了函数的全局变量的字典——如果在函数所在模块中定义了全局变量的话，只读；  
_ _ dict _ _ 包含了支持任意函数属性的命名空间，可写；  
_ _ closure _ _ 要么是 `None`，要么是包括有函数自由变量绑定的单元的元组，只读；  
_ _ annotations _ _ 是一个包含注释的字典参数，字典的关键字是参数名字，并且如果有提供的话，返回注释 `return`，可写；  
_ _ kewdefaults _ _ 是一个只包含默认关键字的字典，可写；     


大部分的“可写”属性需要检查分配的值的类型。

函数对象也支持获取和设置任意属性，它可以被使用，例如，元数据功能。常规属性一样用于获取和设置这些属性。*注意，当前的实现只支持函数属性对用户定义的函数，功能属性内置函数可能在未来才会支持。*

关于函数的定义可以由它的代码对象得到；见下面关于内部对象的描述。

实例方法 Instance methods

实例方法合并了一个类，一个类实例和任何可调用对象（一般是用户自定义函数）。

特殊的只读属性： _ _ self _ _ 是类实例对象，_ _ func _ _ 是函数对象； _ _ doc _ _ 是其方法的文档（同 _ _ func _ _._ _ doc _ _）; [_ _ name _ _](https://docs.python.org/3/reference/import.html#__name__) 是方法名（同 _ _ func _ _._ _ name _ _）； _ _modele _ _ 是定义方法的模块名，如果无效则为无 None。

方法也支持访问（不是设置）的其实际所调用的函数的任意属性。

如果类的属性是一个用户定义函数对象或类方法对象，那么获得类属性的用户可以创建自定义方法对象（也可能通过这个类的一个属性）。

当用户自定义方法对象采用一个用户自定义函数对象来创建时，这个对象来自于它的一个实例类，它的 _ _ self _ _ 属性是个实例，并且该方法称为是捆绑的。新方法的 _ _ func _ _ 属性是原始函数对象。 

当用户自定义方法对象采用另外一个类或实例的方法对象创建时，其特性与函数对象创建时相同的，不同之处是这个 _ _ func _ _ 新类的属性不是原始函数对象，而是它的 _ _ func _ _ 属性。

当调用实例方法对象时，实际调用的是函数（ _ _ func _ _ ），同时在参数列表前插入类实例（ _ _ self _ _ ）。比如：当类 C 包含了一个函数定义 f()，并且 `x` 是 C 的一个实例，调用 `x.f(1)` 相当于调用 `C.f(x,1)`。

当一个实例方法对象时由一个类方法对象衍生而来时，存储在 _ _ self _ _ 的“类实例”实质上将会是类本身，所以不论调用 `x.f(1)` 或者 `C.f(1)` 相当于调用 `f(C,1)`, 其中 `f` 是实际函数。

注意，由函数对象转变到实例方法对象，每次都会检索实例中的属性。在某些情况下，卓有优化是成效的优化是将属性分配给一个局部变量并调用它。同时应注意，这种转变只会发生在在用户自定义函数上，检索其他的可调用对象（以及不可调用对象）不需要这种转变。同样重要的是要注意，具有类实例属性的用户自定义函数不能转换成绑定方法；*当且仅当*发生在函数是一个类的属性时。

生成器函数 Generator functions  
一个使用 [yield](https://docs.python.org/3/reference/simple_stmts.html#yield) 的语句（见 [yield](https://docs.python.org/3/reference/simple_stmts.html#yield) 语句 ）的方法或函数，它叫做生成器函数。这样的函数，在返回后，通常返回一个迭代子对象，一个可以用于执行函数的对象。调用对象的 [iterator.__next__()](https://docs.python.org/3/library/stdtypes.html#iterator.__next__) 方法会引起函数的执行，直到其使用 [yield](https://docs.python.org/3/reference/simple_stmts.html#yield) 语句返回一个值。当函数在执行到 [return](https://docs.python.org/3/reference/simple_stmts.html#return) 语句时，或在末尾结束时，会抛出异常 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration)，此时迭代器也到达了返回值集合的结尾。

内建函数 Built-in functions  
一个内建函数就是一个 C 函数的包装，例如内建函数 len() 和 math.sin() （math 是标准的内建模块）。参数的类型和数目都由C函数检测，特殊的只读属性： _ _ doc _ _ 是函数的文档串， 或者无效为无 `None`； [_ _ name _ _](https://docs.python.org/3/reference/import.html#__name__) 是函数名；_ _ self _ _ 设为 `None`（见下述）； _ _ module _ _ 是模块名，或者无效为无 `None`。

内建方法 Built-in methods  
这实际上是内建函数的一个不同的包装而已，此时传递给C函数一个对象作为隐含的参数。例如，内建方法 `alist.append()` 假定 *alist* 是一个列表对象，此时特殊只读属性 _ _ self _ _ 指定为对象 *alist*。

类 Classes  
类是可调用的。这些对象通常是自己的新实例的工厂，但是变化可能是重写 [_ _ new _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__new__) 类对象。传送调用的参数到 [_ _ new _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__new__)， 而且典型情况是传送到 [_ _ init _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__init__) 来初始化成新实例。

类实例 Class Instances  
任意类实例可以通过定义一个 [_ _ call _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__call__) 方法类去调用。  

模块 Modules  
模块是 Python 代码的基本组成单元，并由导入系统 [*import system*](https://docs.python.org/3/reference/import.html#importsystem) 创建，可以通过 [import](https://docs.python.org/3/reference/simple_stmts.html#import) 语句或者诸如函数 [importlib.import_module()](https://docs.python.org/3/library/importlib.html#importlib.import_module) 和内建函数  [_ _ import _ _ ()](https://docs.python.org/3/library/functions.html#__import__)。一个模块有一个名字空间，用字典对象实现（在模块定义中的函数可以通过 `_ _ globals _ _` 属性来访问这个字典）。把对属性的访问翻译成查找这个字典。例如，`m.x` 等价于 `m. _ _ dict _ _ ["x"]`。模块对象并不包含用来初始化该模块的代码对象（因为一旦初始化完成它就不再需要了）。

属性的赋值更新模块的空间名字，例如，`m.x = 1` 等价于  `m. _ _ dict _ _ ["x"]`。

特殊只读属性： _ _ dict _ _ 是字典形式的模块名字空间。

**Python 实现细节：**由于 CPython 清理模块字典的方式，当模块超出范围时，即使字典还在引用，字典模块也将被消除。为避免这种情况发生，在直接使用字典时应该备份字典或将其保留在模块附近。

预定义的可写属性： [_ _ name _ _](https://docs.python.org/3/reference/import.html#__name__) 是模块名； _ _ doc _ _ 是模块的文档串，如果无效即为 `None`； _ _ file _ _ 是被装载模块的文件路径名， 因为C模块不提供些属性，该模块已连接至解释器中，对于那些从共享库装载的模块，这个属性是那些共享库的路径。

自定义类 Custom classes  
自定义类类型由类定义创建（见类定义  *[Class definitions](https://docs.python.org/3/reference/compound_stmts.html#class)*）。一个类有一个用字典对象实现的名字空间，类属性的访问可以转换成对该字典的查找，例如，`C.x` 被解释成 `C. _ _ dict _ _ ["x"]`（虽然有许多允许定位属性的挂钩）。当属性名未找到时，查找是深度优先的。这种基本类搜索使用 C3 方法解析正确行为，即使在‘钻石’继承结构那里有多重继承回到共同祖先的路径。更多细节在 Python 中使用的 C3 MRO，参考随着 2.3发布的文档里 [https://www.python.org/download/releases/2.3/mro/](https://www.python.org/download/releases/2.3/mro/)。

当一个类属性引用（对于类 C 说）应该让步于一个类方法对象时，它会变成一个实例方法对象，其属性 _ _ self _ _ 是 C。当它应该让步于一个静态方法对象时，它会变成静态方法对象的封装对象。见另一种方式的实现描述 *[Implementing Descriptors](https://docs.python.org/3/reference/datamodel.html#descriptors)* ，从一个类检索属性可能不同于那些实际包含在其字典 _ _ dict _ _。

类属性的赋值会更新类的字典，而不是基类的字典。

一个类对象可以创建(见上)，这样会产生一个类实例(下述)。

特殊属性： [_ _ name _ _](https://docs.python.org/3/reference/import.html#__name__) 是类名； _ _ module _ _ 是类所定义的模块名； _ _ dict _ _ 是包括类名字空间的字典； [_ _ bases _ _](https://docs.python.org/3/library/stdtypes.html#class.__bases__) 是一个元组（可能是空或独元），包括其基类，以基类列表中他们的排列次序出现；　_ _ doc _ _ 是类的文档串，如果无效，它就是无 None。

类实例 Class instances

类实例可以通过调用一个类对象来创建，类实例可以有一个用字典实现的名字空间，它只由搜索属性范围的第一个结果构成。如果属性没在那找到，并且实例的类有那个名字的属性，就继续在类属性中寻找。如果找到的是一个用户自定义函数对象(而不是其它情况)，它被转换成一个实例方法对象，它的 _ _ self _ _ 属性是个实例。 静态方法和类方法对象也可以转换，见前文在 “Classes” 中所述。另一种方式，通过实例检索类的属性可能不同于对象实际存储在类 _ _ dict _ _ 中的，见 *[Implementing Descriptors](https://docs.python.org/3/reference/datamodel.html#descriptors)* 部分。如果没有类属性找到，并且对象的类有方法 [_ _ getattr _ _](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) ()，那就调用它来满足查找请求。

属性赋值和删除会更新实例目录，而不是类的字典，如果类具有方法 [_ _ setattr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__setattr__) 和 [_ _ delattr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__delattr__)，则它们会在更新类实例属性时调用，而不是实例字典直接更新。

类实例可以伪装成数字，序列，或者具有方法映射成某些特别的名字。 见  *[Special method names](https://docs.python.org/3/reference/datamodel.html#specialnames)* 部分。

特殊属性： [_ _ dict _ _](https://docs.python.org/3/library/stdtypes.html#object.__dict__) 是字典属性；[ _ _ class _ _](https://docs.python.org/3/library/stdtypes.html#instance.__class__) 是实例属性。

输入/输出对象（或称文件对象） I/O objects  
一个文件对象 *[file object](https://docs.python.org/3/glossary.html#term-file-object)* 描述了一个开放的文件。 有多种可用的快捷方式来创建文件对象： 内建函数 [open()](https://docs.python.org/3/library/functions.html#open),以及 [os.open()](https://docs.python.org/3/library/os.html#os.fdopen)，[os.fdopen()](https://docs.python.org/3/library/os.html#os.fdopen)，和套接字对象方法 [makefile()](https://docs.python.org/3/library/socket.html#socket.socket.makefile) （或许也可以通过其他扩展模块的函数或方法）。

对象 `sys.stdin`，`sys.stdout`，和 `sts.stderr` 被相应的初始化成解释器的标准输入流，标准输出流，和标准错误输出流；它们都是开放文本模式，因此遵循抽象类 [io.TextIOBase](https://docs.python.org/3/library/io.html#io.TextIOBase) 定义的接口。

内部类型 Internal types  
少部分由解释器内部使用的类型，开放给了用户。它们的定义可能会在未来的解释器版本中改变，但都会在这儿提及。

代码对象 Code objects  
代码对象表达了*字节编译过*的可执行 Python 代码，或者叫[字节码](https://docs.python.org/3/glossary.html#term-bytecode)。代码对象和函数对象的不同在于函数对象包括了一个外在的全局变量引用（其所定义的模块），而代码对象不包括上下文。默认参数值存入函数对象中，而代码对象则不（因为它们的值由运行时计算）。不像函数对象，代码是不可改变的，并且不包括对可变对象的引用。

特殊属性： co _ name 给出了函数名；co _ argument 是位置参数的数目(包括有默认值的参数)；co _ nlocals 是函数中局部变量的数目。co _ varnames 是一个包括局部变量名的元组（从参数名开始）；co _ callvars 是一个元组，包括由嵌套函数所引用局部变量名；co _ freevals 包括了既不是局部变量也不是全局变量的；co _ code 包括了编译过的字节码指令序列的字符串；co _ consts 包括字节码中使用的字面值的元组；co _ names 包括字节码中使用的名字的元组；co _ filename 包括着编译过的字节码文件名；co _ firstlineno 是函数首行行号；co _ lnotab 是一个字符串，是字节码偏移到行号的映射（详见解释器代码）；co _ stacksize 是所需要的堆栈尺寸（包括局部变量）；co _ flags 是一个整数，其解释成为许多解释器的标志。

以下标志位由 co _ flags 定义：如果函数使用 `*argument` 语法来接受任意数目的位置参数，就置位 `0x04`；如果函数使用 `*keywords` 语法来接受任意数量的关键字参数，就置位 `0x08`；如果函数是个生成器，就置位 `0x20`。

未来功能还声明（`from __future__ import division`）在 co _ flages 中使用位表明编译代码对象是否启用了一个特定的嵌入式功能：如果编译函数启用了嵌入式功能，则置位 `0x2000`；而在老版的 Python 中使用位 `0x10` 和 `0x1000`。

在 co _ flages 中的其他为预留为内部使用。

如果一个代码对象描述一个函数，则 co _ consts 的第一项是该函数的文档串，如果未定义，它就是无 `None`。

堆栈结构对象 Frame objects  
堆栈结构对象描述了可执行结构，它们会在跟踪回溯对象中出现（下述）。

特殊只读属性：f _ back 指出前一个堆栈结构（向着调用者的方向），如果位于堆栈的底部它就是 `None`；f _ code 指出本结构中能执行的代码对象。f _ locals 是用于查找局部变量的字典；f _ globals 用于全局变量；f _ builtin 用于内建名字；f _ lasti 给出精确的指令（是一个代码对象的字符串的索引）。

特殊可写属性：f _ trace 如果非空，就是从每个源代码行的开始处调用的函数（用于调试器）;f _ lineno 给出行号——写这个函数从内部跟踪的行（只限于最底层堆栈）。调试器可以通过编写 f _ lineno 实现跳转命令(即设置下一条语句)。

堆栈结构对象支持一种方法：

  
堆栈结构清除 frame.clear()  
这种方法清除所有局部变量的引用。同时，这个结构堆栈属于一个发生器，那么会终结这个发生器。这有助有终止堆栈结构对象循环（比如，供以后使用异常捕获和回溯存储）。

如果当前结构正在执行，上报 [RuntimeError](https://docs.python.org/3/library/exceptions.html#RuntimeError)。

*新版本3.4*。

跟踪回溯对象 Traceback objects  
跟踪回溯对象描述一个异常的栈回溯，跟踪回溯对象在异常发生时创建，在展开可执行堆栈搜索异常处理器时，每个展开层的跟踪回溯对象插进当前跟踪回溯对象的前面。在遇到异常处理器时，跟踪回溯对象也对程序有效了。（见 *[try](https://docs.python.org/3/reference/compound_stmts.html#try)* 语句） 它可以由 `sys.exc _ info()` 返回的元组的第三项访问到。后一种是推荐的接口，因为它也可以使用多线程的程序中工作良好。当程序中未包括适当的异常处理器, 跟踪回溯对象就被打印到标准错误输出流上。如果工作在交互方式上，它也可以通过 `sys.last _ traceback`。

特殊只读属性：tb _ text 是堆栈的下一层（向着异常发生的那一层结构），或者如果没有下一层，就为 `None`。 tb _ frame 指出当前层次的可执行结构；tb _ lineno 给出异常发生的行号；tb _ lasti 指出精确的指令；如果异常发生在没有 except 或 finally 子句匹配的 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 语句中的话，这里的行号和指令可能与结构中的行号和指令不同。

片断对象 Slice objects  
片断对象用片段方法 [_ _ getitem() _ _](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 来描述。他们也是通过内建函数 [slice()](https://docs.python.org/3/library/functions.html#slice) 来创建。

特殊只读属性：start 是下界，step 是上界，step 是步进值，如果在片段中忽略了它，就是 `None`。这些属性可以是任意类型的。

片断对象支持一种方法：

片断索引 slice.indices(self, length)  
这个方法接受一个整数参数 *length* 并计算片断信息，切片对象用于描述 *length* 项序列。它返回一个元组包含为三个整数，分别代表*开始*索引，*停止*索引启动和偏度的*步进*或*步长*，处理缺失或界外指数的方式与普通片断一致。处理缺失或界外索引的方式与普通片断一致。

静态方法对象 Static method objects  
静态方法对象提供一种战胜上述转换函数对象方法。静态方法对象包装在其他对象周围，通常是一个用户定义的方法对象。当一个静态方法对象从一个类或一个类实例检索时，返回的对象实际上是包装对象，它不受任何进一步的转换。它本身并不是调用静态方法对象,尽管他们通常包装对象，但并不能自我调用。静态方法创建的对象是内建函数 [staticmethod()](https://docs.python.org/3/library/functions.html#staticmethod) 构造。

类方法对象 Class method objects  
类方法的对象是一个在其他对象周围的包装器，就像一个静态方法对象，它改变从类和类实例中检索的方式。上述的这种类方法对象的方式在“用户自定义方法”中有体现。 用内建函数 [classmethod()](https://docs.python.org/3/library/functions.html#classmethod) 构建器来创建类方法对象。

##3.3. 特殊方法名

一个类可以实现以特殊句法才调用的某些操作（例如算术运算，下标操作及片断操作），这是通过以特殊名字定义方法来实现的。这是 Python 的操作符重载方法，允许类来定义自己的行为对语言操作符。例如，如果一个类定义了的方法名为 [_ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__)，并且 x 是这个类的实例，那么 `x[i]` 就等价于 `type(x). _ _ getitem _ _ （x,i）`。除了所提及的地方，试图执行没有适当的方法定义的操作会引起一个异常的抛出（典型的 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) 和 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)）。

当实现一个模拟任何内建类型的类时，重要的地方在于模拟的程度只要使对象模拟时有效就行了。例如，某些有序类型的对象可能在单独提取某引起值时有效，但在使用片断时是没有意义的。（这样的一个例子是在W3C的文档对象模型中的NodeList接口。）

###3.3.1. 基本定制

object. _ _ new _ _ (cls[,...])  
在实例创建调用时， _ _ new _ _ 是一个静态方法（特殊情况下不需要声明），它要求类的实例作为第一个参数。剩余的参数传递到对象构造表达式（调用到类），_ _ new _ _ 返回值应该是一个新的对象实例（通常是 *cls* 的实例）。

典型的实现方式是创建一个类的新实例，通过使用具有适当参数的 super(currentclass，cls). _ _ new _ _ (cls[, ...]) 调用超级类的 _ _ new _ _ ，然后根据需要，在返回之前修改新创建的实例。

如果 _ _ new _ _ 返回一个 cls 实例，那么新实例的方法 _ _ init _ _ () 将会像 `_ _ init _ _ (self[,...])`被调用, 这时候，新实例和剩余的参数被传递给 _ _ new _ _ () 相同。

如果 _ _ new _ _ 不返回一个 cls 实例，那么就不会调用这个新实例的 _ _ init _ _ ()。

_ _ new _ _ 的主要目的是允许子类不可变类型（如整数，字符或元组）定制实例创建。也通常覆盖在自定义原类中来创建自定义类。

object. _ _ init _ _ (self[, ...])  
实例在创建（通过_ _ new _ _ 创建）之后才会被调用，但之前返回给调用者。构造函数的参数是指传递表达式。如果一个基本类具有方法 _ _ init _ _ ()或派生类的方法  _ _ init _ _ ()，必须显式的调用它，以确保实例部分的基本类初始化；比如: `BaseClass. _ _ init _ _ ( self, [args...])`。

因为 _ _ new _ _ 和 _ _ init _ _ 同时作用来构建对象 （ _ _ new _ _ 创建它， _ _ init _ _ 来定制），没有 non-'None' 值可能通过 _ _ init _ _ 来返回，这样做会导致在运行时上报 Typeerror 。

object. _ _ del _ _ (self)  
在实例被删掉时被调用，也叫作析构器。 如果其基类也具有 del() 方法，继承类应该在其 del() 显式地调用它，以保证适当地删掉它的父类部分。注意，在 del() 通过创建新的引用来推迟删除操作是允许的，但这不是推荐的做法。它然后在最后这个引用删除时被调用。不能保证在解释器退出时，仍存在的对象一定会调用 del() 方法。

**注意：** `del x` 不直接调用 `x.del()` —— 前者将引用计数减一，而后者仅仅在引用计数减到零时才被调用。有一些经常使用的方法。 可以防止引用计数减为零：对象之间的循环引用（例如，一个双链表或一个具有父结点和子结点指针的树形数据结构）；对某函数堆栈结构上的引发异常的对象进行引用（跟踪回溯对象保存在 	`sys.exc_info()[2]` 是以保持其有效）；或者在交互模式下对某函数堆栈上的引发了没有处理器的异常的对象做引用（跟踪回溯对象保存在 `sys.last_traceback` 中以保持其有效）。第一种方法仅能通过显式地破坏循环才能恢复，后两种情况，可以通过将 `sys.last_traceback` 赋给None来恢复。仅当循环引用检测器选项被允许时（这是默认的）循环引用才能为垃圾回收机制所发觉, 但这只在没有相关的 Python 级的 `del_()` 方法时才会被清除。关于 `del_()` 方法怎样处理循环引用的进一步信息参见 [gc](https://docs.python.org/3/library/gc.html#module-gc) 模块，该处具体地描述了垃圾回收。

**告警：**因为调用 `del_()` 方法的不确定性，在它执行时的异常会被忽略，而只是在 sys.stderr 打印警告信息。另外，当某模块被删除，相应的 `del_()` 方法调用时（例如，程序退出时），有些为 `del_()` 方法所引用的全局名字可能已经删除了。由于这个原因， `del_()` 方法应该对其外部要求保持到最小。 Python1.5 可以保证以单下划线开始的全局名字在其它全局名字被删除之前从该模块中被删除；如果没有其它对存在的全局名字的引用，这会对确定那些已导入的模块在调用 `del_()` 之后有那些还是有效
的时是有所帮助的。

object. _ _ repr _ _ (self)  
由 [repr()](https://docs.python.org/3/library/functions.html#repr) 内建函数调用或者在串转换（保留引号）时用来计算对象的“正式说明”字符串，尽可能的，这应该是一个能以相同的值重建一个对象的有效的 Python 表达式（在给定的适当有环境下），如果这不
可能的，也应该是返回一个“...某些有用的信息...”形式的字符串。 返回值必须是一个字符串。如果类定义 [repr()](https://docs.python.org/3/library/functions.html#repr) 而不是 [_ _ str _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__str__), 那么当一个“非正式”字符串表示的类的实例是必须的时候， [repr()](https://docs.python.org/3/library/functions.html#repr) 也会被使用。  

本函数典型地用法是用于调试，所以这个串表述成详尽并无歧义是十分重要的。

object. _ _ str _ _ (self)  
由 [str(object)](https://docs.python.org/3/library/stdtypes.html#str) 内建函数调用，或由 [format()](https://docs.python.org/3/library/functions.html#format) 和 [print()](https://docs.python.org/3/library/functions.html#print) 语句来计算该对象的“非正式”串描述。返回值必然是个字符串 *[string](https://docs.python.org/3/library/stdtypes.html#textseq)* 对象。

这与 [object. _ _ repr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__repr__) 是不同的，因为它不
要求必须为一个有效的 Python 表达式：可以采用一个更通俗或更简洁的表述方式。

这种典型的实现方法可以通过内建类型 [object](https://docs.python.org/3/library/functions.html#object) 调用 [object. _ _ repr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__repr__) 来定义。

object. _ _ bytes _ _ (self)  
由 [format()](https://docs.python.org/3/library/functions.html#format) 内建函数（和扩展，类 [str](https://docs.python.org/3/library/stdtypes.html#str) 的 [strformat()](https://docs.python.org/3/library/stdtypes.html#str.format) 方法）调用，它产生一个“格式化”对象的字符串表示。 `format_spec` 参数是一个包含了要求格式化选项描述的字符串。  `format_spec` 参数的解释是实现 [_ _ format _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__format__)，然而大多数类代表格式化内置的类型之一，或者使用类似的格式化的语法。

详见 [Format Specification Mini-Language](https://docs.python.org/3/library/string.html#formatspec) 标准格式化特征的描述。

它的返回值必须是一个字符串对象。

在版本*3.4*中的改变：如果传递一个空的字符串，`对象`本身的 _ _ format _ _ 方法会提出类型错误 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)。

object. _ _ lt _ _ (self,other)  
object. _ _ le _ _ (self,other)  
object. _ _ eq _ _ (self,other)  
object. _ _ ne _ _ (self,other)  
object. _ _ gt _ _ (self,other)  
object. _ _ ge _ _ (self,other)   
它们称为“厚比较”方法集, 具体的方法名和相应的运算符的对应关系如下： `x<y` 调用 `x. _ _ lt _ _ (y)`，`x<=y` 调用 `_ _ le _ _ (y)`，`x==y` 调用 `_ _ eq _ _ (y)`，`x！=y` 调用 `_ _ ne _ _ (y)`，`x>y` 调用 `_ _ gt _ _ (y)`，`x>=y` 调用 `_ _ ge _ _ (y)`。

如果它对给定的参数对没有实现操作, 一个厚比较方法可以返回 `NotImplemented`。按照惯例，一个成功的比较会返回 `False`和  `True`。不论如何，这些方法可以返回任何值，因此如果比较操作在布尔型文本中使用（如，`if` 条件语句），Python 将在值上调用 [bool()](https://docs.python.org/3/library/functions.html#bool) 来决定结果是真还是假。

有隐含的比较运算符之间的关系。 `x==y` 为真相并不意味着 `x!=y` 为假。相应的，当定义 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 时，同时也应该定义 [_ _ ne _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__ne__)，比便于达到预期的操作。见支持自定义比较和可作为字典关键字使用的创建 [hashable](https://docs.python.org/3/glossary.html#term-hashable) 的 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__)。

对于这些方法是没有互换参数)版本的（在左边参数不支持该操作, 但右面的参数支持时使用）。虽然, [_ _ lt_ _()](https://docs.python.org/3/reference/datamodel.html#object.__lt__) 和 [_ _ gt _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__gt__)，[_ _ le _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__le__) 和 [_ _ ge_ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__ge__)， [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__)和 [_ _ ne _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__ne__) 看起来是反函数。

厚比较方法的参数并不是非要有。

从一个根操作自动生成命令的操作，见 [functools.total_ordering()](https://docs.python.org/3/library/functools.html#functools.total_ordering)。

object. _ _ hash _ _ (self)  
由内建函数 [hansh()](https://docs.python.org/3/library/functions.html#hash) 调用，用于操作散列的集合，包括 [set](https://docs.python.org/3/library/stdtypes.html#set)，[frozenset](https://docs.python.org/3/library/stdtypes.html#frozenset)，以及 [dict](https://docs.python.org/3/library/stdtypes.html#frozenset). [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__)，应该返回一个整数。 仅有一个要求，具有相同的值的对象应该有相同的散列值，应该考虑以某种方式将散列值与在对象中比较中起作用的部分联系起来（例如，用排斥或）。

**注意：**[hash()](https://docs.python.org/3/library/functions.html#hash) 将返回值从一个对象的自定义 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 方法返回到 `Py_ssize_t` 的大小。这通常是8个字节64位构建和4个字节32位构建。如果对象的 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 互相操作构建不同的位，要确保所支持的构建宽度，一种简单的确认方法是 `python -c "improt sys;print(sys.hash_info.width)"`。

如果一个类不定义 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 方法，它也不应该定义 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 操作；如果它定义 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 而不定义 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) ，它的实例将不能被用作散列集合的项。如果类定义可变对象并且实现了 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 方法, 它就不应该实现 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) ，因为字典实现一个散列表的键值是不可变的（如果对象的散列值改变了，它会放在错误的散列表位置中）。

如果一个类覆盖 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 并且没有定义 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) ，这个类的 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 默认设为 `None`。当一个类的  [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 方法是 `None`，当程序试图检索散列值时，类的实例将报告 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)，当检查 `isinstance(obj,collections.Hashable)时，它也可以正确的识别。

如果一个类覆盖 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 并且需要从父类中保留实现，通过 `_ _ hash _ _ = <ParentClass>. _ _ hash _ _` 设置可以明确的告诉翻译器。

如果一个类没有覆盖 [_ _ eq _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__eq__) 并且希望废止 hash 支持，在类定义中应该包含 `_ _ hash _ _ = None`。一个类定义自己的 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) ，明确提出了一个 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)，将错误的认定为 `isinstance(obj,collection.Hashable)` 调用。

**注意：**默认情况下，[_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 字符， 字节和日期时间对象值是“有经验的”，伴随着一个不可预测的随机值。虽然它们在一个 Python 的过程中保持不变，但是在 Python 之间调用是不可预测的。  
这是旨在提供一种反对拒绝服务的保护机制。更多细节见 [http://www.ocert.org/advisories/ocert-2011-003.html](http://www.ocert.org/advisories/ocert-2011-003.html)。  
散列值变化影响字典,集和其他映射的迭代顺序。Python 从来没有给这种顺序提供保护（典型变化在32位和64位构建时）。  
同时参考 [PYTHONHASHSEED](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONHASHSEED)。  
*版本3.3 以改变：* 默认集成散列随机化。

object.__bool__(self)  
用于实现真值测试盒构建操作 `bool()`，返回 `假`或`真`。如果没有定义这个方法，调用 [_ _ len _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__len__)，如果定义了，它的结构为非零，那么这个对象应该被当做真。如果一个类既没有定义 [_ _ len _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__len__)，也没有定义 [_ _ bool() _ _](https://docs.python.org/3/reference/datamodel.html#object.__bool__)，所有它的实例都认为是真。

###3.3.2. 定制属性访问

可以定义以下方法用于定制类实例属性的访问的含义（用于赋值，或删除 `x.name`）。

object. _ _ getattr _ _ (self, name)  
当以正常的方式的访问属性（就是说,要访问的属性既不是实例的属性,也在其所在的类树结构中找不到）。`name` 是属性名。方法应该返回一个属性值，或抛出一个 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) 异常。

注意如果属性可以通过正常的机制访问，[getattr()](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) 不会被调用（是故意将 [getattr()](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) 和 [setattr()](https://docs.python.org/3/reference/datamodel.html#object.__setattr__) 设置成不对称的），这样的原因是由于效率并且 [getattr()](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) 不能访问实例的其它属性。注意，至少对于实例变量来说，你可能通过不往实例字典里插入任何值来伪装所有控制（但将它们插入到其它对象中）。在下述的[ _ _ getattribute _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__) 方法中获取一个完全控制属性访问的方法。

object. _ _ getattribute _ _ (self, name)  
无条件实现访问类属性时调用。如果类也定义为 [getattr()](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) ，除非使用 [ _ _ getattribute _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__) 才能调用，或抛出一个 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) 异常。方法应该返回一个属性值，或抛出一个 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError) 异常。为了避免这种方法中无限递归，其实现基本类调用方法应该使用相同的名称来访问任何属性，比如，`object.__getattribute__(self, name)`。

**注意：**查找特殊方法时，该方法仍有可能通过语言语法或内建函数作为隐含的调用的结果绕过该方法。见 [Special method lookup](https://docs.python.org/3/reference/datamodel.html#special-lookup)。

object._ _ setattr _ _ (self, name, value)  
在属性将被赋值时调用。这是作为正常机制的代替使用的（就是地实例字典中存储值)。 *name* 是实例值，*vaule* 是要赋的值。

如果在 [_ _ setattr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__setattr__) 内部试图为一个实例属性赋值，应该同时调用基本类方法，如 `object.__setattr__(self, name, value)`。

object. _ _ delattr _ _ (self, name)  
就像 [_ _ setattr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__setattr__) 一样，不过其作用是删除而不是赋值。仅仅对于对象用 `del obj.name` 实现才有意义.

object. _ _ dir _ _ (self)  
对象调用时用 [dir()](https://docs.python.org/3/library/functions.html#dir) 调用，必须返回顺序，它将返回的顺序转换为列表和排序。

####3.3.2.1. 实现描述符

下列方法仅适用于一个包含方法类的实例（所谓的描述符类）出现在一个所有者类中（描述符必须在主类的字典中或者在其一个父类的类字典中）。下述的例子中，“属性”指的是其名字所有者类 _ _ dict _ _ 属性的关键属性。

object. _ _ get _ _ (self, instance, owner)  
获得所有者类或者属性（类访问属性）或那个类实例（实例访问属性）时调用。所有者总是所有者类，而对于实例，它可以访问实例的属性，或通过所有者访问属性为 `None`。这种方法应该返回一个（计算）属性值或抛出属性异常 [AttributeError ](https://docs.python.org/3/library/exceptions.html#AttributeError)。

object. _ _ set _ _ (self, instance, value)  
对所有者类的*实例*，将实例属性设一个新值及值时调用。

object. _ _ delete _ _ (self, instance)  
对所有者类的*实例*，将实例属性删除时调用。

[inspect](https://docs.python.org/3/library/inspect.html#module-inspect) 模块解释 _ _　object _ _ 属性，并指向定义该对象的类（适当设置这个可以在运行时协助动态类属性自查）。对于可调用的，它可能表明给丁磊的一个实例（或一个子类），预计或需要作为第一位参考（比如，CPython 设置这个方法的属性用C语言实现）。 

####3.3.2.2. 调用描述符

一般而言，描述符是一个具有“绑定行为”的对象属性，这些访问属性在描述符协议里通过方法来覆盖：[ _ _ get _ _()](https://docs.python.org/3/reference/datamodel.html#object.__get__)，[ _ _ set _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__set__)，[ _ _ delete _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__delete__)。如果一个对象定义了任何一个这种方法，那么就称为个描述符。

属性访问的默认行为是从一个对象的字典中获取、设置、或者删除属性。比如，`a.x` 是个起始为 `a. _ _ dict _ _ ['x']` 的查询链，然后是 `type(a). _ _ dict _ _ ['x']`， 紧接着通过 `type(a)` 基本类型，不包括元类。

不论如何，如果查询值是一个对象定义的描述符方法，那么 Python 覆盖默认行为，用调用描述符方法替代。这个在链中发生的优先级取决于定义何种描述方法和如何调用它们。

描述符调用的起点是个“绑定”，`a.x`。参数如何装配取决于 `a`:

直接调用  
最简单最常见的调用是用户代码直接调用一个描述符方法：`x. _ _ get () _ _ (a)`。

实例绑定  
如果绑定到一个类实例，`a.x` 转化为调用：`type(a). _ _ dict _ _ ['x']. _ _ get _ _ (a, type(a))`。

类绑定  
如果绑定到一个类， `A.x` 转化为调用：`A. _ _ dict _ _ ['x']. _ _ get _ _ (None, A)`。

超级绑定  
如果`a`是一个超级 [super](https://docs.python.org/3/library/functions.html#super) 实例，在基本类 `A`之前立即`B`，绑定 `super(B, obj).m()` 查找 `obj.__class__.__mro__`，然后调用描述符：`A. _ _ dict _ _ ['m']. _ _ get _ _ (obj, obj. _ _ class _ _ )`。

对于实例绑定，描述符的优先调用取决于该描述符定义方法，一个描述符可以定义成 [ _ _ get _ _()](https://docs.python.org/3/reference/datamodel.html#object.__get__)，[ _ _ set _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__set__)，和 [ _ _ delete _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__delete__) 的任何组合。如果没有定义 [ _ _ get _ _()](https://docs.python.org/3/reference/datamodel.html#object.__get__)，那么访问对象本身的属性将返回描述符，除非有一个值在对象的实例的字典中。如果描述符同时或者选择定义 [ _ _ set _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__set__) 及 [ _ _ delete _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__delete__) ，那么它是个数据描述符；反之，就不是一个数据描述符。一般情况下，数据描述符同时定义 [ _ _ set _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__set__) 及 [ _ _ delete _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__delete__)，相比非数据描述符仅仅只有 [ _ _ get _ _()](https://docs.python.org/3/reference/datamodel.html#object.__get__) 方法。具有 [ _ _ set _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__set__) 及 [ _ _ get _ _()](https://docs.python.org/3/reference/datamodel.html#object.__get__) 的数据描述符总是在实例字典中覆盖重新定义。

Python 的实现方法（包括 [staticmethod()](https://docs.python.org/3/library/functions.html#staticmethod) 和  [classmethod()](https://docs.python.org/3/library/functions.html#classmethod)）都是作为非数据描述符，相应地，实例可以再定义和覆盖方法。这允许单个实例得到不用于其他相同类的实例。

[property()](https://docs.python.org/3/library/functions.html#property) 功能是用于实现一个数据描述符。相应地，实例不能覆盖一个属性的行为。

####3.3.2.3. _ _ slots _ _ 

默认情况下,类的实例都有一本字典来存储属性，这对对象浪费空间有很少的实例变量。当创建大量实例时，空间消耗会变的很严重。

在类定义中可以重新定义 _ _ *slots* _ _ 来覆盖默认值。_ _ *slots* _ _ 声明实例变量序列，在每个实例变量中储备足够的空间来保有变了值。由于每个实例不创建 _ _ *slots* _ _ ，可以节省空间。

object. _ _ slots _ _  
这类变量可以被分配一个可迭代字符串，或与所使用的变量名的字符串序列实例。 _ _ *slots* _ _ 储备空间声明的变量和防止自动为每个实例创建 _ _ dict _ _ 和 _ _ weakref _ _。

#####3.3.2.3.1 使用 _ _ *slots* _ _ 注意事项

继承一个没有 _ _ *slots* _ _ 的类是，总是可以访问类的 _ _ *slots* _ _ 属性，因此在子类中定义 _ _ *slots* _ _ 是没有意义的。  
没有一个 _ _ *slots* _ _ 变量，不能分配实例新变量不在 _ _ *slots* _ _ 定义中，如果试着去分配，那么会抛出异常 [AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError)。如果新变量需要动态分配，那么在 _ _ *slots* _ _ 声明中添加 `_ _ dict _ _` 字符串顺序。  
每个实例变量没有 _ _ *weakref* _ _ ，类定义  _ _ *slots* _ _ 不支持若引用到它的实例。如果需要若引用，那么在 _ _ *slots* _ _ 声明中添加 `_ _ weakref _ _` 字符串顺序。  
对于每一个变量，在类的等级上创建描述符来（[Implementing Descriptors](https://docs.python.org/3/reference/datamodel.html#descriptors)）实现 _ _ *slots* _ _ 。因此，类属性不能将实例变量的默认值设置为  _ _ *slots* _ _ ；否则，类属性将覆盖描述符分配。  
_ _ *slots* _ _ 声明仅限于类定义。因此，除非子类定义 _ _ *slots* _ _ ，否则将会有一个  _ _ *dict* _ _ （必须只包含额外的插槽的名字）。  
如果一个类在基本类中定义了插槽，基本类的实例变量定义的位置不可访问（除了从基本类中检索它的描述符）。这使得未被定义的程序具有意义。在未来，可能添加检查来防止这个。  
对于诸如从可“变成”的内建类型 [int](https://docs.python.org/3/library/functions.html#int)，[bytes](https://docs.python.org/3/library/functions.html#bytes) 和 [tuple](https://docs.python.org/3/library/stdtypes.html#tuple) 继承的类，非空 _ _ *slots* _ _ 不起作用。  
任何一个非字符串的迭代器可能分配到  _ _ *slots* _ _ ；映射也可以使用；但在未来，可能给每个键分配特殊的值。  
只有2个类具有相同的 _ _ *slots* _ _ ，  _ _ *class* _ _ 分配才起作用。 

###3.3.3. 自定义创建类

默认情况下，使用 [type()](https://docs.python.org/3/library/functions.html#type) 构建类。以一个新的命名空间和名字来运行类的主体，本地绑定结果到 `type(name,bases,namespace)`。

在类定义行，通过关键参数 `metaclass` 可以自定义创建类，或者从一个已存在的包含参数的类中继承。在下面的例子中，`MyClass` 和 `MySubclass` 都是 `Meta` 的实例。

class Meta(type):  
&emsp;&emsp;pass

class MyClass(metaclass=Meta):  
&emsp;&emsp;pass

class MySubclass(MyClass):  
&emsp;&emsp;pass

任何其他类定义中指定的关键字参数传递到所有元类操作描述如下。

当执行一个类定义时，会发生如下步骤：

&emsp;&emsp;·确定适当的元素  
&emsp;&emsp;·准备类空间名  
&emsp;&emsp;·执行类主体  
&emsp;&emsp;·创建类对象

####3.3.3.1 定义适当的元类

适当元类的定义如下：

&emsp;&emsp;·如果没有给出基本、没有显式的元类，使用 [type（）](https://docs.python.org/3/library/functions.html#type)  
&emsp;&emsp;·如果给出显式的元类，没有 [type（）](https://docs.python.org/3/library/functions.html#type) 实例，直接使用它作为元类  
&emsp;&emsp;·如果给出 [type（）](https://docs.python.org/3/library/functions.html#type) 实例作为显式的元类，或者定义了基本类，大多使用派生的元类

从显式指向的元类（如果有的话）和详细说明的基本元类（如 `type(cls)`）中选择最派生元类。最派生元类是所有候选元类的子类中的一个。如果没有元类符合标准，类定义将会抛出 `TypeError`。

####3.3.3.2 类命名空间准备

一旦确定了适当的元类，那么类空间名也会准备好。如果元类具有 `_ _ prepare _ _` 属性，它被调用为 `namespace = metaclass. _ _ prepare _ _ (name, bases, **kwds)`（如果有来自类定义的额外关键参数）。

如果元类没有 `_ _ prepare _ _` 属性，类名门空间初始化为一个空 [dict()](https://docs.python.org/3/library/stdtypes.html#dict) 实例。

**也可以参考：**

**PEP 3115 - 在 Python 3000 中的元类**  
&emsp;&emsp;介绍了 `_ _ prepare _ _` 命名空间挂钩

####3.3.3.3 执行类主体

执行类主体（大约）是 `exec(body, globals(), namespace)`。从一个正常的调用到 [exec()](https://docs.python.org/3/library/functions.html#exec) 的主要区别是类定义发生在函数里时，[exec()](https://docs.python.org/3/library/functions.html#exec) 允许类主体（包括任何方法）来引用当前来自内外部范围的名称。

然而，即使类定义发生在函数里时，在类里定义方法仍不能明确在类范围内定义的名字。类变量必须通过实例或类方法的第一个参数，无法访问所有的静态方法。

####3.3.3.3 创建类对象

一旦通过执行类主体填充了类命名空间，通过调用 `metaclass(name, bases, namespace, **kwds)` 创建类对象（这里传递的关键字相同于传递到 ` _ _ prepare _ _ `）。

这个类对象通过无参数形式 [super()](https://docs.python.org/3/library/functions.html#super) 来引用，如果在类主体中的任何方法参考 ` _ _class_ _ ` 或者 `super`，编译器会创建一个隐式封闭引用 _ _class_ _ 。这使得零参数形式的 [super()](https://docs.python.org/3/library/functions.html#super) 正确识别字典范围定义的类， 同时使用类或实例去做当前调用，并识别基于由第一个参数传递到方法的调用。

在创建类对象后，它是传递给在类中定义（如果有）的解释器，以及随之而来的在本地命名空间定义类的对象绑定。

**也可以参考：**

**PEP 3135 - 新超级类**  
&emsp;&emsp;描述了 `_ _ class _ _` 隐式封闭引用

####3.3.3.5.元类实例

元类的潜在使用时无穷无尽的。一些想法，探讨包括日志，借口检查，自动授权，自动属性创建，代理，框架，以及自动资源锁定/同步。

这里有个元类的示例，它使用了 [collections.OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict) 来记忆类变量定义的顺序：

class OrderedClass(type):

     @classmethod
     def __prepare__(metacls, name, bases, **kwds):
        return collections.OrderedDict()

     def __new__(cls, name, bases, namespace, **kwds):
        result = type.__new__(cls, name, bases, dict(namespace))
        result.members = tuple(namespace)
        return result

class A(metaclass=OrderedClass):  

    def one(self): pass  
    def two(self): pass  
    def three(self): pass  
    def four(self): pass  

->>>A.members   
(`_ _ module _ _`, `one`, `two`, `three`, `four`)

当类定义 *A* 得到执行，这一过程开始于调用元类的 _ _ prepare _ _ ()方法，并返回一个 [collections.OrderedDict](https://docs.python.org/3/library/collections.html#collections.OrderedDict)。映射记录 *A* 的方法和属性，它们的类主体语句中定义。一旦执行这些定义就会完全填充命令字典，元类的 [_ _ new _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__new__) 方法被调用。该方法构建的新类型和它在属性中保存命令字典键称为 `members`。

###3.3.4. 自定义实例和子类检查

以下方法用来覆盖内建函数 [isinstance()](https://docs.python.org/3/library/functions.html#isinstance) 和  [issubclass()](https://docs.python.org/3/library/functions.html#issubclass) 的默认行为。特别是元类 [abc.ABCMeta](https://docs.python.org/3/library/abc.html#abc.ABCMeta) 实现这些方法，目的是为了允许抽象基本类（ABCs）作为任何类或类型（包括内建类型）的“虚拟基本类”，包括其他的抽象基本类。 

class. _ _ instancecheck _ _ (self, instance)  
如果考虑*实例*应该是类的（直接或间接）实例，返回真。如果定义了它，调用实现 `isinstance(instance, class)`。

class. _ _ subclasscheck _ _ (self, subclass)  
如果考虑*子类*应该是类的（直接或间接）子类，返回真。如果定义了它，调用实现 `issubclass(subclass, class)`。

注意，在一个类的类型（元类）上查询这些方法，在真实类里，它们不能定义成类方法。这是符合实例上调用特殊方法查找的，也只有在这种情况下，实例本身就是一个类。

**也可以参考：**

**PEP 3119 - 介绍抽象基本类**  
&emsp;&emsp;详细介绍了通过  [_ _ instancecheck _ _ ()](https://docs.python.org/3/reference/datamodel.html#class.__instancecheck__) 和 [_ _ subclasscheck _ _ ()](https://docs.python.org/3/reference/datamodel.html#class.__subclasscheck__)，自定义 [isinstance()](https://docs.python.org/3/library/functions.html#isinstance) 和 [issubclass()](https://docs.python.org/3/library/functions.html#issubclass) 的行为，这个功能推动了上下文添加抽象基本类（见 [abc](https://docs.python.org/3/library/abc.html#module-abc) 模块）的语言。

###3.3.5. 模拟包装类型

object. _ _ call _ _ (self[, args...])

当实例像一个函数使用时调用本方法。如果定义了这个方法，那么 `x（arg1， arg2，...）` 是 `x. call（arg1，arg2， ...）` 的缩写形式。

###3.3.6. 模拟包装器类型

定义以下方法可以实现包容器对象。包容器通常指有序类型（像列表或元组）或映射（像字典），但也可以表示其它包容器。 第一个方法集用于模拟有序类型或映射；有序类型的区别就在于，允许键可以是整数 *k*，其中 `0 < k < N`，*N* 是有序类型的长度，或者是描述了一定范围的片断。在实现映射时，推荐提供 keys()，values()，items()，has key()，get()，clear()，copy()，和 update()，使其行为类似于 Python 标准的字典对象；[collections](https://docs.python.org/3/library/collections.html#module-collections) 模块提供了一个 [MutableMapping](https://docs.python.org/3/library/collections.abc.html#collections.abc.MutableMapping) 抽象基类来从 [_ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__)，[_ _ setitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__setitem__)，[_ _ delitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__delitem__)，和 keys() 抽象基本类集合中创建这些方法。可变的有序类型应该提供方法append()，count()，index()，insert()，pop()，remove()，reverse() 和sort()，像Python标准的列表类型。最后，有序类型应该通过定义下述的方法 [_ _ add _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__add__)，[_ _ radd _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__radd__)，[_ _ iadd _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__iadd__)，[_ _ mul _ _()](https://docs.python.org/3/reference/datamodel.html#object.__mul__)，[_ _ rmul _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__rmul__) 和[ _ _ imul _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__imul__) 实现加法运算（就是指连接）和乘法运算（指重复）。它们不应该定义其它数值运算操作。对于有序类型和字典都推荐实现 [_ _ contains _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__contains__)，以便于高效的使用 `in` 运算符；对于映射， `in`  应该搜索映射值；对于有序类型，应该通过值来搜索。进一步推荐映射和有序类型类型通过容器有效迭代去实现[ _ _ iter _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__iter__)，对于映射，[_ _ iter _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__iter__) 应该等价于 keys() 方法；对于有序类型，通过值来迭代。　

object. _ _ len _ _ (self)  
实现内建函数 [len()](https://docs.python.org/3/library/functions.html#len) 相仿的功能，应该返回对象的长度，并且返回一个`>=` 0的整数。另外，一个没有定义 [_ _ bool_ _（）](https://docs.python.org/3/reference/datamodel.html#object.__bool__)的方法返回0被认为是返回一个逻辑假值。

object. _ _ length_hint _ _ (self)  
调用实现 [operator.length_hint()](https://docs.python.org/3/library/operator.html#operator.length_hint) 。应该返回估算长度的对象(可能是大于或小于实际长度)。长度必须是一个整数 `>=` 0。这纯粹是一种优化方法,从来没有要求正确性。

*新的在版本3.4.*

`**注意：**用以下三种方法完全完成切断，调用像`

`a[1:2] = b`

翻译成

`a[slice(1,2,None)] = b`

等等。确实的部分项用 `None` 来填充。

object. _ _ getitem _ _ (self, key)  
实现 `self[key]` 相仿的功能。对于有序类型，可接受的键包括整数和片断对象。注意对负数索引（如果类希望模拟有序类型）的特殊解释也依赖于 _ _ getitem_ _ () 方法。如果*键*是不合适的类型，一个 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常就会被抛出，如果某个值在有序类型的索引值集合之外（在任何负值索引的特定解释也不能行的通的情况下），会抛出一个 [IndexError](https://docs.python.org/3/library/exceptions.html#IndexError) 的异常。

**注意：** `for` 循环可以通过对由于对无效索引值而抛出的 `IndexError` 异常进行捕获来对访问有序类型的结尾做适当地检测。

object.__missing__(self, key)  
调用  [dict. _ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 实现 `self[key]`，当关键字不在子电子类时。

object. _ _ setitem _ _ (self, key, value)  
在对 `self[key]` 进行赋值时调用。与[ _ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 有着相同的注意事项。通常只对映射实现本方法，并且要求对象支持改变键所对应的值，或支持增加新键；也可以在有序类型中实现，此时支持单元可以替换。在使用无效的*键值*时，会抛出与https://docs.python.org/3/reference/datamodel.html#object.__getitem__相同的异常。

object. _ _ delitem _ _ (self, key)
在删除 `self[key]` 时调用，与[ _ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 有着相同的对象。本方法通常仅仅在映射中实现，并且对象支持键的删除；也可以在有序类型中实现，此时单元可以从有序类型删除。在使用无效的*键值*时，会抛出与[ _ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 相同的异常．

object. _ _ iter _ _ (self)  
要求使用包容器的子迭代时，这个方法被调用。本方法应该返回一个可以迭代包容器所有对象的迭代子对象。对于映射，应该在键的基础上进行迭代，

迭代子对象也需要实现这个方法，它们应该返回它自己。对于更多的关于迭代子对象的信息，参见 *[Iterator Types](https://docs.python.org/3/library/stdtypes.html#typeiter)*。

object. _ _ reversed _ _ (self)  
调用 [reversed()](https://docs.python.org/3/library/functions.html#reversed) 实现反向迭代。它应该返回一个新的迭代器对象，以相反的顺序遍历所有的对象容器。

如果不支持 [reversed()](https://docs.python.org/3/library/functions.html#reversed) 方法，[reversed()](https://docs.python.org/3/library/functions.html#reversed) 将会求助于使用顺序协议（ [_ _ len _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__len__) 和 [_ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) ）。支持序列协议的对象应该只提供 [reversed()](https://docs.python.org/3/library/functions.html#reversed)，除非它们能提供一个比 [reversed()](https://docs.python.org/3/library/functions.html#reversed) 更有效的实现方法。 

成员测试运算符（[in](https://docs.python.org/3/reference/expressions.html#in) 和 [not in](https://docs.python.org/3/reference/expressions.html#not-in)）一般通过对有序类型的迭代来实现。但是包容器也可以提供以下方法得到更有效的实现，不要对象是有序类型。

object.__contains__(self, item)  
使用成员测试运算符时调用。如果 *item* 在 *self* 中，返回true；否则返回 false。对于映射对象，比较应该在键上进行，不应该是键值对。

对于没有定义 [_ _ contains _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__contains__) 的对象，成员第一次迭代测试会通过 [_ _ iter _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__iter__)，那么旧的顺序迭代协议会通过[ _ _ getitem _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) ，见 [this section in the language reference](https://docs.python.org/3/reference/expressions.html#membership-test-details)。 

###3.3.7. 模拟数值类型

以下方法用于模拟数值类型。其中，对于有些种类数值所不支持的操作对应的方法未定义（如，对非整数值的位运算）。

object. _ _ add _ _ (self,other)  
object. _ _ sub _ _ (self,other)  
object. _ _ mul _ _ (self,other)  
object. _ _ truediv _ _ (self,other)  
object. _ _ floordiv _ _ (self,other)  
object. _ _ mod _ _ (self,other)  
object. _ _ divmod _ _ (self,other)  
object. _ _ pow _ _ (self,other[,modulo])  
object. _ _ lshift _ _ (self,other)  
object. _ _ rshift _ _ (self,other)  
object. _ _ and _ _ (self,other)  
object. _ _ xor _ _ (self,other)  
object. _ _ or _ _ (self,other)  
这些方法用于实现二元算术运算（`+`，`-`，`*`，`/`，`//`，`%`，[divmod()](https://docs.python.org/3/library/functions.html#divmod)，[powe()](https://docs.python.org/3/library/functions.html#pow)，`**`，`<<`，`>>`，`&`，`^`，`|`）。比如，对表达式 `x + y` 求值，*x* 是一个具有 [_ _ add _ _](https://docs.python.org/3/reference/datamodel.html#object.__add__)　方法的类的实例，调用　`x. _ _ add _ _ (y)`。 [_ _ divmod() _ _](https://docs.python.org/3/reference/datamodel.html#object.__divmod__) 方法应该相当于使用 [_ _ floordiv _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__floordiv__) 和 [_ _ mod _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__mod__)，它跟[ _ _ truediv _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__truediv__) 不相关。注意如果内建函数 [powe()](https://docs.python.org/3/library/functions.html#pow) 支持三值版，应该定义 [ _ _ pow _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__pow__) 来接受第三个参数。

如果这些方法其中之一不支持提供的参数运算，它应该返回 `NotImplementd`。

object. _ _ radd _ _ (self,other)  
object. _ _ rsub _ _ (self,other)  
object. _ _ rmul _ _ (self,other)  
object. _ _ rtruediv _ _ (self,other)  
object. _ _ rfloordiv _ _ (self,other)  
object. _ _ rmod _ _ (self,other)  
object. _ _ rdivmod _ _ (self,other)  
object. _ _ rpow _ _ (self,other)  
object. _ _ rlshift _ _ (self,other)  
object. _ _ rrshift _ _ (self,other)  
object. _ _ rand _ _ (self,other)  
object. _ _ rxor _ _ (self,other)  
object. _ _ ror _ _ (self,other)  
这些方法用于实现反应（交换）操作数二元算术运算（`+`，`-`，`*`，`/`，`//`，`%`，[divmod()](https://docs.python.org/3/library/functions.html#divmod)，[powe()](https://docs.python.org/3/library/functions.html#pow)，`**`，`<<`，`>>`，`&`，`^`，`|`）。  
只有在左操作数不支持相应操作和操作数类型不同时才会调用。[2] 比如，表达式求值 `x - y`，*y* 是一个具有[ _ _ rsub _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__rsub__) 方法的类的实例，如果 `x. _ _ sub _ _ (y)`返回 *NotImplemented* 调用 `y. _ _ rsub _ _ (x)`。

注意，三元函数 [powe()](https://docs.python.org/3/library/functions.html#pow) 将不会尝试调用 [ _ _ pow _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__pow__) （强制规则会变的国过于复杂）。

**注意：**如果右操作数的类型是左操作数的类型的子类,子类提供了反映操作方法,这个方法将被称为前左操作数的 non-reflected 方法，这个动作允许子类覆盖他们的父类操作。

object. _ _ iadd _ _ (self,other)  
object. _ _ isub _ _ (self,other)  
object. _ _ imul _ _ (self,other)  
object. _ _ itruediv _ _ (self,other)  
object. _ _ ifloordiv _ _ (self,other)  
object. _ _ imod _ _ (self,other)  
object. _ _ ipow _ _ (self,other[,modulo])  
object. _ _ ilshift _ _ (self,other)  
object. _ _ irshift _ _ (self,other)  
object. _ _ iand _ _ (self,other)  
object. _ _ ixor _ _ (self,other)  
object. _ _ ior _ _ (self,other)  
这些方法用于实现赋值运算符（`+=`，`-=`，`*=`，`/=`，`%=`，`**=`，`<<-`，`>>=`，`&=`，`&=`，`|=`）。这些方法应该试图做就地操作（修改 *self* ）和返回结果（可能是，但不需要 *self* 操作），如果没有具体定义一个方法，赋值操作就返回到正常的方法。比如，*x* 是一个具有 [_ _ add _ _](https://docs.python.org/3/reference/datamodel.html#object.__add__)　方法的类的实例，`x += y` 相当于 `x = x. _ _ iadd _ _ (y)`，否则应当考虑 `x. _ _ add _ _ (y)` 和 `y. _ _ radd _ _ (x)` 成为 `x + y` 的求值。在某些特定情况下，参数赋值会产生意想不到的错误 （见 [Why does a_tuple[i] += [‘item’] raise an exception when the addition works?](https://docs.python.org/3/faq/programming.html#faq-augmented-assignment-tuple-error) ），但实际上这种行为也是数据模型的一部分。

object. _ _ neg _ _ (self)  
object. _ _ pos _ _ (self)  
object. _ _ abs _ _ (self)  
object. _ _ invert _ _ (self)  
调用这些方法实现一元算术运算（ `-`，`+`，[abs()](https://docs.python.org/3/library/functions.html#abs) 和 `~` ）。

object. _ _ complex _ _ (self)  
object. _ _ int _ _ (self)  
object. _ _ float _ _ (self)  
object. _ _ round _ _ (self[, n])  
调用这些方法实现内建函数 [complex()](https://docs.python.org/3/library/functions.html#complex)，[int()](https://docs.python.org/3/library/functions.html#int)，[float()](https://docs.python.org/3/library/functions.html#float) 和 [round()](https://docs.python.org/3/library/functions.html#round)，应该返回适当的类型值。

object. _ _ index _ _ (self)  
调用这个方法实现 [operator.index()](https://docs.python.org/3/library/operator.html#operator.index)，和任何时候 Python 需要无损的转换数值对象为一个整数对象（比如在片断内，或内建函数 [bin()](https://docs.python.org/3/library/functions.html#bin)，[hex()](https://docs.python.org/3/library/functions.html#hex) and [oct()](https://docs.python.org/3/library/functions.html#oct)）。该方法的存在表明，数值对象时一个整形，必须返回一个整数。

**注意：**为了保持一致的整形类型，在定义了  [_ _ index _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__index__) 时， [_ _ int _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__int__) 也应该被定义，两者返回相同的值。

###3.3.8. with 语句的上下文管理器

当执行一个 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句时，上下文管理器会定义建立运行时的上下文，它处理运行上下文的代码块的进入和退出，通常调用 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句（ [*The with statement*](https://docs.python.org/3/reference/compound_stmts.html#with) 章节描述）实现这个功能，但也可以通过直接调用方法来实现。

上下文管理器的典型应用包括保存和回复各种全局状态，锁定和释放资源，关闭打开文件等。

更多关于上下文管理器的信息，见 [Context Manager Types](https://docs.python.org/3/library/stdtypes.html#typecontextmanager)。

object. _ _ enter _ _ (self)  
进入这个对象运行时相关的上下文。如果有的话，with 语句将该方法的返回值绑定到 as 语句的子句中。

object. _ _ exit _ _ (self)  
退出这个对象运行时相关的上下文。参数描述导致上下文的异常退出。如果上下文是退出没有例外,三个参数都将是 [None](https://docs.python.org/3/library/constants.html#None)。

如果提供的是个异常，并且方法想要停止异常（如，防止它被传播），它应该返回一个真值。否则，异常处理会退出这个方法

注意， [_ _ exit _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__exit__)方法不应该再次抛出异常，这是调用者负责的。

**另请参考：**

**PEP 0343 - with 语句**  
Python 中 with 语句的规范，背景，以及用例。

###3.3.9. 特殊方法查找

对于自定义类，如果在对象类型上而不是字典对象的实例上定义它，特殊方法的隐式调用只能保证正常地工作。这就是为什么下面代码会抛出个异常：

`>>> class C:`  
`...     pass`  
`...`  
`>>> c = C()`  
`>>> c. _ _ len _ _ = lambda: 5`
`>>> len(c)`
`Traceback (most recent call last):`
`  File "<stdin>", line 1, in <module>`  
`TypeError: object of type 'C' has no len()`

这种现象的基本原理在于可以被所有对象，包括类型对象，实现的方法，诸如 [_ _ hash _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__hash__) 和 [_ _ repr _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__repr__)。如果这些方法的隐式查找使用传统的查找过程，在调用对象类型本身时会失败：

`>>> 1 .__hash__() == hash(1)`  
`True`  
`>>> int.__hash__() == hash(int)`  
`Traceback (most recent call last):`  
`  File "<stdin>", line 1, in <module>`  
`TypeError: descriptor '__hash__' of 'int' object needs an argument` 
  
试图错误地以这种方式调用一个类的非绑定方法有时被称为“元类混乱”，而且避免了避开实例查找特殊方法：

`>>> type(1).__hash__(1) == hash(1)`  
`True`  
`>>> type(int).__hash__(int) == hash(int)`  
`True`

处理避开实例属性的正确性，即使是对象的元类，一般隐式特殊方法查找一般也避开  [_ _ getattribute _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__) 方法：

`>>> class Meta(type):`  
`...    def __getattribute__(*args):`  
`...       print("Metaclass getattribute invoked")`  
`...       return type.__getattribute__(*args)`  
`...`  
`>>> class C(object, metaclass=Meta):`  
`...     def __len__(self):`  
`...         return 10`  
`...     def __getattribute__(*args):`  
`...         print("Class getattribute invoked")`  
`...         return object.__getattribute__(*args)`  
`...`  
`>>> c = C()`  
`>>> c.__len__()             # 通过实例隐式查找`  
`Class getattribute invoked`  
`10`  
`>>> type(c).__len__(c)          # 通过类型隐式查找`  
`Metaclass getattribute invoked`  
`10`  
`>>> len(c)                       # 隐式查找`  
`10`  

以这种方式避开 [_ _ getattribute _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__getattribute__) 的机制在翻译器内提供了重要的优化范围，使特殊方法的处理具有了一定的灵活性。

**脚注**

[[1]](https://docs.python.org/3/reference/datamodel.html#id1) 在一定控制条件下，有可能在某些情况下改变一个对象的类型。通常，这不是一个好主要，因为如果处理不得当，它可能会导致一些非常奇怪的行为。

[[2]](https://docs.python.org/3/reference/datamodel.html#id3) 对于相同类型的操作符， 假定如果 non-reflected 方法（如 [_ _ add _ _ ()](https://docs.python.org/3/reference/datamodel.html#object.__add__)）不支持操作失败，这就是为什么不调用 reflected 方法。

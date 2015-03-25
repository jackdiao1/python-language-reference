# 表达式 - Expressions

>
# 6. 表达式  
本章描述了 Python 中表达式的组成元素的含义。  

**句法注意：**在本章和之后的章节中，描述句法时使用与词法分析时不同的扩展 BNF 记法。当某个句法规则（可能是可选的）具有如下形式：
  
`name ::= othername`  
     
并且未给出特定语义时，name 的这种形式的意义就与 othername 相同。
## 6.1. 数值间的转换  
当用以下短语“数值型参数转换为通用类型”描述数值型操作数时，参数使用第三章结尾处的强制规则进行强制转换。如果两个参数都属于标准数值型，就使用以下的强制规则：  
  
- 如果其中一个参数是复数，另一个也要转换成复数；  
- 否则，如果其中一个参数是浮点数，另一个也要转换成浮点数；  
- 否则，两个都是整数， 不需要转换。  
  
对于某些运算符有特殊的规则（例如，作为运算符‘%’左侧参数的字符）。扩展必须制定转换规则。  
## 6.2. 原子  
 原子是表达式最基本的组成单位，最简单的原子是标识符或者字面值。 以圆括号、方括号或大括号括住的符号在句法上也看成是原子。原子的句法如下：  
  
`atom ::=` [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) | [literal](https://docs.python.org/3/reference/expressions.html#grammar-token-literal) | [enclosure](https://docs.python.org/3/reference/expressions.html#grammar-token-enclosure)  

`enclosure ::=` [parenth_form](https://docs.python.org/3/reference/expressions.html#grammar-token-parenth_form) | [list_display](https://docs.python.org/3/reference/expressions.html#grammar-token-list_display) | [dict_display](https://docs.python.org/3/reference/expressions.html#grammar-token-dict_display) | [set_display](https://docs.python.org/3/reference/expressions.html#grammar-token-set_display) | [generator_expression](https://docs.python.org/3/reference/expressions.html#grammar-token-generator_expression) | [yield_atom](https://docs.python.org/3/reference/expressions.html#grammar-token-yield_atom) 

### 6.2.1. 标识符 （名字）  
作为一个原子出现的标识符是一个名字。参看[标识符和关键字](https://docs.python.org/3/reference/lexical_analysis.html#identifiers)以及[命名空间和结构框架](https://docs.python.org/3/reference/executionmodel.html#naming)。  

当某名字捆绑的是一个对象时，使用该原子就是使用那个对象。当某名字没有捆绑就直接使用它，则会抛出一个 [NameError](https://docs.python.org/3/library/exceptions.html#NameError) 异常。

**私有名字变换:** 在类定义中， 以两个或多个下划线开始，并且尾部不是以两个或多个下划线结束的标识符，它被看作是类的私有名字。在产生它的代码之前， 私有名字被变换成更长的形式。这种变换是在将去掉前导下划线的类名插入到名字前，再在类名前插入一个下划线。例如，在类 `Ham` 中定义的标识符 `_ _spam`，会被变换成 `_ _Ham_spam`。本变换是不依赖于使用该标识符处代码的句法上的上下文的。如果变换后的结果过长（超过 255 个字符）， 就会执行该Python实现定义的截短名字的操作。如果某类的名字仅仅由下划线组成，这种变换是不会发生的。  
###6.2.2. 字面值  
Python 支持字符串、字节和各种数值型的字面值：
  
`literal ::`= [stringliteral](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-stringliteral) | [bytesliteral](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-bytesliteral) | [integer](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-integer) | [floatnumber](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-floatnumber) | [imagnumber](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-imagnumber)  

使用一个字面值会得到一个具有给定值的相应类型的对象（字符串、字节、整数、浮点数、复数），如果是浮点数和复数，那么这个值可能是个近似值，详见 [Literals](https://docs.python.org/3/reference/lexical_analysis.html#literals)。

所有字面值都属于不可变的数据类型，因此对象的标识比起它们的值来说显得次要一些。多次使用相同值的字面值（在程序代码中以相同形式出现或者以不同的形式出现）可能获得的是相同的对象或具有相同值的不同对象。　　
###6.2.3. 括号表达式
一个括号表达式是位于一对小括号内可选的表达式表。  

`parenth_form ::=` "(" [[expression_list]](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) ")"  

表达式表生成什么类型的值括号表达式也就生成什么类型的值：如果表达式表中包括了至少一个逗号，它就生成一个元组；否则，就生成那个组成表达式表的唯一的表达式。  

一个空的表达式表会生成一个空的元组对象。因为元组是不可变的，因此这里适用字符串所用的规则（即两个具有空表达式的元组可能是同一个对象也可能是不同的对象）。  

请注意元组不是依靠小括号成定义的，而是使用逗号。其中空元组是个例外，此时要求有小括号——在表达式中允许没有小括号的“空”可能会引起歧义，并容易造成难以查觉的笔误。  
###6.2.4. 列表、集合和字典的表示  
为了构建一个列表、一个集合或者一个字典，Python 提供了一种特殊的语法叫“表示”，每一个都有两种方式：  

- 容器中的内容被详细列出  
- 或者是通过一套循环和过滤指令来计算，被称为“推导式”。  
 
推导式的一般语法元素是：  

`comprehension ::=`  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) [comp_for](https://docs.python.org/3/reference/expressions.html#grammar-token-comp_for)  
`comp_for      ::=`  "for" [target_list](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target_list) "in" [or_test](https://docs.python.org/3/reference/expressions.html#grammar-token-or_test) [[comp_iter](https://docs.python.org/3/reference/expressions.html#grammar-token-comp_iter)]  
`comp_iter     ::=`  [comp_for](https://docs.python.org/3/reference/expressions.html#grammar-token-comp_for) | [comp_if](https://docs.python.org/3/reference/expressions.html#grammar-token-comp_if)  
`comp_if       ::=`  "if" [expression_nocond](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_nocond) [[comp_iter](https://docs.python.org/3/reference/expressions.html#grammar-token-comp_iter)]  

推导式是由至少一个 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 子句以及后跟零个或多个 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 或 [if](https://docs.python.org/3/reference/compound_stmts.html#if) 子句构成的一个表达式组成，在这种情况下，新列表的元素由每个 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 或 [if](https://docs.python.org/3/reference/compound_stmts.html#if) 子句决定，嵌套是从左至右方向的，而且每执行到最内部的语句块就产生一个列表元素。  

请注意，推导式是在一个单独的范围内执行的，所以目标列表的名字分配不能泄漏到封闭的范围内。

###6.2.5. 列表的表示  
一个列表用一对方括号括住的表达式序列（可能为空）表示：  
  
`list_display ::=` "[" [[expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) | [comprehension](https://docs.python.org/3/reference/expressions.html#grammar-token-comprehension)] "]"  

使用一个列表会生成一个新的列表对。它的值由表达式表或由推导式给出。当给出一个逗号分隔的表达式表时，从左到右地对每个元素求值然后按顺序放进列表对象中。 如果给出的是推导式， 列表是由从推导式得出的元素组合而成。  
###6.2.6. 集合的表示  
一个集合是用花括号来表示的，它和字典的区别是缺少分隔键和值的冒号。  

`set_display ::=`  "{" ([expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) | [comprehension](https://docs.python.org/3/reference/expressions.html#grammar-token-comprehension)) "}"  

集合的表示将产生一个新的集合对象，内容是由一个表达式序列或者一个推导式来制定。当提供一个由逗号分隔的表达式列表时，将从左到由计算它的元素并把它们加入到集合对象中。当提供一个推导式时，集合将由从推导式中得出的元素组成。  

一个空的集合不能由`{ }`构建，这种形式将构建一个空的字典。  

###6.2.7. 字典的表示  
一个字典用一对大括号括住的键/数据对的序列（可能为空）表示：  

`dict_display       ::=` "{" [[key_datum_list](https://docs.python.org/3/reference/expressions.html#grammar-token-key_datum_list) | [dict_comprehension](https://docs.python.org/3/reference/expressions.html#grammar-token-dict_comprehension)] "}"  
`key_datum_list     ::=` [key_datum](https://docs.python.org/3/reference/expressions.html#grammar-token-key_datum) ("," [key_datum](https://docs.python.org/3/reference/expressions.html#grammar-token-key_datum))* [","]  
`key_datum          ::=` [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ":" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
`dict_comprehension ::=` [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ":" [expression comp_for](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  

使用一个字典会生成一个新的字典对象。  

键/数据对按在字典中定义的从左到右的顺序求值：每个键对象作为键嵌入到字典中存储相应的数据。这意味着你可以在键/数据列表中多次指定相同的键，键值是最后一次指定的值。  

字典推导式，与列表和集合推导式相反，需要两个由冒号分开的表达式，并且其后跟一般的 “for” 和 “if” 子句。当推导式在运行时，产生的键和值元素将按照它们生成的顺序插入到新的字典中去。

关于键值类型的限制已在前述章节 [标准类型层次](https://docs.python.org/3/reference/datamodel.html#types) 提及（总而言之，键的类型应该是可散列的，这就排除了所有的可变对象）。重复键之间的冲突不会被检测到；对给定的（有重复的）键来说，最后出现的数据（就是文字显示中出现在最右边的）成为（最终的）胜利者。  
###6.2.8.  生成器表达式  
生成器表达式是由圆括号括起来的紧凑的生成器符号。  

`generator_expression ::=`  "(" [expression comp_for](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ")"  

一个生成器表达式产生一个新的生成器对象。它是被括在圆括号中而不是方括号或者花括号中，除此之外，它的语法和推导式一样。  

当 [__next__()](https://docs.python.org/3/reference/expressions.html#generator.__next__) 方法被生成器对象调用时，在生成器表达式中用到的变量将被计算（和普通生成器相同）。然而，最左边的 for 子句将被立即计算，这样由它产生的错误将在处理生成器代码中其它可能出现的错误之前被发现。随后的 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 子句将不会被立即计算，因为它们将取决于之前的 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 循环。例如 `(x*y for x in range(10) for y in bar(x))`。  

当调用生成器表达式时，圆括号可以被忽略，只保留一个参数。详情请参见[调用](https://docs.python.org/3/reference/expressions.html#calls)章节。

###6.2.9. Yield表达式  
`yield_atom       ::=`  "("[yield_expression](https://docs.python.org/3/reference/expressions.html#grammar-token-yield_expression) ")"  
`yield_expression ::=`  "yield" [[expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) | "from" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
  
ｙield 表达式只有在定义一个[生成器](https://docs.python.org/3/glossary.html#term-generator)功能时才会被使用，所以只能被用在函数定义的过程中。在函数体内使用 yield 表达式将会使该函数变成一个生成器。  

当一个生成器函数被调用时，它将返回一个被称为生成器的迭代器。该生成器将控制生成器函数的执行。当生成器的某个方法被调用时，生成器函数将开始执行。同时，在执行第一个 yield 表达式的过程中，即上次被挂起的地方，将返回[表达式列表](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list)的值给生成器调用程序。所谓挂起，即所有的本地状态都将被保留，包括局部变量的绑定情况、指令指针、内部计算堆栈以及一些异常处理的状态。当调用生成器的某种方法，该执行过程恢复时，该函数可以像 yield 表达式是另外一个外部表达式调用一样运行。恢复执行后 yeild 表达式的值将取决于使该执行过程恢复的方法。如果使用 [_ _next_ _()](https://docs.python.org/3/reference/expressions.html#generator.__next__) 函数（通常是通过 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 语句或者内部 [next()](https://docs.python.org/3/library/functions.html#next) 函数），则结果为 [None](https://docs.python.org/3/library/constants.html#None)。如果使用 [send()](https://docs.python.org/3/reference/expressions.html#generator.send) 函数，结果为传递到该方法中的值。  

所有这些特性让生成器函数和协同程序非常像，它们可以多次输出，有多于一个的程序执行入口点，而且它们的执行过程可以被挂起。唯一的区别是当输出一次执行结果后，需要继续执行时，生成器函数无法控制，只有当生成器被调用时才可以被控制。  

在 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 块中的任何地方都可以使用 yield 表达式。如果一个生成器在它结束（引用计数达到0或者被垃圾收集）之前没有被恢复，迭代器的 [close()](https://docs.python.org/3/reference/expressions.html#generator.close) 方法将会被调用，允许各种 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 语句执行。  

当使用 `yield from <expr>` 时，认为表达式为从迭代器。所有由该从迭代器提供的值将直接返回给当前生成器方法的调用程序。如果有合适的方法，所有由 [send()](https://docs.python.org/3/reference/expressions.html#generator.send) 函数传递的值和所有由 [throw()](https://docs.python.org/3/reference/expressions.html#generator.throw) 函数传递的异常都将传送给底层的迭代器。如果找不到合适的方法，[send()](https://docs.python.org/3/reference/expressions.html#generator.send) 函数将抛出[AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError)或者 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)异常，但是 [throw()](https://docs.python.org/3/reference/expressions.html#generator.throw) 函数只抛出传递异常。  

当底层叠加器完成计算时，抛出的 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) 异常实例的值属性将变成 yield 表达式的值。它可以在抛出异常 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) 异常时明确的设置，或者当子迭代器是一个生成器时（通过子生成器返回值）自动生成。  

&nbsp;&nbsp;&nbsp;&nbsp;*3.3版本修改的地方：*添加 `yield from <expr>` 语句使子生成器具备控制功能。  

当 yield 表达式是赋值表达式右边唯一的表达式时，圆括号可以被忽略。  

**可参见：**
**[PEP 0255](http://www.python.org/dev/peps/pep-0255) – 简单生成器**  
提议在 Python 中 添加生成器和 [yield](https://docs.python.org/3/reference/simple_stmts.html#yield) 的说明.  
**[PEP 0342](http://www.python.org/dev/peps/pep-0342) – 通过高级生成器实现的协同程序**  
提议增强生成器的 API 和语法，让它们和简单生成器一样便于使用。  
**[PEP 0380](http://www.python.org/dev/peps/pep-0380) – 子生成器的授权语法**  
提议介绍 yield-from 语法，让授权子生成器变得容易。  

####6.2.9.1.生成器-迭代器方法  
这一部分将描述生成器迭代器的方法，该方法可以控制生成器程序的执行。  
  
请注意：当生成器已经开始运行的时候，调用以下任何一个方法将抛出 [ValueError](https://docs.python.org/3/library/exceptions.html#ValueError) 异常。  

generator.**_ _next_ _()**  
  
开启生成器函数的执行或者在上次执行 yield 表达式的地方恢复执行。当一个生成器函数被 [_next_()](https://docs.python.org/3/reference/expressions.html#generator.__next__) 方法恢复执行，最近的 yield 表达式通常计算为 [None](https://docs.python.org/3/library/constants.html#None)。当生成器再次被挂起，[表达式列表](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list)的值被返回给 [_next_()](https://docs.python.org/3/reference/expressions.html#generator.__next__) 函数的调用者，执行过程将继续到下一个 yield 表达式。如果生成器没有生成另外一个值就直接退出执行，将引发一个 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) 异常。  

该方法通常被间接调用，如通过一个 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 循环或者通过内建 [next()](https://docs.python.org/3/library/functions.html#next) 函数。  

generator.**send(value)**  

恢复执行过程并且传递给生成器函数一个值。value 参数的值将被赋予当前 yield 表达式。[send()](https://docs.python.org/3/reference/expressions.html#generator.send) 方法返回生成器生成的下一个值。如果生成器没有产生其它值并退出执行后，将引发 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) 异常。当调用 [send()](https://docs.python.org/3/reference/expressions.html#generator.send) 函数启动生成器时，必须以 [None](https://docs.python.org/3/library/constants.html#None) 作为参数，因为没有接收该值得 yield 表达式。  

generator.**throw(type[, value[, traceback]])**  

当生成器被暂停将抛出一个 `type` 类型的异常，并返回由生成器函数生成的 next 值。如果生成器没有返回其它值并退出执行，将抛出一个 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) 异常。如果生成器函数没有对传入的异常进行捕获处理时，异常将返回给调用者。  

generator.**close()**  

当生成器函数被暂停执行时将抛出 [GeneratorExit](https://docs.python.org/3/library/exceptions.html#GeneratorExit) 异常（正常退出或者已被关闭）。如果生成器函数抛出一个 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) （正常退出或者已被关闭）或者 [GeneratorExit](https://docs.python.org/3/library/exceptions.html#GeneratorExit) 异常时（不捕获处理异常），生成器将正常结束。如果生成器返回其它值，将抛出 [RuntimeError](https://docs.python.org/3/library/exceptions.html#RuntimeError) 异常。如果生成器抛出其它类型的异常，则异常将直接被返回给调用者。如果生成器因为异常或者正常退出而结束运行，则 [close()](https://docs.python.org/3/reference/expressions.html#generator.close) 函数不起任何作用。  
  
####6.2.9.2.举例  
此处用一个简单的例子来说明生成器和生成器函数的执行。  
    
    >>> def echo(value=None):  
    ...     print("Execution starts when 'next()' is called for the first time.")  
    ...     try:  
    ...         while True:  
    ...             try:  
    ...                 value = (yield value)  
    ...             except Exception as e:  
    ...                 value = e  
    ...     finally:  
    ...         print("Don't forget to clean up when 'close()' is called.")  
    ...  
    >>> generator = echo(1)  
    >>> print(next(generator))  
    Execution starts when 'next()' is called for the first time.  
    1  
    >>> print(next(generator))  
    None  
    >>> print(generator.send(2))  
    2  
    >>> generator.throw(TypeError, "spam")  
    TypeError('spam',)  
    >>> generator.close()  
    Don't forget to clean up when 'close()' is called.  
  
使用 `yield from` 语句的例子,请参见[PEP380：委托至子生成器的语法](https://docs.python.org/3/whatsnew/3.3.html#pep-380)。  
##6.3. 基元  
基元指和语言本身中接合最紧密的若干操作。它们的语法如下:  

`primary ::=` [atom](https://docs.python.org/3/reference/expressions.html#grammar-token-atom) | [attributeref](https://docs.python.org/3/reference/expressions.html#grammar-token-attributeref) | [subscription](https://docs.python.org/3/reference/expressions.html#grammar-token-subscription) | [slicing](https://docs.python.org/3/reference/expressions.html#grammar-token-slicing) | [call](https://docs.python.org/3/reference/expressions.html#grammar-token-call)  
  
###6.3.1. 属性引用  
一个属性引用是由一个主元（primary）后跟一个句号和一个名字构成:  

`attributeref ::=` [primary](https://docs.python.org/3/reference/expressions.html#grammar-token-primary) "." [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)  

主元必须是一个支持属性引用的类型的对象。引用对象属性时，即要求该被对象生成指定名字的属性。　该过程可以通过重写 [_ _getattr_ _()](https://docs.python.org/3/reference/datamodel.html#object.__getattr__) 方法具体化。　
如果该属性无效，将会抛出异常 [AttribError](https://docs.python.org/3/library/exceptions.html#AttributeError)。否则，对象决定生成的属性的类型和值。对同一属性的引用多次求值是有可能生成不同对象的。  
###6.3.2. 下标  
一个下标选择一个有序类型对象（字符串，元祖或列表）或映射（字典）对象的一项:  

`subscription ::=` [primary](https://docs.python.org/3/reference/expressions.html#grammar-token-primary) "[" [expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) "]"  

主元（primary）必须是一个支持下标计算的对象（例如列表或字典）。用户自定义的对象通过定义 [_ _getitem_ _()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 方法来支持下标。  

如果主元是一个映射，则对表达式表求值的结果必须是映射中的一个键，然后此下标操作在主元映射中选择与该键所对应的值。（如果表达式表只有一项，那么它就是一个元组）。  
  
如果主元是一个有序类型，表达式（列表）的计算结果应该是一个整数或片段（见下方讨论部分）。  

正式的语法对序列中的负索引没有特别的规定；但是，所有内建序列都提供了一个 [_ _getitem_ _()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 方法来解决负索引，即如果索引值是负数， 就加上该序列的长度(例如， x[-1] 选择 x 的最后一项)。  
  
字符串的元素是字符，字符不是单独的数据类型而仅仅是只有一个字符长的字符串。  
###6.3.3. 片段  
一个片断选择某个有序类型对象（如字符串、元组、列表）一段范围之内的项。片断可以作为表达式使用，或者是赋值和 del 语句的目标。 下面是片断的句法：  
  
`slicing      ::=`  [primary](https://docs.python.org/3/reference/expressions.html#grammar-token-primary) "[" [slice_list](https://docs.python.org/3/reference/expressions.html#grammar-token-slice_list) "]"  
`slice_list   ::=`  [slice_item](https://docs.python.org/3/reference/expressions.html#grammar-token-slice_item) ("," [slice_item](https://docs.python.org/3/reference/expressions.html#grammar-token-slice_item))* [","]  
`slice_item   ::=`  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-slice_item) | [proper_slice](https://docs.python.org/3/reference/expressions.html#grammar-token-proper_slice)  
`proper_slice ::=`  [[lower_bound](https://docs.python.org/3/reference/expressions.html#grammar-token-lower_bound)] ":" [[upper_bound](https://docs.python.org/3/reference/expressions.html#grammar-token-upper_bound)] [ ":" [[stride](https://docs.python.org/3/reference/expressions.html#grammar-token-stride)] ]  
`lower_bound  ::=` [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
`upper_bound  ::=`  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
`stride       ::=`  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
  
在这里形式句法的说明中有点含糊： 任何看起来像表达式表的语法构件也能看作是片断表，所以任何下标都可以解释为片断。但这样要比更复杂的句法要合适，该定义是没有歧义的，在这种情况下（在片断表中没有包括适当的片断或者省略写法）优先将其解释为下标，而不是片断。  
  
一个片断的语义如下：主要是由该片断表构成的键作索引（使用相同的 [_ _getitem_ _()](https://docs.python.org/3/reference/datamodel.html#object.__getitem__) 方法作为正常下标）。如果一个片断表包括至少一个逗号，那么键就是一个包括由片断中的所有项转换而来的元组； 否则， 就用独立的片断项作转换成为键。为表达式的片断项转换后仍是该表达式。正常的片断转换后是一个 start，stop 和step 属性为给定的下限，上限和步长的片断对象（见3.2节），对于缺少的表达式用 None 替代。  
###6.3.4. 调用
一个调用就是以一系列[参数](https://docs.python.org/3/glossary.html#term-argument)（可能为空）调用一个可调用对象（例如，[函数](https://docs.python.org/3/glossary.html#term-function)）:  
  
`call                 ::=`  [primary](https://docs.python.org/3/reference/expressions.html#grammar-token-primary) "(" [[argument_list](https://docs.python.org/3/reference/expressions.html#grammar-token-argument_list) [","] | [comprehension](https://docs.python.org/3/reference/expressions.html#grammar-token-comprehension)] ")"  
`argument_list        ::=`  [positional_arguments](https://docs.python.org/3/reference/expressions.html#grammar-token-positional_arguments) ["," [keyword_arguments](https://docs.python.org/3/reference/expressions.html#grammar-token-keyword_arguments)]  
[","\*" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)] ["," [keyword_arguments](https://docs.python.org/3/reference/expressions.html#grammar-token-keyword_arguments)]  
["," "\*\*" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
| [keyword_arguments]() ["," "\*" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
 ["," [keyword_arguments](https://docs.python.org/3/reference/expressions.html#grammar-token-keyword_arguments)] ["," "\*\*"  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
| "\*" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ["," [keyword_arguments](https://docs.python.org/3/reference/expressions.html#grammar-token-keyword_arguments)]
 ["," "\*\*" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
| "\*\*" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
`positional_arguments ::=`  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ("," [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression))*  
`keyword_arguments    ::=`  [keyword_item](https://docs.python.org/3/reference/expressions.html#grammar-token-keyword_item) ("," [keyword_item](https://docs.python.org/3/reference/expressions.html#grammar-token-keyword_item))*  
`keyword_item         ::=`  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) "=" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
  
在关键字参数表后面可以出现一个逗号，但它在语义上是没有任何作用的。  
  
首先的工作是导出一个可调用对象（用户自定义函数，内建函数，内建方法对象，类定义，类实例方法，包含方法 [_ _call_ _()](https://docs.python.org/3/reference/datamodel.html#object.__call__) 的对象都是可调用的）。所有的参数表达都在试图调用之前被计算。关于[形参](https://docs.python.org/3/glossary.html#term-parameter)列表的句法请参考章节[函数定义](https://docs.python.org/3/reference/compound_stmts.html#function)。  
  
如果给出了关键字参数，它们首先被转换为位置参数。具体如下:第一步，根据形参表创建一串空闲槽，如果有N个位置参数，它们就被放在前N个槽中。然后，对于每个关键字参数，它的标识符用于检测其对应的槽(如果其标识符与第一个形参数名相同，它就占用第一个槽，以此类推)如果发现某个槽已经被占用，则引发 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)异常。否则将参数的值(即使为 `None` )放进槽中。当所有关键字参数处理完成后， 所有未填充的槽用在函数定义中的相应的默认值填充。(默认值是由函数定义时计算出来的，所以，像列表和字典这样的可变类型对象作默认值时，它们会被那些没有相应槽指定参数值的调用所共享，通常要避免这样做)。如果仍有未填充默认的槽位，就会引发一个 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。否则，所有被填充的槽当作调用的参数表。  
  
**CPython实现细节：**CPython实现了一种位置参数没有名字的内建函数，即使位置参数被命名也是为了阅读方便，因此无法提供关键词。在实现，这是实现在 C 使用 [PyArg_ParseTuple()](https://docs.python.org/3/c-api/arg.html#c.PyArg_ParseTuple) 解析他们的论点的情况下。  
  
如果位置参数的个数比形参槽数多，并且在未使用 `*identifier` 句法的情况下，会引发 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。使用该种句法时，形参接受一个包括有额外位置参数的元组(如果没有额外和位置参数，它就为空)。  
  
如果任何一个关键字参数与形参名不匹配，并且在未使用 `**identifier`句法的情况下，会引发 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。使用该种句法时，形参接受一个包括有额外关键字参数的字典(关键字作为键，参数值作为该键对应的值)，字典如果没有额外和关键字参数，它就为空。  
  
如果在函数调用中使用了 `*exprsiones` 句法， 那么 `exprsiones`的结果必须是有序类型的。这个有序类型对象的元素被当作附加的位置参数处理; 如果存在有位置参数 *x1，...，xN*，并且 `*exprsiones` 的计算结果为 *y1，...，yM*，那么它与具有 M+N 个参数 *x1，...xN，y1，...，yM* 的调用等效。  
  
由此可以得到一个推论:　尽管 `*exprsiones` 句法出现在任何关键字参数之后，但它在处理关键字参数之前计算。（如果有的话，`**exprsiones` 也是如此，参见下述），所以：  
  
    >>> def f(a, b):  
    ...  print(a, b)  
    ...  
    >>> f(b=1, *(2,))  
    2 1  
    >>> f(a=1, *(2,))  
    Traceback (most recent call last):  
      File "<stdin>", line 1, in ?  
    TypeError: f() got multiple values for keyword argument 'a'  
    >>> f(1, *(2,))  
    1 2  
  
一同使用关键字语法和 `*expression` 的情况十分罕见，所以实际上这种混乱是不会发生的。  
  
如果在函数调用中使用 `**expression` 句法，`expression` 计算结果必须是一个字典（的子类）。其内容作为附加的关键字参数。如果一个关键字出现在 `expression` 中并且是一个显式关键字参数，就会引发 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。  
  
使用 `*identifier”` 或 `**identifier` 句法的形参不能作为位置参数或关键字参数名使用。  
  
一个元组如果没有引发异常，通常会返回一些值，可能为 `None`。怎样计算这个值依赖于可调用对象的类型。  
  
如果它是—  

用户自定义函数：  
执行此函数的代码块，并把参数传给它。它要做的第一件事就是将形参与实参对应起来。关于这点参见[函数定义 ](https://docs.python.org/3/reference/compound_stmts.html#function)当代码块执行到 [return](https://docs.python.org/3/reference/simple_stmts.html#return) 语句时，会指定这次函数调用的返回值。  

内建函数或内建方法：  
结果依赖于解释器，详见 [Built-in Functions](https://docs.python.org/3/library/functions.html#built-in-funcs)。  
  
类对象：  
返回该类的一个新实例。  
  
类实例的方法：  
调用对应的用户自定义函数，其参数个数比普通的函数调用多一：该实例成为方法的第一个参数。  
  
类实例：  
类实例必须定义方法 call ()；效果与调用该方法相同。  
##6.4. 幂运算符
 幂运算符比在操作数左边的一元运算符有更高的优先级；但比右面的一元运算符要低。句法为:  
  
`power ::=`[primary](https://docs.python.org/3/reference/expressions.html#grammar-token-primary) ["**" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr)]  
  
因此，在一串没有括号的由幂运算符和一元运算符组成的序列，会从左到右面求值（没有强制改变求值顺
序）：-1**2 结果为 -1。  
  
当以两个参数调用 [pow()](https://docs.python.org/3/library/functions.html#pow) 时，幂运算符与内建函数 pow() 有相同的语义: 生成左边参数的右边参数次方。数值型参数首先转换成通用类型。结果类型是参数经强制规则转换后的结果。  
  
对于整数操作数，其结果的类型与操作数相同除非第二个参数为负数；在这种情况下，所有的参数被转换为 float 进而结果也是 float。例如，`10\*\*2` 返回 `100`，但 `10\*\*-2` 返回 `0.01`。  
  
`0.0`的任意负数指幂会引发 [ZeroDivisionError](https://docs.python.org/3/library/exceptions.html#ZeroDivisionError) 异常。一个负数的分数指幂结果是一个 [complex](https://docs.python.org/3/library/functions.html#complex)。（早期的版本中会引发 [ValueError](https://docs.python.org/3/library/exceptions.html#ValueError) 异常。）  
##6.5. 一元算术运算符和位运算符  
所有一元算术运算符（和位运算符）有相同的优先级:  
  
`u_expr ::=`[power](https://docs.python.org/3/reference/expressions.html#grammar-token-power) | "-" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr) | "+" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr) | "~" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr)  
  
一元运算符-（减）对其数值型操作数取负。  
  
一元运算符+（加）不改变其数值型操作数。  
  
一元运算符 （取反）对其普通整数或长整数参数求逆（比特级）。x的比特级求逆运算定义为 `-(x+1)`。它仅仅用于整数型的操作数。  
  
在以上所有的三种情况下，如果参数的类型不合法，就会引发一个 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。  
##6.6. 二元算术运算符  
二元算术运算符的优先级符合我们的正常习惯。注意其中有些运算符也可以应用于非数值型操作数。除了幂运算符，它们只分两个优先级:一个是乘法类运算，一个是加法类运算。 
  
`m_expr ::=`  [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr) | [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr) "*" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr) | [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr) "//" [u_expr]() | [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr) "/" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr) | [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr) "%" [u_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-u_expr)  
`a_expr ::=`  [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr) | [a_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-a_expr) "+" [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr) | [a_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-a_expr) "-" [m_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-m_expr)  
  
`*`（乘）运算符计算其操作数的积，其两个参数必须是数值型的，或一个是整数另一个是有序类型。第一种情况下，数值参数被转换成通用类型然后计算积。后一种情况下，重复连接有序类型对象。一个负连接因子产生一个有空的有序类型对象。  
  
`/`（除）和`//`(整除)运算符生成参数的商。数值型参数首先转换成通用类型。整数相除（/）生成一个浮点数，整数进行整除的结果就是对商的精确结果执行 `floor()` 函数的返回值。除（/）以零会引发 [ZeroDivisionError](https://docs.python.org/3/library/exceptions.html#ZeroDivisionError) 异常。  
  
`%`（取模）计算第一个参数除以第二参数得到的余数.数值型参数首先转换成通用类型。右面的参数为零，会引发 [ZeroDivisionError](https://docs.python.org/3/library/exceptions.html#ZeroDivisionError) 异常。参数可以是浮点数，例如 `3.14 % 0.7 = 0.34`（因为 `3.14 = 4 * 0.7 + 0.34`）。模运算结果的符号与其第二个操作数相同（或零）；结果的绝对值小于第二个操作数的绝对值[[1](https://docs.python.org/3/reference/expressions.html#id16)]。  
  
整除和取模运算可以用以下等式联系起来: `x == (x//y)*y + (x%y)` 。整除和取模运算也可以用内置函数 [divmod()](https://docs.python.org/3/library/functions.html#divmod): `divmod(x, y) == (x//y, x%y) `联系起来。[[2](https://docs.python.org/3/reference/expressions.html#id17)]
  
`+`（加）运算符计算参数的和。参数必须都是数值型的，或都是相同有序类型的对象。对于前一种情况，它们先转换成通用类型，然后相加。后一种情况下，所有有序类型对象被连接起来。  
  
`-`（减）计算参数的差，数值型的参数首先转换成通用类型。  
##6.7. 移位运算符  
移位运算符的优先级比算术运算符低。  
  
`shift_expr ::=`  [a_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-a_expr) | [shift_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-shift_expr) ( "<<" | ">>" ) [a_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-a_expr)  
  
这些运算符接受整数作为参数。它们将第一个参数向左或向右移动第二个参数指出的位数。  
  
右移 *n* 位可以定义为除以 `pow(2,n)`,左移 *n* 位可以定义为乘以 `pow(2,n)`;  
  
**注意：**在目前的实现中，右操作数应不大于　[sys.maxsize](https://docs.python.org/3/library/sys.html#sys.maxsize)。如果右操作数是一个大于 [sys.maxsize](https://docs.python.org/3/library/sys.html#sys.maxsize) 会引发 [OverflowError](https://docs.python.org/3/library/exceptions.html#OverflowError) 异常。  
##6.8.  二元位运算符  
三个二元位运算符具有各不相同的优先级:  
  
`and_expr ::=`  [shift_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-shift_expr) | [and_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-and_expr) "&" [shift_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-shift_expr)  
`xor_expr ::=`  [and_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-and_expr) | [xor_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-xor_expr) "^" [and_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-and_expr)  
`or_expr  ::=`  [xor_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-xor_expr) | [or_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-or_expr) "|" [xor_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-xor_expr)  
  
`&` 运算符进行比特级的 AND（与）运算，参数必须是整数。  
  
`ˆ` 运算符进行比特级的 XOR（异或）运算，参数必须是整数。  
  
`|` 运算符进行比特级的 OR（同或）运算，参数必须是整数。  
  
##6.9. 比较运算符  
不像 C 语言，在 Python 中所有比较运算符具有相同的优先级，比所有算术运算符、移位运算符和位运算符的优先级都低，并且，表达式 `a < b < c` 具有和数学上一样的含义:  
  
`comparison    ::=`  [or_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-or_expr) ( [comp_operator](https://docs.python.org/3/reference/expressions.html#grammar-token-comp_operator) [or_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-or_expr) )*  
`comp_operator ::=`  "<" | ">" | "==" | ">=" | "<=" | "!=" | "is" ["not"] | ["not"] "in"  
  
比较运算返回逻辑值: `True` 意味着结果为真，`False` 意味着结果为假。  
  
比较运算符可以任意的连接，例如，`x < y <= z` 等价于`x < y and y <= z`，除了`y`只求值一次（但在这两种情况下，只要发现`x < y` 为假，`z`就不会被计算）。  
  
形式上，如果 `a，b，c，...，y，z` 为表达式，`op1，op2，...，opN` 为比较运算符，则`a op1 b op2 c ... y opN z `等价于`a op1 b and b op2 c and ... y opN z`，但是每个表达式最多只求值一次。  
  
注意`a op1 b op2 c` 并没有隐式地规定 a 和 c 之间的比较运算种类，所以`x < y > z` 是完全合法的（虽然看起来不太漂亮）。  
  
运算符 `<，>，==, >=，<=，` 和 `!=` 比较两个对象的值，这两个对象不需要具有相同的的类型 。如果两个都是数值型的，它们都转换成通用类型。否则，`==` 和 `!=` 认为不同类型的对象之间的比较通常是不等的，而 `<，>，>= `和`<=` 则会引发 [TypeError](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。你可以通过定义丰富的比较方法，比如 [_ _gt_ _()](https://docs.python.org/3/reference/datamodel.html#object.__gt__)（详见[基本定制](https://docs.python.org/3/reference/datamodel.html#customization)）控制非内建类型对象的比较行为。  
  
相同类型的对象的比较法则依赖于该类型:
  
- 数值型按大小比较。  
- float('NaN')和Decimal('NaN')的值比较特殊，它们是相同的，即 x is x，但是它们是不相等的，即 x != x。另外，拿任何值和非数值型进行比较都将返回 False。例如，3 < float('NaN') 和 loat('NaN') < 3都将返回 False。  
- Bytes 对象是通过使用它的元素的值来进行字典序比较。  
- 串按字典序比较（每个字符的序数用内建函数[ord()](https://docs.python.org/3/library/functions.html#ord)得到）。[[3](https://docs.python.org/3/reference/expressions.html#id18)]串和字节对象不能相互比较。  
- 元组和按列表字典序比较（通过比较对应的项）。这意味着如果元组和列表相等，则它们的每个元素都要相等并且两个序列要有相同的类型和相同的长度。  
如果不相等，序列的排列顺序则和它们第一个不同的元素的排列顺序相同。例如[1,2，x]　<=　[1,2,y]　和　x　<=　y　的值是一样的。如果对应的元素不存在，则稍短序列的值较小。(例如： [1,2] < [1,2,3])。  
- 映射（字典）仅当它们的存储（键值对）表一样时相等。顺序比较`('<'，'<='，'>='，'>')`会引发 [TypeErro](https://docs.python.org/3/library/exceptions.html#TypeError) 异常。  
- 可变集合和不可变集合定义比较运算符的含义是测试为子集或者父集。这些关系不完全是定义排序（两个序列{1,2}和{2,3}是不相等的，互相之间也不是子集或者父集的关系）。所以，可变集合不适合作为完全依赖比较的函数的参数。例如，如果输入一系列的集合，则函数　min()，max()　和　sorted()将产生不确定的结果。
- 对大多数内建类型对象进行比较，如果不是相同的对象则结果就是不等的。一个对象被看作比另一个对象小或大，是不可以预知的，但在相同的程序其结果是前后一致的。  
  
不同类型的对象的比较取决于该类型是否对比较算法提供了明确的支持。大多数数字类型可以互相比较。当不支持交叉式的比较时，方法会返回 `NotImplemented`。  
  
[in]() 运算符和 [not in]() 运算符用于测试集合成员。如果 `x` 是集合 `s` 的成员，那么 `x in s` 的结果为真，否则为假。`x not　in s` 的结果与上相反。所有的内置序列和集合类型和和字典一样都支持 `in` 测试，测试字典中是否含有给定的键。对于容器类型，如列表、元组、可变集合、不可变集合、字典，或双向队列，表达式 `x in y` 等价于 `any(x is e or x == e for e in y)`。  
  
对于字符串和字节类型，当且仅当*X*是*y*的子字符串时 `x in y` 为真，等价于 `y.find(X)!= 1`。空字符串被认为是任意一个字符串的子字符串，所以 `"" in "abc"` 返回 `True`。  
  
对于定义了 [ __contains__() ](https://docs.python.org/3/reference/datamodel.html#object.__contains__) 方法的用户自定义类，`x in y` 为真仅当 `y.__contains__(x)` 为真.  
  
对于没有定义 [__contains__()](https://docs.python.org/3/reference/datamodel.html#object.__contains__) 方法但定义有 [__iter__()](https://docs.python.org/3/reference/datamodel.html#object.__iter__) 方法的用户自定义类，计算x in y时，x==z并且在迭代时这些z值不断产生，则返回值为真。如果在迭代过程中抛出了一个异常，从外部看就像是in运算抛出的该异常。
最后，古老的迭代规则是可靠的：如果一个类定义了-getitem-（）函数，当且仅当有一个非负整数索引i且x==y[i]，则x in y的值为真，并且所有较低整数索引都不会抛出IndexErrorexception异常。（如果抛出了其它异常，表面上看也好像是in操作抛出的该异常）  

  
运算符 [not in](https://docs.python.org/3/reference/expressions.html#not-in) 可计算与运算符 [in](https://docs.python.org/3/reference/expressions.html#in) 相反的结果.  
  
运算符 [is](https://docs.python.org/3/reference/expressions.html#is) 和[is not](https://docs.python.org/3/reference/expressions.html#is-not) 用于测试对象标识:`x is y` 为真，当且仅当 x 和 y 是相同的对象，`x is not y` 取其反值。[[4](https://docs.python.org/3/reference/expressions.html#id19)]  

##6.10.  布尔运算  
`or_test  ::=`  [and_test](https://docs.python.org/3/reference/expressions.html#grammar-token-and_test) | [or_test](https://docs.python.org/3/reference/expressions.html#grammar-token-or_test) "or" [and_test](https://docs.python.org/3/reference/expressions.html#grammar-token-and_test)  
`and_test ::=`  [not_test](https://docs.python.org/3/reference/expressions.html#grammar-token-not_test) | [and_test](https://docs.python.org/3/reference/expressions.html#grammar-token-and_test) "and" [not_test](https://docs.python.org/3/reference/expressions.html#grammar-token-not_test)  
`not_test ::=`  [comparison](https://docs.python.org/3/reference/expressions.html#grammar-token-comparison) | "not" [not_test](https://docs.python.org/3/reference/expressions.html#grammar-token-not_test)  
  
在布尔运算的上下文和控制流语句使用的表达式中，以下值解释为假：`False`、`None`、所有类型的数值零、空的字符串和容器（字符串、元组、列表、字典、可变集合和不可变集合）。 所有其它值解释为真。用户自定义对象可以通过提供 [_　_bool_　_()](https://docs.python.org/3/reference/datamodel.html#object.__bool__) 方法自定义真值。  
  
如果运算符 `not` 的参数为 `False`，它返回 `True`，否则返回 `False`。  
  
表达式 `x and y` 首先计算 *x*; 如果 *x* 为假，就返回它的值; 否则，计算 *y* 的值，并返回其结果。  
  
表达式 `x or y` 首先计算 `x`; 如果 *x* 为真，就返回它的值; 否则，计算 *y* 的值，并返回其结果。  
  
（注意 [and](https://docs.python.org/3/reference/expressions.html#and) 和 [or](https://docs.python.org/3/reference/expressions.html#or) 都没有限制它的结果的值和类型必须是 `False` 或 `True`，仅仅是最后一个计算的参数。这在某些情况下是有用的，例如，如果 `s` 是一个若为空就应该为默认值所替换的串，表达式 `s or 'foo'` 就会得到希望的结果。因为 [not](https://docs.python.org/3/reference/expressions.html#not) 根本不会生成一个值，就没必要让它的返回值类型与其参数的相同，所以，`not 'foo'` 返回`False`，而非''）。  
##6.11. 条件表达式  
`conditional_expression ::=`  [or_test](https://docs.python.org/3/reference/expressions.html#grammar-token-or_test) ["if" [or_test](https://docs.python.org/3/reference/expressions.html#grammar-token-or_test) "else" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
`expression             ::=`  [conditional_expression](https://docs.python.org/3/reference/expressions.html#grammar-token-conditional_expression) | [lambda_expr](https://docs.python.org/3/reference/expressions.html#grammar-token-lambda_expr)  
`expression_nocond      ::=`  [or_test](https://docs.python.org/3/reference/expressions.html#grammar-token-or_test) | [lambda_expr_nocond](https://docs.python.org/3/reference/expressions.html#grammar-token-lambda_expr_nocond)  

条件表达式（有时也称为“三元算子”）在所有的Python操作中具有最低优先级。  

表达式 `x if C else y`，首先计算条件 *C*，而不是条件 *x*，如果 *C* 为真，则计算 *x* 并返回 *x* 的值；如果 *C* 为假，则计算 *y* 并返回 *y* 的值。  

关于条件表达式的详细介绍请参见[PEP 308](http://www.python.org/dev/peps/pep-0308)。  

##6.12. lambda表达式  
`lambda_expr        ::=`  "lambda" [[parameter_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter_list)]: [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)  
`lambda_expr_nocond ::=`  "lambda" [[parameter_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter_list)]: [expression_nocond](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_nocond)  
  
lambda 表达式(也称作 lambda 形式)被用于创建类型函数。表达式 `lambda arguments: expression` 生成一个行为与下面定义的函数一致的函数对象:  
  
`def <lambda>(arguments):`  
&nbsp;&nbsp;&nbsp;&nbsp;`return expression`  
  
对于参数表句法，参见[函数定义](https://docs.python.org/3/reference/compound_stmts.html#function)。注意由 lambda 形式创建的函数不能包括语句。  
##6.13. 表达式表  
`expression_list ::=`  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ( "," [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) )* [","]  
  
一个表达式表是一个包括至少一个逗号的元组，它的长是表中表达式的个数。其中表达式从左到右按顺序计算。  
  
最后的逗号仅仅在创建单元素元组（又称为”独元”）时才需要; 在其它情况下，它是可选的。一个没有后缀逗号的表达式不会创建元组，但仍会计算该表达式的值。（可以使用一对空括号() 创建一个空元组）。  
##6.14. 计算顺序  
Python 从左到右计算表达式，注意当计算一个赋值表达式时，先计算右边再计算左边。  
  
下边的内容，表达式将以他们后缀的顺序来计算
  
    expr1, expr2, expr3, expr`  
    
    (expr1, expr2, expr3, expr4`  
    
    {expr1: expr2, expr3: expr4}  
    
    expr1 + expr2 * (expr3 - expr4)  
    
    expr1(expr2, expr3, *expr4, **expr5)  
    
    expr3, expr4 = expr1, expr2  

##6.15. 运算符优先级  
下表总结了 Python 中运算符的优先级，从低优先级（弱捆绑）到高优先级（强捆绑），在同一格子中的运算符具有相同的优先级。如果没有特殊的句法指定，运算符是二元的。一格子内的运算符都从左至右结合（幂运算符是个例外，它从右至左结合）。  
  
注意比较运算、成员测试、标志测试它们都有相同的优先级，且可以从左到右连接起来，具体参考[比较运算符](https://docs.python.org/3/reference/expressions.html#comparisons)。  
  
<table>
<tbody>
<tr><th><em>运算符</em></th><th><em>描述</em></th></tr>
<tr><td>lambda</td><td>lambda 表达式</td></tr>
<tr><td>if - else</td><td>条件 表达式</td></tr>
<tr><td>or</td><td>布尔OR</td></tr>
<tr><td>and</td><td>布尔AND</td></tr>
<tr><td>not x</td><td>布尔NOT</td></tr>
<tr><td>in, not in, is, is not, <, <=, >, >=, !=, ==</td><td>成员测试，标志测试，比较</td></tr>
<tr><td>|</td><td>比特级OR</td></tr>
<tr><td>^</td><td>比特级XOR</td></tr>
<tr><td>&</td><td>比特级AND</td></tr>
<tr><td><<, >></td><td>移位</td></tr>
<tr><td>+, -</td><td>加减</td></tr>
<tr><td>*, /, //, %</td><td>乘，除，整除，取余<a href="https://docs.python.org/3/reference/expressions.html#id20">[5]</a></td></tr>
<tr><td>+x, -x, ~x</td><td>正运算,负运算，比特级not</td></tr>
<tr><td>**</td><td>幂运算 <a href="https://docs.python.org/3/reference/expressions.html#id21">[6]</a></td></tr>
<tr><td>x[index], x[index:index], x(arguments...), x.attribute</td><td>下标，片段，函数调用，属性</td></tr>
<tr><td>(expressions...), [expressions...], {key: value...}, {expressions...}</td><td>表达式分组或元祖，列表，字典，集合</td></tr>
</tbody>
</table>


**脚注**  

[1]虽然在数学上表达式 `abs（x % y）＜ABS `（ `y` 的值为真，但在数值上，对于浮点数来说，由于舍入的原因却不一定为真。例如，假设在一个平台上，Python 的浮点数均为符合 IEEE 754 标准的双精度浮点数，为了使 `-1e-100 % 1e100` 的计算结果和 `1e100` 有相同的符号，计算结果为 `-1e-100 + 1e100`，即数值上等于 1e100。函数 [math.fmod()](https://docs.python.org/3/library/math.html#math.fmod) 的返回值符合和第一个参数值的符号相同，所以在该例中将返回 `-1e-100`。哪种方式更合适取决于具体的应用。  

[2]如果X的值约等于y的整数倍，由于舍入原因，`x//y` 的值很可能比 `x-x%y//y`的值大。在这种情况下，为了保持 `divmod（x，y）[0] * y + x % y` 的值和 x 的值非常接近，Python 将返回后者的值。  

[3]虽然字符串比较在字节水平上讲的通，但对用户来说是难以理解的。例如，字符串 `“/u00c7”` 和 `“u0327/u0043”` 比较起来是不同的，即便它们代表同一个 Unicode 字符（带有变音符号的拉丁文大写字母C）。为了使用更符合人类认知的方法去比较字符串，采用函数[unicodedata.normalize()](https://docs.python.org/3/library/unicodedata.html#unicodedata.normalize)。  

[4]由于自动垃圾回收、灵活的列表以及描述器的动态属性，你或许已经注意到了 [is](https://docs.python.org/3/reference/expressions.html#is) 运算符在某些特定的应用中表现出不寻常的行为，比如涉及到实例方法或者是常数的比较。详情请参阅相关参考资料。  

[5]运算符`%`也被用来格式化字符串；与“取余”操作的优先级相同。  

[6]与算术运算符或位运算符相比，运算符`**`右边的数结合的并没有特别紧密，即`2**-1`的值为`0.5`。  
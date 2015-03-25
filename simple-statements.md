# 7.简单语句 - Simple statements #

简单语句可以在一个逻辑行内表示，一些简单语句可以写在一行由分号分隔，简单语句的句法如下：

simple_stmt ::=  [expression__ stmt  ](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-expression_stmt)  
| [assert__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-assert_stmt)  
| [assignment__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-assignment_stmt)  
| [augmented__ assignment__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-augmented_assignment_stmt)  
| [pass__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-pass_stmt)  
| [del__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-del_stmt)  
| [return__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-return_stmt)  
|[ yield__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-yield_stmt)  
| [raise__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-raise_stmt)  
| [break__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-break_stmt)  
| [continue__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-continue_stmt)  
| [import__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-import_stmt)  
| [global__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-global_stmt)  
| [nonlocal__ stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-nonlocal_stmt)  

# 7.1 表达式语句 Expression statements #


表达式语句用于计算和写一个值(多用在交互方式下), 或者（通常）调用过程（一个返回没有意义的结果的函数;
在Python中,过程返回`None`）。允许其它表达式语句的使用方法，有时也用的到。表达式语句的句法如下：

expression_stmt ::=   [ expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list)

一个表达式语句要对该表达式列表(可能只是一个表达式)求值.

在交互模式下，如果该值不是空，就使用内建函数[repr()](https://docs.python.org/3/library/functions.html#repr)并自己将结果字符串写入一行标准输出，（除非结果为空，导致过程调用不会引起任何输出。）

# 7.2 赋值语句 Assignment statements #

使用赋值把名字绑定(重新绑定)到值，以及修改可变对象的属性或者项目：

assignment_stmt ::=  ([target_ list](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target_list) "=")+ ([expression_ list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) | [yield_ expression](https://docs.python.org/3/reference/expressions.html#grammar-token-yield_expression))  
target_list ::=  [target](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target) ("," [target](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target))* [","]  
target  ::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)  
 | "(" [target_ list](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target_list) ")"  
 | "[" [target_ list](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target_list) "]"  
 | [attributeref](https://docs.python.org/3/reference/expressions.html#grammar-token-attributeref)  
 | [subscription](https://docs.python.org/3/reference/expressions.html#grammar-token-subscription)  
 | [slicing](https://docs.python.org/3/reference/expressions.html#grammar-token-slicing)  
 | "*" [target](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target)  


(请参阅 *attributeref*、 *subscription和slicing的*[基本](https://docs.python.org/3/reference/expressions.html#primaries)语法定义部分。)

一个赋值语句要对该表达式列表求值(请记住这可以是一个表达式或者一个逗号分隔的列表，后者导出一个元组)，然后从左到右地将对象结果依次赋给目的列表里的每个对象。

根据目标（列表）的形式，赋值被递归地定义。当目标是一个可变对象的一部分时(一个属性引用,subscription或者slicing),可变的对象必须最终执行赋值,并确定其有效性,如果在赋值不可接受的情况下，可以引发出一个异常。被各种类型所遵循的规则和抛出的异常给出针对对象类型的定义(见[标准的类型层次结构](https://docs.python.org/3/reference/datamodel.html#types)部分)。

分配对象到目标列表,括号或方括号内为可选，递归地定义如下.

- 如果目标列表是单个目标，该对象就赋予该目标。 
- 如果目标序列是一组用逗号分隔的目标：该对象必须是一个其子项个数与目标列表中的目标个数 一样多的迭代对象，且其子项，从左到右地逐个赋予相应目标。
	- 如果目标列表包含一个前缀带星号的目标，被成为“starred”目标：这个对象必须是一个序列在目标列表中，有尽量多的子项目，不能少于一个子项目，序列从第一项被赋值，在带星的目标之前，从左到右逐个分配到目标列表。序列最后一项被赋值,则赋值到星号之后的目标。列表中剩余的项目最后被赋值给带星的目标（列表可以是空的）。
	- 否则：这个对象必须是一个在目标列表中有相同数目子项的序列，并且子项从左到右地赋予相应目标。

一个对象向单个目标递归地赋值定义如下。

- 如果该目标是一个标志符（名字）:


	- 如果该名字不出现于当前代码块的[global](https://docs.python.org/3/reference/simple_stmts.html#global)或者[nolocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)语句当中：该名字就约束到当前本地命名空间的对象上。
	- 否则：该名字分别约束到全局名字空间或者[nolocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)确定的外部名字空间。

如果名字已经被约束了它就被重新约束。这可能导致早先约束到该名字的对象的引用计数降为 零，导致释放该对象的分配空间并调用其析构器，如果它有一个的话。

- 如果目标是一个用括号或者方括号括起来的目标序列：该对象必须是具有和目标序列中目标个数同样数目的迭代类型，且其子项从左到右地赋值给相应目标。

- 如果目标是一个属性引用：引用中的主元表达式被求值。它应该给出一个带可赋值属性的对象； 如果不是这种情况，就会抛出[TypeError](https://docs.python.org/3/library/exceptions.html#TypeError)异常。然后那个对象就被要求将被赋值的对象赋值给给定 的属性；如果它无法执行该赋值，就会抛出一个例外（通常，但不是必须[AttributeError](https://docs.python.org/3/library/exceptions.html#AttributeError)异常）。

注意：如果对象是类的实例并且属性引用的赋值操作发生在两端，RHS 表达式，`a.x`既能访问一个实例属性，也能（如果没有实例属性存在）访问一个类的属性。这个LHS目标`a.x`通常设定一个实例属性，如果有必要就创建。因此，这两个`a.x`的发生不需要涉及同样的属性，如果RHS表达式涉及到类的属性，这个LHS会创建一个新的实例属性作为赋值的目标。

    class Cls:
    x = 3                 # class variable
    inst = Cls()
    inst.x = inst.x + 1   # writes inst.x as 4 leaving Cls.x as 3

这个描述并不一定适用于描述符属性,像属性创建使用[property()](https://docs.python.org/3/library/functions.html#property)。

- 如果目标是一个下标：引用中的主元表达式被求值。这应给出或者一个可变有序对象(比如，一个列表)或者一个映射对象(比如，一个字典)。接着，下标表达式被求值。

如果基础对象是可变有序对象(例如列表)，下标必须给出一个整数。如果是负数，序列的长度就被加上。最后的值必须是一个小于该序列长度的非负整数，然后该序列就被请求将被赋对象赋值给 它带那个指标的项。如果指标超出范围，就会抛出[IndexError](https://docs.python.org/3/library/exceptions.html#IndexError)异常(给一个用下标引用的有序对象赋 值不会给列表增添新项)

如果基础对象是一个映射对象（比如字典），下标的类型必须和映射的键类型兼容，接着该映射就被 要求创建一个把下标映射到被赋对象的键/数据对。这(操作)要不用新键值取代已存在的具有相同 键的键/值对，要不插入一个新的键/值对(如果不存在相同的键)



用户自定义的对象, [____setitem__ __()](https://docs.python.org/3/reference/datamodel.html#object.__setitem__)方法被调用以适当的参数。

- 如果目标是一片断：引用中的主元表达式被求值。这应给出一个可变有序对象(比如列表)。被赋值 对象应该是同一类型的有序对象。下一步，在它们所出现的范围内，对上下限表达式求值；缺省值是零和序列长度。限值应求值为整数。如果任一限是负的，就加上序列的长度。结果限值被修整至零和序列长度之间(含零和序列长度)。最后，有序对象被要求用被赋有序对象的子项替换该片断。片断的长度可能和被赋序列的长度不同，那就改变目标序列的长度，如果该对象允许的话。


**CPython实现细节**：在当前实现中，目标对象的语法采取同一表达式，同时无效的语法在代码生成阶段内被拒绝,导致更少的详细的错误消息输出.


虽然赋值的定义隐含着左手边和右手边之间的重叠是“同时的”(比如，`a, b = b, a`交换两个变量)，在所赋值变量间的重叠却是不安全的！在集合中出现从左到右分配变量间的重叠是不安全的，有时候值是混乱的，例如，下面的程序打印出`[0, 2]`：
    
    x = [0, 1]
    i = 0
    i, x[i] = 1, 2 # i is updated, then x[i] is updated
    print(x)
    

>**参见:**  
[PEP 3132](http://www.python.org/dev/peps/pep-3132) - Extended Iterable Unpacking  
The specification for the *target feature.

# 7.2.1 增量的赋值语句 Augmented assignment statements#

增量赋值就是在单条语句内合并一个二元运算和一个赋值语句。


augmented_assignment_stmt ::=  [augtarget](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-augtarget) [augop](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-augop) ([expression_ list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) | [yield_ expression](https://docs.python.org/3/reference/expressions.html#grammar-token-yield_expression))  
augtarget ::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) | [attributeref](https://docs.python.org/3/reference/expressions.html#grammar-token-attributeref) | [subscription](https://docs.python.org/3/reference/expressions.html#grammar-token-subscription) | [slicing](https://docs.python.org/3/reference/expressions.html#grammar-token-slicing)  
augop ::=  "+=" | "-=" | "*=" | "/=" | "//=" | "%=" | "**=" | ">>=" | "<<=" | "&=" | "^=" | "|="

（见最后三项符号[基础](https://docs.python.org/3/reference/expressions.html#primaries)语法定义）


一条增量赋值语句对目标(和一般的赋值语句不同，它不能是展开的对象)和表达式列表求值，执行特定于两个操作数的赋值类型的二元运算，并将结果赋值给原先的目标。目标仅求值一次。


一条赋值语句，比如`x+=1`, 可以重写为`x=x+1`，效果是类似的，但并不完全一样。在增量版本中，`x`仅求值一次。而且，只要可能，实际的操作是*就地*进行的，意思是并非创建一个新对象然后将其赋值给目标，而是修改老的对象。

不像普通的赋值语句，增量赋值先计算左边再计算右边。例如，`a[i] += f (x)` 首先查看`a[i]`，然后它计算 `f(x)` 并执行加法操作，最后，把结果写入`a[i]`。

除了在一条语句中赋值给元组和多个对象的情况，增量赋值语句所完成的赋值用与普通赋值同样的方式处理。类似地，除了可能的*就地*方式，由增量赋值执行的二元运算和普通的二元运算也是一样的。


For targets which are attribute references, the same [caveat about class and instance attributes](https://docs.python.org/3/reference/simple_stmts.html#attr-target-note) applies as for regular assignments.


# 7.3 assert 语句 The assert statement#

assert语句是一个在程序中插入调试断言的常用方法:

assert_stmt ::=  "assert" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ["," [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]



简单形式的, ”assert expression”, 等价于:


    if __debug__:
       if not expression: raise AssertionError


扩展形式的, ”assert expression”, 等价于:

    if __debug__:
       if not expression1: raise AssertionError(expression2)
    


这些等价式假定了存在[__ __debug__ __](https://docs.python.org/3/library/constants.html#__debug__)和[AssertionError](https://docs.python.org/3/library/exceptions.html#AssertionError), 而不是具有相同名字的相应内建变量.在当前实现, 内建变量  [__ __debug__ __](https://docs.python.org/3/library/constants.html#__debug__) 在普通情况下为True, 在要求优化的情况下为False(命令行选项-O) 在编译要求优化时, 当前的代码生成器不产生任何断言语句的代码.注意在错误信息包括源代码的作法是多余的; 因为它们会作为跟踪回溯对象的一部分显示。

给[____debug__ __](https://docs.python.org/3/library/constants.html#__debug__)赋值是非法的,解释器是在启动时读取内建变量的值的。


# 7.4 pass 语句 The pass statement #
    pass_stmt ::=  "pass"


[pass](https://docs.python.org/3/reference/simple_stmts.html#pass)是一个空操作 - 当执行它，什么也不会做。它在句法上要求有一个语句时但不需要写代码时用到,例如:


    def f(arg): pass   # a function that does nothing (yet)
    class C: pass      # a class with no methods (yet)



# 7.5 del 语句 The del statement #
del_stmt ::=  "del" [target_list](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target_list)

与赋值的定义方法类似，删除也是递归的。下面是一些说明:

一个目标表的递归删除操作会从左到右地删除其中的每个对象地.

删除名字就是在本地命名空间和全局名字空间删除掉该名字的绑定(必须存在), 从哪个名字空间删除取决于该名字是否出现在其代码块的[global](https://docs.python.org/3/reference/simple_stmts.html#global)语句中。如果名字没有绑定，会抛出[NameError](https://docs.python.org/3/library/exceptions.html#NameError)异常。

对于属性引用, 下标和片断的删除会作用到相关的主元对象, 对片断的删除一般等价于对该片断赋予相应 类型的空片断(但这也受被截为片断的对象的限制).

*3.2版本变化：*之前如果嵌套数据块中有空变量，在本地命名空间中删除名字是不合法的。

# 7.6 return 语句 The return statement #
return_stmt ::=  "return" [[expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list)]

[return](https://docs.python.org/3/reference/simple_stmts.html#return)在句法上仅可以出现在嵌套的函数定义中, 不能出现在嵌套的类定义中.

如果给出了表达式表, 就计算其值, 否则就用`None`代替.

[return](https://docs.python.org/3/reference/simple_stmts.html#return)的作用是离开当前函数调用, 并以表达式表的值(或`None`)为返回值.

当[return](https://docs.python.org/3/reference/simple_stmts.html#return)放在具有[ﬁnally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句的[try](https://docs.python.org/3/reference/compound_stmts.html#try)语句中, [ﬁnally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句中的语句会在函数真正退出之前执行一次.

在生成器函数中,[ return](https://docs.python.org/3/reference/simple_stmts.html#return)语句意味着生成器执行完成，并且将会引发一个[StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration)异常。返回值（如果有的话）是作为[StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration)构造方法的参数，并且会变成StopIteration.value属性。

# 7.7  yield 语句 The yield statement #
yield_stmt ::=  [yield_expression](https://docs.python.org/3/reference/expressions.html#grammar-token-yield_expression)

[yield](https://docs.python.org/3/reference/simple_stmts.html#yield)语句在语义上等同于[yield表达式](https://docs.python.org/3/reference/expressions.html#yieldexpr)。yield语法被用来省略括号，否则需要使用相同的yield表达式语句。例如，yield语句


    yield <expr>
    yield from <expr>

等同于yield表达式语句

    (yield <expr>)
    (yield from <expr>)

yield表达式和语句只有当在定义[生成器](https://docs.python.org/3/glossary.html#term-generator)函数时被使用。只使用在生成器函数体中。在函数定义时使用yield，足够导致定义一个创建生成器函数而不是普通函数。

[yield](https://docs.python.org/3/reference/simple_stmts.html#yield)详细的语法，参考[yield表达式](https://docs.python.org/3/reference/expressions.html#yieldexpr)部分。

# 7.8 raise 语句 The raise statement #
raise_stmt ::=  "raise" [[expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ["from" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]]

如果不给出表达式，[raise](https://docs.python.org/3/reference/simple_stmts.html#raise)重新引发当前范围内上次引发的异常。如果当前范围没有可用的异常，会抛出[RuntimeError](https://docs.python.org/3/library/exceptions.html#RuntimeError)异常，暗示出现错误。

否则，[raise](https://docs.python.org/3/reference/simple_stmts.html#raise)使用第一个表达式作为异常对象求值。 对异常对象的第一个表达式求值。他必须是一个子类或者一个[BaseException](https://docs.python.org/3/library/exceptions.html#BaseException)的实例。如果是一个类，当实例化一个无参数类被需要的时候,异常实例将被获取，

异常的*类型*是异常实例类，*值*是实例本身。

traceback对象通常被自动创建，当异常发生并且被附加到它的__ __traceback__ __ _属性上。你可以创建异常并设置你自己的traceback，在第一步使用异常方法with_ _traceback()（这个方法返回相同的异常实例，traceback设置到他的参数中），就像：


    raise Exception("foo occurred").with_traceback(tracebackobj)
    


from子句用于异常链接:如果给出，则第二个表达式必须是另一个异常类或实例，然后将附加到的 __ __cause__ __ 属性 （它是可写）引发的异常。如果不处理引发的异常，则将打印两个例外情况：

    
    >>>>>>>>> try:
    ... print(1 / 0)
    ... except Exception as exc:
    ... raise RuntimeError("Something bad happened") from exc
    ...
    Traceback (most recent call last):
      File "<stdin>", line 2, in <module>
    ZeroDivisionError: int division or modulo by zero
    
    The above exception was the direct cause of the following exception:
    
    Traceback (most recent call last):
      File "<stdin>", line 4, in <module>
    RuntimeError: Something bad happened

一个类似的机制隐式工作如果里面的异常处理程序引发的异常或 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 子句： 那么前一个异常附加作为新异常 __ __context__ __ 属性：

    >>>>>>>>> try:
    ... print(1 / 0)
    ... except:
    ... raise RuntimeError("Something bad happened")
    ...
    Traceback (most recent call last):
      File "<stdin>", line 2, in <module>
    ZeroDivisionError: int division or modulo by zero
    
    During handling of the above exception, another exception occurred:
    
    Traceback (most recent call last):
      File "<stdin>", line 4, in <module>
    RuntimeError: Something bad happened

更多异常信息见[异常](https://docs.python.org/3/reference/executionmodel.html#exceptions)章节，处理异常的信息[Try语句](https://docs.python.org/3/reference/compound_stmts.html#try)章节。

# 7.9 break 语句 The break statement #
    break_stmt ::=  "break"

[break](https://docs.python.org/3/reference/simple_stmts.html#break)在句法上只能出现在[for](https://docs.python.org/3/reference/compound_stmts.html#for)或[while](https://docs.python.org/3/reference/compound_stmts.html#while)循环中, 但不能出现在循环中的函数定义或类定义中。

它中断最内层的循环, 跳过其可选的[else](https://docs.python.org/3/reference/compound_stmts.html#else)语句(如果有的话)。

如果[for](https://docs.python.org/3/reference/compound_stmts.html#for)循环被[break](https://docs.python.org/3/reference/simple_stmts.html#break)中断, 它的循环控制对象还保持当前值。

当[break](https://docs.python.org/3/reference/simple_stmts.html#break)放在具有[ﬁnally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句的[try](https://docs.python.org/3/reference/compound_stmts.html#try)语句中, [ﬁnally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句中的语句会在循环真正退出之前执行一次。


# 7.10 continue 语句 The continue statement #
    continue_stmt ::=  "continue"


[continue](https://docs.python.org/3/reference/simple_stmts.html#continue)在句法上只能出现在[for](https://docs.python.org/3/reference/compound_stmts.html#for)或[while](https://docs.python.org/3/reference/compound_stmts.html#while)循环中, 但不能出现在循环中的函数定义或类定义，或在循环内部的[finally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句。 它重新开 始最内层的循环。


当[continue](https://docs.python.org/3/reference/simple_stmts.html#continue)通行控制离开一个包含[finally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句的[try](https://docs.python.org/3/reference/compound_stmts.html#try)语句，[finally](https://docs.python.org/3/reference/compound_stmts.html#finally)子句执行之前真的开始下一个循环周期。

# 7.11 import 语句 The import statement #
import_stmt ::=  "import" [module](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-module) ["as" [name](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-name)] ( "," [module](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-module) ["as" [name](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-name)] )*  
 | "from" [relative_module](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-relative_module) "import" [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ["as" [name](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-name)]  
 ( "," [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ["as" [name](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-name)] )*  
 | "from" [relative_module](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-relative_module) "import" "(" identifier ["as" [name](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-name)]  
 ( "," [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ["as" [name](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-name)] )* [","] ")"  
 | "from" [module](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-module) "import" " * "  
module  ::=  ([identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ".")* [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)  
relative_module ::=  "."* [module](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-module) | "."+  
name::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)  
    
基本的import语句（不是[from](https://docs.python.org/3/reference/simple_stmts.html#from)子句）执行下面两个步骤：

1. 查找module，如果需要加载并且初始化它。 
2. 定义一个或多个名称在本地命名空间，在[import](https://docs.python.org/3/reference/simple_stmts.html#import)语句出现的范围内。

当语句包含多个子句（由逗号分隔）分别对每个子句实施两个步骤，就像子句被分隔成个体的import语句。

详细的第一步，查找并加载模块更详细的描述在[import system](https://docs.python.org/3/reference/import.html#importsystem)部分，它同样描述可以被导入的模块和各种类型的包，以及所有的钩子可用于自定义导入系统。请注意在此步骤中的失败可能表明该模块无法找到，或者模块初始化错误，包含可执行模块的代码错误。

如果请求的模块检测到成功，它将提供局部空间中的三种方式中的一种：

- 如果模块名后跟[as](https://docs.python.org/3/reference/compound_stmts.html#as)，那么这个名字后[as](那么这个名字后)会直接绑定到导入的模块。

- 如果没有指定其他名字，并且被导入的是顶层模块，模块名被绑定在本地的命名空间作为引用导入模块。

- 如果导入*不是*顶层模块，那么包含该模块的顶层包名被绑定到本地命名空间，作为一个顶级包的引用。导入的模块，必须可以使用完全限定名称访问而不是直接访问。


[from](https://docs.python.org/3/reference/simple_stmts.html#from)方式的使用过程稍微复杂：

1. 查找[from](https://docs.python.org/3/reference/simple_stmts.html#from)子句中指定的模块，如果需要加载并初始化它。
2. 对于每一个import子句的指定标识符：
	1. 检查import的模块具有该属性的名称
	2. 如果没有，尝试根据名称导入子模块，然后在导入的模块中再次检查该属性。
3. 如果属性没有找到，抛出[ImportError](https://docs.python.org/3/library/exceptions.html#ImportError)异常。
4. 否则，在本地命名空间中存储这个引用值，如果存在使用[as](https://docs.python.org/3/reference/compound_stmts.html#as)子句里的名称，否则使用属性名

Examples:
例子：


    import foo                # foo imported and bound locally
    import foo.bar.baz        # foo.bar.baz imported, foo bound locally
    import foo.bar.baz as fbb  # foo.bar.baz imported and bound as fbb
    from foo.bar import baz   # foo.bar.baz imported and bound as baz
    from foo import attr      # foo imported and foo.attr bound as attr
    

如果标识符列表由星号('*')取代,该模块中定义的所有公共名称，都在[import](https://docs.python.org/3/reference/simple_stmts.html#import)语句所在的本地命名空间中被约束。

一个模块所定义的“*公共名称*”通过检查该模块的命名空间中的名为 __ __all__ __ 的变量决定。如果(该变量)有定义，它必须是一个字符串的有序序列，这些字符串是由该模块定义或者导入的名字。在 __ __all__ __中给出的名字都被认为是公共的且要求其存在。如果 __ __all__ __ 没有定义，(该模块的)公共名字的集合就包含所有在该模块的名字空间中找到的，不以下划线(” _ ”)起首的所有名字。


导入的通配符形式 - `from module import * `- 只允许模块级别。试图在类或者函数中使用它会抛出[SyntaxError](https://docs.python.org/3/library/exceptions.html#SyntaxError)。 

在指定你要导入的模块时，你并不一定要指定模块的绝对名字。当一个模块或包是在其他包之内时，在同一个顶层包下，我们有可能使用不需指明包名的相对加载。通过在[from](https://docs.python.org/3/reference/simple_stmts.html#from)之后的模块或者包之前指定几个句号,
就可以在没有精确名字的情况下，指定在当前包层次中向上搜索多少层。一个句号代表包括导入操作所在模块的当前包。两个句号代表上一个包层次，三个句号代表再上一个包层次,依次类推。所以,如果你在 pkg 包中执行 from.import mod ，你就会导入pkg.mod。如果你从包 pkg.subpkg1 执行 from ..subpkg2 import mod ，你就会导入 pkg.subpkg2.mod。相对导入的规范参见[PEP328](http://www.python.org/dev/peps/pep-0328)

函数[importlib.import_module()](https://docs.python.org/3/library/importlib.html#importlib.import_module)用于支持要确定哪些模块需要动态加载的应用程序。


# 7.11.1 Future 语句 Future statements #

*future语句*是一个编译器指令,在指定的将来的Python版本应该可以使用语法或语义去编译一个特定的模块，到那时Python特性将成为标准。

future语句旨在缓解未来版本Python的迁移，这类版本会引入语言不兼容的变化。在发布前它允许在每个模块的基础上使用新特性，发布后这个特性将成为标准。
    
    future_statement ::=  "from" "__future__" "import" feature ["as" name]
      ("," feature ["as" name])*
      | "from" "__future__" "import" "(" feature ["as" name]
      ("," feature ["as" name])* [","] ")"
    feature  ::=  identifier
    name ::=  identifier

future语句必须出现在模块的顶部。唯一能出现在future前面的语句是：

- 该模块的文档字符串（如果有），
- 注释,
- 空白行，和
- 其它future语句。

Python 3.0 版本公认的特性是`absolute_import`, `division`, `generators`, `unicode_literals`, `print_function`, `nested_scopes`和`with_statement`。他们都是多余的,因为他们总是在启用状态,同时只保持向后兼容的可编译性。

在编程的时候future语句被识别出来，并且被特殊地对待：更改核心构建的语义通常是通过生成不同的代码来实现的。甚至有可能一个新的功能会引入一个新的不兼容的语法（比如一个新的保留字），在这种情况下，编译器可能需要用区别对待的方式解析模块。此类决定不能被推迟到运行时。

对于任何发布的版本，编译器明确已经被定义的功能名称，如果future语句包含未知功能时，编译器会提示编译出错。

直接运行语义作为任何导入语句都是相同的：这里有一个标准的[__ __future__ __](https://docs.python.org/3/library/__future__.html#module-__future__)模块，稍后会解释，当future语句被执行的时候，将以通用方式被引入。

令人关注的运行时语义取决于被future语句启用的特定功能。

请注意，没有什么特别的语句。

    import __future__ [as name]

那个不是future语句;这是一个普通的导入语句没有特殊的语义或语法的限制。

通过调用内置函数[exec()](https://docs.python.org/3/library/functions.html#exec)和[compile()](https://docs.python.org/3/library/functions.html#compile)来编译的代码，此代码在M模块发起，并包含future语句，在默认情况下，会结合future语句采用新的语法和语义。这可以由可选参数来控制去[compile()](https://docs.python.org/3/library/functions.html#compile)——有关详细信息,请参阅文档的功能。


在交互式解析器提示符下键入future语句，重启解析器器会话生效。如果解析器以-i选项开始，且是通过一个脚本名去执行，同时这个脚本包含future语句，它将作用在交互会话中，在脚本执行后开始。

>**参见:**  
[PEP 236](http://www.python.org/dev/peps/pep-0236) - Back to the __ future__   
The original proposal for the __ future__ mechanism.  

# 7.12 global语法 The global statement #
global_stmt ::=  "global" [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ("," [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier))*

[global](https://docs.python.org/3/reference/simple_stmts.html#global)语句是对整个代码块都有作用的一个声明. 它指出其所列的标识符要解释为全局的.如果某名字在本地命名空间中没有定义, 就自动使用相应的全局名字. 没有[global](https://docs.python.org/3/reference/simple_stmts.html#global)是不可能手动指定一个名字是全局的。

在[global](https://docs.python.org/3/reference/simple_stmts.html#global)中出现的名字不能在[global](https://docs.python.org/3/reference/simple_stmts.html#global) 之前的代码中使用.

在[global](https://docs.python.org/3/reference/simple_stmts.html#global)中出现的名字不能作为形参, 不能作为[for](https://docs.python.org/3/reference/compound_stmts.html#for)循环的控制对象, 不能在[类](https://docs.python.org/3/reference/compound_stmts.html#class)定义, 函数定义, [import](https://docs.python.org/3/reference/simple_stmts.html#import)语句中出现.

CPython详细实现：当前实现不强制两个限制，但程序不应该滥用这种自由，作为将来的实现可能会强制他们或默默地改变程序的意义。

程序员注意: [global](https://docs.python.org/3/reference/simple_stmts.html#global)是一个解析器的指示字. 它仅仅会对和[global](https://docs.python.org/3/reference/simple_stmts.html#global)一起解析的代码有效，尤其是，一个[global](https://docs.python.org/3/reference/simple_stmts.html#global)语句被包含在字符串或者代码对象提供内建的[exec()](https://docs.python.org/3/library/functions.html#exec)函数，并不影响包含该函数调用的代码块，相同的机制也应用于[eval()](https://docs.python.org/3/library/functions.html#eval) 和[compile()](https://docs.python.org/3/library/functions.html#compile) 函数。


# 7.13 nonlocal 语句 The nonlocal statement #
nonlocal_stmt ::=  "nonlocal" [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ("," [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier))*

[nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)语句引发列出的标识符，适用于不包括global之外最近的封闭范围，前一个绑定变量。这是重要的因为绑定的默认行为是首先搜索本地命名空间。该语句允许封装的代码绑定除了global（模块）范围之外，局部作用域的变量。


在[nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)语句不同于那些在global语句中列出的名称，必须引用到预先存在的封闭范围中的绑定（不能明确的确定要在其中创建一个新绑定的范围）。

在[nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)语句中列出的名字，在本地范围必须不和已经存在的绑定冲突。

>**参见:**  
[PEP 3104](http://www.python.org/dev/peps/pep-3104) - Access to Names in Outer Scopes  
The specification for the [nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal) statement.

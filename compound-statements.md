# 复合语句 - Compound statements

>
# 8.复合语句

复合语句中包含了其他语句（语句组）；它以某种方式影响或控制着其他语句的执行。一般来讲，复合语句会跨越多个行，然而一个完整的复合语句也可以简化在一行中。

[if](https://docs.python.org/3/reference/compound_stmts.html#if)，[while](https://docs.python.org/3/reference/compound_stmts.html#while) 及[for](https://docs.python.org/3/reference/compound_stmts.html#for) 语句实现了传统的流控制机制。[try](https://docs.python.org/3/reference/compound_stmts.html#try)语句为一组语句指定了异常处理器和/或资源清除代码，[with](https://docs.python.org/3/reference/compound_stmts.html#with) 表达式允许在代码块上下文执行代码初始化并做后续处理。函数及类的定义也被看作是复合语句。

复合语句由一个或多个‘子句’组成。一个子句由一个头部和一个‘代码序列’组成。特定复合语句的子句头具有相同的缩进层次。每个子句头均以一个唯一的标识关键字开始，并以一个冒号结束。一个语句序列是由子句控制的一组语句。一个语句序列可以包含一个或多个以分号分隔且与子句头同行的语句。或者它也可以是一个或多个在后续各行中缩进的语句。只有在后者的情况下子句序列允许包括有嵌套的复合语句，一下形式是非法的，这样限制原因是 [if](https://docs.python.org/3/reference/compound_stmts.html#if) 后面有 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句的话，会导致语义不明确。  

    if test1: if test2: print(x)

还需要注意的是，在这样的上下文中，分号的优先级比冒号的高，所以在下面的例子中，要么所有的 [print()](https://docs.python.org/3/library/functions.html#print) 方法都会被执行，要么所有方法都不会被执行。

    if x < y < z: print(x); print(y); print(z)

总结：  
**compound_stmt** ::=  [if_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-if_stmt)  
| [while_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-while_stmt)  
| [for_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-for_stmt)  
| [try_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-try_stmt)  
| [with_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-with_stmt)  
| [funcdef](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-funcdef)  
| [classdef](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-classdef)  
**suite**         ::=  [stmt_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-stmt_list) NEWLINE | NEWLINE INDENT [statement](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-statement)+ DEDENT  
**statement**     ::=  [stmt_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-stmt_list) NEWLINE | [compound_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-compound_stmt)  
**stmt_list**     ::=  [simple_stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-simple_stmt) (";" [simple_stmt](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-simple_stmt))* [";"]

注意以 `NEWLINE` 结尾的语句可能后缀一个 `DEDENT`。同时需要注意的是，可选的续行子句通常以某个不能开始一个语句的关键字开头，因此这里没有歧义（‘不定义的 [else](https://docs.python.org/3/reference/compound_stmts.html#else) ’的问题已经由 Python 根据对嵌套的语句的缩进要求解决掉了）。

为了能够叙述清楚，以下章节中的每个子句的语法规则格式都被分行说明。

## 8.1 [if](https://docs.python.org/3/reference/compound_stmts.html#if) 语句

[if](https://docs.python.org/3/reference/compound_stmts.html#if) 语句用于条件性执行：

**if_stmt** ::=  "if" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
( "elif" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite) )*  
["else" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)]

通过依次计算每个表达式的值，直到找到表达式为 true 的值时，它会准确地选择执行相应一个语句序列（对真和假的定义参见 [Boolean operations](https://docs.python.org/3/reference/expressions.html#booleans) 章节）；然后该语句序列被执行（ [if](https://docs.python.org/3/reference/compound_stmts.html#if) 语句的其它部分不会被执行或计算）。如果表达式的值都为 false，并且存在 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句，则 else 子句被执行。

## 8.2 [while](https://docs.python.org/3/reference/compound_stmts.html#while)语句

只要条件表达式的值为 true，[while](https://docs.python.org/3/reference/compound_stmts.html#while)语句会重复执行某个代码段：

**while_stmt** ::=  "while" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
["else" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)]

在上例中，会重复计算表达式(expression)的值，并且如果表达式为真就执行第一个语句序列(suit)；如果为假（可能是在执行第一次计算时）就会执行 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句（如果给出的话），并退出循环。

第一个语句序列中的 [break](https://docs.python.org/3/reference/simple_stmts.html#break) 语句可以实现不执行 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句而直接退出循环。第一个语句序列中的 [continue](https://docs.python.org/3/reference/simple_stmts.html#continue) 子句可以跳过该子句的其余部分，直接进入下次表达式的计算。

## 8.3 [for](https://docs.python.org/3/reference/compound_stmts.html#for) 语句

[for](https://docs.python.org/3/reference/compound_stmts.html#for) 语句用于迭代有序序列或其他可迭代对象的元素（比如字符串，数组或列表）。

**for_stmt** ::=  "for" [target_list](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target_list) "in" [expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
["else" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)]

表达式列表（expression list）仅被计算一次，它应该生成一个可迭代的对象。为 `expression_list` 的结果创建一个迭代器。对于迭代器中的每一个元素，语句序列都会以迭代器返回的结果为序执行一次。每个元素使用标准的赋值规则（详见 [Assignment statements](https://docs.python.org/3/reference/simple_stmts.html#assignment) ）依次赋给循环控制对象表, 然后执行语句序列。元素迭代完后（当迭代序列为空或迭代器抛出 [StopIteration](https://docs.python.org/3/library/exceptions.html#StopIteration) 异常），执行语句序列中的 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句（如果存在）然后循环终止。

第一个语句序列中的 [break](https://docs.python.org/3/reference/simple_stmts.html#break) 语句可以实现不执行 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句而终止循环。在第一个语句序列中的 [continue](https://docs.python.org/3/reference/simple_stmts.html#continue) 语句可以跳过该子句的其余部分，直接进行下个元素的迭代计算，或者当迭代完成后进入 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句。

for循环语句序列可以对循环控制对象列表中的变量赋值。这将覆盖所有以前分配给那些变量的值，包括for循环中的语句序列中的变量：

    for i in range(10):
    print(i)
    i = 5             # this will not affect the for-loop
                      # because i will be overwritten with the next
                      # index in the range

循环结束时循环控制对象列表中的名字并未删除，但是如果序列为空，它在循环中根本不会被赋值。提示：内建函数 [range()](https://docs.python.org/3/library/stdtypes.html#range) 返回一个整数列表，可以用于模拟 Pascal 语言中的 `for i := a to b do` 的行为；例如 `list(range(3))` 返回列表 `[0,1,2]`。

>**注意**：如果序列对象在循环过程中被修改（只有可变类型会出现这种情况，例如列表），这里有一些需要注意的地方。有一个内部计数器用于跟踪下一轮循环使用的元素，并且每迭代一次便增加一次。当这个计数器的值达到了序列的长度时循环终止。这就意味着如果从语句序列中删除当前（或前一个元素）元素，下一个元素会被跳过而不被执行（因为当前索引值的元素已经处理过了）。另一方面，如果在当前元素前插入一个元素，下一轮循环时当前元素会被再次重复处理。这会导致难以察觉的错误，但可以通过使用含有整个有序类型对象的片段而生成的临时拷贝避免这个问题，例如：
>   
    for x in a[:]:
        if x < 0: a.remove(x)

## 8.4 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 语句

[try](https://docs.python.org/3/reference/compound_stmts.html#try) 语句可以为一组语句指定异常处理器和/或资源清理代码：

**try_stmt**  ::=  try1_stmt | try2_stmt  
**try1_stmt** ::=  "try" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
("except" [[expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ["as" [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)]] ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite))+  
["else" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)]  
["finally" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)]  
**try2_stmt** ::=  "try" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
"finally" ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)

[except](https://docs.python.org/3/reference/compound_stmts.html#except) 子句指定了一个或多个异常处理器。
如果在 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 子句中未捕获任何异常，则异常处理器不会被执行。当 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 子句中有异常捕获时，就会开始查找异常处理器。它会依次查找异常处理子句，直到找到能够匹配该异常的子句。如果存在未指定异常的 except 语句，则必须放在最后，它会匹配任何异常。当 except 子句中声明了异常类型时，该类型表达式的值会被计算. 如果结果对象与该异常匹配, 那么该子句就匹配了这个异常。只有满足以下条件才认为一个对象匹配某个异常：1、该对象是异常对象本身或其基类；2、该对象是一个数组，包含了一个与该异常兼容的对象。

如果没有 except 子句能够匹配异常，将会在调用栈 [[1](https://docs.python.org/3/reference/compound_stmts.html#id5)] 的外围代码中继续查找异常处理器。

如果在 except 子句头部计算表达式时就引发了异常, 原来的异常处理器查找工作就会被中断, 并在外层代码及调用栈搜索新的异常处理器(就好像处理整个 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 语句发生了异常一样)。

当找到了一个匹配的 except 子句时，异常被赋值给 except 子句中 [as](https://docs.python.org/3/reference/compound_stmts.html#as) 关键字后指定的对象（如果存在），并且执行该 except 语句序列。所有 except 子句都必须包含可执行的代码块。当该代码块执行结束后, 会转到整个 try 语句之后继续正常执行(这意味着, 如果有两个嵌套的异常处理器用于捕获同一个异常, 并且异常由内层的处理器处理, 那么外层处理器就不会响应这个异常)。

当使用 `as target` 关键词给异常赋值时，该 target 会在 except 子句结束后被销毁。例如：

    except E as N:
    foo

可以解释为：

    except E as N:
    try:
        foo
    finally:
        del N

这就意味着必须给异常指派一个不同的名称，以保证在 except 子句结束后可以索引到它。这些异常最后会被清除，因为这些异常与堆栈信息会绑定在一起，他们与栈帧形成了循环引用。直到下次垃圾回收发生前，所有这些栈中的局部变量都是存活的。

except 子句被执行前，该异常的详细信息存储在 [sys](https://docs.python.org/3/library/sys.html#module-sys) 模块中，可以通过 [sys.exc_info()](https://docs.python.org/3/library/sys.html#sys.exc_info).[sys.exc_info()](https://docs.python.org/3/library/sys.html#sys.exc_info) 方法读取，该方法返回由异常类、异常实例以及能够标识程序中异常发生位置的堆栈信息（详见 [The standard type hierarchy](https://docs.python.org/3/reference/datamodel.html#types)）组成的三元组组成。当返回发生异常的函数时，[sys.exc_info()](https://docs.python.org/3/library/sys.html#sys.exc_info) 的值会恢复调用之前的值。

当程序控制器从 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 子句中执行结束后，可以执行可选的 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句。[[2](https://docs.python.org/3/reference/compound_stmts.html#id6)]在 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 子句中引发的异常不会在前面的 [except](https://docs.python.org/3/reference/compound_stmts.html#except) 子句中得到处理。

如果存在 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块，它是一个资源“清理”处理器。当执行[try](https://docs.python.org/3/reference/compound_stmts.html#try) 代码块，包括 [except](https://docs.python.org/3/reference/compound_stmts.html#except) 与 [else](https://docs.python.org/3/reference/compound_stmts.html#else) 过程中有异常发生且未被处理时，这些异常就会被临时保存下来。[finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块会被执行，如果有临时保存的异常的话，该异常会在 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块执行结束后被重新抛出。如果 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块产生了另一个异常，已被保存的异常会作为新异常的上下文保存下来。如果 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块中执行了 [return](https://docs.python.org/3/reference/simple_stmts.html#return) 或 [break](https://docs.python.org/3/reference/simple_stmts.html#break) 语句，则忽略已保存的异常：

    >>> def f():
    ...     try:
    ...         1/0
    ...     finally:
    ...         return 42
    ...
    >>> f()
    42

在执行 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块时，程序的异常信息是无效的。

[try](https://docs.python.org/3/reference/compound_stmts.html#try)...[finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 语句中，[try](https://docs.python.org/3/reference/compound_stmts.html#try) 代码块中的 [return](https://docs.python.org/3/reference/simple_stmts.html#return)，[break](https://docs.python.org/3/reference/simple_stmts.html#break) 或者 [continue](https://docs.python.org/3/reference/simple_stmts.html#continue)执行后，[finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块也会被执行。在 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块中不允许出现 [continue](https://docs.python.org/3/reference/simple_stmts.html#continue) 子句（该问题是由当前实现导致的——这个限制可能会在后续的版本中去掉）。

函数的返回值由程序最后的 [return](https://docs.python.org/3/reference/simple_stmts.html#return) 语句的执行结果决定。由于 [finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块一定会被执行，所以[finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 块中的 [return](https://docs.python.org/3/reference/simple_stmts.html#return) 语句一定是最后才会被执行的。

    >>> def foo():
    ...     try:
    ... return 'try'
    ... finally:
    ... return 'finally'
    ...
    >>> foo()
    'finally'

更多关于异常的信息可以查看 [Exceptions](https://docs.python.org/3/reference/executionmodel.html#exceptions) 章节，关于使用 [raise](https://docs.python.org/3/reference/simple_stmts.html#raise) 语句抛出异常的详细信息可以从 [The raise statement](https://docs.python.org/3/reference/simple_stmts.html#raise) 部分找到。

## 8.5 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句

[with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句使用上下文管理器中定义的方法将执行的代码块包裹起来（详见 [With Statement Context Managers](https://docs.python.org/3/reference/datamodel.html#context-managers)）。为了便于重用，允许使用传统的 [try](https://docs.python.org/3/reference/compound_stmts.html#try)...[except](https://docs.python.org/3/reference/compound_stmts.html#except)...[finally](https://docs.python.org/3/reference/compound_stmts.html#finally) 异常处理模式对该语句进行封装。

with_stmt ::=  "with" with_item ("," with_item)* ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
with_item ::=  [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression) ["as" [target](https://docs.python.org/3/reference/simple_stmts.html#grammar-token-target)]

使用 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句执行一个“元素”的处理过程如下：

1、根据上下文表达式（[with_item](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-with_item) 中给出的表达式）的值得到一个上下文管理器。  
2、加载上下文管理器的 [_  __exit_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__exit__)方法，后续使用。  
3、调用上下文管理器的 [_ _ _enter_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__enter__)方法。  
4、如果 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句中包含一个对象，则将 [_ _ _enter_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__enter__) 方法的返回值赋值给该对象。

>**注意：**[with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句能够保证只要 [_ _ _enter_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__enter__) 方法能够正确返回，则 [_  _ _exit_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__exit__) 方法一定会被调用。因此，如果在给对象列表赋值过程中发生了错误，对该错误的处理方式与语句序列中的错误处理方式是一致的。见步骤6。
  
5、执行语句序列。  
6、上下文管理器的 [_  __exit_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__exit__) 方法被调用。如果异常导致必须退出语句序列，其类型、值以及堆栈信息会作为参数传递给 [_  __exit_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__exit__)。否则，会给该方法传递三个 [None](https://docs.python.org/3/library/constants.html#None) 参数。

如果由于异常导致语句退出，且 [_  _ _exit_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__exit__) 的返回值为 false，该异常会被再次抛出。如果返回值为 true，则异常被抑制并且会继续执行 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句之后的代码。

如果由于其他非异常原因导致语句退出，则[_  _ _exit_ _ _()](https://docs.python.org/3/reference/datamodel.html#object.__exit__)的返回值被忽略，且会从退出的正常位置继续执行。

上下文管理器会像处理多个嵌套 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句一样处理多个元素：

    with A() as a, B() as b:
    suite

等价于
    
    with A() as a:
    with B() as b:
    suite

3.1 版本中新特性：支持多上下文表达式。

>**参见：**  
[PEP 0343](http://www.python.org/dev/peps/pep-0343) - “with” 语句  
详细介绍了Python 中 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句的特性，知识背景以及一些参考实例。

## 8.6. 函数定义

函数定义，定义了一个用户自定义的函数对象（参见 [The standard type hierarchy](https://docs.python.org/3/reference/datamodel.html#types)）。

**funcdef**        ::=  [[decorators](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-decorators)] "def" [funcname](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-funcname) "(" [[parameter_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter_list)] ")" ["->" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)] ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
**decorators**     ::=  [decorator](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-decorator)+  
**decorator**      ::=  "@" [dotted_name](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-dotted_name) ["(" [[parameter_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter_list) [","]] ")"] NEWLINE  
**dotted_name**    ::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) ("." [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier))*  
**parameter_list** ::=  ([defparameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-defparameter) ",")*  
| "*" [[parameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter)] ("," [defparameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-defparameter))* ["," "**" [parameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter)]  
| "**" [parameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter)  
| [defparameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-defparameter) [","] )  
**parameter**      ::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier) [":" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
**defparameter**   ::=  [parameter](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter) ["=" [expression](https://docs.python.org/3/reference/expressions.html#grammar-token-expression)]  
**funcname**       ::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)  

函数定义是一个可执行语句。它在当前局部命名空间中将函数名称与函数对象（函数的可执行代码的组合）捆绑在一起。该函数对象包括着一个全局命名空间的引用，以便在调用时使用。

函数定义不执行函数体；只有当函数被调用时才会执行函数体。[[3](https://docs.python.org/3/reference/compound_stmts.html#id7)]

函数的定义可能被若干 [decorator](https://docs.python.org/3/glossary.html#term-decorator) 修饰表达式修饰。当函数被定义时在函数定义的范围内计算修饰表达式的值。其结果必须是一个回调，该回调作为函数对象的唯一参数被调用。返回值绑定函数名称，而不是绑定函数对象。多装饰被应用于嵌套方式。例如下方的代码：

    @f1(arg)
    @f2
    def func(): pass

等价于

    def func(): pass
    func = f1(arg)(f2(func))

当一个或多个 [parameters](https://docs.python.org/3/glossary.html#term-parameter)（参数）包含形参 `=` 表达式时，这样的函数就称为具有“默认参数值”的函数。在调用有默认参数值参数的函数时，其对应的参数就可以忽略，这种情况下默认值会用于替代该参数。如果一个参数有默认值，该参数之后，`*`之前的所有参数都必须有默认值——这是一个在语法中未表述的句法限制。

默认参数值在函数定义被执行时从左至右计算。这意味着在函数定义时，该表达式仅被执行一次，且在每次调用时都使用相同的“预计算”值。这在理解默认参数值是一个像列表或者字典这样的可变对象时尤其值得注意。如果函数修改了这个对象（例如给一个列表中追加了一个元素），默认值也随之被修改。这显然不是我们期望的。一个避免这个问题的方法就是使用 `None` 作为默认值，并在函数体中做显式测试，例如：

    def whats_on_the_telly(penguin=None):
    if penguin is None:
    penguin = []
    penguin.append("property of the zoo")
    return penguin

函数调用语义的详细介绍请参见 [Call](https://docs.python.org/3/reference/expressions.html#calls) 章节。通常一个函数调用会给所有出现在参数列表中的参数赋值，要么通过位置参数，或者通过关键字参数，或者通过默认值。如果参数列表中包含 `*identifier` 形式，它会被初始化为一个数组用于接收所有额外的位置参数，其默认为空数组。如果参数列表中包含 `**identifier` 形式，它会被初始化为一个新的字典用于接收所有二外的关键字参数，其默认值为一个空字典。`*` 或 `*identifier` 后的参数为 keyword-only 参数，只能通过关键字参数传值。

参数列表可能包含“参数名称后紧跟 `: expression` ”形式的注解。所有参数都有注解，甚至 `*identifier` 或 `**identifier` 形式的参数也有注解。函数参数列表后可能包含 `-> expression` 形式的“return”注解。这些注解可以是任意合法的 Python 表达式，并且会在函数定义执行时计算这些表达式的值。注解的计算顺序可能会与其在源代码中出现的书序不同。注解的出现不会改变一个函数的语义。函数对象的 _ _ _annotations_ _ _属性中的参数名称可以作为字典的 key，其对应的 value 为注解的值。

也可以创建能直接在表达式中使用的匿名函数（未与名字绑定的函数）。这是通过 lambda 表达式实现的，详见 [Lambdas](https://docs.python.org/3/reference/expressions.html#lambda) 
章节。注意 lambda 仅仅是一个简单函数的缩写形式；以“[def](https://docs.python.org/3/reference/compound_stmts.html#def)”定义的函数可以被传递或赋予一个新的名字，就像以 lambda 表达式定义的函数一样。以“[def](https://docs.python.org/3/reference/compound_stmts.html#def)”形式定义的函数功能要更强大些，因为它允许执行多条语句及注解。

**程序员注意：**函数是一类对象。在函数定义中执行“`def`”，定义了一个可返回或传递的局部函数。在嵌套函数中使用的自由变量可以获取包含 def 的函数对象的局部变量。详见 [Naming and binding](https://docs.python.org/3/reference/executionmodel.html#naming)章节。

>**参见：**  
[PEP 3107](http://www.python.org/dev/peps/pep-3107) - **函数注解**  
函数注解的原始规范。

## 8.7. 类定义

一个类定义定义了一个类对象（见 [The Standard type hierarchy] (https://docs.python.org/3/reference/datamodel.html#types)）：

**classdef**    ::=  [[decorators](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-decorators)] "class" [classname](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-classname) [[inheritance](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-inheritance)] ":" [suite](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-suite)  
**inheritance** ::=  "(" [[parameter_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-parameter_list)] ")"  
**classname**   ::=  [identifier](https://docs.python.org/3/reference/lexical_analysis.html#grammar-token-identifier)  

一个类定义是一个可执行的语句。继承关系表通常会给出一个基类列表（获取更多高级应用可以参见 [Customizing class creation](https://docs.python.org/3/reference/datamodel.html#metaclasses)），因此继承关系列表中的每个元素都生成一个允许子类化的类对象。在继承关系列表中未包含的类默认继承自 [object](https://docs.python.org/3/library/functions.html#object) 类；因此，

    class Foo:
    pass

等价于

    class Foo(object):
    pass

然后类的语句序列在一个新的堆栈结构中（见 [Naming and binding](https://docs.python.org/3/reference/executionmodel.html#naming)）使用新创建的局部命名空间以及原始的全局命名空间执行。（通常情况下，该套件包含的大多是函数定义。）当该类的语句序列执行结束后就丢弃其执行堆栈，但是它的局部命名空间会被保存下来。[[4](https://docs.python.org/3/reference/compound_stmts.html#id8)]然后使用其继承关系表创建基类，将保存下来的命名空间作为属性字典创建新的类对象。在原始的命名空间中，类的名称会被绑定在这个类对象上。


类的创建可以使用 [metaclasses](https://docs.python.org/3/reference/datamodel.html#metaclasses) 大量定制。

类中也可以加修饰符：就像修饰函数一样，

    @f1(arg)
    @f2
    class Foo: pass

等价于

    class Foo: pass
    Foo = f1(arg)(f2(Foo))

类修饰符表达式的值计算方式与函数修饰符相同。其结果必须是一个类对象，并将该类对象与一个类名绑定。

>**程序员注意：**类定义中定义的变量都是类的属性；类的实例共享这些属性。实例属性可以通过 `self.name = value` 的方式赋值。类属性及实例属性都可以通过 `self.name` 获取，并且如果使用这种方式属性的话，如果实例属性与类属性由相同的名称，那么实例属性会覆盖类属性。类属性可以作为实例属性的默认值，但是使用可变值可能会导致不可预期的结果。[Descriptors](https://docs.python.org/3/reference/datamodel.html#descriptors) 可以用于创建包含不同实现细节的实例变量。


**参见：** [PEP 3115](http://www.python.org/dev/peps/pep-3115) - Python 3 中的 Metaclasses [PEP 3129](http://www.python.org/dev/peps/pep-3129) - 类修饰符

# 脚注

[[1](https://docs.python.org/3/reference/compound_stmts.html#id1)] 该异常会传递到调用堆栈中，除非在 finally 代码块中碰巧抛出了另一个异常。这个新异常会导致旧异常丢失。

[[2](https://docs.python.org/3/reference/compound_stmts.html#id2)] 在遇到异常或者执行 [return]()，[continue]() 或 [break]() 语句的时，程序控制器会离开 except 块。

[[3](https://docs.python.org/3/reference/compound_stmts.html#id3)] 一个字符串文本作为函数体的第一段语句出现时会被转换为函数的`__doc__`属性以及函数的 [docstring]（https://docs.python.org/3/glossary.html#term-docstring）。

[[4](https://docs.python.org/3/reference/compound_stmts.html#id4)] 一个字符串文本作为类中的第一段语句出现时会被转换为命名空间的 `__doc__`元素以及类的[docstring](https://docs.python.org/3/glossary.html#term-docstring)。


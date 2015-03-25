# 4. 执行模型

## 4.1. 命名空间和代码块

名字 *Names* 参考对象，名字绑定操作介绍了名字，程序代码中每一个名字的产生都参考从建立在包含使用的最里面功能块名字 *bingding*。

一个代码块是一个可以作为一个单元执行的Python程序文本，像模块，类定义或函数体。每个命令类型的交互是一块。一个脚本文件（一个文件作为标准输入翻译或作为一个命令行参数指定的翻译）是个代码块，一个脚本命令（可选 ‘ **-c** ’ 在命令解释行指定）是个代码块。传递给内建函数 [eval()](https://docs.python.org/3/library/functions.html#eval) 和 [exec()](https://docs.python.org/3/library/functions.html#exec) 的字符串参数是个代码块。

代码块在*运行结构框架*内执行，一个框架包含一些管理信息（用来调试），决定执行完代码块后在哪继续执行和怎么执行。

一个*范围*定义代码块内名字的可视性，如果在一个快中定义了局部变量，它的范围就包含那个代码块，如果定义出现在一个函数块里，那么范围就扩展到任何一个包含这个定义的代码块，除非代码块引进一个不同的绑定名字。类代码块中定义名字范围仅限于类代码块；它不能扩展到方法代码块——包括理解式和生成表达式，因为它们使用一个函数实现范围。这意味着下面执行将会失败：

class A:

    a = 42
    b = list(a + i for i in range(10))

在代码块中使用名字时，使用最近封闭范围解决。所有这样的设置可视范围代码块的集合叫做块的*环境*。

如果一个名字绑定在代码块内，除非声明为 [nonlocal](https://docs.python.org/3/reference/simple_stmts.html#nonlocal)，它的局部变量都是属于那个块的。如果一个名字绑定在模块级别，它是一个全局变量。（模块代码块变量是局部和全局。）如果一个变量没有定义却在模块中使用，那么它是*自由变量*。

当根本没发现一个名字时，抛出异常 [NameError](https://docs.python.org/3/library/exceptions.html#NameError) 。如果名字指向一个没有绑定的局部变量，抛出异常 [UnboundLocalError](https://docs.python.org/3/library/exceptions.html#UnboundLocalError)。[UnboundLocalError](https://docs.python.org/3/library/exceptions.html#UnboundLocalError) 是 [NameError](https://docs.python.org/3/library/exceptions.html#NameError) 的子类。

结构绑定名字：正规参数函数，[import](https://docs.python.org/3/reference/simple_stmts.html#import) 语句，类和函数定义（在代码块中定义的类或函数名字绑定），如果在分配中发生，目标就是标示符，[for](https://docs.python.org/3/reference/compound_stmts.html#for) 循环首部，或者在 [with](https://docs.python.org/3/reference/compound_stmts.html#with) 语句中 [as](https://docs.python.org/3/reference/compound_stmts.html#as) 之后，或 [except](https://docs.python.org/3/reference/compound_stmts.html#except) 子句。[import](https://docs.python.org/3/reference/simple_stmts.html#import) 的形式语句 `from ... import ...*` 绑定所有导入模块中定义的名字，除非那些以下划线开始的。这种形式语句可能仅使用在模块级别。

目标发生在 [del](https://docs.python.org/3/reference/simple_stmts.html#del) 语句中也考虑绑定这个目的（尽管真实的情况是不绑定名字）。

每次分配或导入语句发生在代码块内，它通过类或者函数定义或者在模块级别定义（顶端是代码块）。

如果一个名称绑定操作发生在一个代码块内，块内的所有使用的名称被视为对当前块的引用。这可能导致在一个块之前错误使用一个名字，这条规则是微妙的，Python 中在一个代码块内缺乏声明和允许名称进行绑定的操作。一个代码块的局部变量可以由扫描整个文本块的名称绑定操作。

如果 [global](https://docs.python.org/3/reference/simple_stmts.html#global) 声明发生在一个代码块内，所有在声明中指定使用的名字是指绑定顶级名称空间的名字。顶级命名空间中的名字解析通过搜索全局命名空间，即模块的命名空间包含代码块，内建命名空间，内建模块 [builtins](https://docs.python.org/3/library/builtins.html#module-builtins) 命名空间。先搜索全局命名空间，如果没有找到名字,继续搜索内建命名空间。[global](https://docs.python.org/3/reference/simple_stmts.html#global) 声明必须优先于所有名字的声明。

内建命令名称空间相关联的一个代码块的执行实际上是发现通过它的全局名称空间查找名字 `_ _ builtins _ _` ，这应该是一个字典或一个模块（在后一种情况下所使用的模块的词典）。默认情况下，当在 [_ _ main _ _](https://docs.python.org/3/library/__main__.html#module-__main__) 模块中时， `_ _ builtins _ _` 是内建模块 [builtins](https://docs.python.org/3/library/builtins.html#module-builtins)，当在其他模块中时， `_ _ builtins _ _` 是词典内键模块的别名。`_ _ builtins _ _` 可以设置为用户创建字典,是创建一个限制执行的弱形式。

**CPython 实现细节：**用户不会接触到 `_ _ builtins _ _`，它是个严格的细节实现。用户想要覆盖内建命名空间的值就应该 [import](https://docs.python.org/3/reference/simple_stmts.html#import) 内建模块 [builtins](https://docs.python.org/3/library/builtins.html#module-builtins) 并适当地修改它的属性。

一个模块的命名空间在第一次自动创建时总是被导入的。一个脚本的主模块总是叫 [_ _ main _ _](https://docs.python.org/3/library/__main__.html#module-__main__) 。

在一个相同的代码块内，[global](https://docs.python.org/3/reference/simple_stmts.html#global) 声明和名字绑定操作具有相同的范围，如果最近的封闭范围内自由变量包含一个全局声明,自由变量就会当做全局变量。

### 4.1.1. 动态交互功能

Python 语句中有几种情况是在非法使用时结合最近的包含自由变量的范围。

如果在一个封闭范围中引用一个变量,删除这个名字是非法的。在编译时将产生一个错误报告。

如果函数和函数包含中使用导入 `improt*` 通配符形式，或者是最近的一个自由变量块,编译器将抛出异常 [SyntaxError](https://docs.python.org/3/library/exceptions.html#SyntaxError) 。

函数 [eval()](https://docs.python.org/3/library/functions.html#eval) 和 [exec()](https://docs.python.org/3/library/functions.html#exec) 不能访问解释名字的全局变量。名字可能是解决局部和全局命名空间的调用者。自由变量不能解决最近的封闭命名空间,但在全局命名空间中可以。[[1]](https://docs.python.org/3/reference/executionmodel.html#id3) [eval()](https://docs.python.org/3/library/functions.html#eval) 和 [exec()](https://docs.python.org/3/library/functions.html#exec) 函数可选参数覆盖全局和局部命名空间。如果只指定一个命名空间,两个函数都可以使用。

## 4.2. 异常

异常就是为了处理出错或者处理其它意外情况而中断代码块的正常控制流。异常在错误被检测到的位置被抛出，它可以被其周围相关代码的处理，或者错误发生处直接或间接调用的代码块处理。

Python 解释器在它检测到一个运行时错误时抛出一个异常（比如除以零）。某个Python 程序也可以通过 raise 语句显式地抛出异常。异常处理器可以用 try ... except 语句指定。try ... finally 语句指定清理代码块，但是它不处理异常，只是无论先前代码中是否产生异常都会得到执行。

Python使用所谓的“中断”错误处理模型：一个异常处理器能在外层找出错误发生和继续执行的位置。但是它不能修复错误和重试错误的操作（除非重新从头进入该段出错的代码）。

当一个异常没有得到控制，解释器就中断程序的执行，或返回到它的主循环的迭代中。其它情况下，除了不是 [SystemExit](https://docs.python.org/3/library/exceptions.html#SystemExit) 异常，它还打印一个堆栈跟踪回溯对象。

异常由一个字符串对象或一个类实例标识，所匹配的 [except](https://docs.python.org/3/reference/compound_stmts.html#except) 子句的选择是基于对象标识的：except子句必须引用相同的类或其基类，可以接受处理程序，也可以携带更多关于异常条件的信息。

**注意：**异常消息不是 Python 标准处理借口 API 的一部分，它的内容可能会伴随 Python 版本升级改变，而不带有任何告警，不应依赖于在多个版本的编译器下运行代码。

在 [*The try statement* ](https://docs.python.org/3/reference/compound_stmts.html#try) 章节见 [try](https://docs.python.org/3/reference/compound_stmts.html#try) 的描述，以及  [*The raise statement*](https://docs.python.org/3/reference/simple_stmts.html#raise) 章节见 [raise](https://docs.python.org/3/reference/simple_stmts.html#raise) 的描述。

**脚注**

[[1]](https://docs.python.org/3/reference/executionmodel.html#id1) 这种限制是因为这些操作所执行的代码不可用模块编译。

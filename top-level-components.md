# 9.顶层组件 - Top-level components #


Python解释器的可以有几种输入源：从标准输入或程序参数传入的脚本，交互方式下的输入，从模块源文件，等等。本章给出在这些情况下使用的语法。


# 9.1 完整的Python程序 Complete Python programs #

尽管语法规格说明不需要指明语言的解释器是如何执行的，对一个完整的Python程序的了解也是有用的。一个完整的Python程序运行在一个最低限度的初始环境中：所有内置和标准模块都是可用的，但都没有被初始化，除了[sys](https://docs.python.org/3/library/sys.html#module-sys)(各种系统服务)模块、[builtin](https://docs.python.org/3/library/builtins.html#module-builtins)(内置函数、异常和None)模块和[__ main__](https://docs.python.org/3/library/__main__.html#module-__main__) 模块。main被用来为完整程序的运行提供局部和全局名字空间。

针对于文件输入来说的完整的Python程序语法，在下一节给出描述。

解释器也可以以交互方式运行；在这种情况下，它并不读取和运行一个完整程序，而是一次读取和运行一条语句(可能是复合语句)。这种初始环境与完整程序环境是相同的；每条语句都是在[__ main__](https://docs.python.org/3/library/__main__.html#module-__main__) 名字空间下运行。

在Unix上，一个完整程序可以以三种形式传给解释器：使用-c字符串命令行选项，以一个文件作为命令行的第一个参数，或作为标准输入。如果文件或标准输入是一个tty(终端)设备，解释器进行交互模式；否则，它把文件作为一个完整程序来运行。


# 9.2 文件输入 File input #

所有从非交互文件的输入读取具有相同的形式：

file_input ::=  (NEWLINE | [statement](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-statement))*

这个语法用于以下的情况：



- 当解析一个完整Python程序时(从文件或字符串中)；
- 当解析一个模块时；
- 当解析一个传给[exec()](https://docs.python.org/3/library/functions.html#exec)语句的字符串时；


# 9.3 交互式输入 Interactive input #
交互模式输入使用以下语法进行解析：

    
interactive_input ::=  [[stmt_list](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-stmt_list)] NEWLINE | [compound_stmt](https://docs.python.org/3/reference/compound_stmts.html#grammar-token-compound_stmt) NEWLINE

请注意一个(顶层)复合语句后面在交互模式下必须跟着一个空行；需要用它来帮助解释器检测输入的结束。

# 9.4. 表达式输入 Expression input #

eval()用于表达式的输入。它将忽略前面的空格。Eval()的字符串参数必须具有以下形式：

eval_input ::=  [expression_list](https://docs.python.org/3/reference/expressions.html#grammar-token-expression_list) NEWLINE*
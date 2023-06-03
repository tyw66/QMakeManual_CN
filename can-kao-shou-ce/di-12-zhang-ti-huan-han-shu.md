# 第12章 替换函数

qmake提供了在配置过程中处理变量内容的功能。这些函数被称为替换函数。通常，它们会返回您可以分配给其他变量的值。您可以通过添加\$$运算符前缀来获得这些值。替换函数可分为内置函数库和函数库。

另请参见测试函数。

## 内置替换函数 <a href="#_toc26356" id="_toc26356"></a>

基本的替换函数被实现为内置函数。

absolute\_path(path\[, base])

返回path的绝对路径。

如果未指定基础目录，请使用当前目录作为基础目录。如果它是一个相对路径，则在使用之前将它相对于当前目录进行解析。

例如，下面的调用返回字符串“/home/johndoe/myproject/readme.txt”：

message(\$$absolute\_path("readme.txt", "/home/johndoe/myproject"))

这个函数在Qt5.0中引入。

另请参见clean\_path()，relative\_path()。

basename(variablename)

返回在variablename中指定的文件的基名。

例如：

FILE = /etc/passwd

FILENAME = \$$basename(FILE) #passwd

cat(filename\[, mode])

返回filename的内容。您可以为mode指定以下选项：

* Blob - 将文件的全部内容作为一个值返回
* lines - 将每行作为单独的值返回（没有行结尾）
* true（默认）和false - 文件内容作为单独的值返回，根据qmake值列表分割规则（如变量分配）进行分割。如果模式为false，则将只包含换行符的值插入到列表中，以指示文件中换行符的位置。

clean\_path(path)

处理path，返回带规范化后的目录分隔符（转换为“/”）并删除冗余分隔符的路径，”.”和”..”也（尽可能）进行了处理。此函数包装了QDir::cleanPath。

这个函数在Qt5.0中引入。

另请参见absolute\_path()，relative\_path()，shell\_path()，system\_path()。

dirname(file)

返回指定文件的目录名部分。例如：

FILE = /etc/X11R6/XF86Config

DIRNAME = \$$dirname(FILE) #/etc/X11R6

enumerate\_vars

返回所有已定义的变量名的列表。

这个函数在Qt5.0中引入。

escape\_expand(arg1\[，arg2...，argn])

接受任意数量的参数。它为每个参数展开了转义序列\n、\r、\t，并将这些参数以列表的形式返回。

注：如果指定字符串，则需要避开反斜杠，如下代码片段所示：

message("First line\$$escape\_expand(\\\n)Second line")

find(variablename, substr)

返回variablename中与正则表达式substr匹配的所有值。

MY\_VAR = one two three four

MY\_VAR2 = \$$join(MY\_VAR, " -L", -L) -Lfive

MY\_VAR3 = \$$member(MY\_VAR, 2) \$$find(MY\_VAR, t.\*)

MY\_VAR2将包含“-Lone -Ltwo -Lthree -Lfour -Lfive”，而MY\_VAR3将包含“three two three”。

files(pattern\[, recursive=false])

展开指定的通配符模式，并返回一个文件名列表。如果recursive为真，则此函数将下降到子目录。

first(variablename)

返回变量名的第一个值。

例如，以下调用返回名：

CONTACT = firstname middlename surname phone

message(\$$first(CONTACT))

另请参见take\_first(),last()。

format\_number(number\[, options...])

以options指定的格式返回数字。您可以指定以下选项：

* ibase=n设置输入的基数为n
* obase=n将输出的基数设为n
* width=n 将输出的最小宽度设置为n。如果输出小于width，则会用空格填充它
* zeropad 用零而不是空格来填充输出
* padsign 为输出中的正值添加空格
* alwayssign 在输出中加加号
* leftalign 将内边距放置在输出中的值的右侧

目前不支持浮点数。

例如，以下调用将十六进制数BAD转换为002989：

message(\$$format\_number(BAD, ibase=16 width=6 zeropad))

这个函数在Qt5.0中引入。

fromfile(filename, variablename)

将filename视作qmake项目文件，并返回这个文件中的分配给variablename的值。

另请参见infile()。

getenv(variablename)

返回环境变量变量的值。这基本上相当于\$$(variablename)语法。然而，getenv()支持在其名称中添加圆括号的环境变量。

这个函数在Qt5.12中引入。

join(variablename, glue, before, after)

用glue连接variablename的值。如果此值不是空，则此函数将before值作为前缀，after作为后缀。variablename是唯一需要的字段，其他字段默认为空字符串。如果您需要在glue、before或者after中加入空格，您必须把它们括起来。

last(variablename)

返回variablename的最后一个值。

例如，以下电话返回phone：

CONTACT = firstname middlename surname phone

message(\$$last(CONTACT))

另请参见take\_last(), first()。

list(arg1\[，arg2...，argn])

接受任意数量的参数。它创建一个包含参数列表的唯一命名变量，并返回该变量的名称。您可以使用该变量来编写一个循环，代码片段如下所示：

for(var, \$$list(foo bar baz)) {

...

}

来代替：

values = foo bar baz

for(var, values) {

...

}

lower(arg1 \[, arg2 ..., argn])

接受任意数量的参数，并将其转换为小写字母。

另请参见upper()。

member(variablename \[, start \[, end]])

返回包含start和end之间的索引（0起始）的variablename列表值的切片。

* 如果没有给start，则默认为零。这个用法相当于\$$first(variablename)。
* 如果没有给出结束，则它默认为start。这个用法表示简单的数组索引，因为将只返回一个元素。
* 也可以在单个参数中指定开始和结束，并将数字用两个句点分隔。
* 负数表示从列表结尾开始的索引，-1是最后一个元素。
* 如果任一个索引超出范围，则返回一个空列表。
* 如果结束小于开始，则元素将按相反的顺序返回。

注：结束索引是包含和无序的，这意味着只有当索引无效时（输入变量是空的），才会返回空列表。

[另请参见str\_member()。](https://doc.qt.io/qt-5.15/qmake-function-reference.html%22%20/l%20%22str-member)

num\_add(arg1\[，arg2...，argn])

取任意数量的数字参数，并将它们相加，返回和。

减法被隐式地支持，因为可以简单地在一个数值前加一个负号：

sum = \$$num\_add(\$$first, -\$$second)

如果操作数可能已经为负数，则需要另一个步骤来规范化这个数字：

second\_neg = -\$$second

second\_neg \~= s/^--//

sum = \$$num\_add(\$$first, \$$second\_neg)

该函数在Qt5.8中引入。

prompt(question \[, decorate])

显示指定的question，并返回从stdin中读取的值。

如果decorate是_true_（默认），问题将得到一个通用前缀和后缀，将其标识为提示。

quote(string)

将整个string转换为单个实体并返回结果。这只是一种将字符串封装为双引号的奇特方法。

re\_escape(string)

返回包含使用反斜线转义的每个特殊正则表达式字符的string。这个函数包装了QRegExp::escape。

read\_registry(tree, key\[, flag])

返回树tree中注册表key的值。

只支持树HKEY\_CURRENT\_USER(HKCU)和HKEY\_LOCAL\_MACHINE(HKLM)。

该flag可以是WOW64\_32KEY(32)或WOW64\_64KEY(64)。

注：此功能仅在Windows主机上可用。

该函数在Qt5.12.1中引入。

relative\_path(filePath\[, base])

返回相对于base的filepath的路径。

如果未指定base，则它为当前的项目目录。如果它是相对的，则会相对于当前项目目录进行解析。

如果filePath是相对的，则首先根据base目录进行解析；在这种情况下，此函数有效地充当\$$clean\_path()。

这个函数在Qt5.0中引入。

另请参见absolute\_path()，clean\_path()。

replace(string, old\_string, new\_string)

在作为字符串提供的变量的内容中，用new\_string替换old\_string的每个实例。例如，代码

MESSAGE = This is a tent.

message(\$$replace(MESSAGE, tent, test))

会打印消息：

resolve\_depends(variablename, prefix)

这是一个您通常不需要的内部函数。

这个函数在Qt5.0中引入。

reverse(variablename)

以反向顺序返回变量名称的值。

这个函数在Qt5.0中引入。

section(variablename, separator, begin, end)

返回variablename的值的一部分。这个函数包装了QString::section。

例如，以下调用输出surname：

CONTACT = firstname:middlename:surname:phone

message(\$$section(CONTACT, :, 2, 2))

shadowed(path)

将从项目源目录映射到构建目录的路径。此函数返回源代码内构建的路径。如果路径指向源树之外，则它将返回一个空字符串。

这个函数在Qt5.0中引入。

shell\_path(path)

将path中的所有目录分隔符转换为与构建项目时使用的shell(即由make工具调用的shell)兼容的分隔符。例如，当使用Windows shell时，斜杠将被转换为反斜杠。

这个函数在Qt5.0中引入。

另请参见system\_path()。

shell\_quote(arg)

为构建项目时使用的shell引用arg。

这个函数在Qt5.0中引入。

另请参见system\_quote()。

size(variablename)

返回变量名的值的数量。

另请参见str\_size()。

sort\_depends(variablename, prefix)

这是一个您通常不需要的内部函数。

这个函数在Qt5.0中引入。

sorted(variablename)

返回variablename中的值列表，其中包含按ASCII升序排序的条目。

数值排序可以通过借助format\_number()函数将值零填充到固定长度来完成。

该函数在Qt5.8中引入。

split(variablename, separator)

将variablename的值分割为单独的值，并将它们作为一个列表返回。这个函数包装了QString::split()。

例如：

CONTACT = firstname:middlename:surname:phone

message(\$$split(CONTACT, :))

sprintf(string, arguments...)

将在以逗号分隔的函数参数列表中传递的参数替换字符串中的%1-%9，并返回已处理过的字符串。

str\_member(arg \[, start \[, end]])

这个函数与member()相同，只是它操作了一个字符串值，而不是一个列表变量，因此索引引用了字符位置。

这个函数可用于实现许多常见的字符串切片操作：

\# \$$left(VAR, len)

left = \$$str\_member(VAR, 0, \$$num\_add(\$$len, -1))

\# \$$right(VAR, len)

right = \$$str\_member(VAR, -\$$num, -1)

\# \$$mid(VAR, off, len)

mid = \$$str\_member(VAR, \$$off, \$$num\_add(\$$off, \$$len, -1))

\# \$$mid(VAR, off)

mid = \$$str\_member(VAR, \$$off, -1)

\# \$$reverse(VAR)

reverse = \$$str\_member(VAR, -1, 0)

注：在这些实现中，需要单独处理一个零len参数。

另请参见member()，num\_add()。

该函数在Qt5.8中引入。

str\_size(arg)

返回参数中的字符数。

另请参见size()。

该函数在Qt5.8中引入。

system(command\[, mode\[, stsvar]])

您可以使用系统函数的这个变体从命令中获取静态数据，并将其分配给一个变量。

例如：

UNAME = \$$system(uname -s)

contains( UNAME, \[lL]inux ):message( This looks like Linux (\$$UNAME) to me )

与\$$cat()一样，模式参数将blob、lines、true和false作为值。然而，传统的单词拆分规则（即空或true和false）有细微的差别。

如果传递stsvar，该命令的退出状态将存储在该变量中。如果命令崩溃，状态将为-1，否则为命令选择的非负退出代码。通常，将状态与零（成功）进行比较就足够了。

另请参见system()的测试变体。

system\_path(path)

转换path内的所有目录分隔符为与system()函数用来调用命令的shell程序兼容的分隔符。例如，对于WindowsShell，斜杠会被转换为反斜杠。

这个函数在Qt5.0中引入。

另请参见shell\_path()。

system\_quote(arg)

引用system()函数所使用的shell的arg。

这个函数在Qt5.0中引入了。

[另请参见shell\_quote()。](https://doc.qt.io/qt-5.15/qmake-function-reference.html%22%20/l%20%22shell-quote-arg)

take\_first(variablename)

返回变量variablename的第一个值，并将其从源变量中删除。

例如，这为实现队列提供了便利。

该函数在Qt5.8中引入。

另请参见take\_last()，first()。

take\_last(variablename)

返回变量变量的最后一个值，并将其从源变量中删除。

例如，这为实现堆栈提供了便利。

该函数在Qt5.8中引入。

另请参见take\_first()，last()。

unique(variablename)

返回删除重复项的变量名中的值列表。例如：

ARGS = 1 2 3 2 5 1

ARGS = \$$unique(ARGS) #1 2 3 5

upper(arg1\[，arg2...，argn])

取任意数量的参数，并将其转换为大写字母。

另请参见lower()。

val\_escape(variablename)

以一种允许将variablename解析为qmake代码的方式来逃避变量名的值。

这个函数在Qt5.0中引入。

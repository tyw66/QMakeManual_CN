# 第7章 qmake语言

许多qmake项目文件简单地使用name=value和name+=value定义的列表，描述了项目使用的源文件和头文件。qmake还提供了其他操作符、函数和作用域，可用于处理变量声明中提供的信息。这些高级特性允许从单个项目文件中为多个平台生成Makefile。

## 操作符 <a href="#_toc31159" id="_toc31159"></a>

在许多项目文件中，可以使用赋值（=）和附加（+=）操作符来包含有关项目的所有信息。典型的使用模式是为一个变量分配一个值列表，并根据各种测试的结果附加更多的值。由于qmake使用默认值来定义某些变量，因此有时需要使用删除（-=）操作符来过滤掉不需要的值。下面的部分描述了如何使用操作符来操作变量的内容。

### 分配值 <a href="#_toc26656" id="_toc26656"></a>

\=操作符为一个变量分配一个值：

上面的一行将TARGET变量设置为myapp。这将用myapp覆盖之前为目标设置的任何值。

### 添加值 <a href="#_toc28291" id="_toc28291"></a>

\+=操作符将一个新值附加到变量中的值列表中：

上面的一行将USE\_MY\_STUFF附加到要放在生成的Makefile中的预处理器定义的列表中。

### 删除值 <a href="#_toc22777" id="_toc22777"></a>

\-=操作符将从变量中的值列表中删除一个值：

上面这一行将USE\_MY\_STUFF从生成的Makefile中的预处理器定义列表中删除。

### 添加唯一值 <a href="#_toc31287" id="_toc31287"></a>

\*=运算符将值添加到变量中的值列表中，但仅在它不存在的情况下。这可以防止在变量中多次包含值。例如：

在上一行中，如果USE\_MY\_STUFF尚未定义，则只会被添加到预处理器定义列表中。请注意，unique()函数也可以用于确保一个变量只包含每个值的一个实例。

### 替换值 <a href="#_toc31131" id="_toc31131"></a>

\~=操作符将用指定的值替换与正则表达式匹配的任何值：

DEFINES \~= s/QT\_\[DT].+/QT

在上一行中，列表中以QT\_D或QT\_T开头的任何值都将被替换为QT。

### 变量扩展 <a href="#_toc22980" id="_toc22980"></a>

\$$操作符用于提取变量的内容，并可用于在变量之间传递值或将它们提供给函数：

EVERYTHING = \$$SOURCES \$$HEADERS

message("The project contains the following files:")

message(\$$EVERYTHING)

变量可用于存储环境变量的内容。这些数据可以在运行qmake时进行提取，也可以在构建项目时包含在生成的Makefile中以进行提取。

要在运行qmake时获取环境值的内容，请使用\$$(...)运算符：

DESTDIR = \$$(PWD)

message(The project will be installed in \$$DESTDIR)

在上述赋值中，在处理项目文件时，将读取PWD环境变量的值。

要获取在处理生成的Makefile时的环境值的内容，请使用$(...)运算符：

DESTDIR = \$$(PWD)

message(The project will be installed in \$$DESTDIR)

DESTDIR = $(PWD)

message(The project will be installed in the value of PWD)

message(when the Makefile is processed.)

在上述赋值中，在处理项目文件时立即读取PWD的值，但$(PWD)被分配给生成的Makefile中的DESTDIR。这使得，只要环境变量被正确设置，构建过程就更加灵活。

### 访问qmake属性 <a href="#_toc18233" id="_toc18233"></a>

特殊的\$$\[…]操作符可以用来访问qmake属性：

message(Qt version: \$$\[QT\_VERSION])

message(Qt is installed in \$$\[QT\_INSTALL\_PREFIX])

message(Qt resources can be found in the following locations:)

message(Documentation: \$$\[QT\_INSTALL\_DOCS])

message(Header files: \$$\[QT\_INSTALL\_HEADERS])

message(Libraries: \$$\[QT\_INSTALL\_LIBS])

message(Binary files (executables): \$$\[QT\_INSTALL\_BINS])

message(Plugins: \$$\[QT\_INSTALL\_PLUGINS])

message(Data files: \$$\[QT\_INSTALL\_DATA])

message(Translation files: \$$\[QT\_INSTALL\_TRANSLATIONS])

message(Settings: \$$\[QT\_INSTALL\_CONFIGURATION])

message(Examples: \$$\[QT\_INSTALL\_EXAMPLES])

有关详细信息，请参见配置qmake。

该操作符可访问的属性通常用于允许将第三方插件和组件集成到Qt中。例如，如果在_Qt designer_插件的项目文件中执行了以下声明，那么_Qt designer_插件可以与_Qt designer_的内置插件一起安装：

target.path = \$$\[QT\_INSTALL\_PLUGINS]/designer

INSTALLS += target

## 作用域 <a href="#_toc6715" id="_toc6715"></a>

作用域类似于过程性编程语言中的if语句。如果某个条件为真，则会处理作用域内的声明。

### 作用域语法 <a href="#_toc16258" id="_toc16258"></a>

作用域包括一个条件，后跟同一行上的开括号、一系列命令和定义，以及新行上的闭合括号：

\<condition> {

\<command or definition>

...

}

开口支架必须与条件写一致。范围可以连接以包含多个条件，如下节所述。

### 作用域和条件 <a href="#_toc25464" id="_toc25464"></a>

一个作用域被写成一个条件，后面跟着包含在一对大括号中的一系列声明。例如：

win32 {

SOURCES += paintwidget\_win.cpp

}

在构建Windows平台时，上述代码将把paintwidget\_win.cpp文件添加到生成的Makefile中列出的源文件中。当为其他平台构建时，该定义将被忽略。

在给定范围中使用的条件也可以被否定，以提供一组替代的声明，只有在原始条件为假时才能处理这些声明。例如，要在为除Windows之外的所有平台构建时处理某些东西，请否定这样的范围：

!win32 {

SOURCES -= paintwidget\_win.cpp

}

范围可以嵌套以组合多个条件。例如，要只在启用调试的情况下才包含某个特定平台的特定文件，请写入以下内容：

macx {

CONFIG(debug, debug|release) {

HEADERS += debugging.h

}

}

要保存写入许多嵌套作用域，可以使用：操作符嵌套作用域。上述示例中的嵌套范围可以用以下方式重写：

macx:CONFIG(debug, debug|release) {

HEADERS += debugging.h

}

您还可以使用：运算符来执行单行条件分配。例如：

win32:DEFINES += USE\_MY\_STUFF

上面的行仅在构建Windows平台时，才将USE\_MY\_STUFF添加到DEFINE变量中。通常，：运算符的行为就像一个逻辑与(AND)运算符，将许多条件连接在一起，并要求所有条件都为真。

还有|运算符就像逻辑或(OR)操作符，将许多条件连接在一起，并且只要求其中一个为真。

win32|macx {

HEADERS += debugging.h

}

如果需要混合这两个运算符，则可以使用if函数来指定运算符的优先级。

if(win32|macos):CONFIG(debug, debug|release) {

\# Do something on Windows and macOS,

\# but only for the debug configuration.

}

win32|if(macos:CONFIG(debug, debug|release)) {

\# Do something on Windows (regardless of debug or release)

\# and on macOS (only for debug).

}

该条件接受通配符，以匹配一组CONFIG值或mkspec名称。

win32-\* {

\# Matches every mkspec starting with "win32-"

SOURCES += win32\_specific.cpp

}

注：历史上，使用像上面这样的通配符检查mkspec名称是qmake检查平台的方法。现在，我们建议使用在QMAKE\_PLATFORM变量中由mkspec定义的值。

您还可以使用其他作用域为作用域内的声明提供替代声明。如果上述作用域的条件为假，则处理其他作用域。这允许您在与其他范围（由：操作符分隔）结合时编写复杂的测试。例如：

win32:xml {

message(Building for Windows)

SOURCES += xmlhandler\_win.cpp

} else:xml {

SOURCES += xmlhandler.cpp

} else {

message("Unknown configuration")

}

### 配置和作用域 <a href="#_toc32716" id="_toc32716"></a>

存储在CONFIG变量中的值由qmake特别处理。每个可能的值都可以用作一个范围的条件。例如，CONFIG所持有的值列表可以用opengl值进行扩展：

由于此操作，将处理任何测试opengl的范围。我们可以使用此特性来为最终的可执行文件提供一个适当的名称：

opengl {

TARGET = application-gl

} else {

TARGET = application

}

此特性可以很容易地更改项目的配置，而不会丢失特定配置可能需要的所有自定义设置。在上面的代码中，将处理第一个作用域中的声明，最终的可执行文件将被命名为application-gl。但是，如果没有指定opengl，则将处理第二个作用域中的声明，最终的可执行文件将被命名为application。

由于可以将您自己的值放在CONFIG行上，这为您提供了一种方便的方法来定制项目文件和微调生成的Makefile。

### 平台范围值 <a href="#_toc1198" id="_toc1198"></a>

除了在许多作用域条件下使用的win32、macx和unix值外，还可以使用作用域测试各种其他内置平台和编译器特定的值。这些都是基于Qt的mkspecs目录中提供的平台规范。例如，项目文件中的下列几行显示了当前linux-g++规范正在使用和测试：

message(\$$QMAKESPEC)

linux-g++ {

message(Linux)

}

您可以测试任何其他平台-编译器组合，只要在mkspecs目录中存在一个规范即可。

## 变量 <a href="#_toc27347" id="_toc27347"></a>

项目文件中使用的许多变量都是qmake在生成Makefile时使用的特殊变量，如DEFINES，SOURCES和HEADERS。此外，您还可以为自己使用的变量。qmake遇到该名称的赋值时创建具有给定名称的新变量。例如：

您对自己的变量做什么没有限制，因为qmake会忽略它们，除非它在处理作用域时需要计算它们。

还可以通过将变量名前面加上\$$，将当前变量的值赋给另一个变量。例如：

现在，MY\_DEFINES变量包含了项目文件中此时的定义变量中的内容。这等价于：

第二种表示法允许您将变量的内容附加到另一个值中，而无需用空格分隔两者。例如，以下内容将确保最终可执行文件提供一个包含正在使用的项目模板的名称：

TARGET = myproject\_\$${TEMPLATE}

## 替换函数 <a href="#_toc21623" id="_toc21623"></a>

qmake提供了一个内置函数的选择，以允许处理变量的内容。这些函数处理提供给它们的参数，并因此返回一个值或值列表。要将结果分配给变量，请使用具有此类型函数的\$$运算符，就如同将一个变量的内容分配给另一个变量一样：

HEADERS = model.h

HEADERS += \$$OTHER\_HEADERS

HEADERS = \$$unique(HEADERS)

这种类型的函数应该在赋值的右侧使用（即作为一个操作数）。

您可以定义您自己的函数来处理变量的内容，如下所示：

defineReplace(functionName){

\#function code

}

下面的示例函数将一个变量名作为其唯一的参数，使用eval()内置函数从该变量中提取一个值列表，并编译一个文件列表：

defineReplace(headersAndSources) {

variable = \$$1

names = \$$eval(\$$variable)

headers =

sources =

for(name, names) {

header = \$${name}.h

exists(\$$header) {

headers += \$$header

}

source = \$${name}.cpp

exists(\$$source) {

sources += \$$source

}

}

return(\$$headers \$$sources)

}

## 测试函数 <a href="#_toc15044" id="_toc15044"></a>

qmake提供了内置函数，可以用作编写作用域时的条件。这些函数不返回一个值，而是表示成功或失败：

count(options, 2) {

message(Both release and debug specified.)

}

这种类型的函数只能在条件表达式中使用。

可以定义您自己的函数来为作用域提供条件。以下示例测试列表中的每个文件是否存在，如果它们都存在则返回true，如果不存在则返回false：

defineTest(allFiles) {

files = \$$ARGS

for(file, files) {

!exists(\$$file) {

return(false)

}

}

return(true)

}

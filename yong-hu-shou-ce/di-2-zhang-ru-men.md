# 第2章 入门

本教程教您qmake的基础知识。本手册中的其他主题包含了关于使用qmake的更详细信息。

## 从简单开始 <a href="#_toc23288" id="_toc23288"></a>

假设您刚刚完成了应用程序的基本实现，并且创建了以下文件：

* hello.cpp
* hello.h
* main.cpp

您将在Qt发行版的 examples/qmake/tutorial目录中找到这些文件。关于该应用程序的设置，您唯一需要知道的另一件事是它是用Qt编写的。首先，使用您最喜欢的纯文本编辑器，在 examples/qmake/tutorial中创建一个名为hello.pro的文件。您需要做的第一件事是添加几行代码，以告诉qmake作为开发项目中的源文件和头文件。

我们将首先将源文件添加到项目文件中。为此，您需要使用SOURCE变量。只要用SOURCE += 开始一个新的行，并在它后面放上hello.cpp。现在你应该有了类似如下的内容：

我们对项目中的每个源文件重复此操作，直到我们最终得到以下内容：

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

如果您喜欢使用类似make的语法，一次性列出所有文件，您可以使用换行符转义如下：

`SOURCES += hello.cpp \`

`main.cpp`

现在，源文件已列在项目文件中，则必须添加头文件。这些添加方式与源文件的添加方式完全相同，只是我们使用的变量名是HEADERS。

一旦您完成了此操作，您的项目文件应该是这样的：

`HEADERS += hello.h`

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

系统将自动设置目标名称。它与项目文件名相同，但具有适合于该平台的后缀。例如，如果项目文件被称为hello.pro，则目标在Windows上是hello.exe，在Unix上是hello。如果您想使用一个不同的名称，您可以在项目文件中设置它：

完成的项目文件应该如下：

`HEADERS += hello.h`

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

现在可以使用qmake为应用程序生成Makefile。在命令行中的项目目录中，键入以下内容：

`qmake -o Makefile hello.pro`

然后根据您使用的编译器键入make或nmake。

对于VisualStudio用户，qmake还可以生成VisualStudio项目文件。例如：

`qmake -tp vc hello.pro`

## 使应用程序可调试 <a href="#_toc30248" id="_toc30248"></a>

应用程序的release版本不包含任何调试符号或其他调试信息。在开发过程中，生成一个包含相关信息的应用程序的调试版本是很有用的。通过在项目文件中的CONFIG变量中添加debug，可以很容易地实现这一点。

例如：

`CONFIG += debug`

`HEADERS += hello.h`

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

像以前一样使用qmake来生成一个makefile。在调试环境中运行应用程序时，您将获得有关应用程序的有用信息。

## 添加特定于平台的源文件 <a href="#_toc10561" id="_toc10561"></a>

经过几个小时的编码之后，您可能已经开始使用应用程序中特定于平台的部分，并决定将依赖于平台的代码分开。所以你现在有两个新文件要包含到你的项目文件中：hellowin.cpp和hellounix.cpp。我们不能将这些添加到SOURCE变量，因为这会将两个文件都放在Makefile中。所以，我们在这里需要做的是使用一个作用范围，它将根据我们正在构建的平台进行处理。

一个为Windows添加平台依赖文件简单作用范围的如下所示：

`win32 {`

`SOURCES += hellowin.cpp`

`}`

在为窗口构建时，qmake会将hellowin.cpp添加到源文件列表中。当为任何其他平台构建时，qmake只会忽略它。现在所要做的就是为特定于unix的文件创建一个作用域。

当您这样做后，您的项目文件应该是这样的：

`CONFIG += debug`

`HEADERS += hello.h`

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

`win32 {`

`SOURCES += hellowin.cpp`

`}`

`unix {`

`SOURCES += hellounix.cpp`

`}`

像以前一样使用qmake来生成一个makefile。

## 如果文件不存在，则停止qmake <a href="#_toc25570" id="_toc25570"></a>

如果某个文件不存在，则可能不想创建Makefile。我们可以通过使用exists()函数来检查一个文件是否存在。我们可以通过使用error()来阻止qmake的处理。这种工作方式与范围相同。只需用该函数替换作用域条件即可。检查一个名为main.cpp的文件，如下所示：

`!exists( main.cpp ) {`

`error( "No main.cpp file found" )`

`}`

这个 ! 符号用于否定该测试。也就是说，如果文件存在，exists( main.cpp )为真，如果文件不存在，!exists( main.cpp )为真。

`CONFIG += debug`

`HEADERS += hello.h`

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

`win32 {`

`SOURCES += hellowin.cpp`

`}`

`unix {`

`SOURCES += hellounix.cpp`

`}`

`!exists( main.cpp ) {`

`error( "No main.cpp file found" )`

`}`

像以前一样使用qmake来生成生成文件。如果您暂时重命名main.cpp，您将看到消息，而qmake将停止处理。

## 检查多个条件 <a href="#_toc6831" id="_toc6831"></a>

假设您使用Windows，并且您希望在命令行上运行应用程序时能够看到使用qDebug()的语句输出。若要查看输出，您必须使用适当的控制台设置来构建应用程序。我们可以很容易地将console放在CONFIG这一代码行上，以便在Windows上的makefile 中包含此设置。然而，假设当我们只想在Windows上运行且当debug已经CONFIG这一行时，才添加CONFIG这一行。这就需要使用两个嵌套的作用域。首先创建一个范围，然后在其中创建另一个范围。将要处理的设置放在第二个范围内，如下所示：

`win32 {`

`debug {`

`CONFIG += console`

`}`

`}`

嵌套的作用域可以使用冒号连接在一起，所以最终的项目文件如下所示：

`CONFIG += debug`

`HEADERS += hello.h`

`SOURCES += hello.cpp`

`SOURCES += main.cpp`

`win32 {`

`SOURCES += hellowin.cpp`

`}`

`unix {`

`SOURCES += hellounix.cpp`

`}`

`!exists( main.cpp ) {`

`error( "No main.cpp file found" )`

`}`

`win32:debug {`

`CONFIG += console`

`}`

就这样！您现在已经完成了qmake的教程，并且已经准备好为您的开发项目编写项目文件了。

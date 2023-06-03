# 第13章 测试函数

测试函数返回一个布尔值，您可以在条件作用域中进行测试。测试函数可分为内置的函数库和函数库。

另请参见替换函数。

## 内置测试函数 <a href="#_toc30412" id="_toc30412"></a>

基本的测试函数作为内置函数实现。

cache(variablename,\[set|add|sub]\[transient]\[super|stash],\[source variablename])

这是一个您通常不需要的内部函数。

这个函数在Qt5.0中引入了。

CONFIG(config)

这个函数可以用来测试放置在CONFIG变量中的变量。这与作用域相同，但有一个额外的优点，即可以传递第二个参数来测试处于活动状态的配置项。由于值的顺序在CONFIG变量中很重要（也就是说，后一项将被认为是互斥项中的活动配置），所以可以使用第二个参数来指定一组要考虑的值。例如：

CONFIG = debug

CONFIG += release

CONFIG(release, debug|release):message(Release build!) #将会打印

CONFIG(debug, debug|release):message(Debug build!) #不会打印

因为release被认为是活动设置（用于特性解析），所以它将是用于生成构建文件的配置。在通常情况下，不需要第二个参数，但对于特定的互排斥测试，它是无价的。

contains(variablename, value)

如果variablename变量包含value，则成功；否则失败。可以为参数_value_指定一个正则表达式。

您可以使用作用域来检查此函数的返回值。

例如：

contains( drivers, network ) {

\# drivers contains 'network'

message( "Configuring for network build..." )

HEADERS += network.h

SOURCES += network.cpp

}

只有当driver变量包含值network时，才会处理作用域内的内容。如果是这种情况，适当的文件将被添加到SOURCES和HEADERS中。

count(variablename, number)

如果variablename包含具有指定数量的值的列表，则成功；否则失败。

此函数用于确保只有在变量包含正确数量的值时才处理作用域内的声明。例如：

options = \$$find(CONFIG, "debug") \$$find(CONFIG, "release")

count(options, 2) {

message(Both release and debug specified.)

}

debug(level, message)

检查qmake是否在指定的调试级别上运行。如果是，则它返回true并打印调试消息。

defined(name\[, type])

测试是否定义了函数或变量的name。如果省略了type，会检查所有函数。若要只检查变量或特定类型的函数，请指定类型。它可以具有以下值：

* test仅检查测试函数
* replace仅检查替换函数
* var仅检查变量

equals(variablename, value)

测试变量是否等于字符串值。

例如：

TARGET = helloworld

equals(TARGET, "helloworld") {

message("The target assignment was successful.")

}

error(string)

此函数从不返回一个值。qmake将string作为错误消息显示给用户并退出。此函数只能用于不可恢复的错误。

例如：

error(An error has occurred in the configuration process.)

eval(string)

使用qmake语法规则计算字符串的内容，并返回true。在字符串中可以使用定义和分配来修改现有变量的值或创建新的定义。

例如：

eval(TARGET = myapp) {

message(\$$TARGET)

}

注：可以使用引号标记来分隔字符串，如果不需要返回值，则可以丢弃返回值。

exists(filename)

测试具有给定filename的文件是否存在。如果文件存在，则函数成功，否则将失败。

filename参数可能包含通配符。在这种情况下，如果任何文件匹配，此函数将成功。

例如：

exists( $(QTDIR)/lib/libqt-mt\* ) {

message( "Configuring for multi-threaded Qt..." )

CONFIG += thread

}

注：“/”应该用作目录分隔符，而不管使用的是哪个平台。

export(variablename)

将variablename的当前值从函数的本地上下文导出到全局上下文。

for(iterate, list)

启动一个循环，迭代list中的所有值，依次设置iterate到每个值。为了方便起见，如果列表是1..10，那么迭代将迭代值1到10。

例如：

LIST = 1 2 3

for(a, LIST):exists(file.\$${a}):message(I see a file.\$${a}!)

greaterThan(variablename, value)

测试variablename的值是否大于值。首先，这个函数尝试进行数值比较。如果至少有一个操作数无法转换，则此函数将进行字符串比较。

例如：

ANSWER = 42

greaterThan(ANSWER, 1) {

message("The answer might be correct.")

}

不可能直接将两个数字作为字符串比较。作为一种解决方法，可以构造具有非数字前缀的临时值，并将其进行比较。

例如：

VALUE = 123

TMP\_VALUE = x\$$VALUE

greaterThan(TMP\_VALUE, x456): message("Condition may be true.")

另请参见lessThan()。

if(condition)

评估条件。它用于将布尔表达式进行分组。

例如：

if(linux-g++\*|macx-g++\*):CONFIG(debug, debug|release) {

message("We are on Linux or Mac OS, and we are in debug mode.")

}

include(filename)

将filename指定的文件的内容包含到包含它的当前项目中。如果包含filename，此函数将成功；否则将失败。包含的文件将立即处理。

您可以通过使用此函数作为作用域的条件来检查文件是否包含。例如：

include( shared.pri )

OPTIONS = standard custom

!include( options.pri ) {

message( "No custom build options specified" )

OPTIONS -= custom

}

infile(filename, var, val)

如果文件filename（由qmake本身解析时）包含值为val的变量var，则会成功；否则将失败。如果不指定val，则该函数将测试文件中是否已分配了var。

isActiveConfig

这是CONFIG函数的一个别名。

isEmpty(variablename)

如果变量变量为空，则成功；否则失败。这等价于count(variablename, 0)。

例如：

isEmpty( CONFIG ) {

CONFIG += warn\_on debug

}

isEqual

这是equals()函数的别名。

lessThan(variablename, value)

测试variablename的值是否小于value。工作原理同greaterThan()。

例如：

ANSWER = 42

lessThan(ANSWER, 1) {

message("The answer might be wrong.")

}

load(feature)

加载feature指定的特性文件(.prf)，除非特性已经加载。

log(message)

在控制台上打印一条消息。与message()函数不同，既不添加文本，也不添加换行符。

这个函数在Qt5.0中引入。

另请参见message()。

message(string)

总是成功，并将字符串作为一般消息显示给用户。与error()函数不同，该函数允许继续处理。

message( "This is a message" )

上面的一行导致”This is a message”被写入控制台。使用引号是可选的，但建议使用。

注：默认情况下，将为qmake为给定项目生成的每个Make文件写入消息。如果要确保每个项目的消息只出现一次，请结合测试build\_pass变量以及在构建期间过滤出消息的范围不懂不懂。例如：

!build\_pass:message( "This is a message" )

mkpath(dirPath)

创建目录路径目录dirPath。此函数包装了QDir::mkpath函数。

这个函数在Qt5.0中引入。

requires(condition)

评估condition。如果条件为false，qmake将在构建时跳过这个项目（及其子项）。

注：您也可以为此目的使用REQUIRE变量。但是，我们建议您使用这个函数。

system(command)

在次级终端中执行给定的command。如果命令返回为零退出状态，则成功；否则失败。您可以使用作用域来检查此函数的返回值。

例如：

system("ls /bin"): HAS\_BIN = TRUE

另请参见system()的替换函数的变体。

touch(filename, reference\_filename)

更新filename的时间戳，以匹配reference\_filename的时间戳。

这个函数在Qt5.0中引入。

unset(variablename)

从当前上下文中删除变量variablename。

例如：

NARF = zort

unset(NARF)

!defined(NARF, var) {

message("NARF is not defined.")

}

versionAtLeast(variablename, versionNumber)

测试variablename的版本号大于或等于versionNumber。版本号被认为是由.分隔的非负十进制数序列。字符串的任何非数字尾部都将被忽略。从左到右分段进行比较；如果一个版本是另一个版本的前缀，则认为它更小。

该函数在Qt5.10中引入。

versionAtMost(variablename, versionNumber)

测试变量的版本号小于或等于版本号。工作原理同versionAtLeast()。

该函数在Qt5.10中引入。

warning(string)

总是成功，并将string作为警告消息显示给用户。

write\_file(filename, \[variablename, \[mode]])

将variablename的值写入具有名称filename的文件，每个值都在单独的行中。如果未指定变量名，则会创建一个空文件。如果mode是append并且文件已经存在，则附加到它而不是替换它。

这个函数在Qt5.0中引入。

## 测试函数库 <a href="#_toc32519" id="_toc32519"></a>

复杂的测试函数是在一个.prf文件库中实现的。

packagesExist(packages)

使用PKGCONFIG机制来确定在项目解析时是否存在给定的包。

这对于可选择性地启用或禁用特性很有用。例如：

packagesExist(sqlite3 QtNetwork QtDeclarative) {

DEFINES += USE\_FANCY\_UI

}

然后，在代码中：

\#ifdef USE\_FANCY\_UI

// Use the fancy UI, as we have extra packages available

\#endif

prepareRecursiveTarget(target)

通过准备一个遍历所有子目录的目标，方便创建类似于install目标的项目范围的目标。例如：

TEMPLATE = subdirs

SUBDIRS = one two three

prepareRecursiveTarget(check)

具有have\_no\_default或no\_\<target>\_target的子目录。它们的CONFIG被排除在此目标之外：

two.CONFIG += no\_check\_target

您必须手动将准备好的目标添加到QMAKE\_EXTRA\_TARGETS中：

QMAKE\_EXTRA\_TARGETS += check

为了使目标成为全局的，需要将上述代码包含到每个子目录的子项目中。此外，为了使这些目标做任何事情，非子项子项目需要包含各自的代码。实现这一点的最简单的方法是创建一个自定义特性文件。例如：

\# \<project root>/features/mycheck.prf

equals(TEMPLATE, subdirs) {

prepareRecursiveTarget(check)

} else {

check.commands = echo hello user

}

QMAKE\_EXTRA\_TARGETS += check

特性文件需要注入到每个子项目中，例如通过.qmake.conf：

\# \<project root>/.qmake.conf

CONFIG += mycheck

这个函数在Qt5.0中引入。

qtCompileTest(test)

构建测试项目。如果测试通过，则返回true，并将config\_\<test>添加到CONFIG变量中。否则，返回false。

要使此功能可用，您需要加载相应的功能文件：

\# \<project root>/project.pro

load(configure)

这还将变量QMAKE\_CONFIG\_TESTS\_DIR设置为项目父目录的config.test子目录。可以在加载特性文件后覆盖此值。

在测试目录中，每个测试必须有一个包含一个简单的qmake项目的子目录。下面的代码片段说明了该项目的.pro文件：

\# \<project root>/config.tests/test/test.pro

SOURCES = main.cpp

LIBS += -ltheFeature

\# Note that the test project is built without Qt by default.

下面的代码片段说明了该项目的主要.cpp文件：

// \<project root>/config.tests/test/main.cpp

\#include \<TheFeature/MainHeader.h>

int main() { return featureFunction(); }

下面的代码片段显示了对该测试的调用：

\# \<project root>/project.pro

qtCompileTest(test)

如果测试项目构建成功，则测试将通过。

测试结果将被自动缓存，这也使它们可用于所有子项目。因此，建议在顶级项目文件中运行所有配置测试。

要抑制对缓存结果的重用，请将CONFIG+=recheck传递给qmake。

另请参见load()。

这个函数在Qt5.0中引入。

qtHaveModule(name)

检查由name指定的Qt模块是否存在。有关可能的值的列表，请参见QT。

该函数在Qt5.0.1中引入。

# 第8章 高级使用

## 添加新的配置特性 <a href="#_toc959" id="_toc959"></a>

qmake允许您创建您自己的特性(feature)，这些特性可以通过将它们的名称添加到CONFIG变量指定的值列表中来包含在项目文件中。特性是.prf文件中的自定义函数和定义的集合，它们可以驻留在许多标准目录之一中。这些目录的位置定义在许多位置，qmake在查找.prf文件时按以下顺序检查每个位置：

1. 在QMAKEFEATURES环境变量中列出的目录中，环境变量包含由平台的路径列表分隔符分隔（Unix为冒号，Windows为分号）。
2. 在QMAKEFEATURES属性变量中列出的目录中，该变量包含由平台的路径列表分隔符分隔的目录列表。
3. 在位于mkspecs目录中的特性目录中。mkspecs目录可以位于QMAKEPATH环境变量中列出的任何目录的下面，该变量包含由平台的路径列表分隔符分隔的目录列表。例如：$QMAKESPEC/\<features>.
4. 在位于[QMAKESPEC](https://doc.qt.io/qt-5.15/qmake-environment-reference.html%22%20/l%20%22qmakespec)环境变量提供的目录下的特性目录中。例如：$QMAKESPEC/\<features>。
5. 在位于data\_install/mkspecs目录中的特性目录中。例如：data\_install/mkspecs/ \<features>。
6. 在作为[QMAKESPEC](https://doc.qt.io/qt-5.15/qmake-environment-reference.html%22%20/l%20%22qmakespec)环境变量指定的目录的兄弟姐妹存在的特性目录中。例如：$QMAKESPEC/../\<features>。

系统将在以下功能目录中搜索功能文件：

1. features/unix, features/win32, 或features/macx,这取决于正在使用的平台
2. features/

例如，考虑项目文件中考虑以下分配：

添加到CONFIG变量后，qmake将在完成对项目文件的解析后搜索上面列出的myfeatures.prf文件的位置。在Unix系统上，它将查找以下文件：

1. $QMAKEFEATURES/myfeatures.prf（对于QMAKEFEATURES环境变量中列出的每个目录）
2. \$$QMAKEFEATURES/myfeatures.prf（对于在QMAKEFEATURES属性变量中列出的每个目录）
3. myfeatures.prf（在项目的根目录中）。项目根目录由顶级的.pro文件决定。但是，如果您将.qmake.cache文件放置在子目录或子项目的目录中，则项目根目录将成为子目录本身。
4. $QMAKEPATH/mkspecs/features/unix/myfeatures.prf和$QMAKEPATH/mkspecs/features/myfeatures.prf（对于QMAKEPATH环境变量中列出的每个目录）
5. $QMAKESPEC/features/unix/myfeatures.prf和$QMAKESPEC/features/myfeatures.prf
6. data\_install/mkspecs/features/unix/myfeatures.prf和data\_install/mkspecs/features/myfeatures.prf
7. $QMAKESPEC/../features/unix/myfeatures.prf和$QMAKESPEC/../features/myfeatures.prf

注：.prf文件的名称必须为小写字母。

## 安装文件 <a href="#_toc24554" id="_toc24554"></a>

在Unix上，通常也会使用构建工具来安装应用程序和库；例如，通过调用make install。出于这个原因，qmake有一个安装集的概念，这个对象包含了关于项目的一个部分的安装方式的说明。例如，可以用以下方式描述文档文件的集合：

documentation.path = /usr/local/program/doc

documentation.files = docs/\*

路径(path)成员通知qmake，文件应该安装在/usr/local/program/doc，并且文件(file)成员指定应该复制到安装目录中的文件。在这种情况下，文档目录中的所有内容都将复制到/usr/local/program/doc。

一旦完全描述了安装集，就可以将安装集附加到安装列表中：

INSTALLS += documentation

qmake将确保将指定的文件复制到安装目录中。如果您需要对此过程进行更多的控制，那么您还可以为对象的extra成员提供一个定义。例如，下面的行告诉qmake为此安装集执行一系列命令：

unix:documentation.extra = create\_docs; mv master.doc toc.doc

unix作用域确保这些特定的命令仅在Unix平台上执行。可以使用其他作用域规则来定义针对其他平台的适当命令。

在执行对象的extra成员中的指令之前，会先执行在其他成员中指定的命令。

如果您附加了一个内置的安装集到INSTALLS变量中，并且没有指定文件或额外的成员，那么qmake将决定需要为您复制什么。目前，支持target和dlltarget。例如：

target.path = /usr/local/myprogram

INSTALLS += target

在上面的几行中，qmake知道需要复制什么，并将自动处理安装过程。

## 添加自定义目标 <a href="#_toc17864" id="_toc17864"></a>

qmake尝试完成跨平台构建工具的所有期望。当您真的需要运行依赖于平台的特殊命令时，这通常不太理想。这可以通过对不同的qmake后端的特定指令来实现。

Makefile输出的自定义是通过一个对象风格的API来执行的，就像在qmake中的其他地方一样。对象通过指定其成员自动定义对象。例如：

mytarget.target = .buildfile

mytarget.commands = touch \$$mytarget.target

mytarget.depends = mytarget2

mytarget2.commands = @echo Building \$$mytarget.target

上面的定义定义了一个名为mytarget的qmake目标，其中包含一个名为.buildfile的Makefile目标，该目标又是通过touch命令生成的。最后，.depends成员指定mytarget依赖于mytarget2，这是之后定义的另一个目标。mytarget2是一个虚拟目标。它仅被定义为将某些文本显示到控制台。

最后一步是使用QMAKE\_EXTRA\_TARGETS变量来指示qmake，这个对象是一个要构建的目标：

QMAKE\_EXTRA\_TARGETS += mytarget mytarget2

这是实际构建自定义目标所需要做的。当然，您可能希望将其中一个目标与TARGET变量绑定。要做到这一点，您只需要将Makefile目标包含在PRE\_TARGETDEPS的列表中。

自定义目标规范支持以下成员：

| **成员**          | **描述**                                                                                                        |
| --------------- | ------------------------------------------------------------------------------------------------------------- |
| commands        | 用于生成自定义构建目标的命令。                                                                                               |
| CONFIG          | 自定义构建目标的特定配置选项。可以设置为recursive，以表示应该在Makefile中创建规则，以调用子目标特定的Makefile中的相关目标。此成员默认为为每个子目标创建一个条目。                 |
| depends         | 自定义构建目标所依赖的现有构建目标。                                                                                            |
| recurse         | 指定在Makefile中创建要在子目标特定的Makefile中调用的规则时应使用哪些子目标。此成员仅在CONFIG中设置recursive（递归）时使用。典型的值是“调试”和“发布”。                  |
| recurse\_target | 指定应该通过Makefile中规则的子目标Makefile生成的目标。这个成员添加了类似于$(MAKE) -f Makefile的内容。\[子目标]\[递归目标]。此成员仅在CONFIG中设置recursive时使用。 |
| target          | 自定义构建目标的名称。                                                                                                   |

## 添加编译器 <a href="#_toc20229" id="_toc20229"></a>

可以自定义qmake来支持新的编译器和预处理器：

new\_moc.output = moc\_${QMAKE\_FILE\_BASE}.cpp

new\_moc.commands = moc ${QMAKE\_FILE\_NAME} -o ${QMAKE\_FILE\_OUT}

new\_moc.depend\_command = g++ -E -M ${QMAKE\_FILE\_NAME} | sed "s,^.\*: ,,"

new\_moc.input = NEW\_HEADERS

QMAKE\_EXTRA\_COMPILERS += new\_moc

通过上述定义，如果有moc可用，则可以使用插入替换moc。该命令将在提供给NEW\_HEADERS变量（来自输入成员）的所有参数上执行，并将结果写入到由输出成员定义的文件中。此文件将被添加到项目中的其他源文件中。此外，qmake将执行depend\_command来生成依赖信息，并将这些信息放在项目中。

自定义编译器规范支持以下成员：

| **成员**           | **描述**                                                             |
| ---------------- | ------------------------------------------------------------------ |
| commands         | 用于从输入中生成输出的命令。                                                     |
| CONFIG           | 自定义编译器的特定配置选项。详情请参见配置表。                                            |
| depend\_command  | 指定用于生成输出的依赖项列表的命令。                                                 |
| dependency\_type | 指定输出为的文件的类型。如果它是已知类型（如TYPE\_C、TYPE\_UI、TYPE\_QRC），它将作为这些类型的文件之一处理。 |
| depends          | 指定输出文件的依赖关系。                                                       |
| input            | 指定应使用自定义编译器处理的文件的变量。                                               |
| name             | 对自定义编译器正在做些什么的描述。这只在某些后端中使用。                                       |
| output           | 从自定义编译器中创建的文件名。                                                    |
| output\_function | 指定用于指定要创建的文件名的自定义qmake函数。                                          |
| variables        | 表示当在配置文件中被称为$（变体名）时，此处指定的变量将被替换为$(QMAKE\_COMP\_VARNAME)。           |
| variable\_out    | 从输出中创建的文件应该添加到的变量。                                                 |

CONFIG成员支持以下选项：

| **选项**                 | **描述**                                                             |
| ---------------------- | ------------------------------------------------------------------ |
| combine                | 表示将所有输入文件合并成一个输出文件。                                                |
| target\_predeps        | 表示输出应该被添加到PRE\_TARGETDEPS的列表中。                                     |
| explicit\_dependencies | 输出的依赖项只从依赖项成员生成，而不是从其他地方生成。                                        |
| dep\_existing\_only    | 由.depend\_command产生的每个依赖关系都会被检查是否存在。此时将忽略不存在的依赖项。在Qt5.13.2中引入了这个值。 |
| dep\_lines             | 来自.depend\_command的输出被解释为每行有一个文件。默认情况是在空格上分割，仅由于向后兼容的原因进行维护。       |
| no\_link               | 表示不应将输出添加到要链接的对象列表中。                                               |

## 库依赖 <a href="#_toc13136" id="_toc13136"></a>

通常在链接库时，qmake依赖底层平台来知道该库链接的其他库，并让平台将它们拉进来。然而，在许多情况下，这是不够的。例如，当静态链接一个库时，将不会链接到其他库，因此也不会创建对这些库的依赖关系。但是，稍后针对此库链接的应用程序需要知道在哪里找到静态库需要的符号。如果您显式地启用跟踪，那么qmake会在适当的情况下尝试跟踪库的依赖关系。

第一步是在库本身中启用依赖关系跟踪。要做到这一点，您必须告诉qmake保存有关库的信息：

这只与lib模板相关，并且对于其他所有模板都将被忽略。启用此选项后，qmake将创建一个以.prl结尾的文件，它将保存关于该库的一些元信息。这个元文件就像一个普通的项目文件一样，但只包含内部变量声明。在安装此库时，通过在INSTALL声明中将其指定为目标，qmake将自动将.prl文件复制到安装路径中。

此过程中的第二步是在使用静态库的应用程序中启用读取此元信息：

启用此功能后，qmake将处理由应用程序链接到的所有库，并找到它们的元信息。qmake将使用它来确定相关的链接信息，特别是向应用程序项目文件的DEFINES和LIBS添加值。一旦qmake处理了这个文件，它将查看LIBS变量中新引入的库，并找到它们依赖的.prl文件，直到所有库都被解决。此时，像往常一样创建Makefile，并且库与应用程序显式链接。

.prl文件只能由qmake创建，并且不应该在操作系统之间传输，因为它们可能包含与平台相关的信息。

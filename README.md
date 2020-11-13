# WALA-improve
 - 学习wala和静态代码分析,以及涉及到的一些概念。
 - Study WALA and static code analysis . This project introduces the required knowledge and study roadmap.
    
    
# 数据流分析技术 (Data Flow Analysis)

数据流分析是一种程序静态分析技术,它能从程序代码中收集程序的语义信息,并通过代数的方法确定变量的定义和使用。通过数据流分析,可以不必实际运行程序就能
够发现程序运行行为方面的特性,这样可以帮助人们理解程序。数据流分析被广泛用于解决编译优化、程序验证、理解、分片、调试、测试以及并行化等问题。 程序静
态数据流分析是程序测试所采用的一种重要手段。借助于程序静态分析工具,能更直接地暴露和定位程序中的错误。数据流分析作为一种非常重要的程序静态分析技术,
能够在保证软件质量与可靠性方面起到重要的作用.

# 静态代码分析 (Static code Analysis)

静态代码分析是指在不运行代码的方式下，通过词法分析、语法分析、控制流、数据流分析等技术对程序代码进行扫描的技术。它的目的是验证代码是否满足规范性、
安全性、可靠性、可维护性的要求。静态代码扫描处于分层自动化测试的最底层，它和单元测试同级别。为了保证公司代码的规范性、安全性、可靠性的要求，通过
定制公司级的静态代码扫描规范、扫描规则和扫描实施流程保证实施高效落地。

# DevOps & Static code Analysis

DevOps在CI过程中需要确保软件的构建方式正确。DevOps中涉及的责任很多，其中包括支持开发流程，自动化测试，确保质量，提高生产力.....并最终实现持续
部署。良好的代码质量是实现所有这些目标的必要条件，尽管不是充分条件。静态代码扫描可在任何构建/测试/部署步骤中添加的代码质量检验门槛，能够自动执行一
组统一的质量标准，从而确保组织交付更好的软件。

# 白盒测试 (white-box testing)

白盒测试（white-box testing）又称透明盒测试（glass box testing）、结构测试（structural testing）等，软件测试的主要方法之一，也称结
构测试、逻辑驱动测试或基于程序本身的测试。测试应用程序的内部结构或运作，而不是测试应用程序的功能（即黑盒测试）。在白盒测试时，以编程语言的角度
来设计测试案例。测试者输入资料验证资料流在程序中的流动路径，并确定适当的输出，类似测试电路中的节点。测试者了解待测试程序的内部结构、算法等信息，
这是从程序设计者的角度对程序进行的测试。白盒测试设计技术包括以下代码覆盖标准：    
 - 控制流测试
 - 数据流测试
 - 分支测试
 - 语句覆盖
 - 判定覆盖
 - 修正条件/判定覆盖
 - 主要路径测试
 - 路径测试

# WALA介绍
WALA为Java字节码和相关语言以及JavaScript提供了静态分析功能;最初诞生于IBM TJ Watson研究中心，2006年，IBM将其捐赠给开源社区。

# wala的特性 (WALA features)
 - Java类型系统和类层次结构分析
 - 支持Java和JavaScript
 - 过程间数据流分析（RHS求解器）
 - 上下文敏感的基于列表的切片器
 - 指针分析和调用图构造
 - 基于SSA的寄存器传输语言IR
 - 迭代数据流的通用框架
 - 通用分析工具和数据结构
 - 字节码检测库（Shrike）


# 下载并运行WALA
## download project
 - 下载 git clone https://github.com/wala/WALA.git
 - 通过gradle编译项目
 - WALA基于java8运行
 - wala提供了很多test用例，可以debug用；
 
## configuring properties.
 - 拷贝com.ibm.wala.core项目,resource下wala.properties.sample到resource下wala.properties
 - 配置java_runtime_dir地址,以macos为例，
   jvm默认安装路径为/Library/Java/JavaVirtualMachines/jdk1.8.0_261.jdk/Contents/Home/jre/lib,确保路径下包含rt.jar,如果找不到,
   在MacOSX上，它被也被称为classes.jarand，位于/System/Library/Frameworks/Classes
 - 配置output_dir地址，如果测试依赖于SWT不需要配置，如果依赖文件导出，如PDFTypeHierarchy，如果不配置导出路径，执行方法会失败
 
## run test demo
 - 在com.ibm.wala.core项目tests下，同样拷贝并编辑wala.properties,以支持项目正常运行；
 - 配置完成后可以在wala项目tests相关项目中，执行对应的测试代码。

## note
   windows环境下路径的分隔符应该是'/'而不是'\',我没有windows环境未验证，猜测应该是代码适配了平台。
   
# 通常WALA执行顺序
 - WALA提供了一组用于程序分析的库。通常情况下wala将通过一下的顺序来执行这些库，来执行过程间数据流分析：
 
 1. 建立ClassHierarchy,程序将源文件（如:字节码）读入内存，并根据预定义的类型解析一些基础信息。一个类层次对象代表了一组代码分析范围。
 2. 建立CallGraph,通过使用及时调用图执行指针分析，来处理动态调度调用的目标,并生成可能是程序调用结构的CGNode图对象。
 3. 通过构建的CallGraph，执行一些分析大多数类型的分析都是可以被分析出来的。WALA IR 编码了指令集和一些特别的方法的控制流。
    IR是由SSA提供的表示指令集的不可变的控制流图。
 
 # 项目模块
 - com.ibm.wala.core  -- 核心包
 - com.ibm.wala.core.tests -- 核心包测试
 - com.ibm.wala.shrike -- Shrike字节码,包含ShrikeCT和ShrikeBT,WALA依赖Shrike去读取class文件,Shrike也可以做一些字节码操作。
 - com.ibm.wala.cast -- CAst支持，Common Abstract Syntax Tree 通用抽象语法树
 - com.ibm.wala.cast.test -- CAst测试
 - com.ibm.wala.cast.java -- 基于CAst的Java前端，两种实现，一种是polyglot 一种是Eclipse JDT
 - com.ibm.wala.cast.java.test -- 基于CAst的Java前端测试
 - com.ibm.wala.cast.java.test.data -- 基于CAst的Java前端的测试数据
 - com.ibm.wala.ide.jdt -- 基于JDT和CAst的Java前端
 - com.ibm.wala.ide.jdt.test -- JDT前端测试
 - com.ibm.wala.cast.java.polyglot -- 基于Polyglot和CAst的Java前端
 - com.ibm.wala.cast.java.polyglot.test -- 基于Polyglot和CAst的Java前端测试
 

# 核心技术介绍
 - [分析范围](./doc/analysis-scope.md)
 - 调用图
 - Java前端和JavaScript调用图
 - 类层次结构图
 - IR
 - 映射源代码
 - 映射Java实体名称
 - 本地代码
 - 指针分析
 - Slicer
 
# 问题交流
 推荐通过issue/gitter的方式获取帮助；
 - gitter : https://gitter.im/WALAHelp/Lobby
 - issue  : https://github.com/wala/WALA/issues
  
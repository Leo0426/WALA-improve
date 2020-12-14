# Intermediate Representation (IR)

## 概览
WALA IR（中间表示）是表示特定方法指令的中央数据结构。 IR用接近JVM字节码的语言表示方法的指令，但使用基于SSA的寄存器传输语言来表示，该语言消除了
堆栈抽象，而是依赖于一组符号寄存器。 IR在基本块的控制流程图中组织指令，就像编译原理书中说的一样。

IR是不可变的。它不支持通过程序转换代码，并且不支持从IR生成代码。其原理是，可以在生成分析信息的过程中使用IR，以用于支持其他一些工具，
例如IDE或编译器。(可以使用shrike包在WALA中以字节码级别进行程序转换)。通常，分析将建立从IR构造到相关分析信息（例如抽象和数据流）的各种结构和映射。
 
 ## IR Basics
WALA IR类提供了基于SSA的特定方法的中间表示。 IR由基本块的控制流程图和一组指令组成。 可以尝试通过PDFWalaIR的实例来查看IR。
比起处理基础实现，可以用下面公开的入口处理比较方便：
 - IR.iterateAllInstructions(): 以未定义的顺序返回所有指令。对流不敏感地分析很有用。
 - IR.getControlFlowGraph(): 返回控制流图，即IBasicBlock的图。
 - IR.getControlFlowGraph().iterateNodes(): 返回控制流图中的节点（基本块）的迭代集合。
 - IBasicBlock.iterateAllInstructions(): 迭代特定基本块中的所有指令。  
  
 如果您关心迭代指令的顺序，则应遍历基本块，然后迭代每个基本块中的指令。  
 
 通常，如果构建一个调用图，然后通过CGNode.getIR（）获取特定节点的IR。 （您也可以使用AnalysisCache.getIR（IMethod）方法直接为特定的IMethod
 构建IR。）  
   
 请注意，根据CGNode表示的上下文，WALA可能为单个IMethod构建不同的IR。 例如，每个克隆节点在定义接收方类型的上下文中表示克隆IMethod。 关于此上下
 文，每个克隆节点的IR都是专门的。
 
 ## Control Flow Graph
在异常处理上，仅使用声明的类型来确定异常流，假设每个潜在排除指令（PEI）都可能引发异常。 没有智能优化可以消除不可行的异常控制流程。  
  
有时您可能希望调整对CFG的视图。 例如，也许客户希望忽略异常边缘的CFG视图。 为此，请检查PrunedCFG类。  
  
要对CFG进行一些基本导航，请在com.ibm.wala.cfg中检出Util类。 

## Value Numbering
IR中的每个变量都有一个唯一的ID，称为值编号。 因此，您可以想到名称为v1，v2，v3，...的IR中的变量。  

按照惯例，方法的值编号从1开始。因此，对于非静态方法，v1表示this参数。 接下来是方法的参数（v2，v3等）。 对于静态方法，v1表示第一个参数。 
大多数SSA指令最多定义一个变量，并使用一些数字变量。 每条指令均带有其定义和使用的变量编号，可通过getDef（）和getUses（）访问。 
实际上，SSAInvokeInstructions可以另外定义代表异常返回值的第二个变量。  
   
相反，每个变量（值编号）将恰好具有一个def和一些引用。 使用DefUse类查找定义和使用特定值数字的指令。 如果DefUse返回null作为值编号的def，
则该值编号表示参数或常量。 有关常量的信息，请参见下文。 注意，定义可以是phi语句； 请参阅下面的更多细节。   

在SSA转换期间，堆栈位置和本地变量都转换为符号虚拟寄存器。 对于给定的SSA值编号和指令索引，可以使用IR.getLocalNames（）来查找相应本地
名称（如果有）。 您可以查看SSABuilder.SSA2LocalMap以查看IR如何在内部跟踪此信息。

## Phi statements
按照SSA格式的标准，WALA IR包含phi语句，以处理可能会使用多种定义的情况。由于历史原因，这些phi语句（SSAPhiInstructions）不会作为常规指令
存储在IR中。相反，它们存储在IR的控制流图中的BasicBlocks上。 要在IR中查看所有phi指令，请调用IR.iteratePhis()。要查看特定基本块上
的phi语句，请调用BasicBlock.iteratePhis()。逻辑上，基本块上的phi语句在基本块的开头执行，即在该块中的任何普通指令之前执行。

## Type inference
您可以使用TypeInference类在IR中在过程中发现值类型的类型。例如:
```
    IR ir = ...;
    boolean doPrimitives = ...; // infer types for primitive vars?
    TypeInference ti = TypeInference.make(ir, doPrimitives);
    TypeAbstraction type = ti.getType(vn);
```
## Constant Values
IR提供原始常量和字符串常量，每个常量都有一个对应的变量，但是IR中没有明确的声明将常量分配给变量。要发现常量，
请通过调用IR.getSymbolTable()获得的IR的SymbolTable。SymbolTable.isConstant()会告诉您特定变量是否表示一个常量
(将变量的值编号作为参数传递)，SymbolTable.getConstantValue()会为您提供表示的值。

## Pi nodes (advanced)


## From IR to bytecode?
WALA不是编译器； 它没有代码生成后端，并且IR并非为转换而设计。 我们不知道有任何直接从WALA SSA IR生成字节码的实现。
一个可行的选择是在IR上进行分析，将分析结果映射回Shrike表示形式，并在Shrike中进行字节码转换。
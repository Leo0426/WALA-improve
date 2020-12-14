# 指针分析 (别名分析)
WALA提供了一个Andersen风格的不敏感流指针分析框架。你可以通过多种方式自定义上下文相关的指针分析策略。我们发现一些特定的客户端,使用自定义指针
分析策略解决一些问题是很有用的。  
当前，所有指针分析实现都执行即时调用图构造。  
一个上下文相关的指针分析策略可以在两方面变化:  
 - heapModel: 指针分析模型如何抽象指针和堆位置?
 - ContextSelector:  调用图如何基于上下文克隆方法?
 
你可以通过自定义堆模型和上下文选择器来定义自己的指针分析策略。 此外，WALA 提供了许多内置策略。

--- 

## 堆模型(Heap model)
一个HeapModel提供给WALA指针分析怎么去抽象指针和堆的位置。关键的类：
 - PointerKey: 表示抽象指针
 - InstanceKey: 表示抽象堆位置

例如，PointerKey可以表示本地变量或静态字段，或者特定分配点分配的对象的实例字段，或者是其他的变量。更深入地说,PointerKey是来自具体程序的等效类
指针的名称，这些指针在抽象过程中被收集到单个表示中。  

 - InstanceKey可能表示所有的特别类型的对象或者所有在特别分配点分配的对象，或者是在特别地上下文中特别的分配点分配的所有的对象，或者是其它的对象。 
 - HeapModel为指针分析提供回调，以在分析期间创建PointerKeys对象和InstanceKeys对象。 您可以通过实现自己的HeapModel来自定义策略。

---

## 堆图(Heap graph)
   堆图提供了一种方便的方式来获取指针分析的结果。在图中，节点表示的是PointKey和InstanceKeys.如果当且仅当指针分析表明P可能指向I时，
从PointerKey到InstanceKey才存在一条边。当且仅当P代表由I建模的对象实例的字段，或者P代表数组实例I的数组内容时，从InstanceKey
到PointerKey才存在一条边。  
例如，给定HeapGraph h，假设您想知道所有可能为特定PointerKey p。 首先，您使用h.getSuccNodes（p）查找p可能指向的
所有InstanceKey，对于每个这样的InstanceKey i，使用h.getPredNodes（i）查找可能为p别名的其他PointerKey。

## 上下文选择器
  ContextSelector上下文选择器控制在构建调用图构建上下文的策略。调用图节点代表了在特定上下文中一个方法。  
  最简单的上下文策略：Everywhere.EVERYWHERE ,它代表了一个单个的全局的方法。  
  其他策略可以表示调用字符串上下文，命名接收者对象来实现对象敏感性的上下文或其他变体。  
  你可以自定义上下文敏感策略，通过自定义提供的 ContextSelector 对象。
  
## 入口点
   
   
   
   
## 内置策略


## 提升可扩展性

## 需求驱动指针分析



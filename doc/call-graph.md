# 调用图
WALA中CallGraph类，通过方法的逻辑克隆，表示潜在的上下文相关的调用关系图。每一个调用图的节点(CGNode)都表示了上下文(Context)中的
一个方法(IMethod).
- 什么是Context呢?  
```
基本上,一个Context只是一个名称，是IMethod的克隆。
对于上下文无关的调用图,在实现上下文无关的算法时，用 Everywhere.EVERYWHERE 作为默认的上下文的标示。
```
请注意对于给定的IMethod,在上下文相关的调用图中可能有多个表示该方法的节点。 我们可以使用方法 
`CallGraph.getNodes(MethodReference methodReference)`来获取所有的相关的节点。

WALA 提供了一系列的即时调用图构造算法,整合了不敏感流指针分析,请查看Pointer Analysis查看详细说明。  

WALA 还通过快速类型分析(rapid type analysis,RTA)(`Util.makeRTABuilder()`)实现了调用图的构造,但是非常不推荐使用这种方式。
在PointerAnalysis也包含了0-CFA(ZeroCFA) 实现, 而0-CFA几乎总是更快，更精确的。  

此外,RTA对字节码进行操作,其中一些字节可能在SSA构造期间被认证是无效的，因此未在SSA IR中表示。因此,就导致了一些不良的行为，
例如:RTA调用图返回的调用位置没有在SSA IR中出现。  

最后,请注意,另一种情况是RTA调用本地方法的时候(e.g : Object.clone()),这种情况看起来是可以构造出所有可到达代码的调用图的，
但是其他情况就不确定了。
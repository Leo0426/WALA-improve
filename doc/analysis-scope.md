# 分析范围 (analysis-scope)
  一个AnalysisScope对象，指定了要被分析的应用和库代码；
## 常用构造方法
调用
[`AnalysisScopeReader.makeJavaBinaryAnalysisScope()`](http://wala.sourceforge.net/javadocs/trunk/com/ibm/wala/util/config/AnalysisScopeReader.html#makeJavaBinaryAnalysisScope(java.lang.String,%20java.io.File))
构建一个 `AnalysisScope` 对象，入参需要传入String类型的classpath和File类型的exclusions.
如果需要更多的参数，可以调用
[`AnalysisScopeReader.readJavaScope()`](http://wala.sourceforge.net/javadocs/trunk/com/ibm/wala/util/config/AnalysisScopeReader.html#readJavaScope(java.lang.String,%20java.io.File,%20java.lang.ClassLoader))
方法,包含丰富的构造参数，详情请查看java doc.
一个Scope包含包含以下信息： 

    Classloader,Language,Type,Location

对于java语言来说，Classloader的类型为Primordial,Extension和Application中的一种. 

 -   Primordial 应用于Java标准库
 -   Extension 应用于应用程序扩展的其他类型的库
 -   Application 应用于应用程序本身的代码
     Type的支持类型说明：
     - classFile : 对于单个.class文件
     - binaryDir : 用于包含类文件的目录（具有标准的包对应关系）
     - jarFile : 用于.jar文件
     - sourceFile和sourceDir : 用于源文件,用于-Java source frontend
     
# Note 
## TODO    
sourceFile和sourceDir 这个究竟是二进制，还是源文件 这个不太确定，可以看看SourceDirCallGraph里面，等我完全理解这里以后，回来补充；
这是JavaDoc的注释方式，以这样方式写成的注释，可以被JavaDoc等软件处理成文档。

被标记为return的是对返回值的注释，也有对参数的注释等等。这些在生成文档的时候，会被自动加到对应的位置上。

比如：

```c++
 /**       
 * a normal member taking two arguments and returning an integer value.       
 * @param a an integer argument.       
 * @param s a constant character pointer.       
 * @see Test()      
 * @see ~Test()       
 * @see testMeToo()       
 * @see publicVar()       
 * @return The test results       
 */      
int testMe(int a,const char *s);
```

这段代码会被处理成

![img](.\Pic\9e914a867eed48c6a166be3045ba0e.jpg)

这样的文档

说的Doxygen是这类软件中比较常用的，上面的就是它manual中的例子。

它不仅支持这种JavaDoc风格的注释，也支持Qt风格的注释。

另外Visual Studio对C#也有类似的支持，你在函数前打三个"/"，会自动生成一部分注释，你把剩下的填好以后，在调用函数时VS就会有像Framework那些函数一样的提示了。

不过VS自带的这个功能不算太强，有个神一样的插件可以根据你的函数名和参数名自动猜出来它的意思填到对应地方去，并且可以切换不同的注释风格。

> 注：引自维基百科

Doxygen是一个适用于C++、C、Java、Objective-C、Python、IDL（CORBA和Microsoft flavors）、Fortran、VHDL、PHP、C#和D语言的文档生成器。它可以在大多数类Unix操作系统、macOS以及Microsoft Windows上运行。初始版本的Doxygen使用了一些旧版本DOC++的源代码，在那之后，Dimitri van Heesch重写了Doxygen的源代码。

Doxygen是一个编写软件参考文档的工具。该文档是直接写在源代码中的，因此比较容易保持更新。Doxygen可以交叉引用和源代码，使文件的读者可以很容易地引用实际的源代码。

KDE将Doxygen作为它的一部分且KDevelop具有内置的支持。 Doxygen的发布遵守GNU通用公共许可证，是自由软件。

#### 用法

如同Javadoc，Doxygen从源文件提取注解。除了Javadoc的语法，Doxygen支持Qt使用的文档标记，并可以输出成HTML、以及CHM、RTF、PDF、LaTeX、PostScript或手册页。
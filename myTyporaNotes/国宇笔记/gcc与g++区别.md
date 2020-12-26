**gcc与g++区别**

GCC:GNU Compiler Collection(GUN 编译器**集合**)，它可以编译C、C++、JAV、Fortran、Pascal、Object-C、Ada等语言。

gcc是GCC中的GUN C Compiler（C 编译器）

g++是GCC中的GUN C++ Compiler（C++编译器）

一个有趣的事实就是，就本质而言，gcc和g++并不是编译器，也不是编译器的集合，它们只是一种驱动器，根据参数中要编译的文件的类型，调用对应的GUN编译器而已，比如，用gcc编译一个c文件的话，会有以下几个步骤：

Step1：Call a preprocessor, like cpp.

Step2：Call an actual compiler, like cc or cc1.

Step3：Call an assembler, like as.

Step4：Call a linker, like ld

由于编译器是可以更换的，所以gcc不仅仅可以编译C文件

所以，更准确的说法是：gcc调用了C compiler，而g++调用了C++ compiler

**gcc和g++的主要区别**

·对于\*.c和\*.cpp文件，gcc分别当做c和cpp文件编译（c和cpp的语法强度是不一样的）

· 对于 \*.c和\*.cpp文件，g++则统一当做cpp文件编译

· 使用g++编译文件时，**g++会自动链接标准库STL，而gcc不会自动链接STL**

·gcc在编译C文件时，可使用的预定义宏是比较少的

· gcc在编译cpp文件时/g++在编译c文件和cpp文件时（这时候gcc和g++调用的都是cpp文件的编译器），会加入一些额外的宏，这些宏如下：

\#define __GXX_WEAK__ 1
\#define __cplusplus 1
\#define __DEPRECATED 1
\#define __GNUG__ 4
\#define __EXCEPTIONS 1
\#define __private_extern__ extern

·在用gcc编译c++文件时，为了能够使用STL，需要加参数 –lstdc++ ，但这并不代表 gcc –lstdc++ 和 g++等价，它们的区别不仅仅是这个
这章节是第十四章。这章介绍运算符重载，这种机制允许内置运算符作用于类类型的运算对象。这样我们创建的类型直观上就可以像内置类型一样使用，运算符重载是C++借以实现这一目的的方法之一。<br>
类可以重载的运算符中有一种特殊运算符---函数调用运算符。对于重载了这种运算符的类，我们可以“调用”其对象，就好像它们是函数一样。新标准库中提供了一些设施，使得不同类型的可调用对象可以以一种一致的方式来使用，我们也将介绍这部分内容。<br>
最后将介绍另一种特殊类型的类成员函数---转换运算符。这些运算符定义了类类型对象的隐式转换机制。编译器应用这种转换机制的场合与原因都与内置类型转换是一样的。<br>
那么，在第四章中我们看到C++语言定义了大量的运算符以及内置类型的自动转换规则。这些特性使得程序员能编写出形式丰富、含有多种混合类型的表达式。<br>
当运算符被用于类类型的对象时，C++语言允许我们为其制定新的含义；同时，我们也能自定义类类型之间的转换规则。和内置类型的转换一样，类类型转换隐式地将一种类型的对象转换成另一种我们所需类型的对象<br>
@ 当运算符作用于类类型的运算对象时，可以通过运算符重载重新定义该运算符的含义。<br>
## 基本概念
**重载的运算符时具有特殊名字的函数**：它们的名字由关键字operator和其后要定义的运算符号共同组成。和其他函数一样，重载的运算符也包含返回类型、参数列表以及函数体。<br>
**重载运算符函数的参数数量与该运算符作用的运算对象数量一样多。**一元运算符又一个参数，二元运算符有两个。<br>
@ 对于二元运算符来说，左侧运算对象传递给第一个参数，而右侧运算对象传递给第二个参数。除了重载的函数调用运算符operator()之外，其他重载运算符不能含有默认实参。<br>
@ 如果一个运算符函数是成员函数，则它的第一个（左侧）运算对象绑定到隐式的this指针上，因此，成员运算符函数的（显式）参数数量比运算符的运算对象总数少一个。<br>
📒 当一个重载的运算符是成员函数时，this绑定到左侧运算对象。成员运算符函数的（显式）参数数量比运算对象的数量少一个。<br>
**约定：**对于一个运算符函数来说，它或者是类的成员，或者至少含有一个类类型的参数:<br>
```cpp
// ❌ 不能为int重定义内置的运算符
int operator+(int,int);
```
这一约定意味着当运算符作用于内置类型的运算对象时，我们无法改变该运算符的含义。<br>
**我们可以重载大多数（但不是全部）运算符。**<br>
我们只能重载已有的运算符，而无权发明新的运算符号。<br>
有四个符号（+，-，*，&）即是一元运算符也是二元运算符，所有这些运算符都能被重载，从参数的数量我们可以推断到底定义的是哪种运算符。<br>
@ 对于一个重载的运算符来说，其优先级和结合律与对应的内置运算符保持一致。<br>
可以重载的运算符太多，这里列举不能重载的运算符<br>
::    .*    .    ?:<br>
#### 直接调用一个重载的运算符函数
通常情况下，我们将运算符作用于类型正确的实参，从而以这种间接方式“调用”重载的运算符函数。然而，我们也能像调用普通函数一样直接调用运算符函数，先指定函数名字，然后传入数量正确、类型适当的实参：
```cpp
// 一个非成员运算符函数的等价调用
data1+data2;
// 基于“调用”的表达式
operator+(data2,data2);
// 对成员运算符函数的等价调用
```
两次调用都是等价的，它们都调用了非成员函数operator+。传入的data1是第一个实参，传入data2作为第二个实参<br>
显式地调用成员运算符函数。
```cpp
// 基于“调用”的表达式
data1 += data2;
data1.operator+=(data2);
// 对成员运算符函数的等价调用 将this绑定到data1的地址、将data2作为实参传入了函数。
```
#### 某些运算符不应该被重载
@ 使用重载的运算符本质上是一次函数调用，所以关于运算对象求值顺序的规则无法应用到重载的运算符上。<br>
特别是，逻辑与运算符、逻辑或运算符和逗号运算符的运算对象求值顺序无法保留下来.除此之外，&&和||运算符的重载版本也无法保留内置运算符的短路求值属性，两个运算对象总是会被求值。<br>
我们一般不重载逗号运算符和取地址运算符。<br>
✨ 通常情况下，不应该重载逗号、取地址、逻辑与和逻辑或运算符<br>

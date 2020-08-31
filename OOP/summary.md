# :confused::confused::confused:

看完了一遍回放 oop还是屁都不懂.jpg

以下都是胡扯,别骂了别骂了

参考：**xww老师PPT**，**Thinking in C++**

## 指北 :sleepy:

### 面向对象 vs 面向过程

面向过程的思想是具体化、流程化的，需要通过一步一步的分析、调用来解决问题。

面向对象的思想是模型化的，通过抽象出的一个类来进行调用，从而**降低耦合性**，**提高维护性**

:star: 有一天你想吃饺子，面向过程的你买饺子馅，饺子皮，然后擀面，和馅，包饺子... 面向对象的你抽象出了一家餐馆的模型，告诉老板，要吃饺子。可以看到，面向对象不需要知道饺子是怎么包出来的，只需要"吃"就好了。

### 对象

:smile:“万物皆对象”,每个对象有着自己独特的特征，称之为**属性** 

对象 = 属性+方法

**Class**: 一种抽象出的模型，定义了其包含的对象的属性类型以及方法，**对象是类的实例化**

:star:person：类   wzb: 对象  person规定有吃，睡的方法，所以wzb会吃会睡(别骂了)

### 三大特性

- [x] 封装
- [x] 继承
- [x] 多态

### C++ improvements

:star:C语言虽然可以很灵活的访问内存空间，但其属于面向过程的编程语言，在编译时暴露出的问题不多，同时对大型程序的支持效果很差

- [x] Data Abstraction
- [x] Access control
- [x] Initialization & Cleanup
- [x] Function overloading
- [x] Streams for I/O
- [x] constant
- [ ] Name Control
- [x] Inline function
- [x] references
- [x] Operator overloading
- [x] More safe and powerful memory management
- [x] Support for OOP
- [x] templates
- [ ] exception handling
- [ ] More extensive libraries  STL

### Tips

:arrow_down_small: C++不是C语言，C++提供了更高层的语言特性

:arrow_down_small: C++是一种综合性语言，保留了面向过程编程的特性，增加了面向对象编程特性，还增加了泛型编程的特性​



### TODO

- STL (ppt 4)
- 多重继承 (ppt 7)
- static in class (ppt 7)




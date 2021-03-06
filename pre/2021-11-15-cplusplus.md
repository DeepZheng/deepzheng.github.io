---
layout: post
title: "C++知识点总结"
date: 2021-11-15
tag: C++
---  
### 从源代码到可执行程序，中间的过程
编译分为四个过程：编译预处理，编译优化，汇编，链接

* **编译预处理**：处理以 # 开头的指令
  
  将所有的 #define 删除，并且展开所有的宏定义

  处理所有的条件预编译指令，如#if，#ifdef

  处理#include 预编译指令，将被包含的文件插入到该预编译指令的位置

  删除所有的注释

* **编译优化**：将源码 .cpp 文件翻译成 .s汇编代码

  词法分析，语法分析，语义分析，优化代码等

* **汇编**：将汇编代码 .s 翻译成 .o机器指令文件

* **链接**：汇编程序生成的 .o 目标文件，并不会立即执行，因为可能 .cpp 文件中的函数引用了另一个 .cpp 文件中定义的符号或者调用某个库函数中的函数。链接的目的就是将这些文件对应的目标文件连接成一个整体，从而生成可执行的 .exe 文件
![Image](https://pic4.zhimg.com/80/v2-54fa01e15a44a8359f8a112a533920a2.png)

链接分为两种：
* **静态链接**：代码从其所在的静态链接库中拷贝到最终的可执行程序中，在该程序被执行时，这些代码会被装入到该进程的虚拟空间地址中

* **动态链接**：代码被放到动态链接库或共享对象的某个目标文件中，链接程序只是在最终的可执行程序记录共享对象的名字等一些信息。在程序执行时，动态链接库的全部内容会被映射到运行时相应进行的虚拟地址空间

### 栈和堆

* 栈：由操作系统分配，存放函数参数，局部变量等
  
  栈在内存中是连续的一块空间

* 堆：由程序员主动申请，如果程序结束还没有释放，操作系统会自动回收

> 堆内存不连续
> 
> 在 malloc 空间的时候，申请的空间大小不一，容易造成碎片。使用 malloc 的时候系统内部有一个空闲内存映射表，系统会自动寻找合适大小的空间分配

### 内存泄露

内存泄漏是指你向系统申请分配内存进行使用(new)，可是使用完了以后却不归还(delete)，结果你申请到的那块内存你自己也不能再访问（也许你把它的地址给弄丢了），而系统也不能再次将它分配给需要的程序。一个盘子用尽各种方法只能装4个果子，你装了5个，结果掉倒地上不能吃了。这就是溢出！比方说栈，栈满时再做进栈必定产生空间溢出，叫上溢，栈空时再做退栈也产生空间溢出，称为下溢。就是分配的内存不足以放下数据项序列,称为内存溢出

### C++ 三大特性

1. 封装
   
   把数据和操作数据的方法都放到一个类中，给每个成员设置相应的权限，对外只开放调用它的接口
2. 多态

   简单说就是 一个函数，多种实现，在程序运行时根据传入的对象调用不同的函数
   
   C++ 的多态主要是通过虚函数来实现，在基类中定义一个函数为虚函数，在派生类中定义实现方法

3. 继承
   
   在原有类的基础上产生新的类，产生的类是子类或派生类，原有类是基类或父类
   
### 智能指针

在 c++ 中，动态内存的管理是由 new 和 delete 完成的。通过 new 在堆中动态分配一块空间并返回一个指向这个区域的指针，delete 用来销毁对象并释放相对应的内存。但是如果使用之后忘记 delete，就容易出现内存泄露，或者在尚有引用的情况下就释放内存，就会产生非法引用的指针

所以为了更加安全地使用动态内存，C++11 引入智能指针。智能指针的行为类似指针，最重要的区别是它会对指针进行自动管理，就不需要手动地 delete 指针

* shared_ptr
  
  顾名思义，shared_ptr 允许多个指针共享一个对象

```
  //初始化方式
  shared_ptr<T>p1;    //默认初始化的智能指针保存着一个空指针
  shared_ptr<T>p(q);  //p 是 q 的拷贝，此操作会递增 q 中的计数器
  p1 = q;  //此操作会递减 p1 的引用计数，递增 q 的引用技术；若 p 的引用计数变为 0， 则将其管理的原内存释放
```
  **make_shared**:
  最安全的分配和使用动态内存的方法就是调用一个名为 make_shared 的标准库函数，此函数在动态内存中分配一个对象并初始化它，返回指向此对象的 shared_ptr
  ```C++
  shared_ptr<int>p3 = make_shared<int>(42);
  shared_ptr<int>p4 = make_shared<string>(10,'a');
  shared_ptr<int>p5 = make_shared<int>();
  ```

  每个 shared_ptr 都有一个关联的计数器，通常称为**引用计数**，无论何时我们拷贝一个 shared_ptr，计数器都会递增。当我们给 shared_ptr 赋予一个新值或者 shared_ptr 被销毁(例如一个局部的 sharedptr 离开其作用域时)，计数器都会递减，一旦 shared_ptr 的计数器变为 0，他就会自动释放自己所管理的对象

* unique_ptr
  
  unique_ptr 则独占所指向的对象。由于一个 unique_ptr 拥有它指向的对象，因此 unique_ptr 不支持普通的拷贝或赋值操作

  ```C++
  unique_ptr<T> u1;     //默认初始化的智能指针保存着一个空指针
  ```
  虽然我们不能拷贝或者赋值，但是可以通过调用 reset 或 release 将指针所有权从一个（非const） unique_ptr 转移给另一个
  ```C++
  unique_ptr<string> p2 (p1.release());     //release 将p1置为空
  unique_ptr<string> p3 (new string ("deep"));
  p3.reset(p2.release());                   //reset 释放了 p3 原来指向的内存，release 将p2 置为空       
  ```
* weak_ptr

  weak_ptr 是一种不控制所指向对象生存期的智能指针，它指向一个由 **shared_ptr** 管理的对象，将一个 weak_ptr 绑定到 shared_ptr 不会改变 shared_ptr 的计数器。一旦最后一个指向对象的 shared_ptr 被销毁，对象就会被释放，即使有 weak_ptr 指向对象，还是会被销毁 、


### vector 扩容机制

vector 在初始化的时候，会在内存中分配一块连续的空间，大小不小于初始化时内容的大小。由于 vector 支持变长，在添加元素时若超过原有容器 capacity，就需要扩容。

此时 vector 会另外再找一块连续的内存空间，将容量变大（增加倍数不一定，由编译器定），并将原来的元素都复制到新空间中去。所以原来的迭代器就无法使用了

* reserve 函数实现扩容只会改变 vector 的 capacity，不会改变 size
* resize 会改变容器的 capacity 和 size 大小，并且创建对象，如果申请的 n 比原来的 size 小，多出的元素会被丢弃掉

### map 和 unordered_map 区别


### 虚函数

### 构造函数能不能加 virtual，析构函数呢

* 构造函数不可以。

  从继承的概念上讲，总是要先构造父类对象，然后再构造子类对象。虚函数的作用在于通过父类的指针或引用来调用子类的成员函数，而构造函数是在创建对象的时候自己主动调用的，子类在调用构造函数的时候自动调用了基类的构造函数。因此声明成虚函数并没有意义。

* 析构函数可以。
  我们知道，为了能够正确地调用对象的析构函数，一般要求具有层次结构的基类定义其析构函数为虚函数。因为在 delete 一个抽象类指针的时候，必须要通过**虚函数找到真正的析构函数**

  ```C++
  class Base{
    public:
      Base(){}
      virtual ~Base(){}
  };
  
  class Derived : public Base{
    public:
      Derived(){}
      ~Derived(){}
  };
  
  void test(){
    Base *p;
    p = new Derived;
    delete p;
  }
  ```
  这是正确的用法，会发生动态绑定，他会先调用 Derived 的析构函数，然后是 Base 的析构函数

  如果析构函数不加 virtual，`delete p` 只会执行 Base 的析构函数，而不是真正的 Derived 析构函数（因为不是 virtual 函数，所以调用的函数依赖于指向静态类型，即 Base）

  ### static 

 * 静态成员变量

  静态成员变量时该类的所有对象共有的。对于普通成员变量，每个类对象都有自己的一份拷贝，而静态成员变量只分配一次内存，由该类的所有对象共享访问。

  静态成员变量在**全局数据区**分配内存，由本类的所有对象共享。所以在在初始化时就需要分配内存。静态成员变量必须初始化，而且**只能在类体外进行**，否则编译能通过但是链接不能通过

  sizeof 运算不会计算静态成员变量

 * 静态成员函数

  静态成员函数为**类服务**而不是为某一个类的具体对象服务。静态成员函数与静态成员变量一样，都是类的内部实现，属于类定义的一部分。普通成员函数必须具体作用于某一个对象，而静态成员函数并不具体作用于某个对象。

  静态成员函数由于属于类本身，不作用于对象，所以它不具有 this 指针。正因为它没有指向某一个对象，所以它无法访问**属于类对象的非静态成员变量和非静态成员函数**

 > 静态成员变量和静态成员函数在类实例化之前就已经存在可以访问。

  ### new 和 malloc 区别

1. 是否调用构造/析构函数

   new/delete 会调用对象的构造函数/析构函数来完成对象的构造/析构，而malloc/free 不会

2. 对象存储空间
   
   malloc 从堆上动态分配内存。而 new 从自由存储区（free store）上为对象动态分配空间。自由存储区是 C++ 基于 new 操作符的一个抽象概念，凡是通过 new 操作符纪念性内存申请，该内存即为自由存储区。

3. 是否需要指定内存大小
   
   使用 new 操作符申请内存分配时无需指定内存块的大小，编译器会根据类型信息自行计算，而 malloc 则需要显示指出所需内存的尺寸

4. 对数组的处理
   
   c++ 提供了 new[] delete[] 来专门处理数组类型

```C++
A *ptr = new A[10]; 
```

   new 对数组的支持体现在他会分别调用构造函数初始化每一个元素，释放对象时对每个对象调用析构函数。new[] delete[] 要配套使用，不然会造成数组对象部分释放的情况，造成内存泄漏。

   而至于 malloc，他并不知道这块内存上要存放的是数组还是别的什么东西，反正他就给你一块原始的内存，再给你内存的起始地址。所以如果要动态分配一个数组的内存，还需要我们手动指定数组的大小

```C++
int * ptr = (int*) malloc (sizeof(int) * 10);
```

5. new 和 malloc 是否可以相互调用
   
   new/delete 的实现可以基于 malloc， 而 malloc 的实现不能去调用 new

### const 作用

对于指针变量，const 在 * 的左边，则指针指向的变量的值不可直接通过指针改变，在 * 的右边，则指针的指向不可改变。

> 左定值，右定向

对于 const 成员函数（`int getter() const`），可以使用类中所有的成员变量，但是不能修改他们的值。也被称为**常成员函数**

在函数头部加上 const（`const int getter()`），则表示返回值是 const 类型，不能被修改



### inline 内联函数

inline 是一种“**用于实现的关键字**“，inline 关键字必须与函数定义体放在一起才能使函数称为内联，仅将 inline 放在函数声明前不起任何作用

在 C 语言中，可以用宏代码提高执行效率。宏本身不是函数，预处理用复制宏的方式代替函数调用，省去了参数延展、生成汇编语言的 CALL 调用、返回参数、执行 return 等过程，从而提高了速度

而对于 C++ 的内联函数，编译器在符号表种放入函数的声明（包括名字、参数类型、返回值类型）。

如果编译器没有发现内联函数存在错误，那么该函数的代码也被放入符号表里。

在调用一个内联函数时，编译器首先检查调用是否正确（和其他函数一样 进行类型安全检查或者进行自动类型转换），如果正确，内联函数的代码就会直接替换函数调用，省却了函数调用的开销。

这个过程与预处理有显著不同，因为预处理器不能进行类型安全检查或自动类型转换，假如内联函数是成员函数，对象的地址（this）会被放在合适的地方，这是预处理器办不到的。

**慎用内联** 

内联是以代码膨胀（复制）为代价，仅仅省去了函数调用的开销，从而提高函数的执行效率。如果执行函数体内代码的时间，相比于函数调用的开销较大，那么效率的收获会很少。另一方面，每一处内联函数的调用都要复制代码，将使程序的总代码量增大，消耗更多的内存空间。

inline 声明只是一个建议，编译器会根据函数定义自动判断是否需要内联。


---
layout: post
title: "面向对象程序设计期末复习笔记"
date: 2020-12-20 
description: " 面向对象程序设计期末复习笔记"
tag: 课程笔记
---   

---

## 第二章

### 内联函数
    适用于函数体代码不大，但调用频繁的函数

以空间换时间，在编译时该函数不被编译程单独可调用的代码，而是将生成的函数体代码模块直接插入到调用处。

在函数体内定义的成员函数inline属于**建议性操作！！**，编译器会根据不同情况选择是否将inline声明的函数当作内联函数处理

**内联函数和#define的区别**：
1. 内联函数可调试，而define不可以
2. 内联函数可以访问类中的成员变量，而define不可以
3. 内联函数在编译时系统对函数参数安全检查与自动类型转换（同普通函数），define不可以
4. 在类中声明并定义的成员函数自动转成内联函数


**注意事项**：
1. 不允许用**循环语句**和**开关语句** ，上述语句耗时较长，用内联意义不大
2. 使用前定义好
3. 只适合1-5行的小函数（大函数调用和返回的开销相对来说微不足道）
4. 递归函数不能定义成内联函数（自调用，编译时无法生成函数体代码模块）

###  重载函数
重载函数首先用函数参数的不同个数加以区分，然后再以参数类型加以区分，其中**至少要有一个参数类型不同**。

**函数返回类型不同不能区分重载函数** 

匹配规则：

*  寻找与实参个数类型完全相同的函数
*  寻找严格匹配函数（int严格匹配0，char，shortint；double严格匹配float）
*  按照c++内部类型转换规则





## 第三章 类

类在定义时编译系统并不给class类型的每个数据成员分配内存空间

* private 私有部分完全隐藏，只能由本类的成员函数访问，在成员函数体内直呼其名
*  protect 半透明，可由本类成员函数或派生类成员函数访问，不允许程序其他部分直接访问。（protect主要用于类的继承）
*  public 公有部分 对象名.公有成员名

在类体中**不允许**对所定义的数据成员进行初始化

所有的成员函数都必须在类体内用函数原型声明。而定义可在体外，在体外定义时，必须用类名加**作用域运算符**“::”
在体内定义时，编译系统自动将成员函数当作**内联函数**处理，不必写“inline”

只要一个类已经声明（可以没有定义），则指向该类的**对象指针**或该类对象的**引用**都可作为另一个类的成员（而本身却是不可以）。
![Image](https://pic4.zhimg.com/80/v2-cff4bf0c8174fd74d6741f5fb7ba1b25.png)

### 构造函数
* 以类名作为函数名，且不能指定函数**返回类型**和**显式返回值**，连void都不能写
* 作用是为创建的对象分配空间，确保对象自动初始化
* 构造函数可重载
![Image](https://pic4.zhimg.com/80/v2-63dc83071cd5ce30614b64f7ceef85b5.png)


**默认（缺省）构造函数**
若无自己定义的构造函数，则系统提供一个默认构造函数来创建对象，**不做初始化**，所以数据成员赋值是随机数

在使用默认构造函数创建对象时，若创建的是全局对象或静态对象，则对象的所有数据成员都初始化为0或空串

**复制构函**
把已存在对象的每个数据成员都对应复制给新创建对象的数据成员，即对新创建对象进行“位模式拷贝”

*形参前加一个const和引用*

**为什么要引用？**
    若不加引用使用值传递方式的话，编译器又会创建一个相同类型的对象，然后将实参的值传递给形参，这就相当于又要执行一次复制构函，反反复复形成套娃行为。。
    **so，复制构造函数是必须要带引用类型的参数的，而且这也是编译器强制的规定**
    
**为什么要加const？**
    如果在函数中不会改变引用类型参数的值，加不加其实效果一样。但为了数据安全，防止对引用类型参数值的意外修改，还是建议加上const

**对于含有指针类型数据成员的类，且该类的构造函数又含有用new为其对象的数据成员分配堆（heap）中的内存空间时**，自行编写复制构造函数是消除程序隐患的最好方法，它具有用new运算符为新对象分配内存空间的功能。除此之外，都可以借助默认复制构函简化程序

### 析构函数

* 一个类只能有一个析构函数，且析构函数无参数和返回值
* 对于用new创建的对象，只有用delete撤销时编译系统才调用析构函数
* 若没有自己定义的析构函数，系统将自动生成一个缺省析构函数（这个函数是一个空函数）
* 编译系统自动调用构造函数的次序和析构函数的次序相反
 ![Image](https://pic4.zhimg.com/80/v2-d13945b39f346eb9003ff65600c3e5e5.png)


### 结构体
c和c++结构体不同点

*  c中说明结构变量时，必须使用关键字struct

> struct Point p；

而c++中结构体一经定义即可像基本数据类型一样使用

*  c++也可以有成员函数
![Image](https://pic4.zhimg.com/80/v2-bdb79e486bd2181633e3c7c13b0e1bcc.png)

* c++中struct和class的区别：
    1. **struct中访问限制符public：可以缺省，而class中private可缺省**
    2. struct对其对象（结构变量）的初始化可采用大括号包围的初始化列表
    >Point P = {6.6,8.6};
    
    3. 而class对其对象的初始化，只能通过调用自身的成员函数或构造函数进行。只有当该类**没有私有数据成员，没有构造函数，没有虚函数，且不是派生类**才可以用初始化列表


### 引用与指针的区别

* 引用
1. 不能对引用变量取地址
2. 不能建立引用数组
3. 对void类型进行引用是不允许的
4. 引用不能用类型来初始化，引用是变量、对象的引用，而不是类型的引用

5. 无空引用，有空指针
>  int &h = NULL;(X)
> int *p = NULL;(√)
    
### this 指针

> 类名 * const this；

* 常量指针，在成员函数体内不能被修改而重定向

* 可将this指针定义成指向常量的常量指针，用法是在定义或在类体内声明成员函数时，在函数头后加一个 const 。
* ![Image](https://pic4.zhimg.com/80/v2-1061f688628899447f45b59a91e6e89a.png)
    
### 静态成员

静态成员包括静态数据成员和静态成员函数

1. 静态数据成员 ： 
该类的所有对象共享的成员
存放在内存空间的值始终保留，空间分配**不在类的构造函数里完成，且空间回收也不在类的析构函数里完成**
所以必须在所有函数外初始化！
`<类型> 类名 :: 静态数据成员名 = 初值 `
（经常用于记录某个类所创建的对象的个数）

* 静态数据成员在说明为公有时可直接访问，但必须用`类名::`加以指定
* 说明为私有或保护的静态数据成员只有通过调用共有部分成员函数才能访问

2. 静态成员函数

用于解决访问静态数据成员的安全性问题
将关键字`static`加到成员函数头前

* 静态成员函数无隐含的this指针（它属于一个类而非一个对象）。因此调用时必须添加形参指明在哪个类的对象上进行操作。
` 类名 :: 静态成员函数名（实参表）`
![Image](https://pic4.zhimg.com/80/v2-0b8884e9b0cf5f45d77bc8545d92c34b.png)

* 静态成员函数由于没有this指针，只能**访问类中静态数据成员**，不能访问非静态数据成员。
若真的要访问，需要从静态成员函数的参数来进行传递。

### 友元函数

* 不是成员函数，但可访问类的所有成员
* 定义在类体外，不需要用`类名`指定
* 作用 ：提高程序运行效率
* 友元函数是没有 this 指针
* 友元函数可放在类公有部分，保护或私有部分，但是始终开放，直接调用
* 可将一个类的成员函数说明为另一个类的友元
* 将整个类说明为另一个类的友元，简称为“友类”，该类的每个成员函数可访问另一个类的**所有成员**

![Image](https://pic4.zhimg.com/80/v2-555844b187caf47b008e44bf0094966f.png)
![Image](https://pic4.zhimg.com/80/v2-f6fbfdef1c645c2ca154701314564bd7.png)
![Image](https://pic4.zhimg.com/80/v2-71ec186711345baeea43576aecf73f14.png)


### 标识符的作用域和可见性

1. 标识符
第一个字符必须是字母和下划线

2. 标识符的作用域可分为程序级，文件级，函数级，类级和块级

3. 在c++中，通常标识符的可见性和存在期是基本一致的

### 对象数组和成员对象
1. 对象数组
定义格式
> <储存类><类名> 对象数组名 [元素个数]... = {初始化列表}
也可以先定义再赋值

* 对象数组创建时若没有初始化列表，其所属类中必须定义**无参构函**
* 若对象数组所属类含有析构函数，每当建立对象数组时，按元素排列顺序调用构造函数，撤销数组时按**相反**顺序调用析构

2. 成员对象和容器类（containter class）

当一个类的对象作为另一个类的成员时，该对象称为**成员对象或子对象**，这另一个类称为**容器类**，这种方法称为**组合技术**。

对象成员的初始化参数若应放在所属类的**构函头部**，用一个冒号隔开，各成员对象参数表用逗号隔开。每个成员对象只能在初始化参数表中出现**一次**

若构函无参数，则可不写进初始化参数表中

![Image](https://pic4.zhimg.com/80/v2-b7dde64b8a57d1065213120e8c868a80.png)

* 当创建容器类对象时，系统自动先调用成员对象所属类相应的构函，顺序取决于成员对象**在容器类中定义先后顺序**，而不按初始化参数表的排列顺序
* 撤销容器类对象时，先为容器类对象调用析构函数，再调用成员对象的析构函数（顺序与创建时相反）
* 容器类必须有一个构造函数，以便提供一个成员对象的初始化参数表，**编译系统不为容器类提供默认构造函数。**
* 必须在容器类中定义一些公有成员函数，用来访问成员对象中私有数据成员


## 第四章 派生类、基类和继承性

    c++利用派生类机制作为实现继承性的基础，它允许从任何现存的类派生出新的类。
    
    派生类继承了基类的**全部成员**，并且还可以增加基类没有的数据和成员函数
![Image](https://pic4.zhimg.com/80/v2-86e50f07211b72eaf6d7784abd962010.png)

* **垂直多层共享机制**
下层的派生类能自动集成其上各层类的全部数据结构和操作方法

* 继承有两种类型
1. 单继承
每个派生类只能有一个基类
2. 多继承
允许派生类同时具有多个基类

* 派生类的概念和定义

```C++
class 派生类名 : <继承方式> 基类名
{
    <派生类新定义的成员>
}
```

* 继承方式
    * 共有继承（public）： 
    当一个类派生自公有基类时，基类的公有成员也是派生类的公有成员，基类的保护成员也是派生类的保护成员，基类的私有成员不能直接被派生类访问，但是可以通过调用基类的公有和保护成员来访问。
     * 保护继承（protected）：
    当一个类派生自保护基类时，基类的公有和保护成员将成为派生类的保护成员。
    * 私有继承（private）：
    当一个类派生自私有基类时，基类的公有和保护成员将成为派生类的私有成员。


* 访问控制和继承
派生类可以访问基类所有非私有成员
![Image](https://pic4.zhimg.com/80/v2-379b2bf183b93861bd8b256f90b9f0c9.png)
![Image](https://pic4.zhimg.com/80/v2-1902e8d49087f66e3c607c446a193297.png)

一个派生类继承了所有的基类方法，除了：
1. 基类的构造函数，析构函数，复制构函
2. 基类的重载运算符
3. 基类的友元函数

* 多继承

```C++
class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
{
<派生类类体>
};
```
  
```C++
#include<iostream>
using namespace std;


class Base{
		float x,y;
	public:
		Base(float x1, float y1){
			x = x1;
			y = y1;
		}
		void Print(){
			cout << "x = "<< x << '\t'
				<< "y = "<< y<<endl;
		}
}; 

class Derived : public Base{
		float z;
	public :
		//派生类构造函数应包含基类的初始化列表 
		Derived(float x1, float y1, float z1) : Base (x1, y1){
			z = z1;
		}
		/*派生类重新定义的同名成员
		Drrived :: Print()覆盖了从基类继承的Base :: Print()
		即在派生类作用域里，写Print()就是调用Derived中的函数*/
		void Print();
};

void Derived :: Print(){
	//在派生类作用域内调用从基类继承的同名成员函数时，需要“类名：：”指明 
	Base :: Print();
	cout << "z = " << z << endl;
}

int main(){
	Derived d1(3.0,4.0,5.0);
	d1.Print();
	d1.Base::Print();
	d1.Derived::Print();
	return 0;
}
```
![Image](https://pic4.zhimg.com/80/v2-3765c05f541549703dd4d5a0536b6f40.png)

* 派生类的构造函数和析构函数

c++通过派生类的构造函数来使创建派生类的对象时自动初始化它所继承的基类数据成员

将基类看成是派生类的一个无名成员，在派生类构造函数定义时在函数头设置基类构造函数的初始化列表

```C++
#include<iostream>
using namespace std;

class Base{
		float x,y;
	public:
		Base(float x1,float y1);
		~Base();
		void SetBase(float x1,float y1)
		{
			x = x1; y = y1;
		}
		float getx(){
			return x;
		}
		float gety(){
			return y;
		}
		void Print(){
			cout<<"基类对象或派生类对象继承的x值 = "<< x <<'\t'
			<<"y = "<<y<<endl;
		}
};

class Derived : public Base{
		float z;
		Base a;
	public:
		Derived(float x1,float y1,float x2,float y2,float z1);
		~Derived();
		void SetDerived(float x1,float y1,float x2,float y2,float z1){
			Base::SetBase(x1,y1);
			a.SetBase(x2,y2);
			z = z1;
		}
		float getz(){
			return z;
		}
		void Print(){
			Base::Print();
			a.Print();
			cout<<"派生类对象z = "<< z <<endl;
		}
};

Base::Base(float x1,float y1){
	static int i = 0;
	x = x1;
	y = y1;
	cout << "第" << ++i <<"次调用基类构造函数"<<endl;  
}

Base::~Base(){
	static int i = 0;
	cout <<"第"<< ++i << "次调用基类析构函数"<<endl; 
}

Derived:: Derived(float x1,float y1,float x2,float y2,float z1)
: Base(x1,y1),a(x2,y2){
	//派生类构造函数的初始化列表，即包括从基类所继承的成员初始化
	//又包括成员对象 a 的初始化	
	static int j = 0;
	z = z1;
	cout << "第" << ++j << "次调用派生类构造函数"<<endl; 
}

Derived::~Derived(){
	static int j = 0;
	cout << "第" << ++j << "次调用派生类析构函数"<<endl; 
}
int main(){
 	cout << "(1)对基类Base的测试 ： \n";
 	Base b(10,20);
 	b.Print();
 	
 	cout <<"(2)对派生类的测试:\n";
 	Derived d(1,2,3,4,5);
 	d.SetDerived(11,12,6,7,15);
 	d.Print();
 	
	cout<<"(3)其他测试\n";
	float x1 = b.getx();
	float y1 = b.gety();
	float z1 = d.getz();
	cout << "读取基类对象b的x = " << x1 <<"y = " << y1 <<endl;
	cout <<"读取派生类对象d的z = " << z1 <<endl;
	
	cout << "(4)现在开始撤销对象\n";
	
	return 0;
} 
```

![Image](https://pic4.zhimg.com/80/v2-b6884783f7e7205f19635a6cd401cde5.png)
![Image](https://pic4.zhimg.com/80/v2-eb6b9f591f0a3f3ba29861f2fdc4c0e4.png)

> 一个派生类构函的执行次序是：
  1.基类 2. 内部成员对象 3. 派生类
> 而析构函数顺序与构造函数相反

* 在**公有**继承方式下，可以把一个派生类对象赋值给一个基类对象
在此赋值中，只有基类的成员被复制

> 基类对象 = 派生类对象

基类和派生类的赋值是单方向的，即
派生类对象 -> 基类对象

* 在**公有**继承方式下，指向派生类对象的指针可以自动转换为指向基类对象的指针。

> 基类对象指针 = 派生类对象指针
> 基类对象引用 = 派生类对象引用

上述转换均是单方向的

但基类对象指针转换成派生类的对象指针必须**显式进行**
![Image](https://pic4.zhimg.com/80/v2-27df54ecabdf6dc514da6d2458a3bfe4.png)

* 在私有继承方式下,所有转换都不能进行

### 基类和派生类的对象指针

* 一个基类类型的指针可用来指向基类公有派生类的任何对象,但是执政访问公有派生类中从基类继承的公有成员(数据成员和成员函数),不能**访问派生类中新增成员**!
* 如果希望用基类指针访问公有派生类的新增成员,必须将基类指针**强制类型转换**成派生类指针
> ((派生类名 *)基类指针名) -> 新增成员

* 在函数体内凡是对基类对象的数据成员进行操作,既是用来对公有派生类中从基类继承的数据成员进行操作

### 继承传递过程中如下成分不会被继承
* **构造函数和析构函数**

在创建派生类对象时,自动调用基类的构造函数
基类构造函数不能像其他被继承的成员那样由派生类显式调用,写在程序上的只能是初始化列表
当对象离开作用域时由编译器自动调用析构函数

* **友元关系**


### 虚基类
虚基类仅用于多继承
一个类不能多次作为某个派生类的直接基类,但可以多次作为一个派生类的间接基类
![Image](https://pic4.zhimg.com/80/v2-9fb33b611f5073cf6a0696f0a7c0c094.png)

```C++
#include<iostream>
using namespace std;

class A{
	public:
		int value;
		A(int v){
			value = v;
		}
};

class B : virtual public A{
	public:
		int Bvalue;
		B(int v,int b) : A(v){
			Bvalue  = b;
		}
		int bvalue(){
			return Bvalue;
		}
};

class C : virtual public A{
	public:
		int Cvalue;
		C(int v,int c) : A(v){
			Cvalue = c;
		}
		int cvalue(){
			return Cvalue;
		}
};

class D : public B, public C{
	public:
		int Dvalue;
		int Value(){
			return value;
		}
		D(int v1,int v2,int b,int c,int d)
		:B(v1,d), C(v2,c), A(v1){
			Dvalue = d;
		}
		int dvalue(){
			return Dvalue;
		}
};


int main(){
	D obj(1,2,3,4,5);
	cout<<"value of obj = "<< obj.Value()<<endl;
	
	return 0;	
} 
```
输出 `value of obj = 1`

* 二义性:
类A通过两个继承路径到底层派生类D,因此派生类D每个对象同时保存两份类A成员value的值,产生了二义性
* **虚基类**:
将基类定义为虚基类,就可以确保该基类通过多条路径为派生类继承时,派生类仅仅只继承该基类**一次!**

**虚基类仅用于多继承**,且他的构造函数必须只被调用一次

若一个派生类有一个直接或间接的虚基类,那么由该虚基类直接或间接继承的派生类构造函数的初始化列表中,都应列出对该虚基类构造函数的调用

此外,由派生类构造函数的初始化列表中,害必须列出对虚基类构造函数的调用,如没有列出系统将调用该虚基类的缺省构造函数


## 第五章 多态性和虚函数

* 多态性
当基类的成员函数在派生类中重新定义时,其结果是使对象呈现多态性.
因此不是整个类都具有多态性,而是只有**类成员函数具有多态性**
C++的多态性表现了它为编程者提供了**运算符重载,函数名重载,虚函数的运行机制**


* 运算符重载

```C++
#include<iostream>
using namespace std;

class Complex{
		double real,img;
	public:
		Complex(){
			real = img = 0;
		}
		Complex(double r,double i){
			real = r;
			img = i;
		}
		void operator = (const Complex &c);
		Complex operator + (const Complex &c);
		Complex operator - (const Complex &c);
		friend void Print (const Complex &c);
		void show(){
			cout<<real<<'\t'<<img<<endl;
		}
};

void Complex::operator = (const Complex &c){
	static int i = 0;
	real = c.real;
	img = c.img;
	cout << " operator = " << ++i << "called"<<endl;
}
inline Complex Complex :: operator + (const Complex &c){
	return Complex (real+c.real,img+c.img);
}

inline Complex Complex :: operator - (const Complex &c){
	Complex result;
	result.real = real - c.real;
	result.img = img - c.img;
	return result;
}

void Print(const Complex &c){
	if(c.img < 0){
		cout<< c.real << c.img <<'i'<<endl;
	}
	else{
		cout<< c.real <<'+'<<c.img <<'i'<<endl;
	}
}
int main(){
	Complex c1(4.3,2.1),c3;
	Complex c2(3.2,5.6);
	//c2.show();
	Print(c1+c2);
	cout<<endl;
	Print(c1-c2);
	c3 = c1;
	Print(c3);
	return 0;
}
```

运算符函数可定义为类的成员函数或友元(非成员函数)
1. 重载为成员函数
![Image](https://pic4.zhimg.com/80/v2-96e7938aee65274e53564f7f116f7700.png)
2. 重载为友元函数
由于友元函数没有 this 指针,因此对于双目运算符应带两个参数
![Image](https://pic4.zhimg.com/80/v2-0c14a888d83c50f70c2039b4ff1b8b58.png)

* tips:

    * 通常单目运算符最好重载为成员函数,双目运算符最好重载为友元函数
    * 不能改变c++中运算符的优先级和运算量的**个数**
    * 若运算符是一个非成员函数,其参数中至少有一个必须是类的对象
    * 为了防止编译混乱,禁止编程者修改c++中基本数据类型的运算符操作
    * 不能将两个运算符合并去建立一个新的运算符(**可以定义+=,但不能将+和=重定义后合并来实现**)
    * 系统对于运算符重载没有相应的检查措施,使用时应慎重

* 重载赋值运算符
只能实现一次赋值
eg. a = b;
而不能实现多重赋值
eg. c = (b = a)
为了实现多重赋值,可将他定义为
```C++
Complex &Complex :: operator = (const Complex &c){
    real = c.real;
    img = c.img;
    return *this;
}
```
该函数最末端必须有**return *this**
表示返回到this指针所指的对象
![Image](https://pic4.zhimg.com/80/v2-3c77214a2ef75273872a5a0b848ccb1d.png)

没有重载赋值运算符函数时,c++自动提供一个默认赋值运算符函数,但它只能完成**位模式拷贝的函数,而不能为目标对象分配内存空间**

* 赋值运算符和复制构函的区别
1. 用已存在的对象去初始化同类新对象时,用复制构函,两个对象都已存在时,用赋值运算符
2. 赋值运算符应包含两部分,第一部分类似析构函数,第二部分类似复制构造

### 同名成员函数
1. 重载成员函数
在一个类作用域里,多个函数体可以使用相同函数名.实际使用调用哪一个成员函数的匹配规则与普通函数相同.
* 基类和派生类的同名成员函数
基类和派生类有同名的成员函数,在**编译**时加以区分
区分规则:
    1. 根据类对象.基类调用基类成员函数,派生类调用派生类
    2. 在派生类的成员函数体内,需要访问基类同名成员函数必须使用作用域分辨符加以区分,否则将产生无穷循环的递归调用
    3. 在派生类中重定义的成员函数**覆盖(override)**了基类所有同名成员函数,即使基类中同名成员函数的参数表与派生类完全不同,在派生类作用域内基类的同名成员函数也只有用`基类名::`的格式指明才能访问到
    
当基类和派生类具有同名的成员函数时,编译系统根据基类指针定义时的类型说明,确定**基类类型的指针总是指向基类的(函数)**

### 虚函数
* 静态联编
static binding / early binding
在编译时,系统根据对象决定调用固定(程序运行期间不能改变)的函数体代码段
静态联编支持C++中的**运算符重载**和**函数名重载**这两种形式的多态性

* 虚函数机制和动态联编
虚函数是基类的公有部分或保护部分的成员函数在函数头前＋`virtual`

```C++
#include<iostream>
using namespace std;

class A{
	public:
		virtual void Display(){
			cout<<"class A"<<endl;
		}
};

class B : public A{
	public :
		virtual void Display(){
			cout<<"class B"<<endl;
		}
};

void Show (A *p){
	p->Display();
}
int main(){
	A *pa  = new A;
	B *pb = new B;
	pa->Display();
	pb->Display();
	Show(pa);
	Show(pb);
	return 0;
}
```

输出
>
> class A
>
> class B
> 
> class A
> 
> class B

1. 虚函数可以在一个或多个**公有**派生类中被重新定义,但在派生类中重新定义时,该同名**虚函数的原型**,包括返回类型,参数个数,类型,顺序等必须**完全相同**.
2. 一旦在基类中说明了一个虚函数,那么对于后续所有派生类中他仍然是虚函数,(即在派生类中可以不用virtual指明)
3. **必须用指向基类的指针访问虚函数**.只有在同一个指向基类的指针访问虚函数时才能实现多态性

![Image](https://pic4.zhimg.com/80/v2-cca8047e4b830abd3de015b3d85f8391.png)

* 动态联编(dynamic binding /late binding)
在程序运行时,将函数界面(形参)与函数的不同实现版本(函数体)动态进行匹配的过程
访问虚函数的show称为"多态函数"

### 虚函数和重载成员函数的区别
1. 重载函数->静态联编
虚函数->动态联编
2. 重载要求函数原型不完全相同
虚函数要求函数原型完全相同

```C++
#include<iostream>
using namespace std;

class A{
	int a;
	public :
		virtual void f(){
			cout<<"A.f()"<<endl;
		}
		void g(){
			cout<<"A.g()"<<endl;
		}
		virtual void h(){
			cout<<"A.h()"<<endl;
		}
};

class B : public A{
	
	public :
		virtual void g(){
			cout<<"B.g()"<<endl;
		}
		void h(){
			cout<<"B.h()"<<endl;
		}
};

void Do(A &a){
	a.f();
	a.g();
	a.h();
}

int main(){
	A a;
	B b;
	Do(a);
	Do(b);
	return 0;
}
```

1. B内仍有从A继承来的 f() ,所以 f() 采用动态联编
2. g() 在基类中没有使用虚函数,因此不是动态联编.且在编译时将实参b转化为对基类对象的引用,这样以来Do只能访问A类中的 g() ,而不能访问派生类的同名成员函数,因为 A::g( ) 和 B::g() 作用域不同,不发生函数重载
3. h()是动态联编


### 纯虚函数和抽象类
1. 纯虚函数
在基类中声明为虚函数,无实现部分,在各派生类中定义各种实现版本
![Image](https://pic4.zhimg.com/80/v2-7cc9028fba1d60e2b1c6c8629ca9d489.png)
* 在所有派生类中都必须定义该纯虚函数的实现版本

2. 抽象类
含有纯虚函数的类
**至少含有一个纯虚函数**
**只要有一个就是抽象类**

* 只能作为**基类**,不能为抽象类**创建对象**
* 不能用来说明函数参数的类型和返回类型
* 可以为抽象类定义**指针变量和引用**



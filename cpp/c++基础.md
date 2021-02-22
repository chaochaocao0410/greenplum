# c++基础

1、多态

成员函数由调用该函数的对象类型决定。

基类指针指向派生类对象，并且派生类覆盖了基类的虚函数，通过基类指针调用时调的是派生类的成员函数。

多态是通过虚函数来实现的。

c++多态分为运行时多态和编译时多态。

编译时多态由函数重载实现。

运行时多态由虚函数实现。

虚函数表vtbl 存放虚函数地址的表。

虚函数表指针vptr  指向虚函数表的首元素。

虚表结构如下：

​      _vfptr  0x0043dc74{cpptest.exe!const A::`vftable'}

​		[0]	0x0043104b {cpptest.exe!A::func(void)}	void *

​		[1]	0x00431348 {cpptest.exe!A::fun1(void)}	void *

​		[2]	0x00431424 {cpptest.exe!A::fun2(void)}	void *



虚表指针vfptr指向表头。

下面举个例子看下c++多态下对象内存模型

如下Base1、Base2、Base3是3个基类，其中1、3存在虚函数。Derive1继承了这3个类，定义了base3_fun1覆盖了base3的虚函数base3_fun1，derive1_fun1是Derive1自己定义的虚函数。

下面是Derive1的对象内存模型

类Derive1的成员	      取指针的地址              地址	虚指针指向
Base1::vfptr    (&(*((Base1*)(&(d1)))).__vfptr)	fa50	Base1::base1_fun1 下一个是derive1_fun1
base1_1	                                        fa54	
Base3::vfptr    (&(*((Base3*)(&(d1)))).__vfptr)	fa58	Derive1::base3_fun1
base3_1	                                        fa5c	
base2_1	                                        fa60	 



排列规则：

排列时先排列继承的第一个有虚函数的基类，这里是Base1。对于每个对象，先排列虚函数指针，再排列类成员变量。

排列对象时先排列有虚函数的类，最后排列没有虚函数的类。

派生类自己定义的虚函数，排列在第一个具有虚函数的基类的虚函数后面

对于函数调用：

  虚函数：通过__vfptr查找虚函数表，计算偏移量，调用对应虚函数。

普通成员函数：直接调用指针对应的那个基类的函数。

```
class Base1
{
public:
    Base1() : base1_1(11) {}
    int base1_1;
    virtual void base1_fun1() {
        std::cout << "Base1::base1_fun1()" << std::endl;
    }
};

class Base2
{
public:
    Base2() : base2_1(21) {}
    int base2_1;
};

class Base3
{
public:
    Base3() : base3_1(31) {}
    int base3_1;
    virtual void base3_fun1() {
        std::cout << "Base3::base3_fun1()" << std::endl;
    }
};

class Derive1 : public Base1, public Base2, public Base3
{
public:
    Derive1() : derive1_1(11) {}
    int derive1_1;

​```
virtual void base3_fun1() {
    std::cout << "Derive1::base3_fun1()" << std::endl;
}
virtual void derive1_fun1() {
        std::cout << "Derive1::derive1_fun1()" << std::endl;
}
​```

};

void foo(Base1* pb1, Base2* pb2, Base3* pb3, Derive1* pd1)
{
    std::cout << "Base1::\n"
        << "    pb1->base1_1 = " << pb1->base1_1 << "\n"
        << "    pb1->base1_fun1(): ";
    pb1->base1_fun1();

​```
std::cout << "Base2::\n"
    << "    pb2->base2_1 = " << pb2->base2_1
    << std::endl;
 
std::cout << "Base3::\n"
    << "    pb3->base3_1 = " << pb3->base3_1 << "\n"
    <<"    pb3->base3_fun1(): ";
pb3->base3_fun1();
 
std::cout << "Derive1::\n"
    << "    pd1->derive1_1 = " << pd1->derive1_1<< "\n"
    <<"    pd1->derive1_fun1(): ";
pd1->derive1_fun1();
std::cout<< "    pd1->base3_fun1(): ";
pd1->base3_fun1();

std::cout << std::endl;
​```

}
int main() {
	Derive1 d1;
	foo(&d1,&d1,&d1,&d1);
	return 0;
}
```





函数重载

函数重写

函数隐藏



const、static、volatile

const在c语言中修饰指针，生命指针只读

const int *p  p是一个指向常量的指针，常指针 *p不可变

int * const p  p只能指向某一个int型变量，p不可变

在c++中

还有引用

const int & p=&a;

引用本质上是一个自身为常量的指针，p本身不可变

加上const，常引用。p引用的变量也不可变



**static**

修饰全局变量

和普通全局变量相比，限制了作用域为本文件可见。

普通全局变量，多个文件共享，某个文件定义，其他文件通过extern int a；去使用该全局变量

修饰局部变量

和普通局部变量相比，扩大了生命周期为贯穿整个程序。

普通局部变量，在函数体内定义，函数调用完毕则立即销毁，每次调用重新分配内存。

c++

修饰成员变量， 修饰成员变量使所有的对象只保存一个该变量， 而且不需要生成对象就可以访问该成员。

修饰成员函数， 修饰成员函数使得不需要生成对象就可以访问该函数， 但是在 static 函数内不能访问非静态
成员。  

参考文章

https://blog.csdn.net/li1914309758/article/details/79916414
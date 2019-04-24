# c++基础知识

- [c++基础知识](#c基础知识)
  - [1.智能指针](#1智能指针)
  - [2.迭代器类型](#2迭代器类型)
  - [3.深拷贝和浅拷贝](#3深拷贝和浅拷贝)
  - [4.堆栈内存分配](#4堆栈内存分配)
  - [5.C++11特性](#5c11特性)
  - [6.C++三大特性](#6c三大特性)
  - [7.虚函数、纯虚函数](#7-虚函数纯虚函数)
  - [8. 引用和指针的区别](#8-引用和指针的区别)
  - [9.字节对齐](#9字节对齐)
  - [10.Vector底层实现](#10vector底层实现)
  - [11.预编译](#11预编译)
  - [12.用c实现继承和多重继承](#12用c实现继承和多重继承)
  - [13.map和unordered_map区别](#13map和unordered_map区别)

## 1.智能指针

当我们写一个new语句时，一般就会立即把delete语句直接也写了，但是我们不能避免程序还未执行到delete时就跳转了或者在函数中没有执行到最后的delete语句就返回了，如果我们不在每一个可能跳转或者返回的语句前释放资源，就会造成内存泄露。使用智能指针可以很大程度上的避免这个问题，因为智能指针就是一个类，当超出了类的作用域是，类会自动调用析构函数，析构函数会自动释放资源。

将基本类型指针封装为类对象指针（这个类肯定是个模板，以适应不同基本类型的需求），并在析构函数里编写delete语句删除指针指向的内存空间。

STL一共给我们提供了四种智能指针：auto_ptr、unique_ptr、shared_ptr和weak_ptr。
模板auto_ptr是C++98提供的解决方案，C+11已将将其摒弃，并提供了另外两种解决方案。

智能指针需要引入头文件`#include <memory>`。智能指针的构造函数都是用explicit关键字修饰的，即不允许隐式的转换，必须显示调用构造函数。

### 1.auto_ptr

auto_ptr的思路是很简单的，它仅有一个成员变量`_Myptr`，即指定的类型的指针，该指针在auto_ptr初始化时被赋值，当auto_ptr对象被析构的时候将该指针被delete。同时auto_ptr为了保证自身的安全性，需要拥有其指向的对象的指针的所有权，因此他的赋值构造函数和重载的`=`函数都会将传入的指针置为NULL。

```c
template<class _Ty>
    class auto_ptr
    {   // wrap an object pointer to ensure destruction
public:
    typedef _Ty element_type;

    explicit auto_ptr(_Ty * _Ptr = 0) _NOEXCEPT
        : _Myptr(_Ptr)
        {   // construct from object pointer
    }

    auto_ptr(auto_ptr& _Right) _NOEXCEPT
        : _Myptr(_Right.release())
        {   // construct by assuming pointer from _Right auto_ptr
        }

    auto_ptr(auto_ptr_ref<_Ty> _Right) _NOEXCEPT
        {   // construct by assuming pointer from _Right auto_ptr_ref
        _Ty * _Ptr = _Right._Ref;
        _Right._Ref = 0;    // release old
        _Myptr = _Ptr;  // reset this
        }

    template<class _Other>
        operator auto_ptr<_Other>() _NOEXCEPT
        {   // convert to compatible auto_ptr
        return (auto_ptr<_Other>(*this));
        }

    template<class _Other>
        operator auto_ptr_ref<_Other>() _NOEXCEPT
        {   // convert to compatible auto_ptr_ref
        _Other * _Cvtptr = _Myptr;  // test implicit conversion
        auto_ptr_ref<_Other> _Ans(_Cvtptr);
        _Myptr = 0;    // pass ownership to auto_ptr_ref
         return (_Ans);
        }

    template<class _Other>
        auto_ptr& operator=(auto_ptr<_Other>& _Right) _NOEXCEPT
        {   // assign compatible _Right (assume pointer)
        reset(_Right.release());
        return (*this);
        }

    template<class _Other>
        auto_ptr(auto_ptr<_Other>& _Right) _NOEXCEPT
        : _Myptr(_Right.release())
        {   // construct by assuming pointer from _Right
        }

    auto_ptr& operator=(auto_ptr& _Right) _NOEXCEPT
        {    // assign compatible _Right (assume pointer)
        reset(_Right.release());
        return (*this);
        }

    auto_ptr& operator=(auto_ptr_ref<_Ty> _Right) _NOEXCEPT
        {    // assign compatible _Right._Ref (assume pointer)
        _Ty * _Ptr = _Right._Ref;
        _Right._Ref = 0;    // release old
        reset(_Ptr);    // set new
        return (*this);
        }

    ~auto_ptr() _NOEXCEPT
        {    // destroy the object
        delete _Myptr;
        }

    _Ty& operator*() const _NOEXCEPT
        {    // return designated value
 #if _ITERATOR_DEBUG_LEVEL == 2
        if (_Myptr == 0)
            {
            _DEBUG_ERROR("auto_ptr not dereferencable");
            }
 #endif /* _ITERATOR_DEBUG_LEVEL == 2 */

        return (*get());
        }

    _Ty * operator->() const _NOEXCEPT
        {    // return pointer to class object
 #if _ITERATOR_DEBUG_LEVEL == 2
        if (_Myptr == 0)
            {
            _DEBUG_ERROR("auto_ptr not dereferencable");
            }
 #endif /* _ITERATOR_DEBUG_LEVEL == 2 */

        return (get());
        }

    _Ty * get() const _NOEXCEPT
        {    // return wrapped pointer
        return (_Myptr);
        }

    _Ty * release() _NOEXCEPT
        {    // return wrapped pointer and give up ownership
        _Ty * _Tmp = _Myptr;
        _Myptr = 0;
        return (_Tmp);
        }

    void reset(_Ty * _Ptr = 0)
        {    // destroy designated object and store new pointer
        if (_Ptr != _Myptr)
            delete _Myptr;
        _Myptr = _Ptr;
        }

private:
    _Ty * _Myptr;    // the wrapped object pointer
    };

```

auto_ptr提供了三种方法。

1. `get()`方法，获取当前指针。
2. `release()`方法，返回当前指针指向，并将当前`_Myptr`置为`NULL`.
3. `reset(_Ty * _Ptr = 0)`方法，回收当前指针指向的内存，并将`_Myptr`置为传入的参数，可以不传入参数。

使用auto_ptr需要注意：

1. auto_ptr不能共享所有权，因为复制构造函数和重载=会释放传入值的控制权。
2. auto_ptr不能指向数组，因为auto_ptr在析构的时候只是调用delete,而数组应该要调用delete[]。
3. auto_ptr不能作为容器的成员，因为标准库的容器要求对象是可拷贝的，但是auto_ptr的拷贝是会影响原有对象的。
4. auto_ptr必须使用构造函数来初始化,`auto_ptr<int>p = new int(2);`这种语法是不允许的，因为auto_ptr的构造函数被声明为explicit。


### 2.shared_ptr

    当多个shared_ptr管理同一个指针，仅当最后一个shared_ptr析构时，指针才被delete。引用计数指的是，所有管理同一个裸指针（raw pointer）的shared_ptr，都共享一个引用计数器，每当一个shared_ptr被赋值（或拷贝构造）给其它shared_ptr时，这个共享的引用计数器就加1，当一个shared_ptr析构或者被用于管理其它裸指针时，这个引用计数器就减1，如果此时发现引用计数器为0，那么说明它是管理这个指针的最后一个shared_ptr了，于是我们释放指针指向的资源。

## 2.迭代器类型

共有五类迭代器：Input Iterator ,Output Iterator,Forwaed Iterator,Bidirectional Iterator,Random Access Iterator.

- 输入迭代器（Input Iterator）：通过对输入迭代器解除引用，它将引用对象，而对象可能位于集合中。最严格的输入迭代只能以只读方式访问对象。例如：istream。
- 输出迭代器（Output Iterator）：该类迭代器和Input Iterator极其相似，也只能单步向前迭代元素，不同的是该类迭代器对元素只有写的权力。例如：ostream, inserter。
以上两种基本迭代器可进一步分为三类：
- 前向迭代器（Forward Iterator）：该类迭代器可以在一个正确的区间中进行读写操作，它拥有Input Iterator的所有特性，和Output Iterator的部分特性，以及单步向前迭代元素的能力。
- 双向迭代器（Bidirectional Iterator）：该类迭代器是在Forward Iterator的基础上提供了单步向后迭代元素的能力。例如：list, set, multiset, map, multimap。
- 随机迭代器（Random Access Iterator）：该类迭代器能完成上面所有迭代器的工作，它自己独有的特性就是可以像指针那样进行算术计算，而不是仅仅只有单步向前或向后迭代。例如：vector, deque, string, array。


## 3.深拷贝和浅拷贝

对于build-in类型来说，复制是简单的，都是开辟新的内存，将对应的值放入新开辟的位置即可，之后他们不再有关系（指针类型就是地址的复制，和指向的地方没关系）。
对于类而言，如果存在指针和引用类型，（const类型？），需要考虑自己来写拷贝构造函数和重载=操作符。

- 浅拷贝（位拷贝）
    指将一个对象的内存映像按位原封不动的复制给另一个对象，所谓值拷贝就是指，将原对象的值复制一份给新对象。 在用"bitwise assignment"时会直接将对象的内存映像复制给另一个对象，这样两个对象会指向同一个内存区域，当一个对象被释放后，另一个对象的指针会成为野指针（悬垂指针）。这时，就应该编写operator=和copy constructor来实现值拷贝 。
    默认的拷贝构造函数”和“缺省的赋值函数”均采用“位拷贝”而非“值拷贝”的方式来实现，倘若类中含有指针变量，这两个函数注定将出错。

- 深拷贝（值拷贝）
    在“深拷贝”的情况下，对于对象中动态成员，就不能仅仅简单地赋值了，而应该重新动态分配空间。

- 产生复制的场景：
    1. 建立一个新对象，并用另一个同类的已有对象对新对象进行初始化
    2. 当函数的参数为类的对象时，这时调用此函数时使用的是值传递，也会产生对象的复制
    3. 函数的返回值是类的对象时，在函数调用结束时，需要将函数中的对象复制一个临时对象并传给改函数的调用处

    `这里通过查资料存疑， 默认拷贝构造函数基本工作原理：对所有的非POD类型成员变量执行拷贝构造，POD类型成员变量则进行位拷贝，当成员变量都是POD类型的时候，编译器也许不会产生拷贝构造函数，只是对对象进行位拷贝。 默认operator=基本工资原理：对所有的非POD类型成员变量执行operator=操作，POD类型成员变量则进行位拷贝，当成员变量都是POD类型的时候，编译器也许不会产生operator=，只是对对象进行位拷贝。 从上面两个工作原理看到： 1.当class成员变量是有const修饰、引用类型、不能够提供operator=操作(例如boost::nocopyable)时候就不会产生operator=，我们对这个class对象进行赋值操作的时候就是编译出错。 2.当class成员变量是不能够提供拷贝构造操作(例如boost::nocopyable)时候就不会产生拷贝构造函数，我们对这个class对象进行拷贝构造操作的时候就是编译出错。 以前(2005年前？)c++各种参考文献对构造、析构和拷贝关注较多的是“深拷贝”和“浅拷贝”问题，现在的c++可能考虑更多的是如何保证不被拷贝^_^(个人见解)。`

## 4.堆栈内存分配

## 5.C++11特性

## 6.C++三大特性

C++三大特性：封装、继承、多态

封装可以使得代码模块化，继承可以扩展已存在的代码，他们的目的都是为了代码重用。而多态的目的则是为了接口重用

- 封装：封装是在设计类的一个基本原理，是将抽象得到的数据和行为（或功能）相结合，形成一个有机的整体，也就是将数据与对数据进行的操作进行有机的结合，形成“类”，其中数据和函数都是类的成员。

- 继承：如果一个类别B“继承自”另一个类别A，就把这个B称为“A的子类”，而把A称为“B的父类别”也可以称“A是B的超类”。继承可以使得子类具有父类别的各种属性和方法，而不需要再次编写相同的代码。在令子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能。
    1. 访问权限
        - public： 父类对象内部、父类对象外部、子类对象内部、子类对象外部都可以访问。
        - protected:父类对象内部、子类对象内部可以访问，父类对象外部、子类对象外部都不可访问。
        - private：父类对象内部可以访问，其他都不可以访问。

        |访问对象|public|protected|private|
        |-|-|-|-|
        |父类|可见|可见|可见|
        |子类|可见|可见|不可见|
        |父类外部|可见|不可见|不可见|
        |子类外部|可见|不可见|不可见|
    2. 继承方式
        ps.三种继承方式不影响子类对父类的访问权限，子类对父类只看父类的访问控制权。继承方式是为了控制子类(也称派生类)的调用方(也叫用户)对父类(也称基类)的访问权限。public、protected、private三种继承方式，相当于把父类的public访问权限在子类中变成了对应的权限。 如protected继承，把父类中的public成员在本类中变成了protected的访问控制权限；private继承，把父类的public成员和protected成员在本类中变成了private访问控制权。

        ps.友元是类级别的，不存在继承的问题。
- 多态：多态性可以简单地概括为“一个接口，多种方法”，程序在运行时才决定调用的函数，它是面向对象编程领域的核心概念。多态(polymorphism)，字面意思多种形状。
    1. 静态多态：静态多态也称为静态绑定或早绑定。编译器在编译期间完成的，编译器根据函数实参的类型(可能会进行隐式类型转换)，可推断出要调用那个函数，如果有对应的函数就调用该函数，否则出现编译错误。
        - 函数重载

            编译器根据函数不同的参数表，对同名函数的名称做修饰，然后这些同名函数就成了不同的函数（至少对于编译器来说是这样的）。函数的调用，在编译器间就已经确定了，是静态的。也就是说，它们的地址在编译期就绑定了（早绑定）。

        - 泛型编程

            泛型编程就是指编写独立于特定类型的代码，泛型在C++中的主要实现为模板函数和模板类。
            泛型的特性：

                1. 函数模板并不是真正的函数，它只是C++编译生成具体函数的一个模子。
                1. 函数模板本身并不生成函数，实际生成的函数是替换函数模板的那个函数，比如上例中的add(sum1,sum2)，这种替换是编译期就绑定的。
                3. 函数模板不是只编译一份满足多重需要，而是为每一种替换它的函数编译一份。
                4. 函数模板不允许自动类型转换。
                5. 函数模板不可以设置默认模板实参。比如template <typename T=0>不可以。
    2. 动态多态
        c++的动态多态是基于虚函数的。对于相关的对象类型，确定它们之间的一个共同功能集，然后在基类中，把这些共同的功能声明为多个公共的虚函数接口。各个子类重写这些虚函数，以完成具体的功能。客户端的代码（操作函数）通过指向基类的引用或指针来操作这些对象，对虚函数的调用会自动绑定到实际提供的子类对象上去。
    3. 宏多态（？）
        带变量的宏可以实现一种初级形式的静态多态：

        ```c
        #include <iostream>
        #include <string>

        // 定义泛化记号：宏ADD
        #define ADD(A, B) (A) + (B);

        int main()
        {
            int i1(1), i2(2);
            std::string s1("Hello, "), s2("world!");
            int i = ADD(i1, i2);                        // 两个整数相加
            std::string s = ADD(s1, s2);                // 两个字符串“相加”
            std::cout << "i = " << i << "/n";
            std::cout << "s = " << s << "/n";
        }
        ```

    4. 动态多态和静态多态的比较
        1. 静态多态
            - 优点：
                1. 由于静多态是在编译期完成的，因此效率较高，编译器也可以进行优化；
                2. 有很强的适配性和松耦合性，比如可以通过偏特化、全特化来处理特殊类型；
                3. 最重要一点是静态多态通过模板编程为C++带来了泛型设计的概念，比如强大的STL库。
            - 缺点：
                1. 由于是模板来实现静态多态，因此模板的不足也就是静多态的劣势，比如调试困难、编译耗时、代码膨胀、编译器支持的兼容性不能够处理异质对象集合
        2. 动态多态
            - 优点：
                1. OO设计，对是客观世界的直觉认识；
                2. 实现与接口分离，可复用
                3. 处理同一继承体系下异质对象集合的强大威力
            - 缺点：
                1. 运行期绑定，导致一定程度的运行时开销；
                2. 编译器无法对虚函数进行优化
                3. 笨重的类继承体系，对接口的修改影响整个类层次；
        3. 不同点：
            - 本质不同，`早晚绑定`。静态多态在编译期决定，由模板具现完成，而动态多态在运行期决定，由继承、虚函数实现；
            - 动态多态中接口是显式的，以`函数签名`为中心，多态通过虚函数在运行期实现，静态多台中接口是隐式的，以`有效表达式`为中心，多态通过模板具现在编译期完成
        4. 相同点：
            - 都能够实现多态性，静态多态/编译期多态、动态多态/运行期多态；
            - 都能够使接口和实现相分离，一个是模板定义接口，类型参数定义实现，一个是基类虚函数定义接口，继承类负责实现；

## 7.虚函数、纯虚函数

- 概述

    它虚就虚在所谓“推迟联编”或者“动态联编”上，一个类函数的调用并不是在编译时刻被确定的，而是在运行时刻被确定的。由于编写代码的时候并不能确定被调用的是基类的函数还是哪个派生类的函数，所以被成为“虚”函数。

    虚函数只能借助于指针或者引用来达到多态的效果。常用的方式是父类指针指向子类对象。当有多个对于父类的继承时，可以统一用父类指针来表示各子类对象，但是事实上所指向的对象具体是哪一个，或者说所调用的函数是哪一个子类的对象的函数需要在运行时才知道。这就实现了多态。

    ```c
    class A
    {
        public:
            virtual void foo()
            {
                cout<<"A::foo() is called"<<endl;
            }
    };
    class B:public A
    {
        public:
            void foo()
            {
                cout<<"B::foo() is called"<<endl;
            }
    };
    int main(void)
    {
        A *a = new B();
        a->foo();   // 在这里，a虽然是指向A的指针，但是被调用的函数(foo)却是B的!
        return 0;
    }
    ```

- 语法

    ```c
    virtual void fun1()=0 //纯虚函数
    virtual void fun1()s  // 虚函数
    ```

- 纯虚函数
        纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。含有虚函数的类成为抽象类，不能生成对象。
- 虚函数表
    虚表是属于类的，而不是属于某个具体的对象，`一个类只需要一个虚表即可` 。同一个类的所有对象都使用同一个虚表。为了指定对象的虚表，对象内部包含一个虚表的指针，来指向自己所使用的虚表。为了让每个包含虚表的类的对象都拥有一个虚表指针，编译器在类中添加了一个指针，*__vptr，用来指向虚表。这样，当类的对象在创建时便拥有了这个指针，且这个指针的值会自动被设置为指向类的虚表。
    C++的编译器应该是保证虚函数表的指针存在于对象实例中最前面的位置（这是为了保证取到虚函数表的有最高的性能——如果有多层继承或是多重继承的情况下）。

## 8. 引用和指针的区别

指针-对于一个类型T，T* 就是指向T的指针类型，也即一个T*类型的变量能够保存一个T对象的地址，而类型T是可以加一些限定词的，如const、volatile等等。
引用-引用是一个对象的别名，主要用于函数参数和返回值类型，符号X&表示X类型的引用。

- 不同点：

   本质： `指针指向一块内存，它的内容是所指内存的地址；而引用则是某块内存的别名，引用不改变指向。`

    1. 引用不可以为空，但指针可以为空。定义一个引用的时候，必须初始化。因此使用指针之前必须做判空操作，而引用就不必。
    2. 引用不可以改变指向，对一个对象"至死不渝"；但是指针可以改变指向，而指向其它对象。
    3. 引用的大小是所指向的变量的大小，因为引用只是一个别名而已；指针是指针本身的大小，4个字节（32位）。
    4. `const int* p->` 指向常量的指针，`int * const p->`本身是常量的指针.后者需要在定义的时候初始化。对于引用来讲`int const & p=i;`和 `const int &p=i;`没什么区别，都是指指向的对象是常量。
    5. 引用和指针的++自增运算符意义不同，指针的++表示的地址的变化，一般是向下4个字节的大小（一个只针的大小），引用的++就是对应元素的++操作。
    6. 指针传递和引用传递
        - 指针传递参数本质上是值传递的方式，它所传递的是一个地址值。值传递过程中，被调函数的形式参数作为被调函数的局部变量处理，即在栈中开辟了内存空间以存放由主调函数放进来的实参的值，从而成为了实参的一个副本。值传递的特点是被调函数对形式参数的任何操作都是作为局部变量进行，不会影响主调函数的实参变量的值。
        - 引用传递过程中，被调函数的形式参数也作为局部变量在栈中开辟了内存空间，但是这时存放的是由主调函数放进来的实参变量的地址。被调函数对形参的任何操作都被处理成间接寻址，即通过栈中存放的地址访问主调函数中的实参变量。正因为如此，被调函数对形参做的任何操作都影响了主调函数中的实参变量。

## 9.字节对齐

理由：如果不按照平台要求对数据存放进行对齐，会带来存取效率上的损失。比如32位的Intel处理器通过总线访问(包括读和写)内存数据。每个总线周期从偶地址开始访问32位内存数据，内存数据以字节为单位存放。如果一个32位的数据没有存放在4字节整除的内存地址处，那么处理器就需要2个总线周期对其进行访问，显然访问效率下降很多。

    ```c
    struct A{
        int    a;
        char   b;
        short  c;
    };
    struct B{
        char   b;
        int    a;
        short  c;
    };
    struct C{
        int   a;
        int    b;
        short  c;
    };
    ```

A、B、C的sizeof分别是8,12,12。

通过#pragma pack (value)宏可以指定对齐值

## 10.Vector底层实现

1. vector的数据结构
    vector 的数据结构为线性连续空间。迭代器start和finish分别指向配置来的连续空间中目前已经被使用的范围，end_of_storage指向整个连续空间的（包含备用空间）的尾端。

    ```c
    template <class T,class Alloc = clloc>
    class vector{
    ...
    protected:
        iterator start;
        iterator end;
        iterator end_of_storage;
    }
    ```

2. vector中的size表示当前实际数据数量，capacity 则表示当前可容纳的数量，即已开辟的内存。
3. 释放(pop_back)、删除(erase) 和 清空(clear) 只会改变size，不会改变capacity 。只有在vector析构的时候才会清空所有内存。
4. 追加(push_back)、 插入(insert)等操作首先使用备用空间，当发现备用空间小于“新增元素个数”就需要扩展空间。扩展后的空间大小为：旧长度的两倍或旧长度+新增元素个数（或者说是增加max(oldsize,n)。
5. 扩容时的操作流程为：开辟新内存 -> copy数据 -> 释放旧内存。因此频繁的导致vector扩容(如for循环持续push_back)会使得程序效率降低。因此，如有需要，可以提前通过初始化或者resize、reserve来预先开辟较大的容量。
6. 如果想要提前释放掉vector开辟的内存，可以使其与一个空vector进行交换。
    这是因为vector的拷贝构造函数和operator=函数只拷贝begin()到end()的部分，end()到start+end_of_storage部分不会拷贝；而swap函数是原样拷贝，包括capacity部分都会考过来。平时vector的空间是只增不减的，clear()函数只析构，不释放空间。因此只能用swap函数来释放了。swap之后临时的那个vector应该释放掉，方法是放在花括号中，放在函数中，或者最强大的——用临时对象。并且，用他本身去初始化该临时对象，于是，swap后，vector的容量就等于size，没有多余的空间。
7. vector支持速记存取，提供的是Random Access Iterator.

## 11.预编译

## 12.用c实现继承和多重继承

首先考虑问题，在oo的问题上c和c++差了些什么，其实就是封装、继承和多态。所以想要实现继承的话其实这三点都需要用c来实现。

- 封装

很容易想到struct来实现封装，但是struct只能包含成员变量，不能包含成员函数，所以这里需要引入函数指针。`void(*p)(int)`,然后通过具体的函数在外部定义再赋值给函数指针的方法就可以实现struct包含数据成员和成员函数。

- 继承

继承的内涵是子类包含父类的内容，因此可以采用：

1. 父类是子类的成员对象的方法来实现，多重继承则直接多个对象。

2. 将父类内容全部拷贝到子类当中，可以使用宏的方法避免太多层的继承带来的代码修改问题。

- 多态

通过覆盖的方法来实现，将子类中的父类对象的函数指针指向另一个不同的函数。

## 13.map和unordered_map区别

    STL中，map 对应的数据结构是 红黑树。红黑树是一种近似于平衡的二叉查找树，里面的数据是有序的。在红黑树上做查找操作的时间复杂度为 O(logN)。而 unordered_map 对应哈希表，哈希表的特点就是查找效率高，时间复杂度为常数级别 O(1)， 而额外空间复杂度则要高出许多。所以对于需要高效率查询的情况，使用 unordered_map 容器。而如果对内存大小比较敏感或者数据存储要求有序的话，则可以用 map 容器。

## 14.explicit 关键字

在类的定义中允许使用多种方式进行构造函数的调用。如

```c
struct A
{
    A(int) { }      // 转换构造函数
    A(int, int) { } // 转换构造函数 (C++11)
    operator bool() const { return true; }
};
int main()
{
    A a1 = 1;      // OK ：复制初始化选择 A::A(int)
    A a2(2);       // OK ：直接初始化选择 A::A(int)
    A a3 {4, 5};   // OK ：直接列表初始化选择 A::A(int, int)
    A a4 = {4, 5}; // OK ：复制列表初始化选择 A::A(int, int)
    A a5 = (A)1;   // OK ：显式转型进行 static_cast
}
```

`A a1 = 1;`相当于`A temp(1); a(temp);`调用了构造函数创建了temp对象，再通过复制构造函数进行对a1的构造。
`A a2(2);`就是直接调用构造函数。
`A a3 {4, 5};`直接调用构造函数`A a3(4,5);`。
`A a4 = {4, 5};`相当于`A temp(4,5); a(temp);`

这其中可以看出编译器背着你做了很多工作，从而使得类的构造更容易。但是这种隐式的转换也可能带来问题，比如当一个类只接受整形和字符串类型时，可能因为'c'和“c”错误导致调用了不同的构造函数（因为'c'可以认为是整数99）。这种错误难以发现也难以避免，可以通过explicit关键字的形式声明构造函数，从而指定构造函数或转换函数 (C++11 起)为显式，即它不能用于隐式转换和复制初始化。

```c
struct B
{
    explicit B(int) { }
    explicit B(int, int) { }
    explicit operator bool() const { return true; }
};
 
int main()
{
//  B b1 = 1;      // 错误：复制初始化不考虑 B::B(int)
    B b2(2);       // OK ：直接初始化选择 B::B(int)
    B b3 {4, 5};   // OK ：直接列表初始化选择 B::B(int, int)
//  B b4 = {4, 5}; // 错误：复制列表初始化不考虑 B::B(int,int)
    B b5 = (B)1;   // OK ：显式转型进行 static_cast
```

参考<https://zh.cppreference.com/w/cpp/language/explicit>
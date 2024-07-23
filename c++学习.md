# 1、C++类&对象

## C++ 类定义

定义一个类需要使用关键字 **class**，然后指定类的名称，并类的主体是包含在一对花括号中，主体包含类的成员变量和成员函数。

定义一个类，本质上是定义一个数据类型的蓝图，它定义了类的对象包括了什么，以及可以在这个对象上执行哪些操作。

## 类 & 对象详解

到目前为止，我们已经对 C++ 的类和对象有了基本的了解。下面的列表中还列出了其他一些 C++ 类和对象相关的概念，可以点击相应的链接进行学习。

| 概念                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [类成员函数](https://www.runoob.com/cplusplus/cpp-class-member-functions.html) | 类的成员函数是指那些把定义和原型写在类定义内部的函数，就像类定义中的其他变量一样。 |
| [类访问修饰符](https://www.runoob.com/cplusplus/cpp-class-access-modifiers.html) | 类成员可以被定义为 public、private 或 protected。默认情况下是定义为 private。 |
| [构造函数 & 析构函数](https://www.runoob.com/cplusplus/cpp-constructor-destructor.html) | 类的构造函数是一种特殊的函数，在创建一个新的对象时调用。类的析构函数也是一种特殊的函数，在删除所创建的对象时调用。 |
| [C++ 拷贝构造函数](https://www.runoob.com/cplusplus/cpp-copy-constructor.html) | 拷贝构造函数，是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。 |
| [C++ 友元函数](https://www.runoob.com/cplusplus/cpp-friend-functions.html) | **友元函数**可以访问类的 private 和 protected 成员。         |
| [C++ 内联函数](https://www.runoob.com/cplusplus/cpp-inline-functions.html) | 通过内联函数，编译器试图在调用函数的地方扩展函数体中的代码。 |
| [C++ 中的 this 指针](https://www.runoob.com/cplusplus/cpp-this-pointer.html) | 每个对象都有一个特殊的指针 **this**，它指向对象本身。        |
| [C++ 中指向类的指针](https://www.runoob.com/cplusplus/cpp-pointer-to-class.html) | 指向类的指针方式如同指向结构的指针。实际上，类可以看成是一个带有函数的结构。 |
| [C++ 类的静态成员](https://www.runoob.com/cplusplus/cpp-static-members.html) | 类的数据成员和函数成员都可以被声明为静态的。                 |

## [构造函数 & 析构函数]

在c++中，构造函数是类的一种特殊成员函数，会在每次创建类的新对象时执行

- `virtual` 用于基类，表示该函数可以在派生类中被覆盖。

- `override` 用于派生类，表示该函数是用来覆盖基类中的虚函数的，并且编译器会检查基类中是否有匹配的虚函数。

  构造函数声明虚函数

- ```
  #include <iostream>
  
  // 基类
  class Base {
  public:
      // 声明虚函数
      virtual void Show() {
          std::cout << "Base class Show function called." << std::endl;
      }
  };
  
  // 派生类
  class Derived : public Base {
  public:
      // 覆盖基类的虚函数
      void Show() override {
          std::cout << "Derived class Show function called." << std::endl;
      }
  };
  
  int main() {
      // 基类指针指向派生类对象
      Base* bptr;
      Derived d;
      bptr = &d;
  
      // 通过基类指针调用虚函数，将调用派生类的版本
      bptr->Show();
  
      return 0;
  }
  ```

  析构函数声明虚函数：

```
#include <iostream>

class Base {
public:
    virtual ~Base() {
        std::cout << "Base class destructor called." << std::endl;
    }
};

class Derived : public Base {
public:
    ~Derived() override {
        std::cout << "Derived class destructor called." << std::endl;
    }
};

int main() {
    Base* basePtr = new Derived();
    delete basePtr; // 调用 Derived 类的析构函数
    return 0;
}

```


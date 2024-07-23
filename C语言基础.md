## 1、Linux命令入门

打开终端：

​		快捷方式：Ctrl + Alt + T

$ 普通用户  #超级用户

| ![image-20240505145314475](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505145314475.png) |
| :----------------------------------------------------------: |

**入门命令：**

注意命令与选项之间需要有空格进行分割

基础知识：

- 标准输入 stdin 对应的是键盘文件
- 标准输出 stdout 对应的是终端《带缓冲区》（屏幕）
- 标准出错 stderr 对应的是终端《无缓冲区》（屏幕）

调试代码技巧

```
1 printf("%s‐‐%s‐‐%d\n" , __FILE__ , __FUNCTION__ , __LINE__ );
2 __FILE__ 文件名
3 __FUNCTION__ 函数名
4 __LINE__ 行号
```

## 2、整型

概念： 表示整数类型的数据

语法：

```
 int a = 123 ;
```

注意：

整型在 32位系统以及64位系统中都占用 4字节

取值范围

```
1 $ getconf INT_MAX
2 2147483647
3 $ getconf INT_MIN
4 ‐2147483648
```

| 类型           | 存储大小    | 值范围                                               |
| :------------- | :---------- | :--------------------------------------------------- |
| char           | 1 字节      | -128 到 127 或 0 到 255                              |
| unsigned char  | 1 字节      | 0 到 255                                             |
| signed char    | 1 字节      | -128 到 127                                          |
| int            | 2 或 4 字节 | -32,768 到 32,767 或 -2,147,483,648 到 2,147,483,647 |
| unsigned int   | 2 或 4 字节 | 0 到 65,535 或 0 到 4,294,967,295                    |
| short          | 2 字节      | -32,768 到 32,767                                    |
| unsigned short | 2 字节      | 0 到 65,535                                          |
| long           | 4 字节      | -2,147,483,648 到 2,147,483,647                      |
| unsigned long  | 4 字节      | 0 到 4,294,967,295                                   |

sizeof运算符，用于计算 变量/类型 的大小。

sizeof与strlen的区别

1、sizeof是运算符，其返回的是数据类型或对象所占用的字节大小，而不是字符串长度，对于char数组，sizeof会返回数组大小，包括末尾的空字符 \0

2、strlen` 函数是用于获取字符串的长度（不包括末尾的空字符 `\0`）。

```
#include <stdio.h> 
#include<string.h>
int main(int argc, char *argv[])
{ 
    char str[]="aaa" ;
    printf("sizeof:%d ",sizeof(str));
    printf("strlen:%d ",strlen(str));
    return 0;
}
输出sizeof:4 strlen:3
```

- **%d** 十进制有符号整数
- **%u** 十进制无符号整数
- **%f** 浮点数
- **%s** 字符串
- **%c** 单个字符
- **%p** 指针的值
- **%e** 指数形式的浮点数
- **%x, %X** 无符号以十六进制表示的整数
- **%o** 无符号以八进制表示的整数
- **%g** 把输出的值按照 %e 或者 %f 类型中输出长度较小的方式输出
- **%p** 输出地址符
- **%lu** 32位无符号整数
- **%llu** 64位无符号整数
- **%%** 输出百分号字符本身。

## 3、浮点类型

下表列出了关于标准浮点类型的存储大小、值范围和精度的细节：

| 类型        | 存储大小 | 值范围                 | 精度        |
| :---------- | :------- | :--------------------- | :---------- |
| float       | 4 字节   | 1.2E-38 到 3.4E+38     | 6 位有效位  |
| double      | 8 字节   | 2.3E-308 到 1.7E+308   | 15 位有效位 |
| long double | 16 字节  | 3.4E-4932 到 1.1E+4932 | 19 位有效位 |

## 4、void 类型

void 类型指定没有可用的值。它通常用于以下三种情况下：

| 序号 | 类型与描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **函数返回为空** C 中有各种函数都不返回值，或者您可以说它们返回空。不返回值的函数的返回类型为空。例如 **void exit (int status);** |
| 2    | **函数参数为空** C 中有各种函数不接受任何参数。不带参数的函数可以接受一个 void。例如 **int rand(void);** |
| 3    | **指针指向 void** 类型为 void * 的指针代表对象的地址，而不是类型。例如，内存分配函数 **void \*malloc( size_t size );** 返回指向 void 的指针，可以转换为任何数据类型。 |

| 类型   | 描述                                                         |
| :----- | :----------------------------------------------------------- |
| char   | 通常是一个字节（八位）, 这是一个整数类型。                   |
| int    | 整型，4 个字节，取值范围 -2147483648 到 2147483647。         |
| float  | 单精度浮点值。单精度是这样的格式，1位符号，8位指数，23位小数。![img](https://www.runoob.com/wp-content/uploads/2014/09/v2-749cc641eb4d5dafd085e8c23f8826aa_hd.png) |
| double | 双精度浮点值。双精度是1位符号，11位指数，52位小数。![img](https://www.runoob.com/wp-content/uploads/2014/09/v2-48240f0e1e0dd33ec89100cbe2d30707_hd.png) |
| void   | 表示类型的缺失。                                             |

**字符类型：**

```
char c = 'K' ;

1. 申请一片内存并且命名为 c

2. 确定内存的大小为 char （1字节）

3. 把字符‘K’的ASCII码值转换为二进制，并存储到该内存中
4.计算机中存储的所有数据都是以二进制的形式存在的， 因此字符必须映射某一个数字才能够被存放到计算机中，这个映射的表就成为 ASCII 表，可以使用man手册来查看：
  man ascii
```

**字符串：**字符串的表现形式有两种：

形式一 数组（可读 ，可写）：（存储）

1 char s1 [] = "Hello" ; //使用一个数组来存放字符串 "Hello"

以上语句其实是把一个字符串常量 "Hello" ， 复制到数组 s1 所代表的内存当中

| <img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505152215932.png" alt="image-20240505152215932" style="zoom:80%;" /> |
| :----------------------------------------------------------: |



形式二 指针 （只读）：（指向）

1 char * s2 = "Even"; // 使用一个指针来指向常量字符串

| <img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505152348150.png" alt="image-20240505152348150" style="zoom:80%;" /> |
| :----------------------------------------------------------: |

1 char s1[] = "Hello";

2 char *s2 = "Even";

4 printf("%s %s\n" , s1 , s2 )

## 常用的算术转换

**常用的算术转换**是隐式地把值强制转换为相同的类型。编译器首先执行**整数提升**，如果操作数类型不同，则它们会被转换为下列层次中出现的最高层次的类型：

![Usual Arithmetic Conversion](https://www.runoob.com/wp-content/uploads/2014/08/usual_arithmetic_conversion.png)

如果一个运算符两边的运算数类型不同，先要将其转换为相同的类型，即较低类型转换为较高类型，然后再参加运算，转换规则如下图所示。

![img](https://www.runoob.com/wp-content/uploads/2014/09/img1.png)

## 5、 C 中的左值（Lvalues）和右值（Rvalues）

C 中有两种类型的表达式：

1. **左值（lvalue）：**指向内存位置的表达式被称为左值（lvalue）表达式。左值可以出现在赋值号的左边或右边。
2. **右值（rvalue）：**术语右值（rvalue）指的是存储在内存中某些地址的数值。右值是不能对其进行赋值的表达式，也就是说，右值可以出现在赋值号的右边，但不能出现在赋值号的左边。

## 6、 2#define 与 const 区别

1. 使用 **#define** 预处理器： #define 可以在程序中定义一个常量，它在编译时会被替换为其对应的值。

```
#define 常量名 常量值
```

\#define 的声明 只是一个别名，并不会改变其内在逻辑，也就是不会自动加上小括号增加优先级。

  2.使用 **const** 关键字：**const 关键字用于声明一个只读变量**，即该变量的值不能在程序运行时修改。

```
const 数据类型 常量名 = 常量值;
```

![img](https://www.runoob.com/wp-content/uploads/2014/09/c-const-2021-01-15.png)

**静态全局变量与普通全局变量的区别：**

全局变量(外部变量)的说明之前再冠以static 就构成了静态的全局变量。

全局变量本身就是静态存储方式，静态全局变量当然也是静态存储方式，这两者在存储方式上并无不同。

这两者的区别在于非静态全局变量的作用域是整个源程序， 当一个源程序由多个源文件组成时，非静态的全局变量在各个源文件中都是有效的。 而静态全局变量则限制了其作用域， 即只在定义该变量的源文件内有效， 在同一源程序的其它源文件中不能使用它。

## C 中的运算符优先级

**初等运算符>单目运算符>算术运算符>关系运算符>逻辑运算符>条件运算符>赋值运算符**

| 类别       | 运算符                            | 结合性   |
| :--------- | :-------------------------------- | :------- |
| 后缀       | () [] -> . ++ - -                 | 从左到右 |
| 一元       | + - ! ~ ++ - - (type)* & sizeof   | 从右到左 |
| 乘除       | * / %                             | 从左到右 |
| 加减       | + -                               | 从左到右 |
| 移位       | << >>                             | 从左到右 |
| 关系       | < <= > >=                         | 从左到右 |
| 相等       | == !=                             | 从左到右 |
| 位与 AND   | &                                 | 从左到右 |
| 位异或 XOR | ^                                 | 从左到右 |
| 位或 OR    | \|                                | 从左到右 |
| 逻辑与 AND | &&                                | 从左到右 |
| 逻辑或 OR  | \|\|                              | 从左到右 |
| 条件       | ?:                                | 从右到左 |
| 赋值       | = += -= *= /= %=>>= <<= &= ^= \|= | 从右到左 |
| 逗号       | ,                                 | 从左到右 |

## 7、数组与指针

指针与数组的区别

```
char *str="Hello";
char arrgs[]="Hello";
```

函数的参数中 数组类型参数 `int a[]`本质是指针 可以直接换成 `int *a`;

数组名本身是一个常量指针，意味着它的值是不能被改变的，一旦确定，就不能再指向其他地方。

我们可以使用&运算符来获取数组的地址，如下所示：

```
int myArray[5] = {10, 20, 30, 40, 50};
int *ptr = &myArray[0]; // 或者直接写作 int *ptr = myArray;
```

- **&** *运算符访问的地址*
-  ***** 来返回位于操作数所指定地址的变量的值

C 指针的算术运算

- 指针的每一次递增，它其实会指向下一个元素的存储单元。

- 指针的每一次递减，它都会指向前一个元素的存储单元。

- 指针在递增和递减时跳跃的字节数取决于指针所指向变量数据类型长度，比如 int 就是 4 个字节。

- **指针数组**

  **int \*a[3]**：为什么这里是指针数组，**[]** 的优先级高于 ***** ，所以这是一个数组，而 ***** 修饰数组，所以是指针数组，数组的元素是整型的指针。

  示例:

  ```
  int main() {     
     char *str[3];     
     str[0] = "abc";     
     str[1] = "efg";     
     str[2] = "hij";     
     for (int i = 0; i < 3; ++i) {         
        printf("%s\n", str[i]);     
     } 
  }
  ```

  结果：

  ```
  abc 
  efg 
  hij
  ```

  **数组指针**

  **int (\*a)[3]**：同样的方式，首先括号的优先级最高，所以 ***a** 是指针，而 **[]** 修饰 ***a** ，所以是数组指针，一个指向 3 个元素的一维数组指针。

  示例:

  ```
  typedef int arr[3]; 
  int main() {     
     arr b = {1, 2, 3};     
     int (*a)[3] = &b;     
     arr *c = a;     
     for (int i = 0; i < 3; ++i) {         
        printf("%d\n", (*a)[i]);     
        
     } 
  }
  ```

  结果：

  ```
  1 
  2 
  3
  ```
  
  ## 回调函数
  
  ### 函数指针作为某个函数的参数
  
  
  
  函数指针变量可以作为某个函数的参数来使用的，回调函数就是一个通过函数指针调用的函数。
  
  简单讲：回调函数是由别人的函数执行时调用你实现的函数。

## 8、内存管理

**C语言进程的内存布局：**

- 程序：就是我们写好的代码并编译完成的那个二进制文件，它被存放与磁盘中，它是死的。
- 进程：把磁盘中的二进制文件"拷贝"到内存中取执行它，让运行起来，它是活的。

每一个进程所拥有的内存都是一个虚拟的内存，所谓的虚拟内存是用物理内存中映射投影）而来的，对于每一个进程而言所有的虚拟内存布局都是一样的。让每个进程都以为自己独自拥有了完整的内存空间。

- 物理内存（Physical Memory）
- 虚拟内存（Virtual Memory）

| <img src="C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505162307372.png" alt="image-20240505162307372" style="zoom:80%;" /> |
| :----------------------------------------------------------: |

虚拟内存的布局（区域）：

- 栈 （stack）
- 堆（heap）
- 数据段
- 代码段

| ![image-20240505162543162](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505162543162.png) |
| :----------------------------------------------------------: |
| ![image-20240505162639977](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505162639977.png) |

**静态变量：**

在C语言中有两种静态变量

- 全局变量： 定义在函数体之外的变量
- 静态的局部变量： 定义在函数体内部而且被 static 修饰的变量

**数据段与代码段：**

数据段有哪些内容：

- .bss 未初始化的静态数据 ， 会被自动初始化为0
- .data 已初始化的静态数据
- .rodata 存放常量数据 "Hello Even" , 不允许修改的（只读）

代码段中有那些内容：

- 用户的代码 （比如我们自己写函数func.....main）
- 系统初始化代码，由编译器为我们添加的

![image-20240505163318463](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240505163318463.png)

数据段的特点：

- 没有初始化则自动初始化为0
- 初始化语句只会被执行一次（在程序被加载的过程中已经初始化结束）
- 静态数据的内存从程序运行之初就存在，直到程序退出才会被释放（与进程共生死）

**堆内存：**

堆内存，又称为动态内存、自由内存、简称堆。唯一一个由开发者随意分配与释放的内存空间。具体的申请大小，使用的时常都是由我们自己来决定。

堆内存空间的基本特性：

- 相对与栈空间来说 ，堆空间大很多（堆的大小受限于物理内存），系统不会对对空间进行限制。
- 相对与栈空间来说， 堆内存是从下往上增长的。
- 堆空间的内存称为匿名内存， 不像栈空间那样有个名字，只能通过指针来访问
- 堆空间内存的申请与释放都是由用户自己完成，用户申请之后需要手动去释放，直到程序退出。如何申请堆空间内存：



- 使用malloc 申请内存时 ， 内存中的值时随机值 ， 可以使用bzero清空
- calloc 申请内存时 ， 内存中的值会被初始化为 0
- free 只能释放堆空间的内存，不能释放其它区域的内存

```
指针的万能拆解方法：
对于任何的指针都可以分为两部分：
第一部分： 说明他是一个指针 （*p）
第二部分： 说用它所指向的内容的类型 （*p）以外的东西
1 char * p1 ; // 第一部分： * p1 ， 第二部分 char 说明p1 指向的类型为char
2 char **p2 ; // 第一部分： * p2 ，第二部分 char * 说明p2 指向的类型为char *
3 int **p3 ; // 第一部分： * p3 ，第二部分 int * 说明p3 指向的类型为int *
4 char (*p4) [3] ; // 第一部分： * p4 , 第二部分 char [3] , 说明p4 指向一个拥
有3个元素的char 数组
5 char （*p5） (int , float) ; // 第一部分： * p5, 第二部分char (int , float)
， 说明
6 // 说明该指针指向一个 拥有char类型返回， 并需要 一个int 和 float 参数的函数
7 void *(*p6) (void *); //第一部分： * p6, 第二部分 void * (void *)
8 // 说明p6 指向一个 拥有 void * 返回并需要一个void * 参数的函数 函数指针）
```



- int p; //p是整型变量 
- int* p; //p是整型指针 
- int** p; //p是指向「整型指针」的指针 
- int p[3]; //p是数组，数组成员是「int」 
- int* p[3]; //p是数组，数组成员是「整型指针」 
- int (* p)[3]; //p先被 * 修饰，是一个指针，指向一个数组，数组成员是「int」 
- int p(int); //p是一个函数；「所指函数」有一个参数，「参数类型」是「int」，「返回值类型」是「int」 
- int (* p)(int); //p是一个指针，指向一个函数；函数的「参数类型」是「int」，「返回值类型」是「int」 
- int* (* p(int))[3]; //p是一个函数，该函数有一个「int」参数，返回一个指针，指向一个数组，数组里放的是「整型指针」

## 9、函数

**本质上说，C 里面所有的函数参数传递，都是值传递**指针传递之所以能改变传递参数变量的值，是因为 swap 函数交换的不是传递进来的指针本身，而是指针指向的值。

```
void swap(int *x, int *y);
void swap(int *x, int *y){
    int temp;
    //这里操作的是指针指向的值 而不是指针
    temp = *x;
    *x = *y;
    *y = temp;
//   倘若直接交换指针 a、b的值不会交换
//    temp = x;
//    x = y;
//    y = temp;
}

int main( int argc, char *argv[] )
{
    int a = 5;
    int b = 10;
    swap(&a, &b); //调用交换函数
    printf("交换结果为 a = %d, b = %d\n",a,b);
    return 0;
}
```

**静态函数**

背景： 普通的函数都是跨文件可见的，也就是比如a.c里面有个函数swap(), 在b.c中也

可以调用该函数。

静态函数：只能够在当前定义的文件中使用，称为静态函数

语法：

```
 static int func (int , int b )

 {
 	// 函数体
 }
```

**函数指针：**

指向一个函数的指针，称为函数指针；

```
int (*p) (int , float ) ;
```

```
int Printf( int a , float f )
 {


printf("a:%d , f:%f\n" , a , f );

 return 0 ;
 }

 int main(int argc, char const *argv[])
 {
 // 定义一个函数指针， 名字为 p 。它指向的函数 有一个整型返回值
 // 需要 一个整型参数 以及 浮点参数
 int (*p) (int , float ) ;
 p = Printf ; // 函数名其实也是这个函数的入口地址

 Printf(10 , 3.14);
 p(56 ,9.8888);

 return 0;
 }
```

**指针函数：**

一个返回指针的函数，就称为指针函数。

```
int * func(int a , int b)

 {
 int kk ;
 /*

 ..

 ...

 */
 return &kk ;
 }
```

**回调函数：**

概念：函数实现方不方便调用该函数，而由接口提供方来调用该函数，就称为回调函数。

```
#include <stdio.h>
#include <signal.h>

 void func(int num )
 {
 	printf("当前收到 3 号信号 ， 军师让我蹲下 !!\n");
 }
 int main(int argc, char const *argv[])
 {
     void (*p)(int); // 定义一个函数指针
     p = func ; // 让指针p 指向函数 func
     // 设置进程捕获信号 ，如果信号值 为 3的时候 ， 会自行调用 p 所指向的函
    数 （代码/指令）
     signal( 3 , p );
     while(1);
     return 0;
 }
```

总结：

- signal 函数是一个用于捕获信号的函数， 当他捕获到指定信号的时候则会执行用户所提供的函数。
- 由于signal函数是内核提供的函数，修改内核的代码不现实， 因此它提供的接口是一个函数指针，只要某个条件满足则会自动执行我们所给的函数。
- 使得不同软件模块的开发者的工作进度可以独立出来，不受时空的限制，需要的时候通过约定好的接口（或者标准）相互契合在一起

## 10、字符串处理

### strstr 查找指定的字符串 

| ![image-20240506102214461](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102214461.png) |
| ------------------------------------------------------------ |

### strlen 返回字符串长度

| ![image-20240506102255720](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102255720.png) |
| ------------------------------------------------------------ |

### strtok 分割字符串

| ![image-20240506102406010](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102406010.png) |
| ------------------------------------------------------------ |

### strchr 查找字符串中第一个出现的指定字符

| ![image-20240506102513723](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102513723.png) |
| ------------------------------------------------------------ |

### strncpy 拷贝字符串

| ![image-20240506102637161](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102637161.png) |
| ------------------------------------------------------------ |

strcmp 比较字符串

| ![image-20240506102733560](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102733560.png) |
| ------------------------------------------------------------ |

### strcat ( 连接两字符串)

| ![image-20240506102801688](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506102801688.png) |
| ------------------------------------------------------------ |

### sprintf ( 格式化字符串复制 )

| ![image-20240506103023559](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506103023559.png) |
| ------------------------------------------------------------ |

### bzero ( 将一段内存内容全清为零 )

| ![image-20240506103053415](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506103053415.png) |
| ------------------------------------------------------------ |

### memset ( 将一段内存空间填入某值 )

| ![image-20240506103246182](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506103246182.png) |
| ------------------------------------------------------------ |

### memcpy ( 拷贝内存内容)

| ![image-20240506103533063](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506103533063.png) |
| ------------------------------------------------------------ |

- 与strcpy 不同的地方在于，strcpy在遇到结束符时停止拷贝， 而memcpy 则是会完整拷贝内存中的前N字节，不会因为遇到结束符而停止。
- memccpy 在复制的过程中会顺便检查是否出现了 字符 c ， 如果出现， 则停止拷贝（拷贝C之后），后面的内容就没有继续拷贝。《拷贝到字符C为止， 最多拷贝n个字节》

### memcmp ( 比较内存内容 )

| ![image-20240506103648103](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506103648103.png) |
| ------------------------------------------------------------ |

## 11、作用域

### **static 关键字：**

在C语言中非常重要的一个角色， 它在不同的场合表现的意义不一样。

1. 把可见范围进行缩小为本文将可见：

- 修饰全局变量
- 修饰普通的函数

2. 把变量的存储区修改为静态数据（数据段）：

- 修饰局部变量, 使得局部变量的存储区从栈改为数据段

## 12、存储期

C语言中， 每一个变量都有一个生命周期，所谓的生命周期指的是某一个变量的内存从申请到释放的过程。申请一个内存相当于某一个变量的诞生，释放掉该内存则是相当于消亡。



变量的生命周期有一下三种形式：

- 自动存储期
- 静态存储期
- 自定义存储期

| ![image-20240506104600262](C:\Users\xiaolizhi\AppData\Roaming\Typora\typora-user-images\image-20240506104600262.png) |
| :----------------------------------------------------------: |

- .bss段 存放的是未初始化的静态变量（静态存储期） 初始值为 0
- .data 段 已经初始化的静态变量 ， 初始化语句只会被执行一次
- 静态数据从进程运行之初已经存在， 直到进程退出为止

**自定义存储期：**

- malloc 只负责申请空间， 并不会清空内存，因此一般使用bzero 清空
- calloc 负责申请内存，并会默认清空为 0 .
- free 只负责释放，也不会清空 更不会让指针指向空， 因此free之后最好让指针指向NULL

## 13、结构体

**成员引用：**

结构体相当于一个数据的集合， 里面由多个不同类型的数据组合而成，每一个成员都可以通过成员引用符来单独引用

```
结构体变量名.成员 // 普通的结构体变量
结构体指针变量‐>成员 // 结构体指针的访问方法
```

使用 typedef 来给结构体的类型取别名

```
 typedef long unsigned int size_t ;
```

Tz 相当于 struct TieZhu 可以直接定义普通结构体变量

P_Tz 相当于 struct TieZhu * 可以直接定义结构体指针变量

```
 typedef struct TieZhu
 {
     int num ;
     char * Book ;
     float Price ;
     char Name [32] ;
 }Tz , * P_Tz ;
```

**位(bit)**：计算机中的最小数据单位，计算机存储的都是二进制0和1这两个鬼。

**字节(Byte)**：字节是存储空间的基本计量单位，也是内存的基本单位，也是编址单位。例如，一个计算机的内存是4GB，就是该计算机的内存中共有4×1024×1024×1024个字节，意味着它有4G的内存寻址空间。

换算关系：

1 GB = 1024 MB

1 MB = 1024 KB

1 KB = 1024 Bytes

1 Byte = 8 bits

## 14、C语言示例

1、判断字母

```
#include <stdio.h>
 
int main()
{
    char c;
    printf("输入一个字符: ");
    scanf("%c",&c);
 
    if( (c>='a' && c<='z') || (c>='A' && c<='Z'))
        printf("%c 是字母",c);
    else
        printf("%c 不是字母",c);
 
    return 0;
}
```

**C 库函数 int toupper(int c) 把小写字母转换为大写字母。**

**C 库函数 int tolower(int c) 把大写字母转换为小写字母。**

**C库函数isalpha()可判断某个字符是否为字母**

[125. 验证回文串](https://leetcode.cn/problems/valid-palindrome/)

如果在将所有大写字符转换为小写字符、并移除所有非字母数字字符之后，短语正着读和反着读都一样。则可以认为该短语是一个 **回文串** 。

字母和数字都属于字母数字字符。

给你一个字符串 `s`，如果它是 **回文串** ，返回 `true` ；否则，返回 `false` 。

```
class Solution {
public:
    bool isPalindrome(string s) {
        string cleanned;
        for(int i=0;i<s.length();i++){
            if(isalnum(s[i])){
                cleanned+=tolower(s[i]);
            }
        }
     string b=cleanned;
        reverse(cleanned.begin(),cleanned.end());
        if(cleanned==b){
            return true;
        }else{
            return false;
        }
    }
        
};
```

## 15、C标准库

### 1、C 标准库 - <ctype.h>

C 标准库的 **ctype.h** 头文件提供了一些函数，可用于测试和映射字符。

| 序号 | 函数 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | [int isalnum(int c)](https://www.runoob.com/cprogramming/c-function-isalnum.html) 该函数检查所传的字符是否是字母和数字。 |
| 2    | [int isalpha(int c)](https://www.runoob.com/cprogramming/c-function-isalpha.html) 该函数检查所传的字符是否是字母。 |
| 3    | [int iscntrl(int c)](https://www.runoob.com/cprogramming/c-function-iscntrl.html) 该函数检查所传的字符是否是控制字符。 |
| 4    | [int isdigit(int c)](https://www.runoob.com/cprogramming/c-function-isdigit.html) 该函数检查所传的字符是否是十进制数字。 |
| 5    | [int isgraph(int c)](https://www.runoob.com/cprogramming/c-function-isgraph.html) 该函数检查所传的字符是否有图形表示法。 |
| 6    | [int islower(int c)](https://www.runoob.com/cprogramming/c-function-islower.html) 该函数检查所传的字符是否是小写字母。 |
| 7    | [int isprint(int c)](https://www.runoob.com/cprogramming/c-function-isprint.html) 该函数检查所传的字符是否是可打印的。 |
| 8    | [int ispunct(int c)](https://www.runoob.com/cprogramming/c-function-ispunct.html) 该函数检查所传的字符是否是标点符号字符。 |
| 9    | [int isspace(int c)](https://www.runoob.com/cprogramming/c-function-isspace.html) 该函数检查所传的字符是否是空白字符。 |
| 10   | [int isupper(int c)](https://www.runoob.com/cprogramming/c-function-isupper.html) 该函数检查所传的字符是否是大写字母。 |
| 11   | [int isxdigit(int c)](https://www.runoob.com/cprogramming/c-function-isxdigit.html) 该函数检查所传的字符是否是十六进制数字。 |

标准库还包含了两个转换函数，它们接受并返回一个 "int"

| 序号 | 函数 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | [int tolower(int c)](https://www.runoob.com/cprogramming/c-function-tolower.html) 该函数把大写字母转换为小写字母。 |
| 2    | [int toupper(int c)](https://www.runoob.com/cprogramming/c-function-toupper.html) 该函数把小写字母转换为大写字母。 |

### 2、C 标准库 - <stdarg.h>

**stdarg.h** 头文件定义了一个变量类型 **va_list** 和三个宏

| 序号 | 变量 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **va_list** 这是一个适用于 **va_start()、va_arg()** 和 **va_end()** 这三个宏存储信息的类型。 |

```
#include <cstdarg>
#include <iostream>

void print_va_args(const char* format, ...) {
    va_list args;
    va_start(args, format);

    while (*format) {
        if (*format == '%') {
            switch (*(++format)) {
                case 'd': {
                    int arg = va_arg(args, int);
                    std::cout << arg << ' ';
                    break;
                }
                case 'f': {
                    double arg = va_arg(args, double);
                    std::cout << arg << ' ';
                    break;
                }
                case 's': {
                    const char* arg = va_arg(args, const char*);
                    std::cout << arg << ' ';
                    break;
                }
                default:
                    std::cout << "Unexpected format character: " << *format << std::endl;
                    va_end(args);
                    return;
            }
        } else {
            std::cout << *format;
        }
        ++format;
    }

    va_end(args);
}

int main() {
    print_va_args("%d %f %s", 42, 3.14, "hello");
    return 0;
}

```

### 3、C 标准库 - <stdlib.h>

# 16、const和volatile

### 一、const修饰普通类型的变量

```
const int  a = 7; 
int  b = a; // 正确
a = 8;       // 错误，不能改变
```

```
#include<iostream>
 
using namespace std;
 
int main(void)
{
    const int  a = 7;
    int  *p = (int*)&a;
    *p = 8;
    cout<<a;
    system("pause");
    return 0;
}
```

输出仍然是7，在const前加上volatile,输出为8

从结果中我们可以看到，编译器然后认为 a 的值为一开始定义的 7，所以对 const a 的操作就会产生上面的情况。所以千万不要轻易对 const 变量设法赋值，这会产生意想不到的行为。

如果不想让编译器察觉到上面到对 const 的操作，我们可以在 const 前面加上 volatile 关键字。

Volatile 关键字跟 const 对应相反，是易变的，容易改变的意思。所以不会被编译器优化，编译器也就不会改变对 a 变量的操作。

### 二、const 修饰指针变量。

const 修饰指针变量有以下三种情况。

- A: const 修饰指针指向的内容，则内容为不可变量。

```
const int *p = 8;
```

则指针指向的内容 8 不可改变。简称左定值，因为 const 位于 * 号的左边。

- B: const 修饰指针，则指针为不可变量。

```
int a = 8;
int* const p = &a;
*p = 9; // 正确
int  b = 7;
p = &b; // 错误
```

对于 const 指针 p 其指向的内存地址不能够被改变，但其内容可以改变。简称，右定向。因为 const 位于 * 号的右边。

- C: const 修饰指针和指针指向的内容，则指针和指针指向的内容都为不可变量

```
int a = 8;
const int * const  p = &a;
```

这时，const p 的指向的内容和指向的内存地址都已固定，不可改变。

对于 A，B，C 三种情况，根据 const 位于 * 号的位置不同，我总结三句话便于记忆的话：**"左定值，右定向，const修饰不变量"**。

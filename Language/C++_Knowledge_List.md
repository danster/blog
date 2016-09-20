## C++ 学习笔记

#### C++ Knowledge Link
* C++ 入门教程 http://www.runoob.com/cplusplus/cpp-tutorial.html
* C++ 基础教程 http://c.biancheng.net/cpp/biancheng/cpp/rumen/
* C++ 多态的实现原理 http://blog.csdn.net/tujiaw/article/details/6753498

#### Hello World
```
#include <iostream>
using namespace std;
extern int a; // 变量声明
int func(); // 函数声明

int main(){
   int a=1; // 变量定义&初始化
   cout << "Hello World" << endl;
   return 0;
}

int func(){ return 0;} // 函数定义
```
### C C++ Compare
- C/C++ 都是静态强类型语言；C 面向过程，C++面向对象
- C/C++ 关键字比较

```
ANSI C 32 words
auto   break  case	  char   const	   continue	default  do
double else	  enum     extern	float    for	   goto	   if
int    long	  register return	short	   signed	sizeof	static
struct switch typedef  union	unsigned void     volatile while

ISO C++98 63 words
asm        do           if                return      typedef
auto       double       inline            short       typeid
bool       dynamic_cast int               signed      typename
break      else         long              sizeof      union
case       enum         mutable           static      unsigned
catch      explicit     namespace         static_cast using
char       export       new               struct      virtual
class      extern       operator          switch      void
const      false        private           template    volatile
const_cast float        protected         this        wchar_t
continue   for          public            throw       while
default    friend       register          true 
delete     goto         reinterpret_cast  try
```

### 数据类型
- 基本类型：bool, char, int, float, double, void, wchar_t  
- 一些基本类型可以使用一个或多个类型修饰符进行修饰：unsigned, signed, short, long
- typedef type newname;
- enum enum-name { list of names } var-list;

```
enum int { a, b=-1, c, d=20 } x; x=c; // a=0,c=0
enum color { red, green, blue } c; c = blue;
```  
- 左值（lvalue）：指向内存位置的表达式被称为左值（lvalue）表达式。左值可以出现在赋值号的左边或右边
- 右值（rvalue）：指的是存储在内存中某些地址的数值。右值是不能对其进行赋值的表达式，也就是说，右值可以出现在赋值号的右边，但不能出现在赋值号的左边。

**C++ 中的类型限定符** 类型限定符提供了变量的额外信息。 

- const	   const 类型的对象在程序执行期间不能被修改改变。
- volatile	volatile 告诉编译器，变量的值可能以程序未明确指定的方式被改变。  

**C++ 存储类**  存储类定义 C++ 程序中变量/函数的范围（可见性）和生命周期  

- auto auto 存储类是所有局部变量默认的存储类, auto 只能用在函数内 & 只能修饰局部变量
- register 用于定义存储在寄存器中而不是 RAM 中的局部变量
- static  

```
static 存储类指示编译器在程序的生命周期内保持局部变量的存在，而不需要在每次它进入和离开作用域时进行创建和销毁。
因此，使用 static 修饰局部变量可以在函数调用之间保持局部变量的值。
当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。
当 static 用在类数据成员上时，会导致仅有一个该成员的副本被类的所有对象共享。
```
- extern  

```
extern 存储类用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。
当您使用 'extern' 时，对于无法初始化的变量，会把变量名指向一个之前定义过的存储位置。
当您有多个文件且定义了一个可以在其他文件中使用的全局变量或函数时，
可以在其他文件中使用 extern 来得到已定义的变量或函数的引用。

可以这么理解，extern 是用来在另一个文件中声明一个全局变量或函数。
```
- mutable 仅适用于类的对象,它允许对象的成员替代常量。也就是说，mutable 成员可以通过 const 成员函数修改。  
**struct**  

```
struct [structure tag]
{
   member definition;
   member definition;
   ...
   member definition;
} [one or more structure variables];  

struct Books{
   char  title[50];
   int   book_id;
};
Books Book1;

or

typedef struct{
   char  title[50];
   int   book_id;
}Books;
Books Book1, Book2;
```

#### C++ 指针与引用

> int a = 17; int * p = &i;  
> int b = 20; int & new_b = b;

引用很容易与指针混淆，它们之间有三个主要的不同：

- 不存在空引用。引用必须连接到一块合法的内存。
- 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
- 引用必须在创建时被初始化。指针可以在任何时间被初始化。

### C++ 类 & 对象
| 概念|描述|
|---|---|
|[类成员函数](http://www.runoob.com/cplusplus/cpp-class-member-functions.html)|	类的成员函数是指那些把定义和原型写在类定义内部的函数，就像类定义中的其他变量一样。
|[类访问修饰符](http://www.runoob.com/cplusplus/cpp-class-access-modifiers.html)|	类成员可以被定义为 public、private 或 protected。默认情况下是定义为 private。
|[构造&析构函数](http://www.runoob.com/cplusplus/cpp-constructor-destructor.html)|类的构造函数是一种特殊的函数，在创建一个新的对象时调用。类的析构函数也是一种特殊的函数，在删除所创建的对象时调用
|[拷贝构造函数](http://www.runoob.com/cplusplus/cpp-copy-constructor.html)|	拷贝构造函数，是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。
|[友元函数](http://www.runoob.com/cplusplus/cpp-friend-functions.html)|	友元函数可以访问类的 private 和 protected 成员。
|[内联函数](http://www.runoob.com/cplusplus/cpp-inline-functions.html)|	通过内联函数，编译器试图在调用函数的地方扩展函数体中的代码。
|[this 指针](http://www.runoob.com/cplusplus/cpp-this-pointer.html)|	每个对象都有一个特殊的指针 this，它指向对象本身。
|[指向类的指针](http://www.runoob.com/cplusplus/cpp-pointer-to-class.html)|	指向类的指针方式如同指向结构的指针。实际上，类可以看成是一个带有函数的结构。
|[类的静态成员](http://www.runoob.com/cplusplus/cpp-static-members.html)|	类的数据成员和函数成员都可以被声明为静态的。

**C++ 继承** http://www.runoob.com/cplusplus/cpp-inheritance.html  

- 一个派生类继承了所有的基类方法，除了：基类的构造函数、析构函数和拷贝构造函数。 基类的重载运算符。基类的友元函数。
- 运算符重载： 成员函数 Box operator+(const Box& b); 非成员函数 Box operator+(const Box&, const Box&);
- [可重载运算符/不可重载运算符列表与实例](http://www.runoob.com/cplusplus/cpp-overloading.html)
- C++ 数据抽象(add get update), C++ 数据封装(类), C++ 数据隐藏(private,protected), C++ 接口(继承与重写)

**异常处理** http://www.runoob.com/cplusplus/cpp-exceptions-handling.html    
```c++
try{// 保护代码
   ...
   throw "Division by zero condition!";
}catch( ExceptionName1 e ){
  // 处理 ExceptionName1 异常的代码
}catch( ExceptionName2 e ){
  // 处理 ExceptionName2 异常的代码
}catch( ... ){
  // 能处理任何异常的代码
}
```
**C++ 动态内存** http://www.runoob.com/cplusplus/cpp-dynamic-memory.html  
- new 与 malloc() 函数相比，其主要的优点是，new 不只是分配了内存，它还创建了对象。
- delete 与 free() 函数相比，还调用了析构函数
- new [] 与 delete [] 成对出现

**[C++ 模板](http://www.runoob.com/cplusplus/cpp-templates.html)** 是泛型编程的基础，泛型编程即以一种独立于任何特定类型的方式编写代码  
```c++
函数模板
template <class type> return-type func-name(parameter list){ ... }
类模板
template <class type> class class-name { ... }

template <typename T>
inline T const& Max (T const& a, T const& b) { 
    return a < b ? b:a; 
} 

template <class T>
class Stack { 
  private: vector<T> elems;     // 元素 
  public: void push(T const&);  // 入栈
}; 

template <class T>
void Stack<T>::push (T const& elem) { 
   elems.push_back(elem);    
} 

/////////////////////////////////////////////////////////////

int main() 
{ 
   int i = 39;
   int j = 20;
   cout << "Max(i, j): " << Max(i, j) << endl; 
    
   Stack<int>         intStack;  // int 类型的栈 
   Stack<string> stringStack;    // string 类型的栈 
   
   intStack.push(7); 
   stringStack.push("hello"); 
}  
```
**[C++ 信号处理](http://www.runoob.com/cplusplus/cpp-signal-handling.html)** 

信号是由操作系统传给进程的中断，会提早终止一个程序。在 UNIX、LINUX、Mac OS X 或 Windows 系统上，可以通过按 Ctrl+C 产生中断。  
有些信号不能被程序捕获，但是下表所列信号可以在程序中捕获，并可以基于信号采取适当的动作。这些信号是定义在 C++ 头文件 <csignal> 中   
```
信号	   描述
SIGABRT	程序的异常终止，如调用 abort。
SIGFPE	错误的算术运算，比如除以零或导致溢出的操作。
SIGILL	检测非法指令。
SIGINT	接收到交互注意信号。
SIGSEGV	非法访问内存。
SIGTERM	发送到程序的终止请求。
```

```c++
C++ 信号处理库提供了 signal 函数, signal() 函数的语法: 
void (*signal (int sig, void (*func)(int)))(int); 
第一个参数是一个整数，代表了信号的编号；第二个参数是一个指向信号处理函数的指针

C++ 用函数 raise() 生成信号，该函数带有一个整数信号编号作为参数，语法：
int raise (signal sig);
在这里，sig 是要发送的信号的编号，这些信号包括：SIGINT、SIGABRT、SIGFPE、SIGILL、SIGSEGV、SIGTERM、SIGHUP。

#include <iostream>
#include <csignal>
using namespace std;

void signalHandler( int signum ){
   cout << "Interrupt signal (" << signum << ") received.\n";
   ...            // 清理并关闭
   exit(signum);  // 终止程序 
}

int main (){
   signal(SIGINT, signalHandler);   // 注册信号 SIGINT 和信号处理程序
   
   int i = 0;
   while(++i){
      cout << "Going to sleep...." << endl; 
      if( i == 3 ){
          raise( SIGINT);
       }
      sleep(1);
   }
   return 0;
}
```

**[C++ 多线程](http://www.runoob.com/cplusplus/cpp-multithreading.html)**    
两种类型的多任务处理：基于进程和基于线程:  
- 基于进程的多任务处理是程序的并发执行
- 基于线程的多任务处理是同一程序的片段的并发执行

多线程程序包含可以同时运行的两个或多个部分。这样的程序中的每个部分称为一个线程，每个线程定义了一个单独的执行路径。  
C++ 不包含多线程应用程序的任何内置支持。相反，它完全依赖于操作系统来提供此功能。

```c++
#include <pthread.h> // 操作Linux 操作系统的 POSIX 线程

创建线程: pthread_create (thread, attr, start_routine, arg) 
终止线程：pthread_exit (status) 
连接线程：thread_join (threadid, status) 
分离线程：pthread_detach (threadid) 
```

```c++
#include <pthread.h>
using namespace std;
#define NUM_THREADS   5

struct thread_data{ int thread_id; char *message;};

void *PrintHello(void *threadarg){
   struct thread_data *data;
   data = (struct thread_data *) threadarg;
   cout << "ID : " << data->thread_id << " Message : " << data->message << endl;
   pthread_exit(NULL);
}

int main (){
   pthread_t threads[NUM_THREADS];
   struct thread_data td[NUM_THREADS];
   int rc;

   for(int i=0; i < NUM_THREADS; i++ ){
      // creating thread  i
      td[i].thread_id = i;
      td[i].message = "This is message";
      
      rc = pthread_create(&threads[i], NULL, PrintHello, (void *)&td[i]); 
      if (rc){ // 创建线程成功时,函数返回0; 若返回值不为0,则说明创建线程失败
         cout << "Error:unable to create thread," << rc << endl;
         exit(-1);
      }
   }
   pthread_exit(NULL);
}
```

**[C++ Web 编程 与 CGI](http://www.runoob.com/cplusplus/cpp-web-programming.html)**  
NCSA定义：公共网关接口（CGI），是一种用于外部网关程序与信息服务器（如 HTTP 服务器）对接的接口标准。 
CGI 是使得应用程序（称为 CGI 程序或 CGI 脚本）能够与 Web 服务器以及客户端进行交互的标准协议。  
这些 CGI 程序可以用 Python、PERL、Shell、C 或 C++ 等进行编写。

Web 服务器配置  
```
在您进行 CGI 编程之前，请确保您的 Web 服务器支持 CGI，并已配置成可以处理 CGI 程序。所有由 HTTP 服务器执行的 CGI 程序，都必须在预配置的目录中。该目录称为 CGI 目录，按照惯例命名为 /var/www/cgi-bin。
虽然 CGI 文件是 C++ 可执行文件，但是按照惯例它的扩展名是 .cgi。
默认情况下，Apache Web 服务器会配置在 /var/www/cgi-bin 中运行 CGI 程序。如果您想指定其他目录来运行 CGI 脚本，您可以修改httpd.conf
```

```c++
#include <iostream>
using namespace std;
 
int main (){
   cout << "Content-type:text/html\r\n\r\n";
   cout << "<html>\n<head>\n";
   cout << "<title>Title</title>\n";
   cout << "</head>\n<body>\n";
   cout << "<h2>Hello World!</h2>\n";
   cout << "</body>\n</html>\n";
   return 0;
}
```
编译上面的代码，把可执行文件命名为 hello.cgi，并把这个文件保存在 /var/www/cgi-bin 目录中。  
在运行 CGI 程序之前，请使用 chmod 755 hello.cgi , 确保文件可执行。 
访问可执行文件，您会看到输出：Hello World!

GET POST Cookies 文件上传 

### C++头文件一览 
```c
C、传统 C++

#include <assert.h>　　　　设定插入点
#include <ctype.h>　　　　字符处理
#include <errno.h>　　　　 定义错误码
#include <float.h>　　　　浮点数处理
#include <fstream.h>　　　文件输入／输出
#include <iomanip.h>　　　 参数化输入／输出
#include <iostream.h>　　　数据流输入／输出
#include <limits.h>　　　　定义各种数据类型最值常量
#include <locale.h>　　　　定义本地化函数
#include <math.h>　　　　　定义数学函数
#include <stdio.h>　　　　定义输入／输出函数
#include <stdlib.h>　　　　定义杂项函数及内存分配函数
#include <string.h>　　　　字符串处理
#include <strstrea.h>　　　基于数组的输入／输出
#include <time.h>　　　　　定义关于时间的函数
#include <wchar.h>　　　　 宽字符处理及输入／输出
#include <wctype.h>　　　　宽字符分类

标准 C++　

#include <algorithm>　　　  通用算法
#include <bitset>　　　　　 位集容器
#include <cctype>
#include <cerrno>
#include <clocale>
#include <cmath>
#include <complex>　　　　 复数类
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <ctime>
#include <deque>　　　　　 双端队列容器
#include <exception>　　　 异常处理类
#include <fstream>
#include <functional>　　　定义运算函数（代替运算符）
#include <limits>
#include <list>　　　　　　线性列表容器
#include <map>　　　　　　 映射容器
#include <iomanip>
#include <ios>　　　　　　基本输入／输出支持
#include <iosfwd>　　　　 输入／输出系统使用的前置声明
#include <iostream>
#include <istream>　　　　 基本输入流
#include <ostream>　　　　 基本输出流
#include <queue>　　　　　 队列容器
#include <set>　　　　　　 集合容器
#include <sstream>　　　　 基于字符串的流
#include <stack>　　　　　 堆栈容器　　　　
#include <stdexcept>　　　 标准异常类
#include <streambuf>　　　 底层输入／输出支持
#include <string>　　　　　字符串类
#include <utility>　　　　 通用模板类
#include <vector>　　　　  动态数组容器
#include <cwchar>
#include <cwctype>

C99 增加

#include <complex.h>　　 复数处理
#include <fenv.h>　　　　浮点环境
#include <inttypes.h>　　整数格式转换
#include <stdbool.h>　　 布尔环境
#include <stdint.h>　　　整型环境
#include <tgmath.h>　　  通用类型数学宏
```

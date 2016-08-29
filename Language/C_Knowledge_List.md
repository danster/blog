## C 学习笔记

### C Hello World
```
#include <stdio.h>
int main(void){
	printf("Hello ,world.\n");
   return 0;
}
```
### C 知识点链接
* C 语言运算符的优先级和结合性一览表 http://c.biancheng.net/cpp/html/462.html  
* Linux 进程内存分布 http://www.cnblogs.com/fuzhe1989/p/3936894.html  
* C 支持函数重载，但没有对象和类
* C语言深度剖析【进阶篇】http://c.biancheng.net/cpp/u/cjinjie/

### C 数据类型
```
C数据类型
|——基本类型
|  |——数值类型
|  |  |——整型：[unsinged or signed] short, int, long
|  |  |——浮点型：float, double, long double
|  |
|  |————字符类型：char
|
|——构造类型：数组, struct, union, enum
|——指针类型
|——空类型：void
```

###C 逻辑控制
```
if( 表达式 ) { ... }
else if ( 表达式 ) { ... }
else if ( 表达式 ) { ... }
else { ... }

三目运算符 表达式1 ? 表达式2 : 表达式3

switch(表达式){
	case 常量表达式 1: ...
	...
	case 常量表达式 N: ...
	default : ...
}

loop: if(i < 100){
  sum = sum + i;
  i++;
  goto loop;
}

while(表达式){ ... }
do{ ... }while(表达式);

for(表达式1; 表达式2; 表达式3){ ... }
for(; i<100; i++) sum += i;

break and continue
```

###C 数组，指针
```
int a[10]; // 没有初始化
int b[10] = {1,2,3,4}; // b[4]~b[9] are 0
int c[] = {1,2,3,4}; //length is 4

int a[3][3] = {{1,2,3},{11,12,13},{}};
int b[3][3] = {1,2,3,11,12,13};
int c[][3] = {1,2,3,11,12,13};

char c[10] = {"I"}; // c[1] is '\0'
```

```
指针即地址
变量的指针 = 变量的地址
存放变量地址的变量 = **指针变量**

* 指针运算符 or 间接访问运算符，取其指向的内容
& 取址运算符

int i=10; int * i_pointer = &i; 
*i_pointer = 20;
printf("%d", *i_pointer); // 20

int a[10]; int * p;
p = &a[0]; or p = a;

p++ or p+=1 OK .
a++ or a+=1 Error ! 
*(p+i) or *(a+i) = a[i]

char * str="I love China";
printf("%s", str);

指向函数指针定义：数据类型 (* 指针变量名) (函数参数列表);
main(){
  int max(int, int); //声明，函数名max代表该函数的入口地址
  int (* p)(int, int); //定义一个指向函数的指针变量
  p=max; // 将函数max的入口地址赋给p
  result = (*p)(1,2); //result = 3
}
```

###C 关键字
```
auto 声明自动变量，缺省时编译器一般默认为auto
int 声明整型变量
double 声明双精度变量
long 声明长整型变量
char 声明字符型变量
float 声明浮点型变量
short 声明短整型变量
signed 声明有符号类型变量
unsigned 声明无符号类型变量
struct 声明结构体变量
union 声明联合数据类型
enum 声明枚举类型
static 声明静态变量
switch 用于开关语句
case 开关语句分支
default 开关语句中的“其他”分支
break 跳出当前循环
register 声明寄存器变量
const 声明只读变量
volatile 说明变量在程序执行中可被隐含地改变
typedef 用以给数据类型取别名(当然还有其他作用)
extern 声明变量是在其他文件正声明(也可以看做是引用变量)
return 子程序返回语句(可以带参数，也可不带参数)
void 声明函数无返回值或无参数，声明空类型指针
continue 结束当前循环，开始下一轮循环
do 循环语句的循环体
while 循环语句的循环条件
if 条件语句
else 条件语句否定分支(与if 连用)
for 一种循环语句(可意会不可言传)
goto 无条件跳转语句
sizeof 计算对象所占内存空间大小
```

### C 预处理关键字
```
#define 宏定义
#undef 撤销宏定义
#include

#if 常量表达式为true
#else
#elif
#endif 结束if块

#ifdef 如果有定义
#ifndef 如果没有定义
#endif 结束if块

#line number["filename"] 编译时，改变当前行数和文件名
#error 编译时，遇到#error就会产生一个编译错误提示信息，并停止编译
#pragma
```

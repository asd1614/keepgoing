### C语言数据类型

#### 

Origlanl | C90 | C99 
----|----|-----
int| signed | _Bool
long| void|
short | | _Imaginary
unsigned|
char|
float|
double|

#### 运算符优先级
Operators| Associativity
----|----
() [] -> . |left to right
! ~ ++ -- + - * (type) sizeof |right to left
* / % |left to right
+ - |left to right
<< >> |left to right
< <= > >= |left to right
== != |left to right
& |left to right
^ |left to right
\| |left to right
&& |left to right
\|\| |left to right
?: |right to left
= += -= *= /= %= &= ^= |= <<= >>= |right to left
, |left to right


#### #define 

```c
// 先从当前目录查找头文件
#include "filename"
// searching follows an implementation-defined rule to find the file
#include <filename>
```

```c

#define max(A, B) ((A) > (B) ? (A) : (B))
// replacement result
// before
x = max(p+q, r+s);
// after
x = ((p+q) > (r+s) ? (p+q) : (r+s));


#define dprint(expr) printf(#expr " = %g\n", expr)
// replacement result
// before
dprint(x/y)
// after
printf("x/y = &g\n", x/y);

// ## 是将front跟back拼接在一起 有变量的味道
#define paste(front, back) front ## back
// replacement result
// before
paste(name, 1)
// after
name1
// 还有另外一种用法
#define type i##nt
//replacement result
// before
type a;
//after
int a;


#if !defined(HDR)
#deine HDR
/* contents of hdr.h go here */
#endif
// 另一种写法
#ifndef HDR
#define HDR
/* contents of hdr.h go here */
#endif


#if SYSTEM == SYSV
    #define HDR "sysv.h"
#elif SYSTEM == BSD
    #define HDR "bsd.h"
#elif SYSTEM == MSDOS
    #define HDR "msdos.h"
#else
    #define HDR "default.h"
#endif
#include HDR
```

#### #undef

>ensure that a routine is really a function, not
a macro


#### 指针与数组

为了解决goto带来的影响，引入指针类型，使得程序更加小巧高效

```c
// & 取地址
p = &c;

// * 作用于指针类型， 取值
int x=1, y=2, z[10];
int *p;
ip = &x;
y = *ip;
*ip = 0;
ip = &z[0];
```

Multi-dimensional Arrays

```c
// 声明一个长度为2的指向长度为13的整数数组的数组
f(int daytab[2][13]) {}
f(int daytab[][13]) {}
f(int (*daytab)[13]) {}

//  [] 优先级高于 * 
int *daytab[13]  // 是声明一个长度为13的指向int的指针数组

```

初始化

```c
/* month_name: return name of n-th month */
char *month_name(int n)
{
    static char *name[] = {
        "Illegal month",
        "January", "February", "March",
        "April", "May", "June",
        "July", "August", "September",
        "October", "November", "December"
    };
    return (n < 1 || n > 12) ? name[0] : name[n];
}

```

Pointers vs. Multi-dimensional Arrays

```c
/*
  声明将分配了连续的200个内存 类型为整数int
  指向相同长度的数组
*/
int a[10][20];
/*
  声明将连接10个内存 类型为整数int指针，并未分配内存
  指向不同长度的数组
*/
int *b[10];

char **argv
// argv: pointer to char  指向char的指针的指针
// 参考说明 https://stackoverflow.com/questions/897366/how-do-pointer-to-pointers-work-in-c
int (*daytab)[13]
// daytab: pointer to array[13] of int
int *daytab[13]
// daytab: array[13] of pointer to int
void *comp()
// comp: function returning pointer to void
void (*comp)()
// comp: pointer to function returning void
char (*(*x())[])()
// x: function returning pointer to array[] of
// pointer to function returning char
char (*(*x[3])())[5]
// x: array[3] of pointer to function returning
// pointer to array[5] of char
```

#### struct 结构体

```c
struct point {
    int x;
    int y;
}

struct { ... } x, y, z;

struct point pt;
```


#### format output

Character| Argument type; Printed As
----|----
d,i |int; decimal number
o |int; unsigned octal number (without a leading zero)
x,X |int; unsigned hexadecimal number (without a leading 0x or 0X), using abcdef or ABCDEF for 10, ...,15.
u |int; unsigned decimal number
c |int; single character
s |char *; print characters from the string until a '\0' or the number of characters given by the precision.
f |double; [-]m.dddddd, where the number of d's is given by the precision (default 6).
e,E |double; [-]m.dddddde+/-xx or [-]m.ddddddE+/-xx, where the number of d's is given by the precision (default 6).
g,G |double; use %e or %E if the exponent is less than -4 or greater than or equal to the precision; otherwise use %f. Trailing zeros and a trailing decimal point are not printed.
p |void *; pointer (implementation-dependent representation).
% |no argument is converted; print a %


pattern | display
----|----
:%s: |:hello, world:
:%10s: |:hello, world:
:%.10s: |:hello, wor:
:%-10s: |:hello, world:
:%.15s: |:hello, world:
:%-15s: |:hello, world :
:%15.10s: |: hello, wor:
:%-15.10s: |:hello, wor :

#### Variable-length Argument Lists

```c
int printf(char *fmt, ...)

// va_list 指向 ... 变长变量列表
```
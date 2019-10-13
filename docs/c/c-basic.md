### 了解 C 语言

#### 第一个C语言程序

```C
/*
 告诉编译器，将stdio.h头文件包含进来 
 stdio.h是所有C编译器的标准之一，声明标准输入输出的函数
*/
#include <stdio.h> 
// 程序的入口函数名
int main(void) {
    int num; 
    num = 1;
    printf("I am a simple "); 
    printf("computer.\n");
    printf("My favorite number is %d because it is first.\n",num);
    return 0; 
}

```
输出结果
```
I am a simple computer.
My favorite number is 1 because it is first.

```

#### 变量声明

```C
// Multiple Declarations
int feet, fathoms;
// Single Declarations
int feet;
int fathoms;
```

上述两种声明方式的结果是相同的，都声明了两个*int*变量

#### 声明函数

```C
/* two_func.c -- a program using two functions in one file */ 
#include <stdio.h>
/* ANSI/ISO C function prototyping */ 
/* The C90 standard added prototypes */
void butler(void); 

int main(void)
{
    printf("I will summon the butler function.\n"); 
    butler();
    printf("Yes. Bring me some tea and writeable DVDs.\n");
    return 0; 
}
/* start of function definition */ 
void butler(void) 
{
    printf("You rang, sir?\n"); 
}

```

*butler()* 出现了三次
+ 第一次是函数声明
+ 第二次是函数的调用
+ 第三次是函数的实现定义

#### 如何调试

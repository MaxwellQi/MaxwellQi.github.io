---
layout: post
title: c语言基础
description:c语言基础系统总结
category: blog
tag: c语言
---


## 定义常量

定义常量有两种方式：

    #define Pai 3.14159
    const float pai 3.14159

区别： 第一种方式是将Pi定义成一种符号，此时Pai只是3.14159的别名，在编译期间用3.14159去取代Pi的值,define相当于替换；第二种方式是将PI定义成变量，但告诉编译器它的值是固定不变的，如果在程序中试图去修改它的值，在编译时会报错。

`#define`定义常量的好处：

* 通过有意义的单词符号，可以指明该常量的意思，使得程序员在阅读代码时，减少迷惑
* 需要修改常量的时候，可以只需要修改一次,实现批量修改,效率高而且准确

## sizeof运算符

sizeof是个单目运算符，用来计算操作数在内存中占据的字节数，其操作数既可以是括在圆括号中的类型标识符，其返回值是size_t类型，即无符号整数。

```
sizeof(short);				/*返回2*/
sizeof(long);				/*返回4*/
sizeof(int);	       /*不确定，取决于不同的系统*/
也可以是一个表达式，如：
short x；
sizeof(x);					/*返回2*/

```

## 原码反码和补码

原码：最高位为符号位，其余各位为数值本身的绝对值

反码：

* 正数：反码与原码相同
* 负数：符号位为1，其余位对原码取反

补码：

* 正数：原码、反码、补码相同
* 最高位为1，其余位为原码取反，再对整个数加1

在计算机系统中，数值一律用补码来表示（存储）。使用补码，可以将符号位和其它位统一处理；同时，减法也可按加法来处理。另外，两个用补码表示的数相加时，如果最高位（符号位）有进位，则进位被舍弃。

采用原码表示法简单易懂，但它的最大缺点是加法运算复杂。这是因为，当两数相加时，如果是同号则数值相加；如果是异号，则要进行减法。而在进行减法时还要比较绝对值的大小，然后大数减去小数，最后还要给结果选择符号。为了解决这些矛盾，人们找到了补码表示法。机器数的补码可由原码得到。如果机器数是正数，则该机器数的补码与原码一样；如果机器数是负数，则该机器数的补码是对它的原码（除符号位外）各位取反，并在未位加1而得到的。


## 字符型变量

字符型变量在内存中占一个字节，由于存储的是字符的二进制ASCII码，与整型数据存储方式类似，字符型数据和整型数据可以相互运算

注意，字符串常量不允许赋值给字符型变量，C语言也没有专门的字符串变量。要在内存中存取字符串，只能使用数组或指针。 

字符’1’和整数1是不同的概念：

* 字符’1’只是代表一个形状为’1’的符号，在需要时按原样输出，在内存中以ASCII码形式存储，占1个字节
* 整数1是以整数存储方式(二进制补码方式)存储的，占2个或4个字节

## 几种常见的算法

### 数据分离

求水仙花数（一个三位数，其各位数字的立方和等于该数本身）

```
main(  )
{
  int  s,a,b,c;
  for(s=100;s<=999;s++)
       { a=s/100;（百位）
          b=s/10%10;（十位）
          c=s%10;（个位）
          if(s==a*a*a+b*b*b+c*c*c)  printf(“%4d”,s);
        }
}
```

### 辗转相除

求两个整数u,v的最大公约数

分析：

* 若u>v，用u除以v求余数temp
* 若temp==0，则v为最大公约数；若temp!=0，将u=v;v=temp，继续用u除以v，求余数temp
* 直至temp= =0，v为最大公约数

```
main(   )
{
int  u,v,temp;
  scanf(“%d%d”,&u,&v);
  while(v!=0)
     { temp=u%v;
        u=v;
        v=temp;
      }
   printf(“gcd=%d\n”,u);
}

```

### 穷举法

通过循环对问题的所有可能状态一一测试，直到找到解或将全部可能状态都测试过为止。

求100～150之间的全部素数。

```
#include <stdio.h>
#include <math.h>
main( )
{   int n,i,k;
    for(n=101;n<150;n=n+2)
    {   k=sqrt(n);
        for(i=3;i<=k;i=i+2) if(n%i==0)break;
        if(i>k)printf("%d   ",n);
    }
}

```

## 内存分配 

### 动态内存分配

以前的示例程序都是将指针初始化为变量的地址（或用变量的地址来对指针变量赋值），此外，C语言函数库中提供了malloc和free函数，允许用户用户调用以动态申请说需要的内存，给程序的设计带来了很大的灵活性。

先要搞明白一个问题，什么是动态分配，什么是静态分配？举例来说，在声明数组时，必须明确告诉编译器数组的大小，之后编译器就会在内存中为该数组开辟固定大小的内存。有些时候，用户并不确定需要多大的内存，使用多大的数组，为了保险起见，有的用户采用定义一个大数组的方法，开辟的数组大小可能比实际所需大几倍甚至十几倍，这造成了内存的浪费，带来了极大的不便。另一方面，即使用户确切知道要存放的元素个数，但随着问题的深入，元素数目可能会变化，变少还好处理，可如果数目增加呢，用什么来存储的，类似于数组内存这种分配机制就称为静态分配，很明显，静态分配是由编译器完成的，在程序执行前便已指定。
显而易见，静态分配虽然直观，易理解，但存在明显的缺陷，不是容易浪费内存就是内存不够用，为解决这一问题，C语言引入了动态分配机制。

所谓动态分配，是指用户可以在程序运行期间根据需要申请或释放内存，大小也完全可控。动态分配不像数组内存那样需要预先分配空间，而是由系统根据程序需要动态分配，大小完全按照用户的要求来，当使用完毕后，用户还可释放所申请的动态内存，由系统回收，以备他用。

### malloc与free

malloc和free是C标准库中提供的两个函数，用以动态申请和释放内存，malloc()函数的基本调用格式为：

    void *malloc( unsigned int size );

参数size是个无符号整型数，用户由此控制申请内存的大小，执行成功时，系统会为程序开辟一块大小为size个内存字节的区域，并将该区域的首地址返回，用户可利用该地址管理并使用该块内存，如果申请失败（比如内存大小不够用），返回空指针NULL.

malloc()函数返回类型是void*，用其返回值对其他类型指针赋值时，必须进行显式转换。size仅仅是申请字节的大小，并不管申请的内存块中存储的数据类型，因此，申请内存的长度须由程序员通过“长度×sizeof（类型）”的方式给出，举例来说:

    int* p=(int*) malloc(5* sizeof(int) ); 

Free就是释放内存，例如free(p)

### realloc calloc与free

除了malloc与free外，C语言标准库函数还提供了calloc函数用以动态申请内存，和malloc函数以字节为单位申请内存不同，calloc函数是以目标对象为单位分配的，目标对象可以是数组，也可以是后面会讲到的结构体等。 calloc函数的原型为：

    void* calloc(size_t num, size_t size);

malloc()函数返回类型也是void*，需要强制转换才能为其他类型的指针赋值。calloc需要两个参数以指定申请内存块的大小，一是对象占据的内存字节数size，二是对象的个数num

size_t类型是无符号整型，在Windows及LCC编译环境下，其定义为：

    typedef unsigned int size_t;


`realloc`为已经分配的内存重新分配空间并复制内容:

    void *realloc( void *ptr, size_t size );

## 字符串

### strlen和size_t

strlen函数返回的是字符串的长度，即字符串中的实际字符数目，末尾的空字符不计数，返回类型为size_t，大家也许对size_t类型比较陌生，该类型定义于标准头文件stddef.h中：

    typedef unsigned int size_t;

在使用size_t类型时，有一点需要特别注意，假设变量A和B都为size_t类型，则下述关系式恒成立： A > B

这样，不能通过将两个size_t类型变量做差来比较大小，因此，不能通过strlen函数返回的字符串长度做差比较到底哪个字符串更长一些.

### strcpy

```
#include <stdio.h>				/*使用printf要包含的头文件*/
#include <conio.h>
#include <string.h>
void main(void)			/*主函数*/
{
char str1[256]={'\0'};		/*开辟一字符数组str1*/
char str2[256]={'\0'};		/*开辟一字符数组str2*/
printf ("请输入第1个字符串: \n");		/*提示用户输入第1个字符串*/
gets (str1);		/*使用gets函数读取一行输入*/
strcpy(str2,str1);	/*将第1个字符串复制给第2个字符串*/
printf("第2个字符串为：\n%s",str2);	/*输出第2个字符串*/
getch();				  /*等待，按任意键继续*/
}

```

## 结构体

```
struct   student
        {       int num;
                 char  name[20];
                 char sex;
                 int age;
                 float score;
                 char addr[30];
        }; 

```

### 声明结构体变量

定义结构体类型的同时定义结构体变量

```
struct   student
        {       int num;
                 char  name[20];
                 char sex;
                 int age;
                 float score;
                 char addr[30];
        }stu1,stu2; 

```
说明：

* 结构体类型与结构体变量概念不同
    * 类型:不分配内存；                      变量:分配内存
    * 类型:不能赋值、存取、运算;     变量:可以
* 结构体可嵌套
* 结构体成员名与程序中变量名可相同，不会混淆

## #define的用法

`#define`语句称为预定义语句，是预处理指令，在编译预处理时进行简单的替换，不做正确性检查，不管含义是否正确，只是简单的替换，如：

    #define DP double*
程序中，所有出现DP的地方都被替换为double*，再来看下述语句：

    DP pDouble1, pDouble2;

`＃define`不是语句，后面不能加分号，如果＃define结构后出现分号，会一起被替换

## typedef的用法

为特定的类型指定了一个同义字(synonyms)，例如：

    typedef int Num[100]; 
    Num a;      // Num是int [100]的同义词
    typedef int (*Pointer)();
    Pointer  p1;  Pointer是int (*)()的同义词

## 关于B、bits

在内存中，一个字节也就是一个B。（一个字节就是一个大B）

1B = 8bits

在内存中，每个内存单元的容量就是 1B


## 文件存操作

### 文件的存储格式

C语言将文件作为字节序列来对待，但从编码角度，或说从对字节信息的解释来看，文件有两种数据格式：文本形式或二进制形式。

文本形式中，字节是基本单位，字节中存放的字符的ASCII码，文本文件也称为ASCII码文件，这种形式便于对字符进行逐个处理，也便于输出显示，但需要的存储空间相比二进制形式往往要大一些。

二进制形式是把数据在内存中的表示形式照样搬到磁盘上，一般来说，二进制形式与字符不存在对应关系，可读性差一些，但节省存储空间，处理速度快

### 文件的操作与结构体

文件结构体FILE

```
typedef   struct
{   int   _fd;         //文件号
     int   _cleft;     //缓冲区中剩下的字符数
     int   _mode;   //文件操作方式
     char  *_next;  //文件当前读写位置
     char  *_buff;  //文件缓冲区位置
}FILE;

```
指针变量说明： FILE   *fp;

用法：

* 文件打开时，系统自动建立文件结构体，并把指向它的指针返回来，程序通过这个指针获得文件信息,访问文件
* 文件关闭后，它的文件结构体被释放

### 文件的类型指针

使用printf函数时，输入设备默认为标准输入设备（一般是键盘），因此，不需要告诉printf函数键盘在哪，但如果想从文件中读取输入，情况就不同了，系统中有不同的磁盘，每个磁盘又有成千上万的文件，到底应该从哪个读呢？要想对文件进行操作，系统需要很多控制信息，包括文件名，文件当前读写位置，缓冲区位置和大小等，为此，C语言提供了“文件型”结构来标示记录待操作文件的信息，该结构定义于头文件stdio.h中，其形式为：

```
struct _iobuf
 {
char *_ptr;
int   _cnt;
char *_base;
int   _flag;
int   _file;
int   _charbuf;
int   _bufsiz;
char *_tmpfname;
};
typede f struct _iobuf FILE;

```

### 文件操作的步骤

C语言程序在进行文件操作时遵循如下操作步骤：打开读写操作关闭，通俗地说，打开是获取文件结构、系统为文件分配缓冲区的过程，不打开文件就不能对其进行读写，关闭是释放缓冲区和其他资源的过程，不关闭文件就会慢慢耗光系统资源，

在进行文件操作时，系统自动与3个标准设备文件联系，这3个文件无需打开和关闭，它们的文件指针分别是：

* stdin：标准输入文件指针，系统分配为键盘
* stdout：标准输出文件指针，系统分配为显示器
* stderr：标准错误输出文件指针，系统分配为显示器








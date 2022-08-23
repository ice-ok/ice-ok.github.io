# 《C程序设计语言》回顾


前言：</br>
最近重读《c程序设计语言》，把c语言重新捡起来。</br>
这篇文章总结c语言中容易遗忘或者混淆的基础细节，方便以后回顾。</br>
</br>

- ```#include <stdio.h>```
- c仅支持```/*注释*/```，c++引入```// 注释```。
- 所有变量都必须先声明后使用。定义在函数起始处，在任何执行语句之前。
- ```while (fahr <= upper)```括号不能省。
- 宏定义：```#define <名字> <替换文本>```
- ```getchar()```返回EOF(End Of File)标识文件结束。EOF与任何字符都不同。所以```getchar()```返回定义成int类型。
- c语言惯例-紧凑风格：
```
#include <stdio.h>

main()
{
    int c;
    
    while ((c = getchar()) != EOF)
        putchar(c);
}
```
- long的长度：gcc下面的长度为32位，64位为64位；vc++下固定为32位。
- 字符常量，就是整数，如'A'，值为ascii码65。
- ```a = b = c = 0;```，赋值结合顺序是从右到左，```a = (b = (c = 0))```。
- 尽量使用标准库库函数。<font color="red">**TODO：需要浏览一遍c库函数。**</font>
- 函数结构：```int main(int argc, char **argv) { return 0; }```
- unix惯例：返回0表示成功，返回非0表示失败。
- c语言中，所有函数参数都是按值传递的。被调函数不能修改调用函数中变量的值，只能修改其私有的临时副本的值。
  - 要修改调用函数的变量，可以使用指针，还是值传递。
- ```getline```把字符'\0'插入数组末尾，标记字符串结束；c语言字符串约定。
- 自动变量，局部变量，外部变量，全局变量，静态变量，寄存器变量。
- 外部变量，自动初始化为0值；自动变量，不自动初始化。局部静态变量，仅初始化1次。
- 外部变量可以通过extern显式声明。
- 外部变量定义到```.c```中，在```.h```中用extern声明。
- 空参数列表用```void```表示。
- 不要过度使用全局变量，这会严重破坏程序的可读性。
- 所有整型```char,short,int,long,long long```都支持```unsigned```无符号和```signed```有符号。
- 有关这些类型长度定义的符号常量以及其他与机器和编译器有关的属性可以在标准头文件```<limits.h>```和```<float.h>```中找到。
- long类型用字母l或L结尾，无符号通常用字母u或U结尾。默认是int。
- 8进制，0开头；16进制，0x开头。字符转义：'\ooo'，8进制转义，'\xhh'，16进制转义。
- '\0'=0=NULL。
- 常量表达式，在编译期求值，可以用在常量出现的位置，如：数组长度。
- 字符串自动连接：```"hello " "world"```。
- 从技术角度看，字符串常量就是字符串数组，末尾以'\0'结束。
- 枚举常量是另一种类型的常量：```enum boolean {NO, YES}```。
  - 可匿名，仅本```.c```文件范围内可用。
- 用```const```限定符修饰变量，则变量初始化后不可修改。
  - ```const char *```VS```const char *const```
- ```!```逻辑非运算符，将0转换为1，非0转换为0。
- 0=假，非0=真；c++ bool类型，```false/true```。
- 自动类型转换=隐式类型转换：
  - 把"比较窄的"操作数转成"比较宽的"操作数，以不丢失信息。
  - 针对可能丢失信息的表达式，编译器会给出警告。
  - signed会转换为unsigned，以大的为目标。
  - float转换为int，丢失小数部分。
  - double转换为float，截取还是四舍五入，依赖实现。
- 强制类型转换=显式类型转换，明确最好。
- c语言没有规定char类型是unsigned还是signed。
- ```i++```vs```++i```：表达式的值 **后加vs先加**。
- ```strcat(s, t)```将字符串t拼接到s的尾部。strcat假定s中有足够的空间保存拼接后的结果。更安全的函数是strncat。
- 位运算符```&, |, ^(异或), <<, >>, ~(位取反)```只能用于整型。
  - 位置1：```v |= bits```
  - 位置0：```v &= ~bits```
  - 判定位是否1：```if (v & bits)```
  - 判定位是否0：```if (v & bits == 0)```
- 条件表达式=三元运算符：```(a > b) ? a : b```
- c语言没有定义同一运算符中多个操作数的计算顺序，也没有定义函数参数的计算顺序；仅规定参数的副作用必须在函数调用之前生效；
  - ```x = f() + g();```
  - ```printf("%d %d\n", ++n, power(2, n));```
- 复合语句=程序块：```{...}```，语法上等价于单条语句。
- **表达式总以```;```结束；**(go写习惯了，总是忘记```;```)。
- ```switch ... case```，默认fall through，比如显示break。
- 循环：
```cpp
while (condition) {
    // ...
}

do {
    // ...
} while (condition);

for (init; condition; step) {
    // ...
}
```
- ```,```运算符，优先级最低。逗号分隔的多个表达式，从左到右依次求值，各表达式右边的操作数的类型和值即为其结果的类型和值。
```cpp
for (i = 0, j = strlen(s) - 1; i < j; i++, j--)
    c = s[i], s[i] = s[j], s[j] = c;
```
- 少用```goto```，难以维护。提前退出不算。
- c语言中，省略返回值，则默认返回int(NOTE：不是void)；参数没标记类型，则默认认为是int。
- 同时定义变量+声明函数：```double sum, atof(char []);```
- c语言中，如果没有函数原型，则函数将在第一次出现的表达式中被隐式声明。
- 外部变量和函数的性质：通过一个名字对外部变量的所有引用，实际上都是引用同一个对象(外部链接)。
- 外部变量的声明和定义严格区分出来很重要。定义放在```.c```中，头文件中```extern```声明之，方便引用。
  - 定义会分配存储单元；声明不会；
  - 外部变量的初始化只能出现在其定义中；
- 预编译头文件：```#include <...>```vs```#include "..."```；本质上就是文本展开；
  - 前者在预设的lib头文件目录等处查找；后者仅在源文件位置查找； 
  - 仅加载一次：```#ifndef xxx #define xxx #endif```
  - ```#pragma once```
- static修饰全局变量，则只能本```.c```访问，不可再外部链接。
- static修饰局部变量，初始化1次，保持结果下次使用。
- register声明告诉编译器，该变量使用频率高。
  - 由编译器决定是否真的放在寄存器中，所以该修饰符意义不大。
  - 不可取地址。
- 局部变量(自动变量)可以重名，内部隐藏外部重名变量。尽量避免重名变量。
- 外部变量和静态变量，默认初始化为0值；自动变量和寄存器变量，默认不修改，即未定义。
- 外部变量和竞态变量初始化在程序开始之前，值必须是常量表达式，可编译期求解。
- 自动变量和寄存器变量，初始化可即时计算，不需要是常量表达式。
  - 局部变量的数组，c语言中允许长度可变；c++不允许；
- 递归执行时消耗栈内存(参数入栈)，递归太深栈内存会耗尽。递归执行速度并不快，但代码比较紧凑。
  - 递归实现注意跳出条件；
  - 递归实现注意先定深度；
- 宏替换：```#define dprint(expr) printf(#expr " = %g\n)", expr```
  - 注意用好括号，避免错误；
  - c++中建议使用内联；
- ```#undef```取消宏定义
- 日志行号，只能通过宏定义实现；
```cpp
#if !defined(HDR)
#define HDR
...
#endif

#if SYSTEM == SYSV
...
#elif SYSTEM == BSD
...
#else
...
#endif

#ifndef HDR
#define HDR
...
#endif
```
- 指针：
```cpp
int z[10];
int c = 0;
int d = 0;

p = &c;
d = *p;
*p = 123;
p = &z[0];
p = z;

++*p; // c值++，等效(*p)++
p++; // p指向下一个元素

p[1] = 1;
*(p+1) = 1;

int test(char s1[], char *s2) // 参数定义，指针和数组等效
// p[-1]语法OK，但越界会宕机；
```
- ```alloc(), afree()```在栈上分配&释放内存。
- c语言保证，0=NULL永远不是有效的数据地址。因此常用NULL作为返回值表示异常。
- 指针和整数不能互相转换，0=NULL除外。
- NULL定义在```<stddef.h>```中。```ptrdiff_t```表示两个指针的带符号差值，不过我们往往使用size_t。strlen，sizeof等都返回size_t无符号整型。
- 定义数组和定义指针的差别：定义指针字符串，修改其所指字符串内容，行为未定义。
```
char s1[] = "test1";
char *s2 = "test2"; // 内容不可修改。
```
- ```strcop, strncpy, strcmp, strncmp ...```
- 二维数组：```char test[i][j]``；相当于i个元素的数组，元素是char t[j]类型。
```cpp
f(int daytab[2][13]) {...}
f(int daytab[][13]) {...} // 等效前者，数组元素个数无关紧要。
f(int (*daytab)[13]) {...} // 参数是个指针，指向int t[13]类型。

// 字符串数组初始化
char *name[] = {"123", "456", NULL};
char name[][15] = {"123", "456", NULL};
```
- 函数指针：
```cpp
typedef int (*cmp)(void *, void *)
cmp cf = test;
int i = (*cf)(f1, f2);

int *f(); // 函数
int (*pf)(); // 函数指针

char (*(*x())[])() // 函数，返回一个指针，指向一个数组，数组的元素是函数指针
char (*(*x[3])())[5] // 3个元素的数组，元素是函数指针，函数返回指针指向char[5]
```
- 结构体：
```cpp
struct ST { // 定义类型
  ...
} s1, s2;   // 定义变量

struct point p = {1, 2}; // 初始化
struct point pa[] = {{1, 2}, {3, 4}} // 数组初始化
printf("%d,%d", p.x, p.y)
```
- 传给函数的结构体比较大的时候，值传递拷贝消耗较大，用指针较好：```int f(struct point *p);```
- ```. -> () []```运算符的优先级最高，高于```++ --```。
```cpp
++p->len;

*p->str++; // 与后者不等效
(*p->str)++;
*p++->str;
```
- sizeof编译期求长度；
- ```#define COUNT(x) (sizeof(x) / sizeof(*x))```
- 条件编译语句中#if不能使用sizeof，预处理器不对类型名进行分析。
  - c++11引入了静态断言，编译期校验，```static_assert()```。
- 指针可以相减，计算个数差值；不能相加。
- 由于字节对齐，struct内元素并不是紧密排列的；可以通过```#pragma```设定字节对齐；
```cpp
#pragma pack(push)
#pragma pack(0)
...
#pragma pack(pop)
```
- alloc不能保证字节对齐，malloc可以保证；
- typedef定义类型别名；比宏定义好，编译期强类型检查；
- 联合，union，共用内存；只能用第一个成员进行初始化；
- struct/union可结构体内匿名；
- 位字段(bit-field)：
```cpp
struct {
    unsigned int is_keyword : 1; // 不可取地址
    unsigned int is_extern : 1;
    unsigned int is_static : 1;
    unsigned int : 2; // 占位而已
} flags;

// 大小端对齐。大端，低位在后；小端，高位在后；
```
- 重定向：
```shell
# p1的标准输出，重定向到p2的标准输入；p2的标准输出重定向到文件test.log
p1 | p2 > test.log
```
- short类型，printf用h表示；
- 变长参数表：
```cpp
#include <stdarg.h>

void minprintf(char *fmt, ...) {
   va_list ap;
   
   va_start(ap, fmt); // 初始化
   
   int v = va_arg(ap, int); // 从列表中取值
   ...
}

scanf("%d/%d/%d", &month, &day, &year);
```
- 带缓存的文件处理：
```cpp
FILE *fopen(char *name, char *mode);
int fclose(FILE *fp)

int fscanf(FILE *fp, char *fmt, ...); // 文本格式读
int fprintf(FILE *fp, char *fmt, ...); // 文本格式写

size_t fread(void *ptr, size_t size, size_t count, FILE *fp); // 二进制读
size_t fwrite( void *ptr, size_t size, size_t count, FILE *fp); // 二进制写

int feof(FILE *stream);
int ferror(FILE *stream);
void exit(int status);

char *fgets(char *s, int size, FILE *stream); // 读一行
int fputs(const char *s, FILE *stream); // 写一行

int ungetc(int c, FILE *stream); // 回写一个字符c，接下来能读到
```
- 随机数发生器：
```cpp
#include <stdlib.h>

int rand(void);
int rand_r(unsigned int *seedp);
void srand(unsigned int seed);
```
- 低级I/O(无缓冲)：
```cpp
#include <unistd.h>

int open(const char *pathname, int flags);
int open(const char *pathname, int flags, mode_t mode);

int creat(const char *pathname, mode_t mode);

int close(int fildes);

ssize_t write(int fildes, const void *buf, size_t nbyte);
ssize_t read(int fildes, void *buf, size_t nbyte);

off_t lseek(int fd, off_t offset, int whence);
```
- 变长参数标准库的支持：```int vprintf(const char *format, va_list ap);```
- 访问目录：
```cpp
DIR *opendir(const char *name);
int readdir(unsigned int fd, struct old_linux_dirent *dirp,
                   unsigned int count);
int closedir(DIR *dirp);
```



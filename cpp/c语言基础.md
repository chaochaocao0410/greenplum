1、c语言如何处理头文件重复包含的问题

条件编译

#ifndef +头文件命名_H

#define 头文件命名_H

#endif

示例

[root@localhost Documents]# cat a.h
#include "x.h"
int getres(int a,int b) {
return sum(a,b);
}
[root@localhost Documents]# cat b.h
#include "x.h"
int getresb(int a,int b) {
return sum(a,b);
}



文件a.h和b.h都引入头文件x.h，而x.h内容不包含条件编译语句，在c.h中同时引用a.h和b.h并在c.c源文件引用c.h，调用报错。

[root@localhost Documents]# cat c.h
#include "a.h"
#include "b.h"
#include<stdio.h>
int res(int a,int b) {
  printf("%d\n",getres(a,b));
  printf("%d\n",getresb(a,b));
  return 0;
}
[root@localhost Documents]# cat c.c
#include "c.h"

int main() {

res(1,2);
return 0;
}

报错如下：

[root@localhost Documents]# gcc c.c
In file included from b.h:1:0,
                 from c.h:2,
                 from c.c:1:
x.h:4:5: error: redefinition of ‘sum’
 int sum(int a,int b) {
     ^
In file included from a.h:1:0,
                 from c.h:1,
                 from c.c:1:
x.h:4:5: note: previous definition of ‘sum’ was here
 int sum(int a,int b) {
     ^

由于x.h没有条件编译语句，导致c.c在编译时在a.h和b.h同时将x.h引入进来，导致sum重定义。

加上之后错误消失

[root@localhost Documents]# cat x.h

#ifndef _HEADERNAME_H
#define _HEADERNAME_H
int sum(int a,int b) {
return a+b;
}

#endif
[root@localhost Documents]# gcc c.c
[root@localhost Documents]#

2、c语言编译过程

预编译、编译、汇编、链接、运行



3、include时""和<>区别

双引号“”：程序在搜索头文件时先在源文件所在目录搜索，搜索不到再到/usr/include目录搜索。

<>尖括号：仅仅在/usr/include路径（编译器设定的路径）搜索。用户可添加自定义路径。
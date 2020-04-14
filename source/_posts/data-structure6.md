---
title: 数据结构 | C 语言的 typedef
date: 2020-04-07 12:56:11
categories:
- [数据结构,c]
tags:
- 数据结构
---
这个可以简化代码量，提供更好的阅读方式。

<!-- more -->

## 经典

```c
# include <stdio.h>
typedef int SHUZI;


typedef  struct Student{
    int sid;
    char name[100];
    char sex;
}ST;

int main(void){
    SHUZI i = 10;
    ST st;
}
```

## 指针方式

```c
# include <stdio.h>
typedef int SHUZI;


typedef  struct Student{
    int sid;
    char name[100];
    char sex;
}* PST; //PST 等价于 struct Student*

int main(void){
    struct Student st;
    PST ps = &st;
    ps->sid = 99;
}
```

## 多命名

```c
# include <stdio.h>

typedef int SHUZI;


typedef struct Student {
    int sid;
    char name[100];
    char sex;
} *PST, ST; //PST 等价于 struct Student*

int main(void) {
    ST st;
    PST ps = &st;
    ps->sid = 99;
}
```


---
title: 数据结构 | 结构体
date: 2020-03-29 16:14:37
categories:
- 数据结构
tags:
- 数据结构
- 结构体
---
这也是 C 语言的基础。

<!-- more -->

- 结构体里面只有属性，没有方法
- 结构体不是变量，是数据类型

这这里我先说一个非常重要的概念

结构体变量是一个指针，比如

    struct Student st;

无论，st 是以什么方式赋值，st 都是指向这个结构体变量的指针，也就是地址。

## 无指针方式

```c
#include <stdio.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

int main(void) {
    struct Student st = {1, "ss", 20};
    printf("%d,%s,%d", st.sid, st.name, st.age);
    	// 1,ss,20
    return 0;
}
```

也可以这种方式

```c
#include <stdio.h>
#include <string.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

int main(void) {
    struct Student st;
    st.sid = 1;
    strcpy(st.name,"cc");
    st.age = 20;
    printf("%d,%s,%d", st.sid, st.name, st.age);
    return 0;
}
```

## 有指针方式

```c
#include <stdio.h>
#include <string.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

int main(void) {
    struct Student st = {1, "ss", 20};

    struct Student *pst;
    pst = &st;

    pst->sid = 10;
    // 上面这句话等价于 (*pst).sid = 10;
    strcpy(pst->name, "xx");
    pst->age = 20;
    printf("%d,%s,%d", st.sid, st.name, st.age);
    return 0;
}
```

## 结构体里的指针

```c
#include <stdio.h>

struct Student {
    int sid;
    char* name;
    int age;
};

int main(void) {
    struct Student st;

    st.name = "vv";
    printf("%s", st.name);
    return 0;
}
```

## 函数操作结构体

### 传入地址

```c
#include <stdio.h>
#include <string.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

void f(struct Student *pst) {
    (*pst).sid = 1;
    strcpy(pst->name, "vv");
}

int main(void) {
    struct Student st;

    f(&st);
    printf("%s", st.name);
}
```

### 实参传输

#### 无指针

并不改变，非常重要

```c
#include <stdio.h>
#include <string.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

void f(struct Student pst) {
    pst.sid = 1;
    strcpy(pst.name, "vv");
}

int main(void) {
    struct Student st = {1,"cc",20};
    f(st);
    printf("%s", st.name);
    	// 输出 cc
}
```

#### 有指针

```c
#include <stdio.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

void p(struct Student *pst){
    pst->sid = 2;
}

int main(void) {
    struct Student st = {1,"cc",20};
    p(&st);
    printf("%d",st.sid);
    return 0;
}
```

## 传参方式比较

在 c 语言中，对于结构体，一共有两种传参方式。

- 传入结构体变量
- 传入结构体指针

```c
#include <stdio.h>
#include <string.h>

struct Student {
    int sid;
    char name[200];
    int age;
};

void f(struct Student pst) {
    printf("%d",pst.sid);
}

void p(struct Student *pst){
    printf("%d",pst->sid);
}

int main(void) {
    struct Student st = {1,"cc",20};
    f(st);
    p(&st);
    return 0;
}
```

在上面程序里，我们可以把问题变成是 f 好还是 p 好。

先说结论，是 p 好，因为我们传入 f 的时候，传入的是结构体的变量，也就是所有的字节，比如，该 Student 结构体共 208 个字节，而，我们 f 传入的就是 208 个字节的变量。

但是，p 传入的是 4 个字节的地址，所以，p 更加省内存，速度也更加快。
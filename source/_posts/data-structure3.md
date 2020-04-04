---
title: 数据结构 | 动态分配内存
date: 2020-03-30 17:04:20
categories:
- [数据结构,c]
tags:
- 数据结构
- 内存
---
这一章我们将讲述 c 中的 malloc 这个函数。

<!-- more -->

## 基础

	int a[5] = {1,2,3,4,5}

这种方式是静态分配内存，要想动态分配就要使用 malloc 这个函数。

```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int len;
    printf("请输入你想要的长度");
    scanf("%d", &len);
    int *pArr = (int *) malloc(sizeof(int) * len);
    for (int i = 0; i < len; ++i) {
        *(pArr + i) = i;
    }
    for (int i = 0; i < len; ++i) {
        printf("%d",*(pArr + i));
    }
    free(pArr); // 释放内存
    return 0;
}
```

上面这段代码展示了两个。

- 动态分配内存
- 动态释放内存

## 跨函数使用内存

```c
#include <stdio.h>
#include <stdlib.h>

int f();
int _f();

int main() {
    int *p;
    f(&p);
    _f(&p);
    return 0;
}

int _f(int **q) {
    *q = (int *) malloc(4);
}

int f(int **q) {
    int s;
    *q = &s;
}
```

上面两个函数 \_f() 和 f() 哪一个可以给 p 分配一个内存。

先说结论，只有 \_f 可以分配，因为 f 执行完之后，就自动释放了，而 malloc 只有主动才会释放内存。

其实，我并不十分理解，但是，这个应该是算法中很重要的一步，留给后续的我理解吧。

这个，可以有助于理解 class 的对象。

比如

	A a = new A();

为什么，A() 中的内存没被释放呢，是因为它是这样实现的

	A *a = (A*)malloc(sizeof(A));

#### 跨函数使用内存的例子

```c
#include <stdio.h>
#include <stdlib.h>

struct Student {
    int sid;
    char name[20];
};

struct Student *CreateStudent(void);

void ShowStudent(struct Student *);

int main() {
    struct Student *ps;
    ps = CreateStudent();
    ShowStudent(ps);
    return 0;
}

struct Student *CreateStudent(void) {
    struct Student *p = (struct Student *) malloc(sizeof(struct Student));
    p->sid = 10;
    return p;
}

void ShowStudent(struct Student *pst) {
    printf("%d", pst->sid);
}
```
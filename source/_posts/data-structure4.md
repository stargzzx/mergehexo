---
title: 数据结构 | c 语言实现一个数组
date: 2020-03-30 20:51:50
categories:
- [数据结构,c]
tags:
- 数据结构
---
从源代码层面实现一个数组。

<!-- more -->

下面是实现数组排序的代码。

```c
#include <stdio.h>
#include <stdbool.h>
#include <stdlib.h>

struct Arr {
    int *pBase; // 存储的是数组第一个元素地址
    int len; // 数组所能容纳的最大元素个数
    int cnt; // 当前数组有效元素的个数
};

void init(struct Arr *pArr, int length);
bool append(struct Arr *pArr, int val); // 追加
bool insert(struct Arr *pArr, int pos, int val);
bool delete(struct Arr *pArr, int pos, int *pVal);
bool get();
bool is_empty(struct Arr *pArr);
bool is_full(struct Arr *pArr);
void sort(struct Arr *pArr);
void show(struct Arr *pArr);
void inversion(struct Arr *pArr);


int main(void) {
    struct Arr arr;
    init(&arr, 4);
    append(&arr, 1);
    insert(&arr, 1, 2);
    int tmp;
    delete(&arr, 1, &tmp);
    append(&arr, 5);
    append(&arr, 4);
    inversion(&arr);
    sort(&arr);
    show(&arr);
    return 0;
}

void init(struct Arr *pArr, int length) {
    pArr->pBase = (int *) malloc(sizeof(int) * length);
    if (pArr->pBase == NULL) {
        printf("动态内存分配失败\n");
        exit(-1);
    } else {
        pArr->len = length;
        pArr->cnt = 0;
    }
}

bool is_empty(struct Arr *pArr) {
    if (pArr->len == 0) {
        return true;
    } else {
        return false;
    }
}

bool is_full(struct Arr *pArr) {
    if (pArr->cnt == pArr->len) {
        return true;
    } else {
        return false;
    }
}

void show(struct Arr *pArr) {
    if (is_empty(pArr)) {
        // 之所以是 pArr 而不是 &pArr 是因为，
        // pArr 本身就是结构体的地址，而 &pArr 是结构体地址的地址
        printf("数组为空\n");
    } else {
        for (int i = 0; i < pArr->cnt; ++i) {
            printf("%d\n", *(pArr->pBase + i));
        }
    }
}


bool append(struct Arr *pArr, int val) {
    if (is_full(pArr)) {
        printf("数组已满\n");
        return false;
    } else {
        *(pArr->pBase + pArr->cnt) = val;
        pArr->cnt += 1;
        return true;
    }
}

bool insert(struct Arr *pArr, int pos, int val) {
    if (is_full(pArr)) {
        printf("数组已满\n");
        return false;
    }
    if (pos + 1 > pArr->cnt) {
        printf("不满足插入条件\n");
        return false;
    }
    int i = 0;
    for (i = pArr->cnt; i > pos; --i) {
        *(pArr->pBase + i) = *(pArr->pBase + i - 1);
    }
    *(pArr->pBase + i) = val;
    pArr->cnt += 1;
    return true;
}

bool delete(struct Arr *pArr, int pos, int *pVal) {
    if (is_empty(pArr)) {
        printf("数组为空\n");
        return false;
    }
    if(pos > pArr->cnt - 1){
        printf("不满足删除条件\n");
        return false;
    }
    *pVal = *(pArr->pBase + pos);
    int i = 0;
    for (i = pos; i < pArr->cnt; i++) {
        *(pArr->pBase + i) = *(pArr->pBase + i + 1);
    }
    pArr->cnt -= 1;
    return true;
}

void inversion(struct Arr *pArr) {
    int t = (pArr->cnt) / 2;
    int tmp = 0;
    for (int i = 0; i < t; i++) {
        tmp = *(pArr->pBase + i);
        *(pArr->pBase + i) = *(pArr->pBase + pArr->cnt - 1 - i);
        *(pArr->pBase + pArr->cnt - 1 - i) = tmp;
    }
}

// 冒泡
void sort(struct Arr *pArr) {
    int tmp = 0;
    for (int i = 0; i < pArr->cnt; i++) {
        for (int j = pArr->cnt - 1; j - 1 >= i; j--) {
            if (*(pArr->pBase + j) < *(pArr->pBase + j - 1)) {
                tmp = *(pArr->pBase + j);
                *(pArr->pBase + j) = *(pArr->pBase + j - 1);
                *(pArr->pBase + j - 1) = tmp;
            }
        }
    }
}
```
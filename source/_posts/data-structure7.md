---
title: 数据结构 | 链表
date: 2020-04-14 10:04:59
categories:
- [数据结构,c]
tags:
- 数据结构
---
链表是数据库的灵魂，可以说，其他所有的蛮复杂的数据结构，都能找到链表的影子。

<!-- more -->

<br/>

# 链表部分属性

<br/>

- 首节点
	- 第一个有效节点
- 尾节点
	- 最后一个有效节点
- 头结点
	- 不存在有效数据
- 头指针
	- 指向头结点的指针变量
- 尾指针
	- 指向尾节点的指针变量

在这里，有必要区分一下首节点和头结点。

一般我们都可以理解首节点和尾节点，但是，有的时候，我们要让头结点指向首节点。这个头结点一般不存储数据，仅仅只是一个指向（首节点）。之所以，有一个头结点，是因为这个的存在有助于我们对链表的增删改查（代码更有可读性，简便代码）。

头结点和首节点不一定是一个数据类型，可能头结点仅仅是一个指针。

## 确认一个链表需要几个参数

只需要头结点

## 分类

- 单链表
- 双链表
	- 每一个节点有两个指针
- 循环链表
	- 能通过任何一个节点找到其他节点
- 非循环链表

<br/>

# 算法

<br/>

- 遍历
- 查找
- 清空
- 销毁
- 长度
- 排序
- 删除
- 添加

在这里，我的头结点和节点使用的是一个数据类型，当然，有人很多觉得这样不够优雅，但是，也没关系啦，毕竟，我只是想要写写算法而已。

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>


typedef struct Node* PNODE;
typedef struct Node NODE;
struct Node {
    int data;
    struct Node *pNext;
};

PNODE create(void);
void show(PNODE pHead);
void insert_tail(PNODE pHead, int data);
void insert_head(PNODE pHead, int data);
bool is_empty(PNODE pHead);
int length(PNODE pHead);

int main(void) {
    PNODE pHead;
    int len;

    pHead = create(); // 创建一个非循环单链表，并将该链表的头结点的地址付给 pHead
    is_empty(pHead);
    printf("%d\n",length(pHead));
    insert_tail(pHead, 10);
    insert_tail(pHead, 20);
    insert_tail(pHead, 30);
    insert_head(pHead, 0);
    printf("%d\n",length(pHead));
    show(pHead);
    return 0;
}

bool is_empty(PNODE pHead){
    if(pHead->pNext == NULL){
        printf("链表为空\n");
        return false;
    }
    return true;
}

int length(PNODE pHead){
    int i = 0;
    PNODE pNode = pHead;
    while (pNode->pNext != NULL){
        i++;
        pNode = pNode->pNext;
    }
    return i;
}

PNODE create(void) {
    // 头结点
    PNODE pHead = (PNODE) malloc(sizeof(NODE));
    if (!pHead) {
        printf("分配失败，程序终止\n");
        exit(-1);
    }
    pHead->pNext = NULL;
    return pHead;
}

void show(PNODE pHead) {
    PNODE pNode = pHead->pNext;
    while (pNode->pNext) {
        printf("%d\n", pNode->data);
        pNode = pNode->pNext;
    }
    printf("%d\n", pNode->data);
}

void insert_head(PNODE pHead, int data) {
    PNODE pNew = (PNODE) malloc(sizeof(NODE));
    if (!pNew) {
        printf("分配失败，程序终止\n");
        exit(-1);
    }
    pNew->data = data;
    pNew->pNext = pHead->pNext;
    pHead->pNext = pNew;
}

void insert_tail(PNODE pHead, int data) {
    PNODE pNew = (PNODE) malloc(sizeof(NODE));
    if (pNew == NULL) {
        printf("分配失败，程序终止\n");
        exit(-1);
    }
    pNew->data = data;
    pNew->pNext = NULL;
    PNODE pNode = pHead;
    while (pNode->pNext != NULL) {
        pNode = pNode->pNext;
    }
    pNode->pNext = pNew;
}

void traverse(PNODE pHead) {

}
```

## 尾插法

我在书写代码的过程中，曾被尾插法所困扰，请看下面的代码：

```c
void insert_tail(PNODE pHead, int data) {
    PNODE pNew = (PNODE) malloc(sizeof(NODE));
    if (pNew == NULL) {
        printf("分配失败，程序终止\n");
        exit(-1);
    }
    pNew->data = data;
    pNew->pNext = NULL;
    PNODE pNode = pHead->pNext;
    while (pNode != NULL) {
        pNode = pNode->pNext;
    }
    pNode = pNew;
}
```

乍一看，这个尾插没有问题，但是，仔细一考量

	PNODE pNode = pHead->pNext;

这个时候 pNode 指向 pHead->pNext 为 NULL ，但是，下面又将

	pNode = pNew;

对 NULL 的地址进行赋值是无效的。

具体情况，你可以查看我下面的博文。

[c 语言 | NULL 解析](https://benpaodewoniu.github.io/2020/04/14/c4/)


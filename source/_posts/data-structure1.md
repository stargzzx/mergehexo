---
title: 数据结构 | C 的指针
date: 2020-03-29 10:44:08
categories:
- [数据结构,c]
tags:
- 数据结构
- 指针
---
这一章，我将详细的介绍一下指针，尽可能的把指针所有的概念都介绍清楚。

<!-- more -->

## 指针就是地址，地址就是指针

```c
#include <stdio.h>

int main() {
    int* p;
    int i = 10;
    p = &i;
    return 0;
}
```

上面这个是对的，因为是把 i 的指针赋予给 p，但是，下面的是错的

```c
#include <stdio.h>

int main() {
    int* p;
    int i = 10;
    p = i;
    return 0;
}
```

## 指针只能存储相应类型的地址

```c
#include <stdio.h>

int main() {
    int* p;
    int i = 10;
    p = i;

    char a = 'A';
    p = &a; //因为 a 和 p 不是同一类型，所以，这个错误
    return 0;
}
```

## 什么是指向

```c
#include <stdio.h>

int main() {
    int* p;
    int i = 10;
    p = &i;
    printf("%d , %d", &i, p);
    	// -452253396 , -452253396
    printf("%d , %d", i, *p);
		// 10 , 10
    return 0;
}
```

这里面 i 存储着 10 ， p 存储着 i 的地址，所以说 p 指向 i。

假设 i 的地址是 3000H ，那么 p 里面就存储着 3000H ，而这里有一个重要的概念，就是 \*p 就等于 i。

也就是调用 \*p 就相当于调用 i 本身。 

```c
#include <stdio.h>

int main() {
    int* p;
    int j;
    j = *p;
    printf("%d", j);
    return 0;
}
```

假设，p 并没有赋值，那么，c 会自动赋给它一个随机的数字，这个数字可能是没有意义的数字，也可能是某一变量的地址。

上面这句话在老版中应该是成立的，但是，在新版编译器，也可能是（C++，因为我只是学算法，所以，这方面我不打算深究了）中，如果这样赋值的话， \*p = 0 即，j 是等于 0 的。

那么，p 有没有值呢？ p 是有值的，是它的地址。

## 注意点

- 普通变量前面不能加 \*
- 常量和表达式前面不能加 &

```c
int i = 1;
	*i

&(3+5)
```

以上写法都是错误的

## 函数参数指针

```c
#include <stdio.h>

void f(int i){
    i = 100;
}


int main(void) {
    int i = 9;
    f(i);
    printf("%d",i);
    	// 9
    return 0;
}
```

```c
#include <stdio.h>

void f(int* i){
    *i = 100;
}


int main(void) {
    int i = 9;
    f(&i);
    printf("%d",i);
    	// 100
    return 0;
}
```

## 指针和一维数组

注意在这里我们只说一维

```c
#include <stdio.h>

int main(void){
    int a[5] = {1,2,3,4,5};
    printf("%d",*a);
    	// 1
    return 0;
}
```

这里面最重要的是要理解 a 存储着什么？

a 存储着一维数组的第一个元素，在这里是 1 的地址。

```c
#include <stdio.h>

int main(void){
    int a[5] = {1,2,3,4,5};
    printf("%d",*(a+1));
    	// 2
    return 0;
}
```

上面的例子说明下了下面的语句

	a[i] = *(a+i)

```c
#include <stdio.h>

void change_array(int* p){
    *(p + 2) = 10;
}

int main(void){
    int a[5] = {1,2,3,4,5};
    change_array(a);
    printf("%d",*(a + 2));
    	// 10
    return 0;
}
```

数组在 c 语言中是连续的存储地址。以 
	
	int a[3] = {1,2,3}

为例，a 代表 1 的地址，因为 int 占 4 个字节，所以， a + 1 ，指向 1 地址的后四个字节的地址。

假设，改成

	double a[3] = {1,2,3}

那么，a + 1 ，指向 1 地址的后八个字节，因为一个 double 占 8 个字节。

```c
#include <stdio.h>

int main(void) {
    double arr[3] = {1.1, 2.2, 3.3};
    double* q;

    printf("%p\n",arr);  // p 代表 十六 进制输出
    printf("%p\n",&arr[0]);
    printf("%p\n",&arr[1]);
  		 //0x7ffee9bad920
		// 0x7ffee9bad920
		// 0x7ffee9bad928
}
```

## 指针的指针

```c
#include <stdio.h>

void f(int* *q) {
    printf("%p\n", q);
    printf("%p\n", *q);
    printf("%d\n", **q);
		// 0x7ffeeed49930
		// 0x7ffeeed49938
		// 9
}

int main(void) {
    int i = 9;
    int* p = &i;
    printf("%p\n", &i);
    printf("%p\n", &p);
    	// 0x7ffeeed49938
		// 0x7ffeeed49930

    f(&p);
    return 0;
}
```
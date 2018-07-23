---
layout: post
title: "void*에 구조체 넘기기"
author: lynn.baek
date: 2018-05-11 09:42
tags: [C++, void*, structure]
comments: true
---

void*에 구조체를 넘기는 방법입니다.



```c++
#include <stdio.h>
                                                                                
typedef struct{
        int b;
}STRUCT_A;
                                                                                
void test_func(void  * d){
        int a;
        a = ((STRUCT_A*)d)->b;
        printf("");
}
void func(void  *c){
        printf("");
        test(c);
}
                                                                                
                                                                                
int main(void){
        STRUCT_A a;
                                                                                
        a.b = 7;
        func(&a);
                                                                                
        printf("");
        return 0;
}
```


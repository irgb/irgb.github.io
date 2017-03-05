---
title: Quick Sort C++ Implementation
date: 2017-03-05
categories: algorithm
tags: [sort]
description: Recursive Quick Sort implementation.
---

```cpp
#include <iostream>
using namespace std;
void swap(int * arr, int i, int j){
    int tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
}

int partition(int * arr, int l, int r){
    int tmp = arr[r];
    int i = l;
    for(int j=l;j<r;++j){
        if(arr[j]<tmp) swap(arr, i++, j);
    }
    swap(arr, i, r);
    return i;
}

void quick_sort(int * arr, int l, int r){
    if(l>=r) return;
    int p = partition(arr, l, r);
    quick_sort(arr, l, p-1);
    quick_sort(arr, p+1, r);
}
int main(){
    int * arr = new int[1000];
    int len = 0;
    int v;
    while(cin>>v) arr[len++] = v;
    quick_sort(arr, 0, len-1);
    for(int i = 0;i<len;++i) cout<<" ";
    cout<<endl;
}
```

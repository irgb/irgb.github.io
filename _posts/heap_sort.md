---
title: Heap Sort C++ Implementation
date: 2017-03-05
categories: algorithm
tags: [heap, sort]
description: implement Heap Sort using maximum heap.
---

```cpp
#include <iostream>
using namespace std;
void swap(int * arr, int i , int j){
    int tmp = arr[i];
    arr[i] = arr[j];
    arr[j] = tmp;
}

void maxHeapify(int * arr, int len, int i){
    if(i<0 || i>=len) return;
    do{
        int larger = i;
        int l = 2 * i +1, r = 2 * i +2;
        if(l < len && arr[l] > arr[larger]) larger = l;
        if(r<len && arr[r] > arr[larger]) larger = r;
        if(i == larger) break;
        swap(arr, i, larger);
        i = larger;
    }while(i<len);
}

void buildHeap(int * arr, int len){
    for(int i = len/2-1;i >= 0;--i){
        maxHeapify(arr, len, i);
    }
}

void heapSort(int * arr, int len){
    buildHeap(arr, len);
    for(int i = len-1;i>0;--i){
        swap(arr, 0, i);
        maxHeapify(arr, i, 0);
    }
}

int main(){
    int * arr = new int[1000];
    int len = 0;
    int v;
    while(cin>>v) arr[len++] = v;
    heapSort(arr, len);
    for(int i = 0;i<len;++i) cout<<arr[i]<<' ';
    cout<<endl;
}
```

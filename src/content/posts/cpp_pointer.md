---
title: C++ Pointer
published: 2026-03-01
description: '沒什麼，就只是你看到一個白癡連指標都不會做出來的筆記'
image: ''
tags: [程式設計, C++, Pointer]
category: 'Programming'
draft: false 
lang: ''
---
因為我是程式智障，所以我需要做這個筆記讓我牢固整個pointer(指標)的概念。
## 基本觀念與 Code Examples
```cpp
#include<iostream>
using namespace std;

int main(){
    /*
    This is how we declare the pointer.
    (Data Type) *(Variable name);
    or
    (Data Type)* (Variable name);

    The pointer itself is also a variable. It has its own memory address, 
    and it is used to store the memory address of another variable.
    */
    int *ptr = nullptr; // 養成好習慣, 未定向前先設為 nullptr

    /*
    Now we declare a normal integer. 
    If we make "ptr" store the address of "num", 
    then when we output "*ptr", it will return the integer from "num" (10).
    Outputting "ptr" directly will give the memory address.
    */
    int num = 10;
    ptr = &num;

    cout << num << endl; // 10
    cout << &num << endl; // 0x5ffec4 (The memory address of "num")
    cout << ptr << endl; // 0x5ffec4
    cout << *ptr << endl; // 10

    return 0;
}
```

觀念釐清
- Pointer 的本質: Pointer 本身也是一個 variable, 它在記憶體中同樣會佔用專屬的空間, 並且有它自己的 memory address. 差別在於, 一般的 variable 儲存的是普通的 data, 而 pointer 儲存的資料是別人的 memory address.
- Dereference: 當我們 output ptr 的時候, 得到的是 memory address. 必須透過 dereference operator (也就是加上 \*), 去 output \*ptr, 才會順著 address 找到並 return 裡面的 value.

```
Memory Address

0x5ffec4  +------------------+
          |       10         |  <--- int num = 10;
          +------------------+
          (Normal variable: num)

               ^
               | 儲存了 num 的 address (ptr = &num;)
               |

0x67f120  +------------------+
          |    0x5ffec4      |  <--- int *ptr = nullptr;
          +------------------+       ptr = &num;
          (Pointer variable: ptr)
```

## 進階核心觀念與 Code Examples
下面是AI幫我整理出來的，但他們的概念我很眼熟
### nullptr (空指標)
在 C++ 中, 如果 declare 一個 pointer 但沒有立刻讓它指向某個 variable, 它會變成 wild pointer (隨機指向 memory 中的某個未知位置), 這非常危險. 良好的習慣是將尚未使用的 pointer 設為 `nullptr`.

```cpp
#include<iostream>
using namespace std;

int main(){
    int *ptr1; // 壞習慣: wild pointer, 指向未知位址
    int *ptr2 = nullptr; // 好習慣: 明確表示目前沒有指向任何東西

    if (ptr2 == nullptr) {
        cout << "ptr2 is safe to check." << endl;
    }
    return 0;
}
```

### Pass by Pointer (透過指標傳遞)
將 pointer 當作 parameter 傳進 function. 這樣可以在 function 內部直接修改外部 variable 的值, 因為你是直接對 memory address 進行操作, 而不是只修改 local copy.

```cpp
#include<iostream>
using namespace std;

// function 接收 pointer 作為 parameter
void addTen(int *ptr) {
    *ptr = *ptr + 10; // 透過 dereference 修改原本位址裡的值
}

int main(){
    int num = 5;
    cout << "Before: " << num << endl; // 5

    addTen(&num); // 傳遞 num 的 memory address

    cout << "After: " << num << endl;  // 15
    return 0;
}
```

### Pointer Arithmetic (指標算術)
當對 pointer 進行加減運算時, 它實際移動的 memory address 大小取決於 Data Type 的大小. 例如 int 通常佔 4 bytes, 所以 ptr + 1 會讓 address 往前推進 4 bytes.

```cpp
#include<iostream>
using namespace std;

int main(){
    int arr[3] = {10, 20, 30};
    int *ptr = arr; // ptr 指向 array 的第一個 element (10)

    cout << "Address: " << ptr << ", Value: " << *ptr << endl;
    
    ptr = ptr + 1; // pointer 往前移動一個 int 的大小
    cout << "Address: " << ptr << ", Value: " << *ptr << endl; // 輸出 20
    
    return 0;
}
```

### Pointers and Arrays (指標與陣列)
在 C++ 裡, array 的名稱本質上可以 decay (退化) 成一個指向該 array 第一個 element 的 pointer. 因此, `arr[i]` 其實等同於 `*(arr + i)`.

```cpp
#include<iostream>
using namespace std;

int main(){
    int arr[3] = {100, 200, 300};
    int *ptr = arr; // array 名稱可以直接當作 pointer 使用

    for(int i = 0; i < 3; i++) {
        // 以下兩種寫法結果完全相同
        cout << "Array syntax: " << arr[i] << endl;
        cout << "Pointer syntax: " << *(ptr + i) << endl;
    }
    return 0;
}
```

### Dynamic Memory Allocation (new 與 delete)
當你不確定需要多大的 space, 或者想在 runtime 才決定 array 大小時, 可以用 pointer 動態配置 memory. 記得用完後必須手動釋放 memory, 否則會造成 memory leak.
```cpp
#include<iostream>
using namespace std;

int main(){
    int size;
    cout << "Enter array size: ";
    cin >> size;

    // 在 runtime 動態配置 memory
    int *dynamicArr = new int[size]; 

    for(int i = 0; i < size; i++) {
        dynamicArr[i] = i * 10;
        cout << dynamicArr[i] << " ";
    }
    cout << endl;

    // 用完必須手動釋放 memory
    delete[] dynamicArr; 
    
    // 釋放後將 pointer 設為 nullptr 是好習慣
    dynamicArr = nullptr; 

    return 0;
}
```

## 一個簡單的Example

```cpp
#include<iostream>
using namespace std;

void ptrSwap(int *num1, int *num2){
    int temp = *num1;
    *num1 = *num2;
    *num2 = temp;
}

int main(){
    int a = 10, b = 5;

    cout << "Before: " << endl;
    cout << "a = " << a << endl;
    cout << "b = " << b << endl;
    
    ptrSwap(&a, &b);

    cout << "After: " << endl;
    cout << "a = " << a << endl;
    cout << "b = " << b << endl;

    return 0;
}
```

Output:
```
Before: 
a = 10
b = 5
After: 
a = 5
b = 10
```
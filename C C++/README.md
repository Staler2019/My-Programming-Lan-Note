# C & C++

C++ 在非 mingw 編譯器下有萬用標頭黨 #include <bits/stdc++.h>，囊括大部分標頭黨在內十分方便

- 語言特色

  > 1. 型態嚴謹、語法冗長，但效率驚人。
  > 2. 擁有指標使得程式碼難懂但資源效率增加。
  > 3. 常用於資源受限及追求執行效率的程式。

- 常犯錯誤
  > 忘記分號

## Variable 變數

> ⚠️ 特殊使用法可以套用在陣列中

### C 的變數

```.c
int a = 100;        // 整數，還有byte, long, long long
char b = 'b';       // 字元
char* str = "abcd"; // 字串
// 字串操作 -> #include <string.h>

float f = 1.2;      // 單精度小數
double d = 1.6e-4;  // 雙精度小數
bool tt = true;     // 布林值，需要#include <stdbool.h>
// true == 1, false == 0
```

#### C 變數的特殊使用法

> ⚠️ 記憶中還有遺漏

```.c
static int = 1;
/*
static 宣告靜態變數，會在可使用的區塊內從第一次使用後持續存在
*/

const int = 2;
/*
const 在編譯階段宣告變數存在，直到在main程式結束後消失
一般常用的 #define AAA 100 建議以此方式替代
*/

int* a = NULL;
/*
C 語言中，NULL == ((void *)0)，大多用在 linked_list 的 next 指標，表示下一個連接的物件不存在
*/

unsigned int = 0;
/*
宣告整數變數範圍不包含負數 (signed bit 拿來增加正整數上限)
*/

typedef int anotherInt;
/*
宣告變數型態代稱: int 別名 anotherInt
***千萬別用#define***
*/
```

### C++ 的變數

```.cpp
int a = 100;                // 整數
char b = 'b';               // 字元
std::string str = "abcd";   // 字串 std::string == char[] != char*
// 字串操作 -> #include <cstring>
/*
C++ 的 std::string 跟 char[] 還是有點不同的
，char arr[] = "abcd" 則實際上是 "abcd\0"
，雖然 strlen 仍是 4 但是需要有 '\0' 的結束符號
，於是 char* 在 C++ 中因為如果沒有經過 malloc 要求記憶體
，較易因為位置取代 '\0' 記憶體而出問題
，進而讀取到其他資訊進入字串中造成溢位，不建議使用
*/

float f = 1.2;              // 單精度小數
double d = 1.6e-4;          // 雙精度小數
bool tt = true;             // 布林值
// true == 1, false == 0
```

#### C++ 變數的特殊使用法

```.cpp
static int = 1;
/*
static 宣告靜態變數，會在可使用的區塊內從第一次使用後持續存在
*/

const int = 2;
/*
const 在編譯階段宣告變數存在，直到在main程式結束後消失
一般常用的 #define AAA 100 建議以此方式替代
*/

int* a = nullptr;
/*
C++ 語言中，nullptr == ((void *)0)，而 NULL == 0。
nullptr 大多用在 linked_list 的 next 指標，表示下一個連接的物件不存在
*/

unsigned int = 0;
/*
宣告整數變數範圍不包含負數 (signed bit 拿來增加正整數上限)
*/

typedef int anotherInt; // first way
using anotherInt = int; // second way
/*
宣告變數型態代稱: int 別名 anotherInt
***千萬別用#define***
*/
```

<!-- TODO undone -->

## Array 陣列

<!-- enum -->

## Pointer 指標

## Function 函式

### 函式的特殊使用法

### Template 模板

## C 的物件 (Structure)

## C++ 的物件 (Structure, Class, NameSpace)

## 少用語法

### C++ 的少用語法

- std::cerr 偵錯訊息輸出
  ```.cpp
  std::cerr << "error msg" << std::endl;
  ```
- 延伸閱讀
  - [我的競程模板](https://gist.github.com/Staler2019/0bbbfd4fb13413a44d48ed0ce91576ee)

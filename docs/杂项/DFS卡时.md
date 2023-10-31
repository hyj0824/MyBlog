---
tags:
  - 技巧
  - 搜索
date: 2023-06-25
dg-publish: true
dg-permalink: DFS卡时
---



```cpp
#include<ctime>
#define sec 1000

using namespace std;
int ti;
int main()
{
    ti=clock();
    while (clock()-ti<sec);
    cout<<"Time's up!"<<endl;
    return 0;
}
```
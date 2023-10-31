---
tags:
  - 技巧
date: 2023-10-23
dg-publish: true
dg-permalink: tuple用法
---


`std::get<_index_>(...)` 或 `std::get<_type_>(...)` 都是引用，可以直接修改、访问。

```cpp
std::tuple<int,char> fu;
cin >> std::get<0>(fu) >> std::get<char>(fu);
```

`std::tie()`：只能传左值，因为要对参数有读写权；返回一个由当前值组成的 tuple。
`std::make_tuple()/std::make_pair()`：能传左右值，都调用构造函数。

```cpp
std::tuple<int,char>(1,'a');
std::tie(1,'a'); // 报错
std::make_tuple(1,'a');
```

Q：有没有一些操作来实现类似C++17中的Structure Bindings？
A：有。

```cpp
int a; char b;
auto p = std::make_tuple(std::ref(a),std::ref(b)) = fu;
// 本质创建了一个std::tuple<ref_wrapper,ref_wrapper>引用a,b两个变量
// 因此 cout << std::get<0>(p) << std::get<char>(p); 会报错
a++,b++;
cout << std::get<0>(p) << std::get<1>(p);
```

```cpp
int a; char b;
auto p = std::tie(a,b) = fu;
a++,b++;
cout << std::get<0>(p) << std::get<1>(p);
```


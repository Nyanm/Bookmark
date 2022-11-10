
关键字`auto`的类型推导实现基于模板类型推导，但有一些细节实现略有不同。有关`auto`的用法与注意事项将集中在Item 5，该条例主要讲解了其类型推导的内容。

由于其与模板类型推导共穿一条裤子，所以大多数情况下可以假象有一个对应的模板对参数的类型进行推导。`auto`关键字对应模板中的参数`T`，而整个变量的类型对应`ParamType`，参看下面的例子：

```C++
auto num = 63;              // 类型说明符为auto
const auto c_num = num;     // 类型说明符为const auto
const auto& r_num = c_num;  // 类型说明符为const auto&

// 它们对应下列的模板
template<typename T>
void func_for_num(T param)  // T的类型为int，ParamType为int -> auto为int

template<typename T>
void func_for_c_num(const T param)  // T的类型为int，ParamType为const int -> const auto为const int

template<typename T>
void func_for_r_num(const T& param)  // T的类型为int（常量性被忽略），ParamType为const int& -> const auto&为const int&
```


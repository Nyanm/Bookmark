
<!--# C++ Template 进阶指南(https://github.com/wuye9036/CppTemplateTutorial)读书笔记-->

## 1. 基本语法

模板类可看作一种可替换类型的“未完成类”，当传入类型参数进行类型绑定时，就生成了一个“普通的类”，称之为模板实例化（Template Instantiate）。

模板类的变量调用成员函数时需要得到完整的函数定义，因此成员函数多以内联方式实现（即写在定义处，更进一步地，写在.h文件中）。

## 3. 偏特化

一个折叠表达式实现获取指定类型在类型列表中位置的方法：

``` c++
constexpr size_t invalidTypeIndex = std::numeric_limits<size_t>::max();  
  
template<typename ...T>  
struct get_remaining_index;  
  
template<typename Target, typename Head, typename... Remain>  
struct get_remaining_index<Target, Head, Remain...> {  
    static constexpr size_t remain_index = std::is_same<Target, Head>::value ?  
                                           sizeof...(Remain) : get_remaining_index<Target, Remain...>::value;  
};  
  
template<typename Target>  
struct get_remaining_index<Target> {  
    static constexpr size_t remain_index = invalidTypeIndex;  
};  
  
template<typename Target, typename... TypeList>  
constexpr size_t getTypeIndex() {  
    return get_remaining_index<Target, TypeList...>::value == invalidTypeIndex ?  
           invalidTypeIndex : sizeof...(TypeList) - get_remaining_index<Target, TypeList...>::value - 1;  
}  
  
template<typename Target, typename... TypeList>  
constexpr bool getTypeValidity() {  
    return get_remaining_index<Target, TypeList...>::value == invalidTypeIndex;  
}
```

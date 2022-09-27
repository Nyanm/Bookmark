
# Mechanism: Template
<!--# C++ Template 进阶指南(https://github.com/wuye9036/CppTemplateTutorial)读书笔记-->

## 1. 基本语法

### 模板类

模板类可看作一种可替换类型的“未完成类”，当传入类型参数进行类型绑定时，就生成了一个“普通的类”，称之为模板实例化 (Template Instantiate)。声明语句`template <typename Type>`中，关键字`typename`亦可写作`class`，代表着参数Type是一种待传入的类型。

模板类的变量调用成员函数时需要得到完整的函数定义，因此成员函数多以内联方式实现（即写在定义处，更进一步地，写在.h文件中）。

### 自动推导类型

即使在不显式声明的情况下，编译器也可以通过传入参数推导部分模板参数的类型，需要注意的是：
	1. 多个参数由一个类型决定时这些参数类型必须一致
	2. 返回类型必须显式声明
	3. 传入类型参数时，显式声明的类型必须写在自动推导的类型前

### 整型模板参数

除类型外，模板参数也可以是整型数，包括布尔值、不同位数`int{N}_t`、不同符号 `{un}signed`、甚至指针`T{*}`。由于C++的模板匹配在编译期完成，因此传入的整形模板参数也需要能在编译期确定。

## 2. 模板元编程

### 特化

特化 (Specialization) 根据传入模板参数的整数或类型，映射到相应模板实例化内容。实现模板特化时，首先需要声明模板的一般形式（空模板），随后要根据对应的整数或类型给出实例；在调用时，就会根据传入的模板参数进行相应的匹配，执行相应特例化的逻辑；如果匹配失败，则会执行一般形式的逻辑。

下面是一个模板特化的例子。首先声明一个空模板作为其匹配失败的逻辑：

```C++
template <typename Fruit>
bool analyzeFruit(Fruit& fruit, Result& result){
	return false;
}
```

该一般形式定义了一个模板参数`Fruit`，之后进行特例化的类型都是该模板的一个实例：

```C++
template <>
bool analyzeFruit<Banana>(Banana& fruit, Result& result){
	result.weight = fruit.height;
	result.comment = "I'm a banana!";
	return true;
}

template <>
bool analyzeFruit<Coconut>(Coconut& fruit, Result& result){
	result.texture = fruit.fur;
	result.hardness = HARDNESS_INFINITE;
	return true;
}
```

在特化模板的声明语句中，由于该函数是一个模板函数，所以其声明前要添加`template`关键字；同时该函数是一个针对`Banana`类的一个实例，实参列表要加入相应的类型；而此时模板的类型已经被声明，也就无需传入相关的参数，所以`template <>`只需要留一对尖括号即可。

### 模板匹配

模板参数接受任意一个C++能够合法解析的类型，包括但不限于`void*`指针，`volatile`关键字修饰的类型等。模板匹配的通用规则是从特殊到一般，先会去尝试匹配最符合当下类型特征的模板参数（如指针），再去匹配更普适的那些。比如，传入一个指针类型`vector*`时会优先去匹配`bool traversal<T*>`的模板函数，之后才回去匹配不带指针运算符的`bool traversal<T>`。`typename`关键字用于告知编译器其修饰的名称是一个类型名，用以在模板的使用过程中显式地区分变量与类型。

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

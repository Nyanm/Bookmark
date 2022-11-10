
自模板诞生起，类型推导机制就成为了C++的一块基石，润泽了C++11推出的诸多自动类型推导功能，将程序员将类型名的沉疴中解放出来。作为类型推导的核心，模板类型推导已经在学习模板的过程中了解了不少，但Effective C++仍提供了一些有趣的内容。

从一个简单的模板看起：

```C++
template<typename T>
void fun(const T& param);
```

如果我们传入模板参数`int`，那么显然类型`T`会被推导为`int`；相应的，`param`的类型会被推导为`const& int`。当然在实际使用中模板类型匹配的情况会更加复杂，`T`的类型不仅取决于传入模板参数的类型，还取决于`ParamType`，即`param`的类型。根据`paramType`的类型，主要分为以下三种情况：

### 1. `ParamType`是一个指针或引用，但不是通用引用(&&)

如果设置以下模板函数与变量：

```C++
template<typename T>
void fun(T& param);

int val = 10;            // T推导为int，ParamType推导为const int&
const int c_val = val;   // T推导为const int，ParamType推导为const int&
const int& r_val = val;  // T推导为const int，ParamType推导为const int&
```

在后两个例子中传入的参数都被`const`修饰，而形参是实参的引用，因此也保留了参数的**常量性**(constness)，被保留为了`T`的一部分，最终被推导为`const int`。

通常情况下不能直接定义引用的引用，但是通过别名或者模板类型参数却可以绕过这个限制，但也因此产生了对引用的**折叠**。如例三中传入了一个对`const int`的引用，但是推导`T`的类型时`r_val`的引用性(reference-ness)却被忽略了。这种情况也会出现在下文提到的，`ParamType`为通用引用的情况中。

折叠规则可以简单表示为下：
- `val& &`，`val& &&`， `val&& &`会被折叠为`val&`
- `val&& &&`会被折叠为`val&&`

同样地，如果将`const`修饰符也加入到模板函数中：

```C++
template<typename T>
void fun(const T& param);

int val = 10;            // T推导为int，ParamType推导为const int&
const int c_val = val;   // T推导为int，ParamType推导为const int&
const int& r_val = val;  // T推导为int，ParamType推导为const int&
```

可以看到传入变量的常量性也像引用性一样被忽略了，这种折叠方式是模板类型推导的特性。类似的，如果`ParamType`是一个指针也会发生类似的情况：

```C++
template<typename T>
void fun(T* param);

int val = 10;             // T推导为int，ParamType推导为int*
const int* p_val = val;   // T推导为const int，ParamType推导为const int*
```

### 2. `ParamType`是一个通用引用(&&)

如果`ParamType`是一个通用引用，那么当传入参数为左值时`ParamType`会被推导为左值，传入参数为右值时`ParamType`会被推导为右值，这也是通用引用的通用所在。根据上文中的折叠规则也能得出相同的结论。设置以下模板函数与变量：

```C++
template<typename T>
void fun(T&& param);

int val = 10;            // val是左值，故T被推导为int&，ParamType被推导为int&
const int c_val = val;   // c_val是左值，故T被推导为const int&，ParamType被推导为int&
const int& r_val = val;  // r_val是左值，故T被推导为const int&，ParamType被推导为int&
fun(11);                 // 27为右值，T被推导为int，ParamType是右值引用int&&
```

### 3. `ParamType`既不是指针也不是引用

这种情况下更为容易理解，参数会通过传值(pass-by-value)的方式传进来一份拷贝，在此期间对参数的所有修饰都会被忽略（包括前文没有提到的`volatile`），因为对对象的拷贝并不具备原对象的有关性质。设置以下模板函数与变量：

```C++
template<typename T>
void fun(T param);

int val = 10;            // T和ParamType都被推导为简单的int
const int c_val = val;   // 同上
const int& r_val = val;  // 同上
```

但是当传入参数是一个指向`const`对象的`const`指针时，情况还会变得有些绕，但本质上是一样的：

```C++
template<typename T>
void fun(T param);

const char* const ptr = "mole";  // T和ParamType被推导为const char*
```

由于是传值的方式传入参数，因此指针指向的内容的每一个比特都会被原样拷贝进`param`，其常量性自然也会被保留；但此时`param`成为了新的指针，来自`ptr`的常量性自然就会被忽略。

### 数组实参与函数实参

在模板类型推到中，传入的数组与函数会退化成相应类型的指针；更明确地说，数组会退化成指向其第一个元素的指针，而函数会退化成相对应的函数指针。对于数组来说还有一个例外，在形参接受一个引用时，数组又会被正确地推导。见下面的例子：

```C++
template<typename T>
void fun(T param);

const char slogan[] = "mole";  // 数组被退化为const char*，T被推导为该类型
void anotherFun(int, float);   // T和ParamType被推导为函数指针void(*)(int, float)


template<typename T>
void fun_ref(T& param);

const char slogan[] = "mole";  // T被正确推导为const char[5]，同时ParamType被推导为const char&[5]
void anotherFun(int, float);   // ParamType被推导为函数指针的引用void(&)(int, float)
```

可以借助推导出数组的机制创造出一个推导数组大小的模板：

```C++
template<typename T, std::size_t Size>
constexpr std::size_t arraySize(T(&)[Size]) noexcept { return Size; }
```

在该模板函数中，参数`T`会被推导为传入数组的引用，相应的，`Size`会被推导为该数组的长度，并且作为返回值进行返回。
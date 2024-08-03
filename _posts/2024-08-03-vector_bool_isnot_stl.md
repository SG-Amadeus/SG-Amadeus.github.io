---
title: vector_bool不是STL容器
author: SG-Amadeus
date: 2024-08-03 15:50:00 +0800
categories: [CPP, CppTraps]
tags: [vector_bool]
---
# `vector<bool>`不是STL容器



## 为什么`vector<bool>`不是STL容器

是否为 STL 容器，C++ 标准库中有明确的判断条件，其中一个条件是：如果 cont 是包含对象 T 的 STL 容器，且该容器中重载了 [ ] 运算符（即支持 operator[]），则以下代码必须能够被编译：

```cpp
T *p = &cont[0];
```

此行代码的含义是，借助 `operator[]` 获取一个 `cont<T>` 容器中存储的 T 对象，同时将这个对象的地址赋予给一个 T 类型的指针。

 `std::vector<bool>` 底层采用了独特的存储机制：为了节省空间，`std::vector<bool>` 底层在存储各个 bool 类型值时，每个 bool 值都只使用一个比特位（二进制位）来存储。也就是说在 vector `<bool>` 底层，一个字节可以存储 8 个 bool 类型值。

 由于位压缩的存在，`std::vector<bool>` 的 `operator[]` 返回的是一个特殊的引用类型 `std::vector<bool>::reference` 而不是 `bool&`。故它没有满足所有*容器*(Container)或*序列容器* (SequenceContainer)的要求。

 严格意义上讲，`std::vector<bool>` 并不是一个 STL 容器；将 `std::vector<bool>`当作容器使用可能导致编译时或运行时错误。

## `vector<bool>`的潜在隐患

假设我们有一个函数，该函数返回一个 `std::vector<bool>`，代表一系列的开关状态。我们想要检查第二个开关是否开启，并基于此执行一些操作：

```cpp

std::vector<bool> switch_states();


voidtoggle_light(boolis_on) {

    if (is_on) {

        std::cout <<"Turning on the light."<< std::endl;

    } else {

        std::cout <<"Turning off the light."<< std::endl;

    }

}


intmain() {

    auto states = switch_states();

    bool second_switch_on = states[1];

    toggle_light(second_switch_on);  // 正确的行为：传递一个bool值

}

```

以上代码将按预期工作。

但是，如果我们决定使用 auto 来声明 second_switch_on 并简化代码：

```cpp

auto second_switch_on = states[1];

toggle_light(first_switch_on);  // 可能导致未定义行为

```

在这种情况下，second_switch_on 实际上是 `std::vector<bool>`::reference 类型，而不是 bool。这可能会导致 toggle_light 函数接收到非 bool 类型的参数，从而产生未定义行为。

实际过程中，如果我们按照std::vector `<bool>`是容器的思路去写代码，很可能会导致编译时或者运行时错误。

## 替代方案

那么，如果在实际场景中需要使用 `std::vector<bool>` 这样的存储结构，该怎么办呢？很简单，可以选择使用 `std::deque<bool>` 或者 `bitset` 来替代 `std::vector<bool>`。

要知道，deque 容器几乎具有 vecotr 容器全部的功能（拥有的成员方法也仅差 reserve() 和 capacity()），而且更重要的是，deque 容器可以正常存储 bool 类型元素。

若位集的大小在编译时已知，可使用 `std::bitset` ，它提供一组更丰富的成员函数。

另外[boost::dynamic_bitset](http://www.boost.org/doc/libs/release/libs/dynamic_bitset/dynamic_bitset.html) 作为 `std::vector<bool>` 的替用者存在。

当然，[ vector 的 Boost.Container 版本](https://www.boost.org/doc/libs/1_69_0/doc/html/boost/container/vector.html)不对 `bool` 特化。

## `vector<T>`引发的惨案

既然知道 `std::vector<bool>`不是STL容器，那么避开所有的雷点也就不是难事，甚至可以避免使用它，转而使用其他替代方案。非也！当我们使用 `vector<T>`模板时，容易踩坑的地方是模板函数里的任意类型T，要小心 `T = bool`。

```cpp

// T = int 时是对的， T = bool 时是错的

template<typenameT>

voidparallel_not(std::vector<T>&v) {

     int n = v.size();

     for(int i = 0; i < n; ++i) {

         v[i] = !v[i];

     }

}

```

### 如何防止 `std::vector<bool>` 的特化

答案是使用模板特化：使用模板特化处理除bool类型之外的其他类型，并对bool类型进行特殊处理。

```cpp

template <typenameT>

structanything_but_bool {

    typedef T type; // 对于非bool类型的T，type将保持为T

};


template <>

structanything_but_bool<bool> {

    typedefchar type; // 对于bool类型，type将被替换为char类型

};

```

anything_but_bool 是一个模板特化结构。对于大多数类型T（除了bool），它简单地将type定义为T。但是当T是bool时，它将type定义为char。

```cpp

template <typenameT>

classyour_class {

    std::vector<typename anything_but_bool<T>::type> member;

};

```

对于非bool类型的T：

    如果T不是bool类型，那么`anything_but_bool<T>::type`就是T，因此member将是一个 `std::vector<T>`。

对于bool类型的T：

    如果T是bool类型，那么`anything_but_bool<bool>::type`就是char，因此member将是一个 `std::vector<char>`。

这样便避免了使用 `std::vector<bool>`的坑。

当然还有其他方法可以避免该情况发生，这个坑以后再填。

## 相关参考

[`std::vector<bool>`](https://en.cppreference.com/w/cpp/container/vector_bool)


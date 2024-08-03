---
title: vector<bool>不是STL容器
author: SG-Amadeus
date: 2024-08-03 15:50:00 +0800
categories: [CPP, CppTraps]
tags: [vector<bool>]
---
# vector `<bool>`不是STL容器

[std::vector `<bool>`](https://en.cppreference.com/w/cpp/container/vector_bool)

## 为什么vector `<bool>`不是STL容器

是否为 STL 容器，C++ 标准库中有明确的判断条件，其中一个条件是：如果 cont 是包含对象 T 的 STL 容器，且该容器中重载了 [ ] 运算符（即支持 operator[]），则以下代码必须能够被编译：

```cpp

T *p = &cont[0];

```

此行代码的含义是，借助 `operator[]` 获取一个 `cont<T>` 容器中存储的 T 对象，同时将这个对象的地址赋予给一个 T 类型的指针。

 `std::vector<bool>` 底层采用了独特的存储机制：为了节省空间，`std::vector<bool>` 底层在存储各个 bool 类型值时，每个 bool 值都只使用一个比特位（二进制位）来存储。也就是说在 vector `<bool>` 底层，一个字节可以存储 8 个 bool 类型值。

 由于位压缩的存在，`std::vector<bool>` 的 `operator[]` 返回的是一个特殊的引用类型 `std::vector<bool>::reference` 而不是 `bool&`。故它没有满足所有*容器*(Container)或*序列容器* (SequenceContainer)的要求。

 严格意义上讲，`std::vector<bool>` 并不是一个 STL 容器；将 `std::vector<bool>`当作容器使用可能导致编译时或运行时错误。

## vector `<bool>`的潜在隐患

假设我们有一个函数，该函数返回一个 std::vector `<bool>`，代表一系列的开关状态。我们想要检查第二个开关是否开启，并基于此执行一些操作：

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

在这种情况下，second_switch_on 实际上是 std::vector `<bool>`::reference 类型，而不是 bool。这可能会导致 toggle_light 函数接收到非 bool 类型的参数，从而产生未定义行为。

实际过程中，如果我们按照std::vector `<bool>`是容器的思路去写代码，很可能会导致编译时或者运行时错误。

## 替代方案

那么，如果在实际场景中需要使用 `std::vector<bool>` 这样的存储结构，该怎么办呢？很简单，可以选择使用 `std::deque<bool>` 或者 `bitset` 来替代 `std::vector<bool>`。

要知道，deque 容器几乎具有 vecotr 容器全部的功能（拥有的成员方法也仅差 reserve() 和 capacity()），而且更重要的是，deque 容器可以正常存储 bool 类型元素。

若位集的大小在编译时已知，可使用 `std::bitset` ，它提供一组更丰富的成员函数。

另外[boost::dynamic_bitset](http://www.boost.org/doc/libs/release/libs/dynamic_bitset/dynamic_bitset.html) 作为 `std::vector<bool>` 的替用者存在。

当然，[ vector 的 Boost.Container 版本](https://www.boost.org/doc/libs/1_69_0/doc/html/boost/container/vector.html)不对 `bool` 特化。

## vector `<T>`引发的惨案

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

### 如何防止 std::vector `<bool>` 的特化

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

[std::vector `<bool>`](https://en.cppreference.com/w/cpp/container/vector_bool)

## 如何避免使用vector `<bool>`

**二、std::vector `<bool>`底层源码分析**

`std::vector<bool>`，是类 `sd::vector<T,std::allocator<T>>` 的部分特化，为了节省内存，内部实际上是按bit来表征bool类型。从底层实现来看，`std::vector<bool>` 可视为动态的 `std::bitset`，只是接口符合 `std::vector`，换个名字表达为 `DynamicBitset` 更为合理。

**三、解决方案**

那么，如果在实际场景中需要使用 vector `<bool>` 这样的存储结构，该怎么办呢？很简单，可以选择使用 deque `<bool>` 或者 bitset 来替代 vector `<bool>`。

要知道，deque 容器几乎具有 vecotr 容器全部的功能（拥有的成员方法也仅差 reserve() 和 capacity()），而且更重要的是，deque 容器可以正常存储 bool 类型元素。

和 vector 容器不同的是，bitset 的大小在一开始就确定了，因此不支持插入和删除元素；另外 bitset 不是容器，所以不支持使用迭代器。

### what

---

做为一个STL容器，vector确实只有两个问题。第一，它不是一个STL容器。第二，它并不容纳bool。 除此以外，就没有什么要反对的了。

### how

---

如果c是一个T类型对象的容器，且c支持operator[]， 那么以下代码必须能够编译：

```

T *p = &c[0]; // 无论operator[]返回什么， 都可以用这个地址初始化一个T*

```

### why

---

如果你使用operator[]来得到Container中的一个T对象，你可以通过取它的地址而获得指向那 个对象的指针。（假设T没有倔强地重载一些操作符。）然而如果vector是一个容器，这段代码必须能 够编译:

```

vector v; bool *pb = &v[0]; 

// 用vector::operator[]返回的东西的地址初始化一个bool*

```

它不能编译。因为vector是一个伪容器，并不保存真正的bool，而是打包bool以节省空间。在一个典 型的实现中，每个保存在“vector”中的“bool”占用一个单独的比特，而一个8比特的字节将容纳8 个“bool”。在内部，vector使用了与位域（bitfield）等价的思想来表示它假装容纳的bool。

### 替代品

---

第一个是deque。deque提供了几乎所有vector所 提供的（唯一值得注意的是reserve和capacity），而deque是一个STL容器，它保存真正的bool值。当 然，deque内部内存不是连续的。所以不能传递deque中的数据给一个希望得到bool数组的C API。

第二个vector的替代品是bitset。bitset不是一个STL容器，但它是C++标准库的一部分。与STL容器不 同，它的大小（元素数量）在编译期固定，因此它不支持插入和删除元素。此外，因为它不是一个STL容 器，它也不支持iterator。但就像vector，它使用一个压缩的表示法，使得它包含的每个值只占用1bit,它提供vector特有的flip成员函数，还有一系列其他操作位集（collection of bits）所特有的成员函 数。如果不在乎没有迭代器和动态改变大小，你也许会发现bitset正合你意。

vector `<bool>`并不是一个STL容器，不是一个STL容器，不是一个STL容器！

首先**vector< bool> 并不是一个通常意义上的[vector容器](https://www.zhihu.com/search?q=vector%E5%AE%B9%E5%99%A8&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A148258487%7D)**

，这个源自于历史遗留问题。 早在[C++98](https://www.zhihu.com/search?q=C%2B%2B98&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A148258487%7D)的时候，就有vector< bool>这个类型了，但是因为当时为了考虑到节省空间的想法，所以vector< bool>里面不是一个[Byte](https://www.zhihu.com/search?q=Byte&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A148258487%7D)一个Byte储存的，它是一个bit一个bit储存的！

因为C++没有直接去给一个bit来操作，所以用operator[]的时候，正常容器返回的应该是一个对应元素的引用，但是对于vector<

 bool>实际上访问的是一个"proxy reference"而不是一个"true

reference"，返回的是"std::vector< bool>:reference"类型的对象。 而一般情况情况下

```text

vector<bool> c{ false, true, false, true, false }; 

bool b = c[0]; 

auto d = c[0]; 

```

对于b的初始化它其实暗含了一个隐式的类型转换。

而对于d，它的类型并不是bool，而是一个vector< bool>中的一个内部类。

而此时如果修改d的值，c中的值也会跟着修改

```text

d = true;

for(auto i:c)

    cout<<i<<" ";

cout<<endl;

//上式会输出1 1 0 1 0

```

而如果c被销毁，d就会变成一个[悬垂指针](https://www.zhihu.com/search?q=%E6%82%AC%E5%9E%82%E6%8C%87%E9%92%88&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A148258487%7D)

，再对d操作就属于未定义行为。

而为什么说vector< bool>不是一个标准容器，就是因为它不能支持一些容器该有的基本操作，诸如取地址给[指针初始化](https://www.zhihu.com/search?q=%E6%8C%87%E9%92%88%E5%88%9D%E5%A7%8B%E5%8C%96&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A148258487%7D)

操作

```text

vector<bool> c{ false, true, false, true, false }; 

&tmp = c[0];    //错误，不能编译，对于引用来说，因为c[0]不是一个左值 

bool *p

```

```text

 = &c[0];   //错误，不能编译，因为无法将一个临时量地址给绑定到指针 ``` 

```

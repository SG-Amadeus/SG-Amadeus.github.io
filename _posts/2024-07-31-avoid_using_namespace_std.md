---
title: 在头文件里避免使用`using namespace std`
description: Examples of text, typography, math equations, diagrams, flowcharts, pictures, videos, and more.
author: SG-Amadeus
date: 2024-07-31 14:55:00 +0800
categories: [CPP, CppTraps]
tags: [using namespace std]
---

# 在头文件里避免使用`using namespace std`

在C++编程中，`using namespace std`是一个常见的语句，用于引入标准命名空间std，以便在代码中直接使用std命名空间中的标识符，而无需使用前缀`std::`。但在实际开发中，建议避免在头文件里使用该语句。在函数或代码块的局部范围内，可以适当使用该语句。

通俗的来说，当你在编程时，`using namespace std`;这条语句就像是：我希望编译器能把标准库里的所有工具和功能都直接放到工作台上，这样我就可以不用每次都写`std::`这个前缀了。 这似乎听起来很方便。但是你的工作台堆满了各种各样的工具，你可能找不到自己想要的东西了，因为它们都被这些的家伙挤占了位置。比如，你可能自定义了一个`min`的函数，但在标准库里也有一个`min`函数。当你写了`using namespace std`之后，编译器就不知道你提到的`min`到底是你自定义还是标准库的，这就可能导致你的程序出错。

如果你在头文件里写了`using namespace std`，那么任何使用这个头文件的人也会受到同样的困扰:他们想用的函数名和标准库里的重复了。不仅仅是`std`如果你的头文件（*.h、*.hpp）有被外部使用，则不要使用任何 using 语句引入其他命名空间或其他命名空间中的标识符。因为这可能会给使用你的头文件的人添麻烦。

所以，大多数程序员会建议你在自己的小角落里——也就是某个函数或代码块里面——小心地使用std::前缀，或者只用using声明引入你真正需要的几个工具，又或者局部使用`using namespace std;`，最小化`using namespace std;`的范围，这样就不会搞混了。

示例代码：
```cpp
#include <iostream>

//using namespace std;   //不建议使用

//使用std::前缀
void print_1(int num) {
    std::cout << "print_1" << num << std::endl;
}

//使用using声明
void print_2(int num) {
    using std::cout;
    using std::endl;
    cout << "print_2" << num << endl;
}

//
void print_2(int num) {
    using namespace std;
    cout << "print_2" << num << endl;
}
int main() {
    int num= 1;
    processInput();
    return 0;
}
```


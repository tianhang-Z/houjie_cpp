![image-20250610211600836](./image/STL-standard template library_image/image-20250610211600836.png)

cpp标准库和STL不同，后者是标准模板库。

1. **STL（Standard Template Library）**
   - 容器（`vector`, `map`, `set`）
   - 迭代器（`iterator`）
   - 算法（`sort`, `find`）
   - 函数对象（`function`, `bind`）
2. **非 STL 组件**
   - 输入/输出（`<iostream>`, `<fstream>`）
   - 多线程（`<thread>`, `<mutex>`）
   - 智能指针（`<memory>` 中的 `unique_ptr`, `shared_ptr`）
   - 字符串处理（`<string>`）
   - 数值计算（`<numeric>`, `<random>`）

### STL体系介绍 

![image-20250611142252845](./image/STL-standard template library_image/image-20250611142252845.png)



<img src="./image/STL-standard template library_image/image-20250611173759505.png" alt="image-20250611173759505" style="zoom:67%;" />

### 容器分类和测试

参考课件代码test_array等

![image-20250624212005451](./image/STL-standard template library_image/image-20250624212005451.png)

#### array

![image-20250624195840333](./image/STL-standard template library_image/image-20250624195840333.png)

 #### vector

测试发现 

* 扩容按照2倍的方式
* 不管vector的元素是否实现了移动构造和赋值函数，**对vector使用移动构造和swap的效率很高**；原因在于，**vector的底层维护的是动态数组的起始指针，对vector使用移动构造只需要交换指针**。同理，对两个vector进行swap的效率也很高，这是因为swap使用移动语义实现，调用了vector的移动赋值和构造函数。
* 当vector的**元素是包含指针的类对象时，若类实现了移动语义，则vector扩容时**，在新空间构造元素时**会利用元素的移动语义**，避免了拷贝构造的操作，从而更快。
* 对vector使用快排qsort和binary_search的速度未必比std::find快，原因在于快排耗时。
* 使用vector的insert时

```shell
how many elements: 100000000

test_vector().......... 
capacity change from 0to 1
capacity change from 1to 2
capacity change from 2to 4
capacity change from 4to 8
capacity change from 8to 16
capacity change from 16to 32
capacity change from 32to 64
capacity change from 64to 128
capacity change from 128to 256
capacity change from 256to 512
capacity change from 512to 1024
capacity change from 1024to 2048
capacity change from 2048to 4096
capacity change from 4096to 8192
capacity change from 8192to 16384
capacity change from 16384to 32768
capacity change from 32768to 65536
capacity change from 65536to 131072
capacity change from 131072to 262144
capacity change from 262144to 524288
capacity change from 524288to 1048576
capacity change from 1048576to 2097152
capacity change from 2097152to 4194304
capacity change from 4194304to 8388608
capacity change from 8388608to 16777216
capacity change from 16777216to 33554432
capacity change from 33554432to 67108864
capacity change from 67108864to 134217728
micro-seconds : 9552166
vector.max_size()= 288230376151711743
vector.size()= 100000000
vector.front()= 201083078
vector.back()= 487522885
vector.data()= 0x7fd91dd6e010
vector.capacity()= 134217728
vector last value 487522885
target (0~2147483647):    0
std::find(), micro-seconds : 715853
not found! 

sort(), micro-seconds : 47167972
bsearch(), micro-seconds : 5
not found! 



test, with moveable elements
construction, micro-seconds : 13885425
size()= 100000000
8MyString -- 
 CCtor=0 MCtor=234217727 CAsgn=0 MAsgn=0 Dtor=234217727 Ctor=100000000 DCtor=0
copy, micro-seconds : 3632080
move copy, micro-seconds : 2
swap, micro-seconds : 0


test, with non-moveable elements
construction, micro-seconds : 17951088
size()= 100000000
11MyStrNoMove -- 
 CCtor=234217727 MCtor=0 CAsgn=0 MAsgn=0 Dtor=234217727 Ctor=100000000 DCtor=0
copy, micro-seconds : 6065149
move copy, micro-seconds : 1
swap, micro-seconds : 1 
```

#### list

* 标准库提供了sort全局算法，list也提供了自己的sort算法。

#### forward_list

只能push_front，只能从头到尾遍历

### 源代码位置

GNU是一个开源组织，其提供了gcc等编译器，gcc提供了STL

/usr/include/c++/9，该路径下包含vector，list，mutex，memory等头文件。

**vector文件中包含#include <bits/stl_vector.h>，指向vector的实际实现。**

bits下包含stl_vector.h等 ，即vector的实现

![image-20250624214451323](./image/STL-standard template library_image/image-20250624214451323.png)

### OOP和GP(Genreric Programming)

* **list不能使用std:sort**

  `std::sort` 的典型实现（如快速排序、内省排序）依赖以下操作：

  ```c++
  // std::sort 内部可能使用的操作（伪代码）
  auto mid = begin + (end - begin) / 2;  // 计算中间位置（要求随机访问）
  std::swap(*begin, *(begin + 5));       // 直接跳转到第 5 个元素（要求随机访问）
  ```

**要求容器提供随机访问迭代器（Random Access Iterator）**。而 `std::list` 的迭代器是 **双向迭代器（Bidirectional Iterator）**，无法满足 `std::sort` 的要求

![image-20250624220204738](./image/STL-standard template library_image/image-20250624220204738.png)

![image-20250624220225247](./image/STL-standard template library_image/image-20250624220225247.png)

![image-20250624220401938](./image/STL-standard template library_image/image-20250624220401938.png)

![image-20250624220412272](./image/STL-standard template library_image/image-20250624220412272.png)

### 分配器


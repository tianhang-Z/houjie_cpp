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

gcc还会在/usr/include/x86*_64-linux-gnu/*搜索头文件。

`x86_64-linux-gnu` 表示 **64 位 x86 架构（AMD64/Intel 64）的 Linux 系统**，使用 GNU 工具链（如 GCC、glibc）。

gcc-9的C++头文件位置一般在/usr/include/c++/9，该路径下包含vector，list，mutex，memory等头文件。

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

### 分配器allocator

VC GNU-C是不同的编译器和工具链。前者是Microsoft的MSVC，后者是gcc/g++，他们有不同的标准库和STL的实现版本。

查看源码 可以知道 std::allocator最终调用了operator new，从而调用malloc 

下面是VC和GNU-C的不同实现的代码

##### VC如下![image-20250626112755638](./image/STL-standard template library_image/image-20250626112755638.png)

##### GNU-C2.9

![image-20250626172053836](./image/STL-standard template library_image/image-20250626172053836.png)

**GNU-C2.9中容器使用了alloc分配器**

![image-20250626172322652](./image/STL-standard template library_image/image-20250626172322652.png)

使用malloc分配的内存块，其头尾包含一些信息（比如块大小，块状态），malloc是动态内存分配器，底层需要管理空闲内存块，方法有：显示空闲链表、隐式空闲链表、分离链表等，这些方法的实现都用到了内存块头部信息。

而**GNU-C2.9实现了基于malloc的内存池分配器alloc，其实现如下**：下图是16种不同大小的空闲链表，0号链表内存块为8字节，之后的链表按8字节等差递增。alloc使用molloc申请了连续了内存块，然后将这些连续的内存块分割成空闲链表。

以vector为例，当其保存一百万个元素时，假设元素大小为56。使用下面的某个链表，为vector分配内存。于是vector的单个元素就没有头部信息了，而是一百万个元素共享malloc给出的大的内存块的头部信息。这个内存块分成了一百万份小内存块依次保存单个元素。好处是节约了内存，不需要为每个元素保存元素使用的小的内存块信息。

![image-20250626165501701](./image/STL-standard template library_image/image-20250626165501701.png)

##### GNU-C4.9

![image-20250626171654823](./image/STL-standard template library_image/image-20250626171654823.png)

**GNU-C4.9中alloc改名为pool_alloc**

![image-20250626172433753](./image/STL-standard template library_image/image-20250626172433753.png)

**GNU-C中容器使用的是stl::allocator 而不是pool_alloc**

但可以手动指定分配器为pool_alloc

![image-20250626172507668](./image/STL-standard template library_image/image-20250626172507668.png)

### sizeof component

```shell
test_components_size().......... 
sizeof(array<int,100>)= 400
sizeof(vector<int>)= 24
sizeof(list<int>)= 24
sizeof(forward_list<int>)= 8
sizeof(deque<int>)= 80
sizeof(stack<int>)= 80
sizeof(queue<int>)= 80
sizeof(set<int>)= 48
sizeof(map<int,int>)= 48
sizeof(multiset<int>)= 48
sizeof(multimap<int,int>)= 48
sizeof(unordered_set<int>)= 56
sizeof(unordered_map<int,int>)= 56
sizeof(unordered_multiset<int>)= 56
sizeof(unordered_multimap<int,int>)= 56
sizeof(_Rb_tree<...>)= 48
sizeof(array<int,100>::iterator)= 8
sizeof(vector<int>::iterator)= 8
sizeof(list<int>::iterator)= 8
sizeof(forward_list<int>::iterator)= 8
sizeof(deque<int>::iterator)= 32
sizeof(set<int>::iterator)= 8
sizeof(map<int,int>::iterator)= 8
sizeof(multiset<int>::iterator)= 8
sizeof(multimap<int,int>::iterator)= 8
sizeof(unordered_set<int>::iterator)= 8
sizeof(unordered_map<int,int>::iterator)= 8
sizeof(unordered_multiset<int>::iterator)= 8
sizeof(unordered_multimap<int,int>::iterator)= 8
sizeof(_Rb_tree<...>::iterator)= 8
sizeof(      std::allocator<string>)=1
sizeof(__gnu_cxx::malloc_allocator<string>)= 1
sizeof(__gnu_cxx::new_allocator<string>)= 1
sizeof(__gnu_cxx::__pool_alloc<string>)= 1
sizeof(__gnu_cxx::bitmap_allocator<string>)= 1
sizeof(__gnu_cxx::__mt_alloc<string>)= 1
sizeof(__gnu_cxx::array_allocator<int>)= 16
sizeof(__gnu_cxx::debug_allocator<std::allocator<double>>)= 16
```

### 深度探索list

G2.9版本的list只保存了一个node指针

#### 内部结构

![image-20250627164445320](./image/STL-standard template library_image/image-20250627164445320.png)

#### ⭐list_iterator

iterator的实现和使用 ，**（pointer-like class），包含一个node指针** ，**需要重载解引用和箭头操作符，前置++和后置++。** 

![image-20250627165328538](./image/STL-standard template library_image/image-20250627165328538.png)

后置++ 返回的是一个右值（临时对象）

下图中 `self temp=*this` 隐式转为 `self temp(*this)`，调用拷贝构造函数

![image-20250627165427516](./image/STL-standard template library_image/image-20250627165427516.png)

![image-20250627165211608](./image/STL-standard template library_image/image-20250627165211608.png)

#### G4.9对G2.9的改进

上述是G2.9，下面是G4.9；

改进1：iterator的别名更简单，__list_iterator只有一个模板参数TP

改进2：不再采用void*的类型的prev和next

![image-20250627170850970](./image/STL-standard template library_image/image-20250627170850970.png)

G4.9的实际实现更复杂，有一些继承和组成关系。

![image-20250627172248103](./image/STL-standard template library_image/image-20250627172248103.png)

#### 双向环状list，且包含一个空node

**所有的容器都是前闭后开区间，这样end()返回一个不属于容器的位置。**

![image-20250627171954532](./image/STL-standard template library_image/image-20250627171954532.png)

### 迭代器的设计原则和iterator trait的设计    

下图是一个rotate算法，箭头指示了算法的调用过程，其中`__iterator_category`萃取了iterator的类型（前进，后退，随机访问的，跳着走的等），`__rotate`还萃取了difference_type和value_type

下图RAI指的是 `__iterator_category`返回的是`random_access_iterator_tag`

iterator需要回答算法的这几个问题。

![image-20250627213906876](./image/STL-standard template library_image/image-20250627213906876.png)

#### ⭐ iterator相关的五种类型-便于回答算法的提问

其中difference_type指的是两个迭代器的距离（即元素个数）。是有符号整数类型。

bidirectional_iterator_tag指的是双向迭代器类型。

![image-20250627215049455](./image/STL-standard template library_image/image-20250627215049455.png)

由于iterator（迭代器）未必是个class，而可能是一个原生指针，因此需识别iterator是那种，并且回答相关的value_type等。

于是产生了Traits，萃取机。用来分离class iterator个non-class iterator

**![image-20250627220423068](./image/STL-standard template library_image/image-20250627220423068.png)**

#### iterator_traits

下面的2、3是特化的iterator_traits，当iterator是non class时，则会起作用。

![image-20250627220542216](./image/STL-standard template library_image/image-20250627220542216.png)

![image-20250627220820648](./image/STL-standard template library_image/image-20250627220820648.png)

### traits有很多种

![image-20250627221057447](./image/STL-standard template library_image/image-20250627221057447.png)

### 深度探索vector

vector是动态数组，维护三个迭代器变量，start，finish，end_of_storage。所以sizeof大小是8.

![image-20250627222846609](./image/STL-standard template library_image/image-20250627222846609.png)

#### 扩容

![image-20250628182205422](./image/STL-standard template library_image/image-20250628182205422.png)

![image-20250628182211416](./image/STL-standard template library_image/image-20250628182211416.png)

#### vector iterator

​	

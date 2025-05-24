## 头文件和类声明

### 类的两种设计

带指针和不带指针有区别

  ### 头文件的方位式声明

避免重复include

```c++
#ifdef __xxx__
#define __xxx__

#endif
```





####  类内定义的函数要短小 此时可作为内联函数候选（默认inline）

对于类的成员函数，如果在类内部定义（即直接在类体中实现），则该函数默认为`inline`候选，无需显式添加`inline`关键字。

但若在类外部定义成员函数，则需显式添加`inline`关键字。

<img src="image\侯捷-OOP总结_image\image-20241228125949653.png" alt="image-20241228125949653" style="zoom:67%;" />

#### 优先使用构造函数的列表初始化

<img src="image\侯捷-OOP总结_image\image-20241228130055818.png" alt="image-20241228130055818" style="zoom:67%;" />

#### 单例模式：构造函数用private保护，使用static成员函数调用构造函数

<img src="image\侯捷-OOP总结_image\image-20241228130716867.png" alt="image-20241228130716867" style="zoom:67%;" />

#### const成员函数，const修饰this指针时，才能定义const类对象。否则会有权限扩大问题

<img src="image\侯捷-OOP总结_image\image-20241228140719261.png" alt="image-20241228140719261" style="zoom:67%;" />

#### 传参by value和by ref ，思考是否用const和引用&，引用传递类时，相当于按指针传递

<img src="image\侯捷-OOP总结_image\image-20241229133029640.png" alt="image-20241229133029640" style="zoom:67%;" />
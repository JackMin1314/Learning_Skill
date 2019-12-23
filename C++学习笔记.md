[TOC]

# C++学习笔记

## 一、**C++基础知识**

### 1. C++容器(STL)

+ [容器(container)]( https://zh.cppreference.com/w/cpp/container ) 用于存放数据的类模板, 它们实例化后就成为容器类。用容器类定义的对象称为容器对象 。可变长数组、链表、平衡二叉树等数据结构在STL中都被实现为容器,内含有许多算法(函数模板)插入、排序、查找、删除等。
+ 容器管理为其元素分配的存储空间，并提供直接或间接地通过迭代器（拥有类似指针属性的对象）访问它们的函数。 
+ 容器中可以存放基本类型的变量 <u>( 整型、浮点型、字符型、布尔型、空类型 )</u> ，也可以存放 <u>对象</u> 。对象或基本类型的变量被插入容器中时，实际插入的是对象或变量的一个复制品。(不是引用，也不是指针，不同于Go里面的切片) 
+ 需要注意的是当存放自定义类型时 <u>( 枚举类型、数组类型、结构体类型、共同体类型、指针类型、类类型 )</u>,需要自己实现重载运算符 `== ` 和 `<`.
+ 容器的选择考虑各自的特点和适用场景以及效率功能等

#### 1.1 序列容器

 序列容器（Sequence containers）实现能按顺序访问的数据结构。有时也叫顺序容器，但个人认为会造成误解，元素不会自动排序，元素值和所在位置无关系）

容器含有的成员函数大都类似 size() 、empty()、at()、begin()、erase()、clear()、end()、front()、back()、push_back()、pop_back()等

+ **array**（C++11起）**静态的连续数组**

```c++
#include<array>
...
array<int, 10> arr1 ={1,2,3,4,6,5,4};// 后面默认为空值0

    for(int i=0;i<arr1.size();i++) {
        cout<<arr1.at(i)<<"IsEmpty?"<<arr1.empty()<<endl;// at会先进行越界检查，然后在访问该位置
    }
...
```

* **vector**  **向量是一个能够存放任意类型的动态数组** 

```c++
#include<vector>
...
vector<int> vet(5,0); // vector<int> vet;也是可以的
    for(int i=0;i<vet.size();i++) {
        cout<<vet[i]<<"IsEmpty?"<<vet.empty()<<endl;
    }
...
```

 vector只在额外内存耗尽时重分配(开销大，元素复制移动)，自动动态分配的内存总量可用 capacity() 函数查询.可以通过迭代器和指针去访问( 元素相继存储 )

> capacity 和 size 属性区别 :
>
> **1) size** 是当前 vector 容器真实占用的大小，也就是容器当前拥有多少个容器。
>
> **2) capacity** 是指在发生 realloc 前能允许的最大元素数，即预分配的内存空间
>
>  *vector*上的常见操作复杂度（效率）如下：
>
> 1) 随机访问——常数 *O(1)*
>
> 2) 在末尾插入或移除元素——均摊常数 *O(1)*
>
> 3) 插入或移除元素——与到 vector 结尾的距离成线性 *O(n)* （在尾部插入删除效率高）

虽然vector在插入和删除时候具有最差O(n)，但是由于顺序结构和存任何类型以及在尾部(推荐)快速插入高效、自动调整容量是优势；但是一旦涉及元素的位置移动和复制、头部插入等操作其代价也是很大。

* **deque** ( double-ended queue ) **双端队列，有下标顺序容器和支持在首尾快速插入删除的变长数组**

 所有适用于 vector 的操作都适用于 deque， 在 deque 中，随机存取任何元素都能在常数时间内完成（但慢于vector），优点是在两端插入删除操作性能比vector更好，且内存开销小，比vector多两个成员函数

```c++
void push_front (const T & val);  //将 val 插入容器的头部
void pop_front();  //删除容器头部的元素
```

```c++
#include<deque>
...
deque<int> deq = {1,2,3};
    deq.push_back(10); // 在尾部添加 
    deq.push_front(0); // 在首部添加
    for(int i:deq) // 迭代并打印deque值
    {
        cout <<i<<endl;
    }
...
```

* **forward_list** **单向链表从容器中的任何位置快速插入和移除元素的容器。不支持快速随机访问 **

 forward list内部实现是单链表，元素类型可以是任意指定的T ，行为受限(只能单向)，只提供了单向的迭代器iterator,一些成员函数没有，例如 不提供成员函数size() ，不提供类似back()等成员函数。但是更有效地利用空间的存储(list双向链表空间利用率很低)，插入移除元素效率高。

* **list** **双向链表从容器任何位置插入和移除元素的容器。不支持快速随机访问 ** 

  双向链表的每个元素中都有一个指针指向后一个元素，也有一个指针指向前一个元素.因而可以任意位置插入删除元素。此容器提供双向迭代但在空间利用上效率稍低 。有很多成员函数用法跟vector类似.

(list不支持快速随机访问，因而不能用sort算法对list容器排序而vector、deque可以，所以list干脆自己内置了sort排序）

```C++
#include<list>
...
list<int>list1={1,2,5,6,3,4};
    list1.sort();
    for(int n:list1) {
        cout<<n; // 输出的顺序是1 2 3 4 5 6有序的
    }
...
```

#### 1.2 关联容器

 关联容器(Associative containers)实现能快速查找或插入（ **O(log n)** 复杂度）的数据结构，内部的元素都是排好序的。

| set          | 唯一键的集合，按照键排序，不允许有相同元素               |
| ------------ | -------------------------------------------------------- |
| **map**      | **键值对的集合，按照键排序，键是唯一的不允许有相同元素** |
| **multiset** | **键的集合，按照键排序，允许有相同的元素**               |
| **multimap** | **键值对的集合，按照键排序，元素关键字允许重复**         |

* 修改上述容器的关键字需要先从容器中删除，在插入新的元素，关联容器内部用 `<` 比较大小,自动和已有元素比较查到合适位置。其次有序,故查找速度很快

*  关联容器set, multiset, map, multimap内部采用的就是一种非常高效的 **`平衡检索二叉树：红黑树`**，也成为RB树(Red-Black Tree)。RB树的统计性能要好于一般平衡二叉树，所以被STL选择作为了关联容器的内部结构。 

* 红黑树在平衡二叉树性质上要求更严格，操作复杂但非常高效。要想改变里面的节点，不能直接修改里面的元素，需要先删除在插入

  ---

> **红黑树**（自平衡）的 **特性**:
>
> 1. 根节点是黑色的
> 2. 所有叶子节点黑色的
> 3. 每个红色节点下面两个节点必须是黑色的
> 4. 所有路径不能出现两个连续的红色节点
> 5. 任意节点到叶子节点路径上黑色节点数相同
>
> ---
>
> **红黑树(RBT)** 和 **二叉查找树(BST)** 和 **自平衡二叉树(AVL)** 的**区别**:
>
> *二叉查找树* binary search tree形态不一，且最坏情况会退化成一个线性链表O(n); 而红黑树特性保证了其高度以及时间复杂度O(logn)
>
> *自平衡二叉检索树* AVL(人名缩写)算法虽然查找时间复杂度和红黑树一样，但是在删除一个节点并维护平衡时候最坏是O(n)，而红黑树还是O(1)最多调整不超过三次。对于大量的数据插入删除时候AVL会频繁Rebalance，开销大。 RBT读取略逊于AVL 
>
> 总言之， 搜索的次数**远远大于**插入和删除，那么选择AVL，如果搜索，插入删除次数几乎差不多，应该选择RB ；**频繁的插入删除**选择红黑树.
>
> ---
>
> **红黑树**相对于**哈希表**，在选择使用的时候怎么**抉择**？（hash和map的选择）（unordermap和map的区别）
>
>  考虑：查找速度, 内存使用，数据量，可扩展性 
>
> * **是否有序上**：hash不会根据key的大小进行排序，存储时是根据key的hash值判断元素是否相同，红黑树基于二叉平衡树，是有序的( 内部元素自动排序，使用map，不需要排序使用unordered_map )
> * **查找速度上**：hash查找速度会比map快，而且查找速度基本和数据量大小无关，属于常数级别 ， 而map     的查找速度是log(n)级别。hash还有hash函数的耗时，在元素达到一定数量级时，查找考虑hash。
> * **内存使用上**：当hash容量达到一定容量时，会进行rehash开销很大(而且速度较慢 ),而红黑树只是指针的指向和颜色的调整相对来说空间需求不大。
> * **可扩展性上**：对于动态变化的需要时使用红黑树会比hash具有更好的伸缩性;而一些静态数据比如表格使用hash会性能更高



#### 1.3 无序关联容器

 无序关联容器(Unordered associative containers)提供能快速查找（**均摊 *O(1)* **，**最坏情况 *O(n)* **的复杂度）的无序（哈希）数据结构。 

| unordered_set(C++11 起)         | 唯一键的集合，按照键生成散列                   |
| ------------------------------- | ---------------------------------------------- |
| **unordered_map(C++11起)**      | **键值对的集合，按照键生成散列，键是唯一的**   |
| **unordered_multiset(C++11起)** | **键的集合，按照键生成散列，允许有相同的元素** |
| **unordered_multimap(C++11起)** | **键值对的集合，按照键生成散列，允许键重复**   |

 在内部，元素并不以任何特别顺序排序，而是组织进桶中。元素被放进哪个桶完全依赖其值的哈希。这允许对单独元素的快速访问，因为哈希一旦完成，就准确指向元素被放入的桶。（关于hash的相关详细介绍见其他内容）

 不可修改容器元素（即使通过非 const 迭代器），因为修改可能更改元素的哈希，并破坏容器.

### 2. 相关名词

#### *extern*

C++关键字，用于变量或者函数声明前面。

1. 标示全局变量或者函数的定义在别的文件中，提示编译器在其他模块中找这个定义
2. 用于链接指定，与 ‘’C‘’ 连用。表示按照C语言的规则去翻译相应的函数。例如 `extern "C" void funcname(int a,int b);` 而不是用C++的规则去翻译(C++不同编译器的翻译规则和效率都不一样。例如 Visual C++、Intel C/C++ Compiler、gcc(windows下用MinGW较多，linux下g++) 等)

需要注意的是：1. extern的两个作用  2.extern需要严格对应声明的格式

> C++编译器是如何处理函数重载的问题 --> 将函数名和参数联合起来生成一个中间的函数名称。 但是C编译器不会，因为C语言不支持函数重载和先定义后声明。
>
> 声明变量的时候一般在头文件(\*.h)里面而不是源文件(\*.cpp)

#### *static*

C++关键字，用于修饰声明全局变量和函数并定义同时进行。表明变量或者函数生命周期贯穿整个程序。被编译器放在静态存储区而不是堆栈

> static有编译单元的概念，不能被不同的源文件调用，作用域只能是当前的源文件
>
> static一般定义全局变量时放在源文件(\*.cpp)而不是头文件(\*.h)

#### *explicit*

C++关键字，修饰只有一个参数的类构造函数或者转换函数, 它的作用是表明该构造函数是显示的, 而非隐式

> 如果类的构造函数只有一个参数时 或者 有n个参数前面n-1个确定了, 那么在编译的时候就会有一个缺省的转换操作: 将该构造函数对应数据类型的数据转换为该类的对象 
>
> 所谓隐式和显示转换就是指是否人为提醒编译器进行转换，而不是让编译器自己去进行类型转换：
>
> ```c++
> float ft = 3.14159;
> int integer = ft;// 隐式转换提示warning:implicit conversion turns floating-point number into integer: 'float' to 'int'
> int integer2 = int(ft);// 显示转换不会提示warning;C语言里面用int integer2 = (int)ft;
> ```

```c++
#include<iostream>
using namespace std;
class Student
{
public:
    Student(){

    }
    // 没有explicit默认就是隐式转换
    Student(int age){
        cout<<"it's "<<age<<" years old"<<endl;
    }
    Student(string name, int age){
        cout<<name <<" is "<<age<<" years old"<<endl;
    }

};
int main(){
    Student std1 = 25; // 隐式类型转换：将int类型转换成Student对象.
    Student std2 ("jack",25); // 显示类型转换
    return 0;
}
/*尝试修改Student(int age){}
class Student :
explicit Student(int age){...}
main() :
Student std1 = 25; // 报错！！
Student std2(25); // 成功，没有warning
*/
```

与explicit相对的是implicit，他们存在的意义是什么？有一点就是让开发者关注和知道这个值是什么类型的，避免让人疑惑,不合时宜的类型转换可能在开发过程会导致出错。

#### *inline*

C++关键字，用来修饰定义的，而不是声明。为了解决一些频繁调用小函数大量消耗栈空间(栈内存)的问题以及函数调用开销.栈空间就是放置程序局部数据(也就是函数内数据)的内存空间。

> 一般inline函数的定义放在头文件里面。仅仅是建议编译器做内联展开处理，而不是强制。能否内联展开还需要看编译器是否同意。
>
> ```c++
> #include <stdio.h>
> //函数定义为inline即:内联函数;函数很简短，执行时间相对于调用开销而言代价较小
> inline char* Parity_judgment(int a) {
>     return (i % 2 > 0) ? "奇" : "偶";
> } 
> 
> int main()
> {
>    int i = 0;
>    for (i=1; i < 100; i++) {
>        printf("i:%d    奇偶性:%s /n", i, Parity_judgment(i));// => i,(i % 2 > 0) ? "奇" : "偶"    
>    }
> }
> ```
>
> 
>
> 注意内联函数的使用限制:只适合简单的函数，其定义不能包含复杂的结构控制语句例如switch，while（原因见下）；且不允许内部调用自己；要与函数定义放在一起。

内联是以代码膨胀(内联展开)为代价，换取函数调用的开销

#### *typedef*  

C++关键字，用来定义数据类型的，来替代系统默认的基本类型名称、数组类型名称、指针类型名称与用户自定义的结构型名称、共用型名称、枚举型名称等；起了别名。

> 四种用法：
>
> 1. 为基本数据类型定义新的类型名 （解决跨平台类型范围的问题）
>
>    ```c++
>    // platform 1
>    typedef long double REAL;
>    // platform 2
>    typedef float REAL;
>    ```
>
> 2. 为自定义数据类型定义简洁的类型名称
>
>    ```c++
>    struct listNode
>    {
>        int Item;
>        struct listNode *pNext; // C 语言是允许在结构中包含指向它自己的指针的
>    };
>    typedef struct listNode *pNode;
>    ```
>
> 3. 为数组定义简洁的类型名称
>
>    ```c++
>    typedef int INT_ARRAY_100[100];
>    INT_ARRAY_100 arr;
>    ```
>
> 4. 为指针定义简洁的名称
>
>    ```c++
>    // PFun是我们创建的一个类型别名
>    typedef int *(*PFun)(int,char*);
>    // 使用定义的新类型来声明对象，等价于int*(*a[5])(int,char*);
>    PFun a[5];
>    ```
>
>    远不如Go简洁，清晰，难怪被吐槽~
>
> 与 #define 不同的区别：
>
> 1. typedef创建的字符串只限于类型不能用于值,#define可以 `#define PI 3.14159`, #define是字符串展开替换，typedef不是
> 2. typedef由编译器解释，#define语句是由预编译器进行展开替换处理
> 3. 总体上typedef比#define更加灵活
>
> typedef 后面不能加static、auto、extern等存储类关键字

#### template

C++关键字，泛型编程的基础；采用类型作为参数的程序设计，一个模板就是一个创建类或函数的公式 

**函数模板** 和 **类模板** （函数模板$\not=$模板函数;类模板$\not=$模板类）

> * 函数模板
>
> 函数模板能够用来创建一个通用的函数，函数使用的数据类型作为參数，以支持多种不同的形參。避免重载函数的函数体反复设计。
>
> 编译器会用函数实参来匹配模板的参数，然后实例化一个特定版本的函数
>
> ```C++
> #include<iostream>
> using namespace std;
> template<typename T> void mySwap(T &a, T &b){
>     T temp;
>     temp = a;
>     a = b;
>     b = temp;
> }
> int main(){
>     int a = 0,b = 1;
>     char ca = 'A',cb = 'B';
>     string sa = "AA",sb = "BB";
>     mySwap(a,b); // 此时a = 1，b = 0
>     mySwap(ca,cb); // 此时ca = B，cb = A
>     mySwap(sa,sb); // 此时sa = BB，sb = AA
>     return 0;
> }
> ```
>
> * 类模板
>
> 一个类模板允许用户为类定义一种模式。使得类中的某些数据成员、成员函数的參数、返回值，能够取随意类型（常见类型或者自定义）。
>
> （通常处理的是类中数据成员的数据类型不能确定，将它设置成类模板）
>
> 与函数模板不同，编译器不能为类模板推断模板参数类型;因而必须在模板名后的尖括号中提供信息正如 vector<int> 
>
> 对于类模板注意事项:
>
> 1. 首先要定义类
>
>    ```c++
>    template <class T> // 这里不要加';'
>    class myClass
>    {
>    public:
>        myClass():name("JackMin1314") {} // 构造函数初始化参数时必须使用初始化列表对【常量】初始化
>        void getDetail(T &a);
>    private:
>        T age;
>        const T name;
>        static T score;
>    };
>    ```
>
> 2. 类模板内部成员函数的参数是模板参数，当在外部定义成员函数时需要在前面加上模板类声明
>
>    ```c++
>    template <class T >
>    //void myClass::getDetail(T &a){} // 这里报错提示为"myClass is not a class,namespace,enumeration";因为myClass是一个模板类，没有具体实例化。
>    void myClass<T>::getDetail(T a){
>        age = "18";
>        cout<<a<<"->"<<name<<'\t'<<age<<'\t'<<score<<endl;
>    }
>    ```
>
> 3. 在类定义体外初始化const成员和static成员变量，需要在前面加上模板类声明
>
>    ```C++
>    template <class T>
>    T myClass<T>::score = "90";
>    ```
>
>    使用:
>
>    ```C++
>    int main(){
>        myClass<string > realClass;
>        realClass.getDetail("hello");
>        return 0;
>    }
>    ```

#### *友元机制*

允许一个类(class A)将其非公有成员(A.member1)的访问权授予指定的类(class B)或者函数(function C),同时仍阻止一般的访问的情况下,不是该类(class A)的成员，因而不受public ,private等访问控制影响，以friend开头,可以出现在类的任何地方,一般放在类定义的开始或结尾。

**友元函数**、**友元成员函数**、**友元类**

> * 友元函数
>
> 虽然不是该类的成员函数但是具有访问该类的所有成员的权利
>
> ```c++
> #include<iostream>
> using namespace std;
> class A
> {
> public:
>     friend void set_age(int x, A &a); // set_age()不是class A的成员函数;也不受访问控制影响，放在private也是可以的
>     void display();
>     A() {age = 0;}
> private:
>     int age;
> };
> void A::display(){
>     cout<<"it is "<<age<<" years old now."<<endl;
> }
> // set_age函数的定义
> void set_age(int x, A &a){
>     a.age = x;
> }
> int main(){
>     A a;
>     a.display(); // 0
>     set_age(10,a);
>     a.display(); // 10
>     return 0;
> }
> 
> ```
>
> * 友元类
>
> 友元类的所有成员函数都是另一个类的友元函数。（此时这个友元类可以存取另一个类的所有成员）
>
> *友元关系不能被继承和传递性，更不具有双向性*；关键要看在类中是否有相应的声明
>
> ```c++
> #include<iostream>
> using namespace std;
> class A
> {
> public:
>     friend class B; // 声明B是A的一个友元类
>     void display();
>     A() {age = 0;}
> private:
>     //friend void set_age(int x, A &a);
>     int age;
> };
> void A::display(){
>     cout<<"it is "<<age<<" years old now."<<endl;
> }
> 
> class B
> {
> public:
>     void set_age(int x, A &a); // 所有B的成员函数都可以访问A的所有成员
>     B() {}
> };
> // set_age函数的定义
> void B::set_age(int x, A &a){
>     a.age = x;
> }
> int main(){
>     A a;
>     B b;
>     a.display(); // 0
>     b.set_age(10,a);
>     a.display(); // 10
>     return 0;
> }
> ```
>
> * 友元成员函数
>
> 一个类的部分成员函数可以访问另一个类的所有成员。（需要注意友元声明和友元定义之间的相互依赖）
>
> ```c++
> #include<iostream>
> using namespace std;
> class A; // 加上A声明是为了B的定义需要
> class B
> {
> public:
>     void set_age(int x, A &a);
>     B() {}
> };
> //  这里不能立即定义set_age，因为要用到A里面的age
> class A
> {
> public:
>     //friend class B;
>     void display();
>     A() {age = 0;}
> private:
>     friend void B::set_age(int x, A &a); // 这里声明B中的一个成员函数set_age为A的友元函数
>     int age;
> };
> void A::display(){
>     cout<<"it is "<<age<<" years old now."<<endl;
> }
> // 需要获取A的age，因而必须在class A定义后面
> void B::set_age(int x, A &a){
>     a.age = x;
> }
> 
> int main(){
>     A a;
>     B b;
>     a.display();
>     b.set_age(10,a);
>     a.display();
>     return 0;
> }
> 
> ```
>

#### *函数指针和指针函数*

函数指针本质上是一个指针(指针自己在内存区域有自己的地址，内容保存的是指向一个变量、结构体、函数等的地址)，保存的是一个函数的地址

> 对于某些解释型语言，如Python有高阶函数之说，对于Golang更有"函数是一个值"的概念->函数的参数可以是函数，甚至返回值是一个函数。
>
> 但是C++天生不支持，要想实现类似的操作，必须采用函数指针的方式。
>
> 参见 [Github源码](https://github.com/JackMin1314/CPlusPlus_Learning/blob/master/SortCodes/bubble_quickSort.cpp) 统计一个程序运行时间getRuntime函数
>
> ```c++
> #include<iostream>
> #include<stdlib.h> // srand, rand
> #include<time.h>   // time, clock_t
> ...
> // 相关函数的声明
> void makeRandomNum(int* arr, unsigned int n);
> // getRuntime函数的参数是一个函数指针（将一个函数作为参数传递给另一个函数需要用到函数指针）
> double getRuntime(int* arr, unsigned int n, void (*pfunc)(int*, unsigned int));
> // 被调函数的声明和定义时，其参数需要给出函数指针的参数类型(int*,unsigned int)，具体值需要放在前面参数
> int main() {
>     ...
>     double duration = getRuntime(arr, n, makeRandomNum);
>     return 0;
> }
> // 被调函数的声明和定义时，其参数需要给出函数指针的参数类型(int*,unsigned int)，具体值需要放在前面参数
> double getRuntime(int* arr, unsigned int n, void (*pfunc)(int*, unsigned int)) {
>     start = clock();
>     pfunc(arr, n);// 这里就用到了实际函数所需要的参数了
>     finish = clock();
>     double t = double((finish - start)) / 1000;// 用CLOCKS_PER_SEC也是可以的
>     return t;
> }
> ```

指针函数本质上是一个函数，函数的返回值是一个指针. unsigned int *multifyValue(unsigned int &a,unsigned int &b)

> ```c++
> #include<iostream>
> using namespace std;
> 
> unsigned int sumValue(unsigned int a,unsigned int b)
> {
> return a + b;
> }
> // 这里用了函数指针
> void display(unsigned int a, unsigned int b,unsigned int (*pfunc)(unsigned int, unsigned int)){
>     cout<<"a = "<<a<<";"<<"b = "<<b<<endl;
>     unsigned int sum = pfunc(a,b);
>     cout<<"result: a + b = "<<sum<<endl;
> }
> // 这里用了指针函数
> unsigned int *multifyValue(unsigned int &a,unsigned int &b){
>     a =a*b;
>     return &a;
> }
> int main(){
>     unsigned int a = 1;
>     unsigned int b = 2;
>     display(a,b,sumValue); // 直接给出函数名即可
>     unsigned int pa = a;
>     unsigned int pb = b;
>     unsigned int *c = multifyValue(pa,pb);
>     cout<<"c的地址为:"<<c<<'\t'<<"c的值为:"<<*c<<endl;
>     return 0;
> }
> ```

#### *封装、继承、多态*

面向对象的三大特性：封装、继承、多态

> **1. 封装**（**代码**重用）
>
> 封装，也就是把客观事物封装成抽象的类，并且类可以赋予自己的数据和方法访问权限控制。
>
> 封装的目的：隐藏对象的属性和实现细节，仅对外公开接口和对象进行交互，代码模块化
>
> **2.继承**（**代码**重用）
>
> 可以让某个类型的对象获得另一个类型的对象的属性的方法并实现了扩展。继承可以扩展已存在的代码模块（类）
>
> 继承的概念实现方式有两种：
>
> 1. **实现继承**：是指直接使用基类的属性和方法而无需额外编码的能力；
> 2. **接口继承**：是指仅使用属性和方法的名称、但是子类必须提供实现的能力。
>
> **3.多态**（**接口**重用）
>
> 多态是指不同子类型的对象对同一行为作出不同的响应。是类与类之间的关系，是发生在不同的类之间的，子类重写父类的方法。实现不同的子类，不同的实现形态。（简单来说，接口重用！“一个接口，多种方法”）
>
> 多态有3个条件
> 1: **继承**
> 2: **重写**(重写父类继承的方法)【若子类和父类的某个函数具有相同的函数名，相同的形参列表，且父类中的函数被定义为虚函数，则子类对该函数的实现被称为函数的重写】
> 3: **父类引用指向子类对象**
>
> 注意
>
> **重载overload：**函数名相同，但是函数参数不同。调用时根据参数的不同决定调用哪一个函数。只是一种语言特性，与多态无关，与面向对象也无关。
>
> **重写override：** 若子类和父类的某个函数具有相同的函数名，相同的形参列表，且父类中的函数被定义为虚函数，则子类对该函数的实现被称为函数的重写。与多态有关。（晚绑定）
>
> *C++重载和多态的区别*  (无论从本质上还是表面上都是不同的)
>
> 1. 定义的区别(见上)
>
> 2. C++语言的多态性表现在: 静态多态、动态多态（不能以偏概全）
>
>    *静态多态*：C++语言允许函数重载和运算符重载、模板（template<>）、宏多态（#difine）。函数的调用在编译期间就确定了，地址在编译期就绑定了（*早绑定*）
>
>    *动态多态*：C++语言通过定义虚函数来支持动态联编，动态联编是多态性的一个重要的特征。函数调用在编译期间是无法确定的（调用的子类的虚函数的地址无法给出），函数地址是在运行期绑定的（*晚邦定*）
>
>    如果硬要说重载与多态的关系也就是：重载体现了静态多态；虚函数体现了动态多态。（个人并不喜欢这句话）
>
> 关于虚函数、继承、多态、类对象释放和指针释放问题小记
>
> ```c++
> 
> #include<iostream>
> using namespace std;
> // 基类
> class Person
> {
> public:
>     Person() {cout<<"call Person constructor \n"<<endl;}
>     virtual void func(); // 在类内只是声明
>     virtual ~Person(){cout<<"call Person destructor \n"<<endl;}
> };
> /** If a class is defined in a header file and has a vtable (either it has virtual methods or it derives from classes with virtual methods),
>  *  it must always have at least one out-of-line virtual method in the class.
>  * Without this, the compiler will copy the vtable and RTTI(运行时类型信息) into every .o file that #includes the header, bloating .o file sizes and increasing link times
>  *
>  * 也就是说：头文件定义了虚函数时，编译器会自动生成虚函数表vtable，用来对应每一个虚函数(因为编译器不知道哪个翻译单元（.cpp文件）会使用这个函数)
>  * 因此它会在所有这些中发出一个副本，并且链接器会处理它（复制虚函数表和RTTI）。这会在目标文件中产生额外的工作和并且使.o文件变得更庞大。
>  *
>  * 对于类内定义虚函数时，编译器会将虚函数变成内联函数，报警告错误。因而记住:类内只是声明，类外进行定义。
> **/
> void Person:: func(){cout<<"call Person func \n"<<endl;} // 在类的外面定义实现Person的func函数
> 
> // 公有继承的派生类
> class Student :public Person
> {
> public:
>     Student() {cout<<"call Student constructor \n"<<endl;}
>     void func() override;
>     ~Student()override {cout<<"call Student destructor \n"<<endl;}
> };
> void Student::func(){cout<<"call Student func \n"<<endl;}
> 
> /**
>  * 构造函数和创建对象的顺序有关，构建父类，在构建子类
>  * 析构函数与构造函数调用的顺序相反
>  * @brief main
>  * @return
>  */
> int main()
> {
>     cout<<"*** situation1 ***\n"<<endl;
>     //【其实是先创建一个class person指针，再申请创建一个Student空间，最后将该person指针指向这个区域】
>     Person* person = new Student (); // call Person constructor --> call Student constructor
> 
>     person->func(); // call Student func （！因为子类重写了父类虚函数，父类指针根据赋给它的不同子类指针，动态的调用属于子类的该函数！）【重点！】
>     cout<<"*** Start to delete person ***\n"<<endl;
>     delete person;  // call Student destructor --> call Person destructor
> 
>     cout<<"*** situation2 ***\n"<<endl;
>     Person person2; // call Person constructor
>     Student student2; // call Person constructor --> call Student constructor 子类创建对象的时候，会先调用父类的，在调用子类的构造函数
>     person2 = student2; //
>     person2.func();  // call Person func
>     // 类的对象在其生命周期结束的时候会自动被析构函数释放掉，但是指针不行，需要人为的释放，而且重复释放会造成内存泄漏问题
>     // call Student destructor(student2) --> call Person destructor(student2) --> call Person destructor(person2)
>     return 0;
> }
> 
> ```
>
> 

#### 其他语言的super

java不允许多继承，要想实现多继承则需要用接口实现；Python是允许多继承的。对于Go语言不是完全的面向对象，也是需要接口(值)实现。

对于Java：super()语句必须是构造方法的第一个子句；定义子类的一个对象时，会先调用子类的构造函数，然后在调用父类的构造函数，如果父类函数足够多的话，会一直调用到最终的父类构造函数，函数调用时会使用栈空间。因而栈顶是父类最终的父类最终一个构造函数。引用当前对象的直接父类中的成员。

对于Python：

> ```python
> '''
> class SubClass(BaseClass):
>     def method(self):
>         super(SubClass, self).method()
>         #do some thing here...
>         
> # 最常见的method()是__init__()
> '''
> class FooParent:
>   def bar(self, message):
>     print(message)
> class FooChild(FooParent):
>   def bar(self, message):
>     super(FooChild, self).bar(message)
>     
> >>> FooChild().bar("Hello, Python.")
> Hello, Python
> ```
>
> 在super机制里可以保证公共父类仅被执行一次，至于执行的顺序，是按照MRO（Method Resolution Order）：方法解析顺序 进行的

构造、析构、拷贝



文件读写fstream

> ![img](C:\Users\MECHREVO\Desktop\C++StreamPic.jpg)

this指针的理解http://c.biancheng.net/cpp/biancheng/view/201.html

try{}catch(){}与catch(...){}	

## 二、**C++底层实现细节详解**

unordered_map，hashmap和map其他区别呢？怎么实现的？关联容器？序列化容器？

stack 

heap

生存周期

静态全局变量

深拷贝、浅拷贝、0拷贝、用户态和内核态、缓冲区和用户缓冲区、DMA和CPU

内存泄漏

智能指针和简单实现

virtual

那些函数不能定义为虚函数？
1）友元函数，它不是类的成员函数
2）全局函数
3）静态成员函数，它没有this指针
3）构造函数，拷贝构造函数，以及赋值运算符重载（可以但是一般不建议作为虚函数）；注意析构函数可以

函数指针、指针函数、函数返回值是指针；与python 、java中高阶函数的区别



虚函数表和多态，https://blog.csdn.net/hackbuteer1/article/details/7475622#comments重载没有体现C++的多态特性，纯虚函数，抽象类？https://blog.csdn.net/Hackbuteer1/article/details/7558868

c/c++跟寄存器和缓存打交道

webapi和websocket与webservice区别

## 三、**C++实现相关算法原理**

knn

kalman filter

pathloss单圆双圆相交优化

Cjson实现

## 四、**C/C++相关代码**


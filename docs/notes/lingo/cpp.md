---
comment: true
---


# Object-Oriented Programming
> in c++ 

!!! abstract "摘要"
    
    这是我在学习oop时的memo，我不会系统的整理整个课程的笔记，但是会写下一些备忘和例子方便复习时使用

    完整的笔记我参考的是[NoughQ同学的笔记](https://note.noughtq.top/lang/cpp)

## Lec1

```cpp
string str1 = "foo, bar!";
ofstream fout("out.txt");
ifstream fin("out.txt");
string str2, str3;
fin >> str2 >> str3;
```

在这里我们使用两个字符串变量来接out.txt是因为其中的内容包含一个white space，也就是空格或者tab，这样会打断读取。最终得到的就是：```str2 = foo,``` ```str3 = bar!```


## Lec2

>这节课展示了c++的抽象能力


- C语言在函数传递参数时如果将数组作为参数传入，其实数组是退化了的，传入的实际是指针，指向数组首元素：

```cpp
//原先的：
void print_array(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        cout << arr[i] << " ";
    }
}

//如果这样写
void print_array(int arr[]) {
    int size = sizeof(arr) / sizeof(arr[0]);
    ...
}

//这样写是错的，因为sizeof(arr)返回的是指针的大小，而不是数组的大小。

//因此不能这样写

```

- swap函数对数组中的元素没有交换效果

c++的处理方式是使用引用类型，也就是在函数参数中使用&符号：

```cpp
void swap(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}
```

- 函数重载：

在第一个函数中我们只考虑了int类型，如果想要支持多种类型，我们可以使用函数重载：

```cpp
void print_array(int arr[], int size) {
    for (int i = 0; i < size; i++) {
        cout << arr[i] << " ";
    }
}

void print_array(double arr[], int size) {
    for (int i = 0; i < size; i++) {
        cout << arr[i] << " ";
    }
}
```

允许同名函数但是参数表不一样，会根据调用情况来决定使用哪个函数。

这是我们发现如果每次都写一个不同变量类型的函数很麻烦，因此c++引入了模板：

```cpp
template <typename T>
void print_array(T arr[], int size) {
    for (int i = 0; i < size; i++) {    
        cout << arr[i] << " ";
    }
}
```
这样可以将类型作为参数传递

- 定义结构体也可以这样操作吗？

会发现问题出在**比较**上，因为结构体中包含多个变量，编译器不知道需要怎么比较、输出等操作，这时候需要自己进行定义：

```cpp
struct Student {
    int id;
    string name;

    bool operator<(const Student& s) {
        return id < s.id;
    }
};

ostream& operator<<(ostream& out, const Student& s) {
    return out << "(" << s.id << "," << s.name << ")";
}

```


## Lec3
>STL

### Container

- Sequencial:
    - array(static)
    - vector(dynamic)动态数组
    - deque
    - forward_list(single linked)
    - list(doubly-linked)双向链表

- Associative
    - set(keys)
    - map(keys and values)
    - multimap(允许重复key)
    - multiset(允许重复key)

- Unordered associative
    - unordered_set,map,multiset,multimap


- Adapters
    - stack
    - queue
    - priority_queue

---

- insert函数：

```cpp
vector<int> evens;
...
evens.insert(evens.begin()+4, 5, 10);
```

就是在begin+4的位置插入5个10

- iterator方式遍历

```cpp
for (vector<int>::iterator it = evens.begin(); it < evens.end(); ++it) 
    cout << *it << ' ';
```

也可以在这里使用auto：

```cpp
for (auto it = evens.begin(); it... )
...
```

因为在这里编译器可以根据后面的赋值判断出it的类型，但如果直接随便写一个```auto it;```是没有意义的

也可以这样遍历：

```cpp
for (int x : evens)
    cout << x << ' ';
```

这是一个语法糖，编译器将你写的东西转换成了iterator

如果需要对其中的内容进行修改：

```cpp
for (int& x : evens)
    x *= 2;
```

将x声明为引用即可

!!! warning "注意"

    在进行遍历时，只有vector可以用 < 符号来进行终止判断，因为vector是连续内存，其他容器通常用```it != container.end()```来判断（链表比较大小没有意义）


- Map

```cpp
#include <map>
#include <string>

map<string, float> price;
price["snapple"] = 0.75;
price["coke"] = 0.50;
string item;
double total=0;
while (cin >> item)
    total += price[item];
```

如果我们在进行输入的时候写进total的字符不是已知的snapple、coke，那么他其实会帮你插入这个字符，并将他的value设置为默认值0.0

```cpp
int main() {
    map<string, int> word_map;
    for (const auto& w: {"we", "are", "not", "humans", "we", "are", "robots"})
        ++word_map[w];
    for (const auto& [word, count]: word_map)
        cout << count << " occurrences of word '" << word << "'\n";
}
```

这里运用了map的[]运算符和输出时的结构化绑定


- Algorithms

算法作用的对象都是迭代器，我们传入迭代器后算法进行操作，同一种算法可以作用于不同的容器，因为算法是不依赖于容器的，这样形成了算法与容器的解耦，提高了代码的复用性。

- reverse

```cpp
vector<int> v = {1, 2, 3, 4};
reverse(v.begin(), v.end());
```

可以看到reverse直接作用在vector本身，是直接对vector进行修改的

```cpp
vector<int> u;
copy(v.begin(), v.end(), u.begin());
```

这样程序会报错segmentation fault，因为u没有初始化大小，默认是空的，因此也不会有begin这个位置，也就是说我们需要u中有数据

如果想要能够做到插入可以换一个迭代器：

```cpp
vector<int> u(v.size());
copy(v.begin(), v.end(), back_inserter(u));
```

还能够用algo来实现输出流：

```cpp
copy(v.begin(), v.end(), ostream_iterator<int>(cout," ,"));
```

但是这样我们输出的最后会多一个逗号，可以用一个头文件来解决：

```cpp
#include "infix_iterator.h"
```
这个头文件是在除了第一个元素以后的遍历中都保留操作


然后我们就直接使用：

```cpp
copy(v.begin(), v.end(), infix_ostream_iterator<int>(cout, ", "));
```

- erase

```cpp
list<int> L;
list<int>::iterator li;
li = L.begin();
L.erase(li);
++li;// WRONG
```

迭代器在erase后会失效，因此不能++li，需要重新赋值

```cpp
li = L.erase(li);//RIGHT
```



## Lec4

>内存模型 Memory Model


<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp1.png" style="width: 80%;">
    </div>

- Global 变量是可以跨文件访问的：使用```extern```关键字

- Static 的全局变量不是外部可见的，只能在一个编译单元（.cpp）中使用


```string s;``` 和 ```string *ps;```的区别：

- 前者是全局变量，后者是全局指针
- 前者在data段，后者在bss段
- 前者在main执行前初始化，后者在main中初始化
    因为string本身是一个class，会有一个默认的构造函数进行初始化，而ps没有初始化每次跑结果都不一样
- 前者在程序结束时自动释放，后者需要手动释放

```cpp
string s1, s2;
s1 = s2 // 将s2的值赋给s1

string *ps1, *ps2;
ps1 = ps2 // 将地址赋给ps1
```

### Referennce

```cpp
char c;
char *p = &c;
char &r = c;
```

对上面的三句话进行解释：

- 第一句进行一个声明，表示c是一个char类型的变量，占据1 byte，位置在某个地址address
- 第二句话表示p是一个指针，里面有8个bytes，表示一个地址，这个地址指向的是c的地址address
- 但三句话表示r是一个引用，可以完全把c和r看作是等价的，只是一个别名，位置还是在address

但是区别在于指针可以重新赋值，而引用不能

同时引用是必须要初始化的不能为空：

```cpp
int f(
    char &r; // wrong
)
```

但是可以作为函数参数进行传递：

```cpp
int f(char &r) {
    ...
}
```

这样是可以的

还有一种是class：

```cpp
class A {
    private:
        T &a;
    public:
        A(T &t) : a(t) {}
};
```

引用的意义在于：

- 可以作为函数参数传递
- 可以作为函数返回值
   
引用的限制：

- 引用不能嵌套
- 没有引用类型的指针，但是有指针的引用（可以是* &，不能是 & *）
- 数组中不能放引用

### Dynamic Memory Allocation

类似于malloc free DMA使用new delete

但对于非原生数据类型，比如class，使用new进行定义的时候不仅会malloc一块内存，还会使用构造函数进行初始化，这样就确保了定义时不仅是一块内存，而是初始化后的内存，同样delete也会执行析构函数，而不仅仅是释放内存


---

const 可以理解为一个宏，但是具备了类型检查

注意const和指针的结合：

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp2.png" style="width: 80%;">
    </div>


下面前两个是一样的
<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp3.png" style="width: 80%;">
    </div>




- 字符指针是不可以进行修改的

```cpp
char *p = "hello";
p[0] = 'A'; //wrong error
```

- 读取地址的运算：

```cpp
char *p = "hello";
cout << (void*)p << endl;
```

读到地址后发现，原因是这个char *的地址保留在代码段，不能修改，如果想要修改，需要使用const char *p = "hello";


## Lec5

> Class

实现一个类，c也可以，但是cpp提供的是将数据和操作数据的方法放在一起，这样更符合面向对象的思想

- 为了保护数据，需要在类声明中使用private
- class默认是private，struct默认是public

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp4.png" style="width: 80%;">
    </div>

注意头文件内不能存放全局变量，因为头文件会被包含到多个cpp文件中，如果头文件中包含全局变量，那么每个cpp文件中都会有一个相同的变量，这样就会导致冲突

类似的，也不能存放函数原型和类/结构体声明

include时，用尖括号表示标准库头文件，用双引号表示自定义头文件

## Lec6

Constructor and Destructor


- 构造函数可以有多份，编译器会根据参数表来决定使用哪个构造函数

在自己没有写构造函数时，编译器会自动生成一个默认的构造函数：

```cpp
class A {
    ...
};

->编译器生成后：

```cpp
class A {
    ...
    A() {
        ...
    }
};
```

但是一旦自己写了构造函数，编译器就不会生成默认的构造函数


- 析构函数只能有一份，在对象生命周期结束时调用
    - 通常析构函数不需要添加参数，如果遇到资源的调用，则需要在析构函数中显式释放

- inline 函数

函数在调用时，会进行压栈，如果函数体过大，则会导致栈溢出，这时候可以使用inline函数，将函数体直接插入到调用处，这样就不会进行压栈操作（用空间换时间）

对函数主体较小，调用代价较大时，使用inline

对函数主体较大，调用代价较小时，不使用inline

与marco不同，marco是纯粹的文本替换，而inline函数涉及到编译

## Lec7

Composition and Inheritance

组合：

```cpp
SvingsAccount::SvingsAccount(const string& name, const string& address, int cents)
    : m_saver(name, address), m_balance(cents)
    {}
```

这时我们想做一个print函数来打印信息，一个初学者的思路就是找到m_saver和m_balance，然后直接打印，但是这样就违背了面向对象的思想，我们应该直接使用m_saver的print函数和m_balance的print函数

```cpp
void SavingsAccount::print() const {
    m_saver.print();
}
```
因为具体print怎么做，是m_saver的职责


Inheritance：

与组合的区别就是：组合是has-a的关系，继承是is-a的关系

在派生类中，可以调用基类的成员函数，但是不能动基类的private成员，如果我们有一个private data，想写一个setdata对其进行更改，那么使用public会导致非派生类（别人）也可以更改，这时候需要使用protected，这样派生类可以访问，但是非派生类不能访问

- 顺序是：基类在派生类前面，字段在主体前

- name hiding: 派生类中不能使用基类中被隐藏的变量名，例如我在基类中有一个print函数，在派生类中同样加了print函数，那么派生类中的print函数会隐藏基类中的print函数，默认使用派生类中的print函数，这时候需要使用作用域运算符::来访问基类中的print函数


## Lec8


- 在类的外部访问private：

通过friend关键字来访问

- 继承关键字：

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp5.png" style="width: 80%;">
    </div>


> Polymorphism 多态

- virtual function 虚函数

在定义基类时，如果想要在派生类中重写基类中的函数，那么需要在基类中使用virtual关键字，这样在派生类中使用时，会根据对象的实际类型来调用相应的函数，而不是根据指针或引用的类型来调用

在派生类中写的时候可以通过添加override关键字来显式声明，这样如果基类中没有这个函数，编译器会报错


通过使用虚函数完成多态，在调用函数时，根据内部的对象类型决定其中对象调用的函数类型

```cpp
move_and_draw(&a, &b);
```
这时会根据a和b的实际类型来决定调用draw函数的哪个版本

- 析构函数也可以改写成虚函数，只要类中有任何一个虚函数，就应该提供一个虚析构函数。即使类不打算被继承，这样做也无大碍，并且还更安全。


- 如果我们对一个对象赋值为另一个对象（例如将派生类赋值给基类），那么数值部分会直接赋值（但是会根据基类的大小进行截断），但是虚函数表只会赋值基类中的虚函数表，不会赋值派生类中的虚函数表



## Lec9

Copy Constructor


对象创建的时候，会调用构造函数，对象销毁的时候会调用析构函数，但是在对一个对象进行拷贝的时候，会调用拷贝构造函数

```cpp
A a1;
A a2 = a1;

void f(A a) {
    ...
}

f(a1);
```

也就是说在这里，a2会调用拷贝构造函数来进行初始化，f(a1)也会调用拷贝构造函数，如果我们对创建的对象个数进行统计，我们会发现有三个对象被创建，有三次析构函数被使用

- 拷贝构造函数默认是存在的，如果自己没有写，编译器会自动生成一个
- 拷贝构造函数被调用后是会调用析构函数的
- 编译器自己生成的拷贝构造函数在拷贝的时候如果内层有嵌套的结构体或者类，会嵌套的进行拷贝构造
- 编译器自己生成的拷贝构造函数是浅拷贝，如果类中包含**指针**，那么拷贝的是指针的地址，而不是指针指向的内容，这样会导致两个指针指向同一个内容，当一个指针被析构时，另一个指针也会被析构，这样就会导致重复析构，因此需要自己写拷贝构造函数


- 返回值优化：

```cpp
A f() {
    A a;
    return a;
}

A b = f();
```

在返回值优化中，编译器会直接将a的值返回给b，而不是先创建一个临时对象，再拷贝给b，这样就避免了拷贝构造函数的调用

但是在内存扩容时（例如vector），编译器不会进行返回值优化，每次扩容都是重新新建一个更大的内存，然后将原来的内容拷贝过去，这样就会导致拷贝构造函数的调用

- 如果想要避免元素新建再拷贝，可以使用emplace_back，而不是push_back，emplace_back可以接收到内部元素所需要的参数直接在内存中构造，而不是新建再拷贝



### Static

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp6.png" style="width: 80%;">
    </div>

表格分为两列：
* **where to use (在哪里使用)**：列出了`static`关键字可以应用的不同程序元素。
* **what does it mean (它意味着什么)**：解释了`static`关键字在相应上下文中具体的行为或含义。

下面是对表格内容的详细解释：

1.  **free functions (自由函数)**
    * **What it means:** Internal linkage (内部链接)
    * **Explanation:** 当`static`关键字用于自由函数（即不属于任何类的函数）时，它表示该函数具有内部链接。这意味着该函数只能在其定义所在的编译单元（通常是`.cpp`文件）内部访问。其他编译单元无法链接到并调用这个`static`自由函数。这有助于避免命名冲突，并实现更好的模块化。

2.  **global variables (全局变量)**
    * **What it means:** Internal linkage (内部链接)
    * **Explanation:** 类似于`static`自由函数，当`static`关键字用于全局变量时，也表示该变量具有内部链接。这意味着该全局变量只能在其定义所在的编译单元内部访问，而不能被其他编译单元访问。这同样有助于避免命名冲突，并确保变量的局部性。

3.  **local variables (局部变量)**
    * **What it means:** Persistent storage (持久存储)
    * **Explanation:** 当`static`关键字用于函数内部的局部变量时，它的含义与前两者完全不同。这被称为“静态局部变量”。
        * **生命周期:** 静态局部变量的生命周期与整个程序的生命周期相同，而不是随着函数的结束而销毁。它只在第一次执行到其声明语句时初始化一次。
        * **作用域:** 尽管生命周期是全局的，但它的作用域仍然是局部的，即它只能在其定义的函数内部访问。
        * **用途:** 常用于需要保持函数调用之间状态的情况，例如一个计数器，每次函数被调用时递增，但其值在函数返回后仍然保留。

4.  **member variables (成员变量)**
    * **What it means:** Shared by all instances (所有实例共享)
    * **Explanation:** 当`static`关键字用于类的成员变量时，它表示该成员变量是“静态成员变量”或“类变量”。
        * **不属于任何实例:** 静态成员变量不属于类的任何特定对象（实例），而是属于类本身。
        * **共享:** 类的所有对象共享同一个静态成员变量的副本。对一个对象的静态成员变量的修改会影响到所有其他对象。
        * **初始化:** 静态成员变量通常需要在类定义之外进行初始化。
        * **用途:** 常用于存储类的所有对象共享的数据，例如一个计数器，记录创建了多少个类的对象。
    在对static成员变量进行初始化时，需要使用类名来访问，而不是对象名

```cpp
class A {
    static int data;
};

int A::data = 10;
```

同时这块data的数据并不存放在对象中，而是一个单独的全局变量

5.  **member functions (成员函数)**
    * **What it means:** Access static members only (只能访问静态成员)
    * **Explanation:** 当`static`关键字用于类的成员函数时，它表示该成员函数是“静态成员函数”。
        * **不属于任何实例:** 静态成员函数不与类的任何特定对象关联，可以直接通过类名调用，无需创建对象。
        * **无`this`指针:** 静态成员函数没有`this`指针，因此它不能直接访问非静态（实例）成员变量或非静态成员函数，因为这些成员需要一个特定的对象来访问。
        * **访问范围:** 它们只能访问静态成员变量和静态成员函数。
        * **用途:** 常用于执行与类相关的操作，但不依赖于任何特定对象的状态，例如工厂方法或辅助函数。

总而言之，`static`关键字在C++中是一个多功能的关键字，其确切的含义取决于它所应用的上下文（自由函数、全局变量、局部变量、成员变量或成员函数）。理解这些不同上下文中的含义对于编写健壮和高效的C++代码至关重要。

由于每个对象都会有一个隐式参数传进自己，而静态成员函数在调用时可以：

```cpp
class A {
    static void f() {
        ...
    }
};

A::f();
```

这显然是与对象无关的，所以静态成员函数没有this指针

并且，在静态成员函数内部无法访问非静态成员变量，因为非静态成员变量需要一个特定的对象来访问：

```cpp
class A {
    int data;
    static void f() {
        cout << data << endl; //error
    }
};
```

## Lec10

> Operator Overloading

可以进行重载的运算符：

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp7.png" style="width: 60%;">
    </div>

- 重载运算符的限制：
    - 不能重载的运算符：
        - ::
        - .
        - ?:
        - sizeof
        - typeid
        - const_cast
        - dynamic_cast
        - reinterpret_cast
        - static_cast


- 不能定义全新的运算符
- 参数个数一致
- 优先级不变

!!! example
    
    ```cpp
    class Integer {
        public:
            Integer(int n = 0) : i(n) {}
            Integer operator+(const Integer& other) const {
                return Integer(i + other.i);
            }
        private:
            int i;
    };

    Integer a(1), b(2);
    Integer c = a + b; // c = a.operator+(b)
    ```

- 第一个参数是隐式this指针，不能写```c = 3 + a```

    当然也可以转换成双目运算符


主要的类型（根据返回值和参数个数）：

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp8.png" style="width: 80%;">
    </div>

- 单目运算符：

前缀运算符：```Integer operator++()```

后缀运算符：```Integer operator++(int)```



实现：

```cpp
//prefix:
Integer& Integer::operator++() {
    this->i += 1;
    return *this;
}

//postfix:
Integer Integer::operator++(int) {
    Integer old(*this);
    ++(*this); // 重用前缀表达式
    return old;
}

```

## Lec11

operator 也会有自动生成，如果内层的class定义了一个operator，外层的class使用内层类进行operator的时候，会自动生成一个operator重写

- = 可以连用

```cpp
A = B = C;
// 等价于 A = (B = C)
```

- self assignment

在进行赋值的时候，如果对象的地址相同，那么会进行self assignment，因为为了避免内存泄露我们会把原地址先进行删除，然后再进行赋值，如果发生了self assignment，原地址就会直接不存在，这时候内部逻辑比较原值和新值的时候就会出错，所以要先检查一下是否是self assignment

- functor

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp9.png" style="width: 80%;">
    </div>

对（）进行重载，看起来是在调用函数，但是实际上是在调用operator()，调用对象


- lambda function

可以直接将函数对象作为参数传递，例如：

```cpp
#include <functional>

void f(function<int(int, int)> func) {
    ...
}
int c = 10;
f([c](int a, int b) {
    return a + b + c;
});
```

这个等价于：

```cpp
class Lambda {
    private:
    int c;
    public:
    Lambda(int c) : c(c) {}
    int operator()(int a, int b) {
        return a + b + c;
    }
}
```


这里的中括号是lambda表达式的捕获列表，捕获列表中的变量会在lambda表达式中被捕获，就像上述函数调用一样，在使用的时候，lambda函数作为一个main函数中的对象，捕获列表中的元素会作为类中的成员变量，在lambda表达式中使用



## Lec12


User defined type to be transfomed: Conversion

- Single argument constructor

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp10.png" style="width: 80%;">
    </div>

如果在类定义中加入了explicit关键字，那么这个构造函数就只能被显式调用，不能被隐式调用


- Conversion operator

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp11.png" style="width: 80%;">
    </div>

- 这里的double是关键字（要么是内置类型，要么是用户定义类型），表示转换的目标类型


我们可以自己选用转换的方式：

但是这里注意如果有相同的类型转换，会出现ambiguous的情况


<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp12.png" style="width: 80%;">
    </div>

- stream operator

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp13.png" style="width: 80%;">
    </div>




- 这里对ostream进行重载，所有的其他stream都会自动重载，因为ostream是所有stream的基类



## Lec13

> Template

- function overloading

同名函数根据参数表进行使用，编译器根据参数表进行匹配时，也会对某些类型做隐式转换：

比如char和int，float和double，但是如果可以转成多个类型的变量如long，就会产生歧义，编译器会报错

- default argument

cpp的参数表的默认值有给定的顺序，从右到左，不能跳着给。默认值在函数声明中给定，在函数定义中不能重复给定

模版在做实例化的时候不能做隐式类型转换，但是可以显式转换





```cpp
template <class T>
void print(T a, T b) {
    cout << a << " " << b << endl;
}

print<int>(1, 2.1);
```

这样会有一个warning，因为2.1被隐式转换成了int（2），虽然会丢掉一些东西，但是不会报错

当然，也可以```print<double>(1.1, 1)```，这样就不会丢东西了

如果有普通函数版本，也有模版版本，那么会优先使用普通函数版本，如果没有严格匹配的普通版，才会调用模版。实在不行，会进行隐式转换

- class template

模版类可以有多个参数，同时也不一定是自定义的类型，可以是固定类型：

```cpp
template<class T, int N>
class Array {
    private:
        T m_arr[N];
    public:
        int size() const {return N;}
        T& operator[](int i) {return m_arr[i];}
}

int main() {
    Array<int, 10> a;
    a[0] = 1;
    Array<int, 4> b;
    Array<int, 4> c;

    a = b; // 这里会报错，因为Array<int, 10>和Array<int, 4>是不同的类型
    b = c; // 这里不会报错，因为是相同类型
}
```

- member template

<div align="center" >
    <img src="/../../../../assets/pics/cpp/cpp14.png" style="width: 80%;">
    </div>

再写一个typename U，用来使用别的参数



## Lec14

> iterator

可以理解为一种智能指针

提供一种顺序的数据访问方式，不会暴露底层的数据结构，这样我们在设计算法时，只需要借助这种接口，而不关心底层的数据结构

!!! example

    ```cpp
    template<class InputIterator, class T>
    InputIterator find(InputIterator first, InputIterator last, const T& value) {
        while (first != last && *first != value) ++first;
        return first;
    }
    ```

    使用的时候，只要保证提供的迭代器是有其中的操作的：

    ```cpp
    vector<int> vecTemp;
    if (find(vecTemp.begin(), vecTemp.end(), 3) == vecTemp.end()) {
        cout << 3 not found in vecTemp << endl;
    }
    ```

- 注意不同的底层数据结构在使用同一种算法（例如遍历）时，可能会有不同的表现，比如二叉搜索树无论给定的数据是什么顺序，都是采用中序遍历，而对于链表则是按照创建顺序





- Template Specialization

模版的特化：对于一个正常的模版（主模版），我们有一个类的定义使用模版中的参数

```cpp
template<class T1, class T2, int N>
class A{...}
```

当我们指定T1和T2为某个类型，N为某个值时，我们可以重新定义这个类，这样就形成了特化

```cpp
template<class T1, class T2, int N>
class A<T1, T2, 10> { //全部参数都给定的全特化
    ...//新的实现
}
class A<int, T2, 10> { //部分参数给定的部分特化
    ...//新的实现
}
```


- iterator category

    - input
    - output
    - forward
    - bidirectional
    - random access

从能力上说，input+output -> forward -> bidirectional -> random access

每一层都有上一层不具备的能力，最强的就是random access

## Lec15

> Exception

...是cpp中异常的通配符


- throw
  
throw语句将异常向上抛出，直到被catch语句捕获，可以写不带东西的throw语句，表示抛出异常，只能在catch语句中使用


- Exception in cons & des

在进行构造函数的过程中如果发生了异常，此时析构函数不会被调用，因为对象还没有被完全构造出来，所以需要手动调用析构函数，或者将新建资源放在init函数中，但这样违背了构造函数初始化对象的初衷


## Lec16

> Smart Pointer

三种智能指针，都是用来管理裸指针的

- unique_ptr

```cpp
class A {
    ...
}

unique_ptr<A> p1(new A()); //这里就是用一个裸指针(new A())对p1进行初始化
```

也可以用make_unique来创建智能指针

```cpp
auto p1 = make_unique<A>();
```
unique的意思就是管理权唯一，所以不能拷贝构造和赋值，但是可以移动构造

```cpp
auto p2 = move(p1);
//move就是右值引用，也就是：
p2 = (unique_ptr<A> &&)p1;
```

- shared_ptr

shared的意思就是多个人管理同一个裸指针，只有所有管理权都释放了，裸指针才会被释放，可以用.use_count来查看有多少个管理权

- weak_ptr

weak_ptr是用来解决shared_ptr的循环引用问题，因为shared_ptr的计数器是基于引用计数的，所以当两个shared_ptr互相引用时，会导致计数器无法释放，从而导致内存泄漏


- Name Cast


constant_cast: 常量类型转换，用于添加或者移除const或者volatile


reinterpret_cast: 重新解释类型，如果一个类型占用四个字节，转换成double后就变成了8个字节，但是不会进行任何的转换，只是重新解释，也就是占用八字节，但数据还是在前四个字节中

static_cast: 静态类型转换，可以进行一些简单的类型转换，例如int到double，但是不能进行一些复杂的类型转换，例如int到char*

dynamic_cast: 动态类型转换，可以进行一些复杂的类型转换，例如int到char*，但是不能进行一些简单的类型转换，例如int到double

```cpp
int a = 7;
double* p;

p = (double*)&a; //ok but a is not a double
p = static_cast<double*>(&a); //error
p = reinterpret_cast<double*>(&a); //ok
```






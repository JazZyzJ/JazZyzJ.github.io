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
- 没有引用类型的指针，但是有指针的引用（可以是*&，不能是 &*）
- 数组中不能放yinyong

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

```
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


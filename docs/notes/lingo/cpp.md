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
copy(v.begin(), v.end(), ostream_iterator<int>(cout," "));
```





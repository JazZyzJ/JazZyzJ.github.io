---
comment: true
---


# Object-Oriented Programming
> in c++ 

!!! abstract "摘要"
    
    这是我在学习oop时的memo，我不会系统的整理整个课程的笔记，但是会写下一些备忘和例子方便复习时使用

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









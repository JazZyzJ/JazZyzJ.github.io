# Object-Oriented Programming
> in c++ 

!!! abstract "摘要"
    
    这是我在学习oop时的memo，我不会系统的整理整个课程的笔记，但是会写下一些备忘和例子方便复习时使用


```cpp
string str1 = "foo, bar!";
ofstream fout("out.txt");
ifstream fin("out.txt");
string str2, str3;
fin >> str2 >> str3;
```

在这里我们使用两个字符串变量来接out.txt是因为其中的内容包含一个white space，也就是空格或者tab，这样会打断读取。最终得到的就是：```str2 = foo,``` ```str3 = bar!```

---











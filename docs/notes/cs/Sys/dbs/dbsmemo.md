---
comment: true
---

# Database System

!!! abstract "摘要"
    
    这是我在学习数据库系统时的memo，我不会系统的整理整个课程的笔记，但是会写下一些备忘和例子方便复习时使用


## Relational Model

- 关系的定义最基本是集合，集合是没有重复元素的

<div align="center">
<img src="/../../../../assets/pics/dbs/dbs1.png" style="width: 40%;">
</div>

mutiset 是允许重复元素的

- Union 中要求：
    - 两个集合的arity相同（same number of attributes）
    - attribute domains 是要compatible的，不是说完全相同，但是要类似

- Rename：
    
    重命名中的E不一定是关系，也可以是表达式

- Natural Join:

    没有增加表达能力但是简化了操作，可以用基本操作代替：

    - 笛卡尔积
    - 选择共同属性相同的部分
    - 投影

    还有一种left outer join $\mathbin{⟕}$，是笛卡尔积后选择，如果属性不同，就用null来填充
    
    <div align="center">
    <img src="/../../../../assets/pics/dbs/dbs2.png" style="width: 60%;">
    </div>

    同理还有right outer join $\mathbin{⟖}$，left outer join的镜像，以及full outer join $\mathbin{⟗}$，两个的并集：

    <div align="center">
    <img src="/../../../../assets/pics/dbs/dbs3.png" style="width: 60%;">
    </div>

- Semijoin:
    
    用符号$\ltimes$表示，是natural join的子集，只保留natural join后左边关系中的属性，同理也有$\rtimes$，是right semijoin，保留右边关系中的属性：

    <div align="center">
    <img src="/../../../../assets/pics/dbs/dbs4.png" style="width: 60%;">
    </div>

- Division:

    用符号$\div$表示，对$r \div s$，要求：

    - $s$是$r$的子集
    - $s$的属性是$r$属性的子集

    结果是包含$s$属性的对应$r$的元组：

    <div align="center">
    <img src="/../../../../assets/pics/dbs/dbs5.png" style="width: 60%;">
    </div>

    
    用处：

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs6.png" style="width: 60%;">
    </div>


- Multiset:
    
    多重集，允许重复元素（去除重复元素代价很大）这样就支持SQL中操作
    
    
## Intro to SQL

- interval:period of time 
    - 用两个时间做差

- create table
来个例子：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs7.png" style="width: 60%;">
    </div>
create table只是在定义一个schema，需要在后续的如insert操作中创建instance

- Integrity Constrains

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs8.png" style="width: 60%;">
    </div>

    - foreign key

    对于foreign key，要求在主键中存在，或者为null，但是如果一个操作导致foreign key指向的对象被删除，会有以下可选项：

    - on delete cascade: 级联删除，将有关的全部删除（系没了学生也删掉）
    - on delete set null: 设置为null（系没了学生还在但不知道系名）
    - on delete restrict: 拒绝删除（系里有学生，系就不能删）
    - on delete set default: 设置为默认值（系没了，学生还在，设置到默认的系中）

    同样对于update，也就是被引用的对象的更新，也有on update+四个一样的选项，只是这里的级联是更新所有引用者的值


- alter table

支持动态更改表的定义


- group by 

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs9.png" style="width: 60%;">
    </div>

- natural join

简化操作

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs10.png" style="width: 60%;">
    </div>


- 通配符

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs11.png" style="width: 60%;">
    </div>

中文字符占位是两个字节，有可能出现前后两个字中间的部分被截断匹配的情况，因此中文建议完全匹配或者用 _ _ 来表示一个汉字


- limit

用于控制返回的行数

``` sql
select * from student limit 5;
==
select * from student 0, 5; /* offset, row_count*/
```

- not exist的一个例子

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs12.png" style="width: 60%;">
    </div>

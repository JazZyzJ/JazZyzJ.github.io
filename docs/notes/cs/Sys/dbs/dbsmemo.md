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
    
    

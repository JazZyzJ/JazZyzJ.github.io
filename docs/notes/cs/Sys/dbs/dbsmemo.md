---
comment: true
---

# Database System


<head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/KaTeX/0.16.7/katex.min.js"
            integrity="sha512-EKW5YvKU3hpyyOcN6jQnAxO/L8gts+YdYV6Yymtl8pk9YlYFtqJgihORuRoBXK8/cOIlappdU6Ms8KdK6yBCgA=="
            crossorigin="anonymous" referrerpolicy="no-referrer">
    </script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.css">
    <script src="https://cdn.jsdelivr.net/npm/pseudocode@latest/build/pseudocode.min.js">
    </script>
</head>


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







## Intermediate SQL

- 连接表达式

连接条件之前的表达式中这样写：

```sql
select *
from a, b
where a.id = b.id
```

现在可以采用```join using```来写：

```sql
select *
from a join b using (id)
```
以及：```join on```

```sql
from a join b on a.id = b.id
```

虽然看上去on的作用可以被using和where代替，但是在使用outer join时，on可以起到作用
    
- 使用outer join的一个例子：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs13.png" style="width: 80%;">
    </div>

通过outer join解决：保留左边关系中的没有课程的学生的ID，其余信息null

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs14.png" style="width: 80%;">
    </div>

如果使用的是where：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs15.png" style="width: 80%;">
    </div>

也就是说outer join只是对结果关系进行的补充，而不是对参与连接的关系进行补充，因此在使用where（作用对象就是参与连接的关系）时，上述例子会出现根本找不到Snow这个学生的ID，where就会过滤掉Snow的信息


连接类型可以和连接条件组合使用：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs16.png" style="width: 80%;">
    </div>


- 事务 trasaction


- 完整性约束



- 用户定义类型

```sql
create type dollars as numeric(12, 2) final; /*final 表示是最小的类型，不能被继承*/

create table sales (
    id integer,
    amount dollars
);
```

- Domain 类型定义

domains与type相比可以添加约束，比如：

```sql
create domain dollars as numeric(12, 2) check (value >= 0);
```

- Large Objects Types

图像、视频等大体积文件被存储为大对象类型


- Authorization

角色是权限的集合

## Advanced SQL

- Procedure and Functions



## Design and ER Model

- Attribute

复合属性：对应的组件属性会紧放在复合属性的下方，并且开头有缩进

多值属性：被花括号包裹

派生属性：末尾有圆括号


- Cardinality


用横线上的lh来表示全连接、单射

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs17.png" style="width: 80%;">
    </div>



- 关系集的主键

我们需要确保得到的关系是唯一的，因此需要通过选取一个或多个属性来确保唯一性，这个属性或属性集被称为关系集的主键

所以在多对一多对多等问题中，只需要思考怎么让关系集唯一即可

- 三元关系中，不能使用超过一个箭头

原因就是在唯一性这里：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs18.png" style="width: 80%;">
    </div>

可以看到有两种结果

解决这个问题有两种方法：一种是将关系集转变成实体集，另一种是使用函数依赖

- Weak Entity Set

我们在建立section和course之间的关系sec_course时，发现如果sec_course中保留了course_id，那么其实section中也有，这样就十分多余，如果不保留这个关系，那么section和course之间的关系就无法建立

我们将section中的course_id去掉，然后建立section和sec_course之间的关系，现在面临的问题是section不具有唯一性了，这时我们定义section为弱实体集，他的唯一性由两点因素决定：

- identifying entity set：在标识性实体集中选择主键

+

- discriminator：一些附加的用作区分的属性


然后连接强弱实体集的关系就被称为identifying relationship

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs19.png" style="width: 80%;">
    </div>

- Redundant Attributes

对于组成属性，直接写最小子项，中间项不保留：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs20.png" style="width: 80%;">
    </div>

对于多值属性，有一个特殊情况：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs21.png" style="width: 80%;">
    </div>

直接转换的话，得到的是两个关系集，一个是time_slot，一个是time_slot_detail，可以选择不要前者，但是这样就无法定义section的外键

- Design Mistakes

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs22.png" style="width: 80%;">
    </div>

assignment不能是一个数值，应该是多值的，有右边两种方式改正


## Extended ER Feature





## Relational Database Design

这章的内容有点多，我进行一个梳理：

- Features of Good Relational Design
- Atomic Domains and First Normal Form
- Decomposition Using Functional Dependencies
- Functional Dependency Theory
- Algorithms for Functional Dependencies
- Decomposition Using Multivalued Dependencies 
- More Normal Forms
- Database-Design Process
- Modeling Temporal Data


总体的思路是：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs24.png" style="width: 80%;">
    </div>


### Features of Good Relational Design

- lossless decomposition

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs23.png" style="width: 80%;">
    </div>

### Atomic Domains and First Normal Form

原子化的域是指不能再进行分解的，FNF就是指每个属性都是不可分解的，一个例子就是CS1102，表示计算机系11年的02号课程，可以被分解为三个属性，另外一个需要注意的例子是，the set of all sets of xx(int, string)，表示所有int和string的集合，这就是non-atomic的了

### Decomposition Using Functional Dependencies

Define functional dependency:

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs25.png" style="width: 80%;">
    </div>

由箭头左侧能够完全决定右侧即可

- superkey和candidate key的定义如下：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs26.png" style="width: 80%;">
    </div>

这里需要注意的是K需要是某个属性的闭包，也就是K+能不能推出所有属性

也就是说candidate key是superkey中去掉一个属性后不再是superkey的子集的集合，可以称为minimal superkey

当然如果K+可以，那么K也就可以，并且K是最小的话，K就是candidate key


- trivial 的定义和性质：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs27.png" style="width: 80%;">
    </div>

左边囊括右边，一定是trivial

- Closure 

- 依赖闭包：这里的闭包和离散数学中的类似，就是计算函数依赖的扩展，在这里计算时要用到几个律：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs28.png" style="width: 80%;">
    </div>

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs29.png" style="width: 80%;">
    </div>

- 关系闭包：这里是计算在一组函数依赖下，一个属性能推导出的所有属性

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs30.png" style="width: 80%;">
    </div>

??? example 

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs31.png" style="width: 80%;">
    </div>

!!! tip

    几个好用的性质：

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs32.png" style="width: 80%;">
    </div>
    
    第一个讲过了，第二个就是验证用trivial性质来反向推到依赖，比较方便

    第三个就是可以有条理的计算出依赖闭包

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs33.png" style="width: 80%;">
    </div>
    
    先写出所有关系的组合，计算每个组合的闭包，再将他们展开

### Functional Dependency Theory

- Canonical Cover

计算一个依赖集的minimal：

首先确定什么依赖是多余的：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs34.png" style="width: 80%;">
    </div>

验证一个属性是否在依赖中多余的方法：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs35.png" style="width: 100%;">
    </div>

计算一个依赖的最小覆盖：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs36.png" style="width: 80%;">
    </div>

??? example

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs37.png" style="width: 80%;">
    </div>

    当然这道题可以通过更直观的方式观测出结果，不使用transitive，那么只剩下了两个依赖

??? example "Exercise"

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs38.png" style="width: 80%;">
    </div>

### Algorithms for Functional Dependencies

进行范式的介绍并使用范式进行分解，这里与教材的方式不太相同，教材是开篇就介绍范式，这里孙老师先在前半部分把工具介绍清楚，这里就能在介绍范式后直接将范式分解

- BCNF

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs39.png" style="width: 80%;">
    </div>

如果有不符合BCNF的，进行分解

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs40.png" style="width: 80%;">
    </div>

Algorithm：

这个算法对关系进行分解：

找到一个不符合BCNF的依赖（左边不是superkey的），然后进行如下分解

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs41.png" style="width: 100%;">
    </div>

??? example 

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs42.png" style="width: 80%;">
    </div>

- Dependency Preservation

就是看一个依赖集在被分解后的并集是否与原依赖集相等

??? example

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs43.png" style="width: 80%;">
    </div>

如果 $F$ 内的每个成员能够在分解中的某个关系上被检查，那么说明这个分解保留了依赖。但这种方法并不总是有效，因为即使分解是依赖保留的，但是可能会出现仅看其中的一个关系无法检测依赖的情况。所以这个方法只是一个充分条件

对于 $F$ 中的每个依赖 $\alpha \rightarrow \beta$，执行以下过程：

<pre class="pseudocode" lineNumber="true">
    \begin{algorithm}
    \caption{Alternative test for dependency preservation}
    \begin{algorithmic}
        \STATE $result = \alpha$
        \REPEAT
            \FORALL{$R_i$ in the decomposition}
                \STATE $t = (result \cap R_i)^+ \cap R_i$
                \STATE $result = result \cap t$
            \ENDFOR
        \UNTIL{($result$ does not change)}
    \end{algorithmic}
    \end{algorithm}
    </pre>


??? example "Exercise"

    <div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs44.png" style="width: 80%;">
    </div>


BCNF不能保证得到的一定是依赖保留的分解，因此引入其他范式

- 3NF

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs45.png" style="width: 80%;">
    </div>

如果左边不是key，那么右边必须是key的一部分

分解算法：

<div align="center" >
    <img src="/../../../../assets/pics/dbs/dbs46.png" style="width: 100%;">
    </div>

先得到最小覆盖，将覆盖里的每个依赖都进行分解，如果分解出来的关系中没有一条包含key，那么将key单独组成一个关系










































<script>
    pseudocode.renderClass("pseudocode");
</script>


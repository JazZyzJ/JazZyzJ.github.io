# UCB CS61 A

##  Lecture 1

- what is computer science?

what problem to solve using computation

how to 

what techniques to effectively solve 

**managing complexity**

-  mastering abstraction
-  programming paradigms

------



- Types of expressions

```python
#function call notation
>>>2015
2015
>>>2000+15 #expression
2015 #value
 
```

- primitive expressions

number numeral name string

- call expression

`operator` `(operands)`

`fun`              `argument`

nested expression -->tree

​     

##  Lecture 2 Function

- Assignment statement

**bind** **names** **to** **values**

`name` = `value`

use function :`from xxx import xx`

build your own :`def name(arguments):`

​					`return xx`

- Environment Diagrams

visualize the interpreter`s process

- Design Functions

**bind names to expressions**

`def <name>(<fomal parameter>):`     **signature** 
	`return < return expression >`    **body**

**Python uses a frame to define the body of a func**∂

**which require the same indent for one suite of statement**

guide:

give it an exact job

don't repeat yourself 

define generally

## Lecture 3 Control and Iteration

### print and none 

none represents nothing in python

**func the doesnt explicitly return a value return none**

like --> print

#### pure func 

just return value

####  non-pure func

have side effects

### features in python

// floordiv  `2013 // 10 = 201`

 multiple assignments  `a=b=c=1`

multiple return 

`a,b = func(x,y)`

### Condition Statements

**if **:

one if 

oneormore elif

zeroorone else

#### boolean context :

False: False, 0, ' ', None

### Iteration

#### while:

search func:

```python
def search(f):
  x = 0
  while not f(x):
    x += 1
    return x
```

inverse func:

```python
def inverse(f):
  return lambda y:search(lambda x:f(x) == y)
```



### Control

funcs always evaluate all the components 

but controls just excuse part of it 

that's why there isn't if function

### logical operator

and == &&

or == ||

## Lecture 4 Higher-Order Func

assert statement: 

`assert [!con], 'statement'`

### Generalizing Process

find the common piont of a sequence of funcs

make different parts a func to be the argument as the higher func

eg: summation

```python
def summation(n, term):
  sum, k = 0, 1
  while k <= n:
    k += 1
    sum += term(k)
  return sum
```

uses term to define the elements of the summation 

-->**higher order func** : 

take a func to be an element of other func

return a func 

make adder:

```python
def make_adder(n):
  """return a function that takes one argument k and returns k + n"""
  def adder(k):
    return k + n
  return adder
>>>add_three == make_adder(3)
>>>add_three(4)
7
<=> make_adder(3)(4)
  
```



### Function Composition

compose func:

```python
def compose1(f,g):
  def h(x):
    return f(g(x))
```

###  Func Currying

transit a multi-arguments func to a single-argument higher order func



```python
curry = lambda f: lambda x: lambda y: f(x, y) 
```



### Func abstraction

### Arbitrary number arguments

 

```python
def printed(f):
  def print_and_return(*args):
    result = f(*args)
    print('Result:', result)
    return result 
```

## Lecture 5 Environments

a sequence of frame 

global frame 

local frame  

- every expression is evaluated in the context of an environment 

- **A name is always bound to the earliest frame`value of the current environment**

```python
def square(square):
  return mul(square, square)
square(4)
#when calling square(4), in f1 frame square doesnt go back to Global frame to find the value, but bound to 4 which is the earliest frame of the current environment 
```

![cs61a-1](/../../../../assets/pics/CS61a/cs61a-1.png)

### Lambda function

is a expression return a function

unnamed before

 cannot contain a statement 

```python
lambda [keyword]:[return expression using keyword]
#eg:
square = lambda x: x*x
>>>square(3)
9
>>>(lambda x: x*x)(3)
9
```

lambda is a name (square isn't created)

but def creat a func called square

###  Function Decorator

```python
@trace #@xx+func == xx(func)
def triple(x):
  return 3*x

def trace(fn):
  def traced:
  	……a sequence of operation
    retunrn fn
  return traced
```



## Lecture 6 Recursion

### Mutual Recursion

```python
def split(n):
  return n//10, n%10

    """Return the digit sum of n computed by the Luhn algorithm.
    First: From the rightmost digit, which is the check digit, moving left, double the value
of every second digit; if product of this doubling operation is greater than 9 (e.g., 7 *
2 = 14), then sum the digits of the products (e.g., 10: 1 + 0 = 1, 14: 1 + 4 = 5) 

    >>> luhn_sum(2)
    2
    >>> luhn_sum(12)
    4
    >>> luhn_sum(42)
    10
    >>> luhn_sum(138743)
    30
    >>> luhn_sum(5105105105105100) # example Mastercard
    20
    >>> luhn_sum(4012888888881881) # example Visa
    90
    >>> luhn_sum(79927398713) # from Wikipedia
    70
    """
  def luhn_sum(n):
    if n < 10:
        return n
    else:
        all_but_last, last = split(n)
        return luhn_sum_double(all_but_last) + last

def luhn_sum_double(n):
    """Return the Luhn sum of n, doubling the last digit."""
    all_but_last, last = split(n)
    luhn_digit = sum_digits(2 * last)
    if n < 10:
        return luhn_digit
    else:
        return luhn_sum(all_but_last) + luhn_digit
```

## Lecture 7 Tree Recursion

makes more than one call of func

```python
def compose1(f, g):
    """"Return a function h, such that h(x) = f(g(x))."""
    def h(x):
        return f(g(x))
    return h

def repeated(f, n):
    """Return the function that computes the nth application of func (recursively!).

    >>> add_three = repeated(lambda x: x + 1, 3)
    >>> add_three(5)
    8
       """
    if n == 0:
        return lambda x: x
    else:
        return compose1(f, repeated(f, n-1))
```

## Lecture 8 Sequence and Data Abstraction

### List

initialize :

`digits = [1, 2, 3, 4]`

`digits[3] == getitem(digits, 3)`

concentrate and repeat :

`[5, 6] + 2*digits = [5, 6, 1, 2, 3, 4, 1, 2, 3, 4]`

List unpacking:

list operations :

![cs61a-2](/../../../../assets/pics/CS61a/cs61a-2.png)

![cs61a-3](/../../../../assets/pics/CS61a/cs61a-3.png)

### For Statement

```python
for <name> in <expression>:
  <suite>
 #name can bu multiple if expression is mutiple
#eg:
for x, y in [[1, 2], [3, 4]]:
```



### Range

insert rangement on the left side of index

`range(-2, 2) -> -2, -1, 0, 1`

including start value exclude end value

**list func:**

return back a list contain the element in a sequence

### List Comprehensions

```python
def divisor(n):
  return [1] + [x in range(n) if n%x == 0]
```

### String

quote:

' / " : the same 

""" : for multiple lines

 **an element of a string is itself a string, but only one element**

```python
so:
>>>'here' in "where`s it"
True
but:
>>>234 in [2, 3, 4, 5]
False
>>>[2, 3, 4] in [2, 3, 4, 5]
False
```

### Data abstraction 

- compound objects combine objects together
- a methodology by which funcs enforce an abstraction barrier between representation and use



### Abstraction Barriers

![cs61a-4](/../../../../assets/pics/CS61a/cs61a-4.png)



### Dictionaries

```python
dic = {<key>: <value>, ...}
dic[key] == value
```

- keys can't be dictionaries or list
- dictionaies aren`t ordered
- two keys can't be equal

### Handling iterables functions:

zip(*iterables) 将可迭代对象按照索引位置一一配对，并生成一个新的迭代器

enumerate(iterable, start=0)：返回一个枚举对象，包含可迭代对象中的元素以及对应的索引值。可以通过指定 start 参数来指定起始索引值。

filter(function, iterable)：返回一个由满足指定条件的可迭代对象元素组成的迭代器。function 参数是一个用于筛选元素的函数。

map(function, *iterables)：对一个或多个可迭代对象中的元素应用指定的函数，并返回一个包含结果的迭代器。

reduce(function, iterable[, initializer])：对可迭代对象中的元素依次应用指定的二元函数来进行累积计算。需要导入 functools 模块才能使用。

sorted(iterable, key=None, reverse=False)：返回一个排序后的可迭代对象的新列表。可以通过 key 参数指定一个函数来生成排序的关键字。

any(iterable)：如果可迭代对象中的任何一个元素为真，则返回 True；否则返回 False。

all(iterable)：如果可迭代对象中的所有元素都为真，则返回 True；否则返回 False。



## Lecture 9 Functional Decomposition & Debugging

### Handling Errors

#### exceptions:

assertion statement

raise statement: 

`raise <statement>`

```python
#eg:
raise TypeError('bad argument')
```



1. TypeError:  a func was passed in the wrong num/type of argument
2. NameError: a name wasn't found
3. KeyError: dic doesn't have the key
4. RuntimeError: 

### Try Statement

```python
def invert(x):
  y = 1/x
  print('Never printed if x is 0')
  return y
def invert_safe(x):
  try:
    return invert(x)
  except ZeroDivisionError as e:
    print('handled', e)
    return 0  #return str(e)
"""
>>>invert_safe(0)
handled division by 0
>>>invert_safe(1/0)
Error:....
#Error hapends before excuting func

"""
```

**Reduce a sequence to a value**

```python
def reduce(f, sq, initial):
  """Combine elements of sq starting with initial using f(a two arguments func)#归约"""
  for x in sq:
    initial = f(initial, x)
  return initial

	#recursive version
  if not sq:
    return initial
  else:
    sq, initial = sq[1:], f(initail, sq[0])
    
def divide_all(n, ds):
  try:
    return reduce(truediv, ds, n)
  except:ZeroDivisionError:
    return float('inf')
```



## Lecture 10 Trees

### slicing:

```python
odds = [1, 3, 5, 7]
[odds[i] for i in range(1, 3)]
==
odds[1:3]
== [3, 5]
```

slicing creats a new list

### Sequence Aggregation

sum  max

input can be values or interables such as list

`max(range(10), key = lambda x: f(x))`

key: return an x within 0,9 which gets the biggest f(x)

all:

return True if bool(x) is True 

### Tree Abstraction 

**Two discription**

**Recursive-->wooden tree**

**Relative-->family tree**

![cs61a-5](/../../../../assets/pics/CS61a/cs61a-5.png)

**A tree has a root label and a list of branches **

 **each branch is a tree** 

```python
#implement the tree
"""
>>>tree(3, [tree(1), tree(2, [tree(1), tree(1)])])
[3, [1], [2, [1], [1]]]
---->
         3
       1   2
          1  1
"""
def tree(label, branches=[]):
  for branch in branches:
    assert is_tree(branch)
  return [label] + list(branches)

def label(tree):
  return tree[0]

def branches(tree):
  return tree[1:]

def is_tree(tree):
  if type(tree) != list or len(tree) < 1:
    return False
  for branch in branches(tree):
    if not is_tree(branch):
      return False #resursively check
  return True

def is_leaf(tree):
  return not branches(tree)#check if tree has a branch

def count_leaves(t):
  if is_leaf(t):
    return 1
  else:
  	branches_count = [count_leaves(b) for b in branches(t)]
  return sum(branches_count)

def leaves(tree):
  """Return all the leaves of the tree"""
  if is_leaf(tree):
    return [label(tree)]
  else:
    return sum([leaves(b) for b in branches(tree)], [])
  """
  >>>sum([[1], [2, 3]], []) #use sum(, []) to combine list
  [1, 2, 3]
  """
def increment_leaves(t):
  if is_leaf(t):
    return tree(label(t)+1)
  else:
    branches = [increment_leaves(b) for b in branches(t)]
    return tree(label(t), branches)
def increment(t):
  return tree(label(t)+1, [increment(b) for b in branches(t)])


```

## Lecture 11 Mutable Sequences

### Objects

• Objects represent information 

• They consist of data and behavior, bundled together to create abstractions 

• Objects can represent things, but also properties, interactions, & processes

 • A type of object is called a class; classes are first-class values in Python

-  Object-oriented programming: 
   - A metaphor for organizing large programs
   - Special syntax that can improve the composition of programs

In Python, every value is an object 

All objects have attributes(属性)

 A lot of data manipulation happens through object methods

Functions do one thing; objects do many related things

### Tuples and Sequences

`tuple -->(1, 2, 3, 4)`

`sequence -->[1, 2, 3, 4]`

sequence is mutable

tuple is immutable

![cs61a-6](/../../../../assets/pics/CS61a/cs61a-6.png)



### Mutation

**Equality and Identity**

```python
<exp0> is <exp1>
evaluates True if they evaluate to the same object
<exp0> == <exp1>
evaluates True if they evaluate to equal values
#eg:
>>>[10] == [10]
True
>>>a = [10]
>>>b = [10]
>>>a == b
True
>>>a is b
False
>>>a.append(1)
>>>a
[10, 1]
>>>b
[10]   #change doesnt happen between diff objects
>>>b = a
>>>a.append[1]
>>>b
[10, 1, 1]   #change happens because a is b, they are things with diff names
```

## Lecture 14 Mutable Functions

Assignment in the current frame cannot affect values in the parent frame

to solve this -->nonlocal assignment

```python
def make_withdraw(balance):
  def withdraw(amount):
    nonlocal balence # this statement means change will affect balance in "def make_withdraw" 
    if amount > balance:
      return 'Insuffient funds'
    balance = balance - amount
    return balance
  return withdraw
	
```

when re-binding a nonlocal value 

it will return to find the frame which the name was first binded

--> Future assignments to that name change its pre-existing binding in the first non-local frame of the current environment in which that name is bound.

- a nonlocal name has to appear in previous frame but not in current frame

another way: creat a list (list is also mutable)

### Python Particulars:

- Python pre-computes which frame contains each name before excuting the body of a func
- Within the body of a func, all instances of a name must refer to the same name



**Func mutation will cause referential transparency lost**

## Lecture 14 Iterators & Generators 

### Iterate:

```python
iter(iterable s)#turn s into a iterator
next(iterator)#go to the next element of s
```

over dictionaries :

```python
d = {'one':1, 'two':2}
k = iter(d.keys())
v = iter(d.values())
i = iter(d.items())
>>>next(k)
one
>>>next(v)
1
>>>next(i)
('one', 1)
```

change size of a dictionary while iterating it will cause runtime error -->need to build a new iterator

### Generator

adv: compute lazily(compute when needed)

func uses yield to replace return

yield from < iterable > can yield directly an iterable

```python
def countdown(x):
  if x > 0:
    yield x
    for k in countdown(x-1):
      yield k
    <=>
    yield from countdown(x-1)#avoid using for iterator
```

## Lecture 15 Objects

### Object construction

```python
class Account:
  def __init__(self, account_holder):
    self.balance = 0
    self.holder = account_holder
```

__ init __ is a constructor 

self is the first argument

### Methods 

is an attribute and a function

```python
...
	def deposit(self, amount):
    self.balance = self.balance + amount 
    return self.balance
```

`Object + Function = Bound Method`

Bound method , which couple together a func and the obj on which that method will be invoked 

```python
>>>type(Account.deposit)
<class 'function'>
>>>type(tom_account.deposit)
<class 'method'>
```

### Attributes

use . notation to access or

funcs : getattr(< class >, < attri >)

if we add this to a class:

```python
class Account:
  interest = 0.02
  def __init__:
    ...
    
```

**we say interest is a class attribute, rather a instance one**

**and when finding attributes, instance is prior to calss** 

![cs61a-7](/../../../../assets/pics/CS61a/cs61a-7.png)

## Lecture 16 Inheritance

 relating classes together 

```python
class <subclass>(<base class>):
  <suite>
```

when creating a subclass just put the different part in the suite

**Inheritance represents a is-a relationship**

eg. checkaccount is an account

## Lecture 17 Linked lists & Trees

```python
class Link:
  empty = ()
  def __init__(self, first, rest = empty):
    assert rest is Link.empty or isinstance(rest, Link)
    self.first = first
    self.rest = rest
  def __getitem__(self, i):
    if i == 0:
      return self.first
    else:
      return self.rest[i-1]
  def __len__(self):
    return len(self.rest)+1
	@property 
  def second(self):
    return self.rest.first
  @second.setter
  def second(self, value):
    self.rest.first = value
    
```

`isinstance(a, class b)` is a builtin func to check if a is an instance of b or inherent from b

### Tree Class

```python
class Tree:
  def __init__(self, label, branches=[]):
    self.label = label
    for branch in branches:
      assert isinstance(branch, Tree)
    self.branches = list(branches)
```

### Memorization 

```python
def memo(f):
  cache = {}
  def memorized(n):
    if n not in cache:
      cache[n]=f(n)
    return chache[n]
  return memorized
```

### Circle Linked List

在链表中存在循环意味着链表中的某些节点通过指针形成了一个环路。换句话说，链表中的某个节点的指针指向了链表中已经访问过的节点，从而形成了一个闭合的环。

具体来说，对于一个单向链表，如果链表中存在一个节点，它的指针指向了链表中的另一个节点，并最终导致回到了之前已经访问过的节点，那么就存在循环。

例如，考虑以下链表：

1->2->3->4->5->2->circle

**solution:**

double pointer:

```python
#use a faster pointer and a slower one, faster moves two steps onetime and slower moves one step onetime, if circle exsits, two pointer will meet eventually.
def has_circle(link):
  p_slow, p_fast = link, link
    while p_fast != Link.empty:
        p_slow = p_slow.rest
        if p_fast.rest != Link.empty:
            p_fast = p_fast.rest.rest
            if p_fast == p_slow:
                return True
        else:
            break
    return False
```



## Lecture 18 Interfaces

### String representation

- `str` to humans
- `repr` to Python interpreter

## Lecture 19 Scheme

**Scheme consists of expressions**

primitive expressions and combinations

### Special Forms

- If : (if < judge > < result > < alter >)
- and or :(and/or e1 e2 ...)
- binding :
  - (define < symbol > < expression >)
  - new procedures :(define (< symbol > < formal parameters >) < body >)

### Lambda Expressions

`(lambda(formal parameter) body)`

```scheme
(define (plus4 x)(+ x 4))
==
(define plus4(lambda(x) (+ x 4)))
```

### Scheme Lists

is same as Python`s linked list

```scheme
# x = (cons 2 1)-> creates a list x:2->1
# (car x) -> return the first of x: 2
# (cdr x) -> return the rest of x: 1
# nil : the empty list
```

### Symbolic 

single  ``  `  used on the left side of the element is to express itself 



### Programming Language

a programming language has :

- Syntax:语法
- Semantics:语义

To create a new one, need:

- Specification: a document describes S&S
- Canonical Implementation : an interpreter or compiler



## Lecture 20 Interpreters



## Lecture  21 More Scheme

### Scope:

**lexical:** the parent frame is where the funcs defined(Python Scheme )

**dynamic**: the parent frame is where the funcs called

#### tail recursive 

## Lecture 22 Macros

new kinds of special form 

```scheme
eg:
(define-macro (twice expr)
	(list 'begin expr expr))
#begin means do all of the things
>(twice (print 2))
2 
2
duplicate before evaluating/
otherwise:
(define (twice expr)(list 'begin expr expr))
>(twice (print 2))
None
None
```

define for statement:

```scheme
(define-macro(for sym vals expr)
    list 'map (list 'lambda (list sym) expr) vals)

```

## Lecture 23 Streams

streams are lazy lists in scheme

```scheme
;building a stream range:
(define (range-stream a b)
  (if (>= a b)
      nil
      (cons-stream a (range-stream (+ a 1) b))))
```

## Lecture 24 Declarative Programming

### SQL 


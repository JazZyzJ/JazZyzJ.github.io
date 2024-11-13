# GNU Make
对于大型的项目，我们无法在命令行中一条一条地输入编译命令，这时候就需要一个自动化的编译工具，这个工具就是 make。

一个工程中的源文件不计其数，并且按类型、功能、模块分别放在若干个目录中，makefile定义了一系列的规则来指定，哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为makefile就像一个Shell脚本一样，其中也可以执行操作系统的命令，然后只需要一个make命令，makefile里的规则就会自动生效。

即：**makefile关系到整个工程的编译规则, 采用自动化编译**

## 关于程序的编译和链接
正常的文件在进行编译时，需要经过四个步骤：预处理、编译、汇编和链接。而在makefile中，我们可以将这四个过程简化为两个过程：
1. **编译**：将所有的.c文件编译成.o文件
编译时，编译器需要的是语法的正确，函数与变量的声明的正确。


2. **链接**：将所有的.o文件链接成可执行文件
链接时，主要是链接函数和全局变量。
链接器并不管函数所在的源文件，只管函数的中间目标文件（Object File），在大多数时候，由于源文件太多，编译生成的中间目标文件太多，而在链接时需要明显地指出中间目标文件名，这对于编译很不方便。所以，我们要给中间目标文件打个包，在UNIX下，是Archive File，也就是`.a`文件。

## makefile

make命令执行时，需要一个makefile文件，以告诉make命令需要怎么样的去编译和链接程序。

基础的规则是：
1. 如果这个工程没有编译过，那么我们的所有c文件都要编译并被链接。
2. 如果这个工程的某几个c文件被修改，那么我们只编译被修改的c文件，并链接目标程序。
3. 如果这个工程的头文件被改变了，那么我们需要编译引用了这几个头文件的c文件，并链接目标程序。

makefile的规则：
```makefile
target ... : prerequisites ...
    command
    ...
    ...
```
- target: 目标文件，可以是Object File，也可以是执行文件, 还可以是一个标签.
- prerequisites: 生成target所需要的文件或者目标.
- command: 生成target的命令(命令行)，任意Shell命令.(必须以一个Tab键开始)

target这一个或多个的目标文件依赖于prerequisites中的文件，其生成规则定义在command中, 也就是说
**prerequisites中如果有一个以上的文件比target文件要新的话，command所定义的命令就会被执行。**


!!! example "一个实例"
    ```makefile 
    edit : main.o kbd.o command.o display.o \
            insert.o search.o files.o utils.o
        cc -o edit main.o kbd.o command.o display.o \
            insert.o search.o files.o utils.o

    main.o : main.c defs.h
        cc -c main.c
    kbd.o : kbd.c defs.h command.h
        cc -c kbd.c
    command.o : command.c defs.h command.h
        cc -c command.c
    display.o : display.c defs.h buffer.h
        cc -c display.c
    insert.o : insert.c defs.h buffer.h
        cc -c insert.c
    search.o : search.c defs.h buffer.h
        cc -c search.c
    files.o : files.c defs.h buffer.h command.h
        cc -c files.c
    utils.o : utils.c defs.h
        cc -c utils.c
    clean :
        rm edit main.o kbd.o command.o display.o \
            insert.o search.o files.o utils.o
    ```

    在实际使用过程中，我们通常会使用`make -f <makefile_name>`来指定makefile文件，如果不指定，make会默认使用当前目录下的`Makefile`文件。

    在这个例子中，我们```make```一下就会生成```edit```文件，然后使用```make clean```就会删除```edit```文件。



## 参考

  GNU Make: https://seisman.github.io/how-to-write-makefile/overview.html
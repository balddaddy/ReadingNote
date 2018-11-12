# 2018

## 《Practices of an Agile Developer: Working in the Real World》
### 态度决定一切
做事的原则：
```
1. 出了问题，最高有限级是解决问题，而不是寻找罪魁祸首；遇到问题首先要问“为了解决或缓解这个问题，我能够做什么”
2.
```



## 《PATTERN RECOGNITION and MACHINE LEARNING》 -- Christopher M. Bishop

### P1： Introduction

The machine learning algorithm can be expressed as a function `y(x)`, the precise form of `y(x)` is determined during **traning** phase, a.k.a. **learning** phase on the basis of the training data. Then the model can be used to identify new inputs, which is called **test set**. The ability to categorize correctly new examples that differ from traning data is called **generilization**. **<u>Generilization is a central goal in pattern recognization</u>**.

The original input variables are typically *preprocessed* to transform them into some new space of variables where, it is hoped, the pattern recognition problem will be easier to solve. This pre-processing stage is sometimes also called **feature extraction**.

Cases such as the digit recognition example, in which the aim is to assign each input vector to one of a finite number of discrete categories, are called ***classification problems***. If the desired output consists of one or more continuous variables, then the task is called **regression**.

Applications in which the training data comprises examples of the input vectors along with their corresponding target vectors are known as **supervised learning problems**. In other pattern recognition problems, the training data consists of a set of input vectors without any corresponding target values, which is called **unsupervised learning problems**. Thirdly, the technique of **reinforcement learning** is concerned with the problem of finding suitable actions to take in a given situation in order to maximize a reward.

There are three important tools that are used throughout the machine learning techniques, namely **probability theory**, **decision theory**, and **information theory**.

## 《跟我一起写makefile》

### 一、程序的编译和链接
C/C++的编译过程是
```
首先，编译（compile），把源文件编译成中间代码文件object files。Windows下是.obj文件，UNIX下是.o文件
其次，链接（link），把大量的object files合并成执行文件。
```

* 编译只检查语法正确性，查看函数和变量的声明；链接则基于中间文件链接函数和全局变量，需要查看函数的实现。
* 如果中间文件打包就成为“库文件”（library file，Linux下是Archive File）。


### 二、Makefile介绍
#### 2.1 Makefile 规则

```
target ... : prerequisites ...
command
...
...
```
这里，target表示object file、执行文件或**标签**， 标签是作为**伪目标**使用；prerequisites指要生成target所需要的文件和目标；command为make需要执行的命令。也就是说target这一个或多个目标文件的生成依赖于prerequisites中的文件，而生成规则定义在command中。

举个栗子：
   例2.1

```
edit : main.o kbd.o command.o display.o \
insert.o search.o files.o utils.o
cc -o edit main.o kbd.o command.o display.o \
insert.o search.o files.o utils.o
clean :
rm edit main.o kbd.o command.o display.o \
insert.o search.o files.o utiles.o
```
这里，反斜杠（\）是换行符的意思。可以看出makefile文件的**本质就是定义依赖关系**，即目标文件是由哪些文件生成的。依赖关系定义好以后，后续一行定义了如何生成目标文件的操作系统命令，一定要以*Tab*键做开头。
:warning:clean不是一个文件，冒号后面没有依赖文件列表，因此make不会找文件依赖性，因而也不会执行后面以后命令。通过make后面显示增加label名称，可以强制执行该命令。通过这种label方法，可以完成和编译无关打包、备份等命令。

#### 2.2 makefile中使用变量

 makefile变量相当于C语言中的宏。对于例2.1中的.o文件列表很长的情况，可通过定义变量objects来指代文件列表，在其后通过*$(objects)*来使用该变量，从而减少书写工作量

例2.2：

```
objects = main.o kbd.o command.o display.o \
insert.o search.o files.o utils.o

edit: $(objects)
cc -o edit $(objects)
clean：
rm edit $(objects)
```

### 三、Makefile总述
#### 3.1 Makefile 五大功能

1. 显示规则： 说明要生成的一个或多个目标文件，依赖文件，生成命令；
2. 隐晦规则： 基于makefile的自动推导功能，简略书写；
3. 变量定义： 类似于C语言中的宏，格式为 objects = main.o kbd.o command.o display.o
4. 文件指示： 包括三种，引用另一个makefile，指定makefile中的有效部分，定义一个多行命令；
5. 注释： 用#进行行注释。

> :warning:Makefile中命令必须以[**tab**]键开始。

#### 3.2 Makefile 的文件名规则

   * make命令会寻找文件名为“GNUmakefile”、“makefile”和“Makefile”的文件，这三种最好使用“**Makefile**”。

#### 3.3 引用其它Makefile
   * 用`include<filename>`可以把别的Makefile文件包含进来
      :warning:include之前不能有[**tab**]

#### 3.4 环境变量MAKEFILES
   如果当前环境中定义了环境变量MAKEFILES，则make会将其include。

### 四、书写规则

在Makefile中有且只有一个最终目标，因此必须清晰的表述最终目标。通常，第一条规则中的第一个目标为最终目标。

#### 4.1 规则举例

```
foo.o : foo.c defs.h # foo模块
cc -c -g foo.c # 以Tab开头
```
以上为基本书写规则，foo.o为目标，foo.c, defs.h为依赖文件。
其规则如下：

> 1. 如果foo.c和defs.h的文件日期比foo.o文件日期新，或是foo.o不存在，则依赖关系发生；
> 2. cc命令说明了如何生成foo.o这个文件。
<!--以上规则说明 makefile文件主要说明两件事，即文件依赖关系如何和如何生成目标文件-->

#### 4.2 使用通配符

  > `*` 应用在系统中，指代任意字符；
   `%` 应用在Makefile中，指代任意字符，一旦%匹配到某一字符，后续%也指该字符；
   `$@` 目标的名字；
   `$^` 所有依赖文件
   `$<` 第一个依赖文件
   `$?` 所有更新过的依赖文件
   `$(subst 要被替换的字符串，用来替换的字符串，被处理的字符串)`，例如`$(subst .c, .o, test.c)`得到`test.o`
   `$(wildcard 寻找的文件)`系统中搜寻文件，例如`$(wildcard *c)`寻找所以后缀为.c的文件，返回成以空格隔开的一行字符
   `$(basename 文件名)`得到去掉后缀的文件名，例如`$(basename test.c)`得到`test`.

#### 4.3 文件搜寻

在大型工程中，存在大量源文件，通常做法是把源文件分类存放于不同目录，在makefile文件中加上路径信息，让make根据该信息自动寻找文件。该功能可通过特殊变量*VPATH*实现。如果定义该变量，则make将在当前目录找不到指定文件的情况下，到该变量定义目录下去寻找。

例如：
  > `VPATH=src:../headers`表示在当前目录找不到的情况下到`src`和`../headers`目录下继续寻找；目录由`:`分隔。

另一个方法是使用make的*vpath*关键字。该方法更为灵活，其使用主要有三种方法：
  > 1. `vpath <pattern> <directories>`：为符合模式<pattern>的文件指定搜索目录<directories>
   2. `vpath <pattern>`清除符合模式<pattern>的文件搜索目录
   3. `vpath` 清除所有已经设置好的文件搜索目录

vpath方法中的`<pattern>`需要包含`%`字符，其含义是匹配0或若干字符。例如：
```
vpath %.h ../headers
```
表示在“../headers”目录下搜索所有“.h”结尾的文件。

如果连续使用vpath语句，则出现相同pattern可以省略，例如
```
vpath %.c foo:bar
vpath %   blish
```
上述规则表示现在"foo"目录下寻找".c"结尾的文件，之后是"bar"目录，最后是“blish”目录。


#### 4.4 伪目标

  **伪目标** 并非一个文件，只是一个标签。必须显示地指明该目标才能使其生效，从而完成一些命令操作。为了避免为目标与文件重名，可通过`.PHONY`显示指明一个目标是伪目标,例如
  `.PHONY: clean
  clean:
  rm *.o temp`
  上述代码表示定义伪目标clean，其作用为删除所有.o文件和temp目录。不论是否有`clean`这个文件，要运行clean这个目标都必须使用“make clean”来显示指定执行。

  伪目标一般没有依赖文件，但也可以指定依赖文件。例如：
  ```
  all ： prog1 prog2 prog3
  .PHONY : all

  prog1: prog1.o utils.o
  cc -o prog1 prog1.o utils.o

  prog2 ： prog2.o
  cc -o prog2 prog2.o

  prog3 : prog3.o sort.o utils.o
  cc -o prog3 prog3.o sort.o utils.o
  ```
  由于上述文件中第一个目标(默认目标)为伪目标，而伪目标总会被执行，所以其依赖目标总会被决议，从而生成了三个不同的目标。

  目标也可以做依赖项，伪目标同样可以作为依赖项，例如:
  ```
  .PHONY : cleanall cleanobj cleandiff

  cleanall : cleanobj cleandiff
  rm program

  cleanobj :
  rm *.o

  cleandiff :
  rm *.diff
  ```
  上述代码中，通过“make clean”可以清楚所有要被清除的文件；而“make cleanobj”和“make cleandiff”两个命令可以清楚相应的不同文件。

 #### 4.5 多目标

  Makefile规则中可以使用多目标，同时使用自动化变量来实现不同的执行命令，例如：
 ```
	bigoutput littleoutput : text.g
	generate text.g -$(subst output,,$@) > $@
 ```
  上述规则定价于
 ```
	bigoutput : text.g
	generate text.g -big > bigoutput
	littleoutput : text.g
	generate text.g -little > littleoutput
 ```

#### 4.6 静态模式

静态模式可以更容易地定义多目标规则，使规则变得更加灵活。规则如下:

  ```
  <targets ...> : <target-parttern> : <prereq-patterns ...>
  <command>
  ....
  ```
  上述代码中，*targets*定义了一系列目标文件，可使用通配符，是目标的集合；
​            *target-pattern*指明了targets的模式，也就是目标集模式；
​		       *prereq-patterns*是目标依赖模式，它对target-pattern形成模式再进行一次依赖目标的定义。
​     如果将<target-pattern>定义为“%.o”，意思是<target>集合中都以".o"结尾，而<prereq-patterns>定义为“%.c”，则会对<target-pattern>中形成的目标集进行二次定义，即取<target-pattern>模式中的“%”，为其加上“.c”结尾，形成新的集合。
​	 
  下面举一个:chestnut::

  ```
  objects = foo.o bar.o

  all: $(objects)

  $(objects) : %.o : %.c
  $(CC) -c $(CFLAGS) $< -o $@
  ```
  上述代码中最后两行相当于如下命令：
   ```
   foo.o : foo.c
   $(CC) -c $(CFLAGS) foo.c -o foo.o
   bar.o : bar.c
   $(CC) -c $(CFLAGS) bar.c -o bar.o
   ```
   从上述代码可以看出，静态模式可以用极少量代码实现大量功能。下面再举一个:chestnut::
   ```
   files = foo.elc bar.o lose.o

   $(filter %.o, $(files)) : %.o : %.c
   $(CC) -c $(CFLAGS) $< -o $@
   $(filter %.elc, $(files)) : %.elc: %.el
   emacs -f batch-byte-compile $<
   ```

#### 4.7 自动生成依赖项

  C/C++编译器支持“-M”选项，即自动寻找源文件包含的头文件，并声称依赖关系，通常采用如下命令：
  ```
  cc -M main.c
  ```
  其输出是
  ```
  main.o : main.c defs.h
  ```
  Makefile可以通过该命令，将[.c]文件的依赖关系存放在[.d]文件中，通过make自动更新[.d]文件，从而实现自动化生成依赖关系。举个:chestnut::
  ```
  %.d: %.c
  @set -e; rm -f $@; \
  $(CC) -M $(CPPFLAGS) $< > $@.$$$$; \
  sed 's, \($*\)\.o[ :]*, \1.o $@ : ,g' < $@.$$$$ > $@; \
  rm -f $@.$$$$
  ```
  上述代码中意思是所有[.d]文件依赖于[.c]文件，`rm -f $@`的意思是删除所有目标，也就是[.d]文件，第二行的意思是，为每个依赖文件"$<"，也就是[.c]文件生成依赖文件，"$@"表示模式"%.d"文件。如果一个C文件是name.c，那么%就是name，"$$$$"表示一个随机编号，因此第二行生成的文件可能是"name.d.12345",第三行使用sed命令做了一个替换；第四行是删除临时文件。
  总之，这个模式所做工作就是在编译器生成的依赖关系中加入[.d]文件的依赖，几把依赖关系：
  `main.o : main.c defs.h`
  转换为
  `main.o main.d. : main.c defs.h`
  于是，[.d]文件实现了自动更新。如果在[.d]文件中不但加入完整的依赖项，同时加入生成命令，则该文件将包含文章的规则。之后就可以通过“include”命令来加入该规则，:chestnut::
  `sources = foo.c bar.c`
  `include $(sources: .c = .d)`
  上述代码中"$(sources: .c = .d)"表示把.c文件后缀改为".d"。

  ### 五、书写命令

  #### 5.1 显示命令

  通常make在执行命令时会将命令行显示在屏幕上，当用命令行前放置“@”字符，该命令将不被make显示，例如：
  ```
  @echo 正在编译XXX模块....
  ```
  当执行时，界面输出不会是`echo 正在编译XXX模块`，而是`正在编译XXX模块`。
  若make命令＋参数"-n"或"--just-print"，则只显示命令而不执行；参数"-s"或“--silient”则全面禁止命令显示。

  #### 5.2 命令执行

  当依赖目标比目标新时，make就会执行其后的命令。如果希望上一条命令的结果应用在下一条命令，则应该将两条命令放在同一行，并用分号分隔两条命令。举个:chestnut:
  ```
  exec:
  cd /home/hchen
  pwd
  ```
  上述代码cd没有起到作用，pwd会打印出当前Makefile目录；要想打印出cd进入的目录，需要用下面一个:chestnut:
  ```
  exec:
  cd /home/hchen; pwd
  ```
  上述代码pwd打印目录会是“/home/hchen”

  #### 5.3 命令出错
  当命令成功后会执行下一条命令，如果出错泽慧终止所有规则的执行。若想让make忽略错误，可以在命令行前（Tab之后）加一个减号"-"，标记为不论命令是否出错都认为成功。举个:chestnut:
  ```
  clean:
  -rm -f *.o
  ```
  还有一个全局办法，给make加"-i"或"--ignore-errors"参数，则Makefile所有命令都会忽略错误。如果一个规则以".IGNORE"为目标，则该规则所有命令错误都将忽略。

  另外，make参数"-k"或是"--keep-going"，使得某命令出错以后，其规则仍继续执行。

  #### 5.4 嵌套执行make

  如果工程分为多个目录，则可以在每个目录下放置一个makefile文件，通过总控makefile文件来编译，这样既利于维护每个部分，又使得makefile文件显得简洁。举个:chestnut:
  若子目录subdir下有Makefile文件，则总体makefile可写为：

  ```
  subsystem:
  cd subdir && $(MAKE)
  ```
  其等价于：
  ```
  subsystem:
  $(MAKE) -C subdir
  ```
  上述代码含义是，先进入"subdir"目录，然后执行make命令。

  总控Makefile变量可以向下传递，但不会覆盖下层Makefile定义的变量，除非指定变量传递到下一级：
  ```
  export <variable ...>
  ```
  或者指定变量不要传递到下一级：
  ```
  unexport <variable>
  ```
  下面举个:chestnut:
  ```
  export varialbe = value
  unexport variable += value
  ```
  如果向下传递所有变量，则只要一个`export`命令即可，后面不用跟任何变量。但是:warning:：有两个变量`SHELL`和`MAKEFLAGS`，不论是否export都会传递到下层makefile。
  在嵌套执行make命令时，可以添加参数`-w`或者`--print-directory`，则make在进入或离开目录时会进行信息的显示。

  #### 5.5 定义命令包
​        如果Makefile出现相同命令序列，可以为其定义变量，该变量以`define`开始，以`enddef`结束，举个:chestnut:：
​       ```
​	define run-yacc
​	yacc $(firstword $^)
​	mv y. tab.c $@
​	enddef
​       ```

### 六、使用变量

#### 6.1 变量基础

1. 变量是区分大小写的；

2. 变量在声明时要给予初值，使用时变量名前加“$”符号，最好用“（）”或者"{}"把变量包裹起来；

3. 变量会在使用它的地方精确的展开；

下面举个:chestnut::
```
objects = program.o foo.o utils.o
program : $(objects)
cc -o program $(objects)

$(objects) : defs.h
```

#### 6.2 变量中的变量

变量的定义有两种方法，**第一种使用“=”** ， 该方式会一直查找并使用变量的最终定义，比如下面一个:chestnut::
```
foo = $(bar)
bar = $(ugh)
ugh = Huh?

all:
echo $(foo)
```

上述代码执行后'$(foo)'的值是'Huh?'，因为Makefile会一直查找并使用最终值。

**第二种使用“:=”**，该定义方式中变量不能使用后面的值，只会使用当前定义好的值，:chestnut::

```
x := foo
y := $(foo) bar
x : = later
```
上述代码中"y"的值等于"foo bar"，而不是"later bar"

另外还有一个操作符"?="，其操作为`FOO ?= bar`，含义是：如果FOO没有定义过，定义值为bar，如果定义过，什么都不做。相当于：
```
ifeq ($(origin FOO), undefined)
FOO = bar
end if
```
#### 6.3 变量高级用法

两种变量高级用法：
 1. 替换：替换变量中共有部分，格式为`$(var: a=b)`或`${var: a=b}`，意为把变量‘var’中所有以'a'结尾的'a'替换为'b'，结尾指以空格或结束符结尾。 举个:chestnut:：
 ```
 foo := a.o b.o c.o
 bar := $(foo:.o=.c)
 ```
 上式中，`$(bar) = a.c b.c c.c`

 2. 静态模式，用`%`字符来替换，上面的公式用静态模式表示为：
 ```
 foo := a.o b.o c.o
 bar := $(foo:%.o=%.c)
 ```

 3. 把变量值再当变量，举个:chestnut::
 ```
 x = y
 y = z
 a := $($(x))
 ```
 上面代码中，`$(x)`等于`y`,所以`$($(x))`就是`$(y)`，因此`$(a)`就是`z`。

 另一个:chestnut::
 ```
 x = variable1
 variable2 := Hello
 y = $(subst 1,2,$(x))
 z = y
 a := $($($(z)))
 ```
 上式中，$(a)最终等于Hello。

 #### 6.4 追加变量值

 使用`+=`可以给变量追加值
 ```
 objects = main.o foo.o bar.o utils.o
 objects += another.o
 ```
 这里，`$(objects)`的值变为： “main.o foo.o bar.o utils.o another.o”

 #### 6.5 多行变量
 用`define`关键字设置变量可以换行。`define`后面跟变量名，之后重起一行定义变量的值，定义以endef结束。变量值可以包含函数、命令、文字，或其他变量，注意:warning:命令前面需要有`[Tab]`开头。 下面举个:chestnut::
 ```
 define two-lines
 echo foo
 echo $(bar)
 endef
 ```

 #### 6.6 环境变量

 1. make运行时的环境变量会在make运行时载入Makefile文件；
 2. 如果Makefile中已经定义该变量，或make命令行定义了该变量，则环境变量中的相应值将被替代；
 3. 若make嵌套使用，则上层Makefile定义的变量会以环境变量的方式传递给下层Makefile。默认情况下，只有命令行设置的变量会被传递，定义在文件中的变量如果要向下层传递，需要使用`export`命令。

 #### 6.7 目标变量

 之前提到的变量均为全局变量，而目标变量（Target-specific Variable）则为局部变量，其作用域为本条规则及连带规则中，不影响规则链以外的全局变量值。其语法为：
 ```
 <target...> : <variable-assignment>
 <target...> : overide <variable-assignment>
 ```
 这里，<variable-assignment>可以用`=`、`:=`、`+=`、`?=`等赋值表达式。
 举个:chestnut:：

 ```
 prog : CFLAGS = -g
 prog : prog.o foo.o bar.o
 $(CC) $(CFLAGS) prog.o foo.o bar.o

 prog.o : prog.c
 $(CC) $(CFLAGS) prog.c

 foo.c : foo.c
 $(CC) $(CFLAGS) foo.c

 bar.o : bar.c
 $(CC $(CFLAGS) bar.c
 ```
 上述代码中，不论`$(CFLAGS)`原本值是什么，在prog目标，及其引发的prog.o foo.o bar.o规则中，均为`-g`。

 #### 6.8 模式变量

 目标变量将某一个变量定义在某一个目标相关规则上；而模式变量将一个变量定义在所有符合某种模式的规则上。其语法为：
 ```
 <pattern ...> : <variable-assignment>
 <pattern ...> : overide <variable-assignment>
 ```
 举个:chestnut:：
 `%.o : CFLAGS = -o`，表示所有以[.o]结尾的目标定义CFLAGS变量值为-o。

 ### 七、使用条件判断

 #### 7.1 :chestnut::

 ```
 libs_for_gcc = -lgnu
 normal_libs =

 foo: $(objects)
 ifeq ($(CC), gcc)
 $(CC) -o foo $(objects) $(libs_for_gcc)
 else
 $(CC) -o $(objects) $(normal_libs)
 endif
 ```

上述公式相当于以下代码
```
libs_for_gcc = -lgnu
normal_libs =
ifeq ($(CC), gcc)
libs = $(libs_for_gcc)
else
libs = $(normal_libs)
endif

foo: $(objects)
$(CC) -o foo $(objects) $(libs)
```

 #### 7.2 语法
 条件表达式的语法为:
 `<conditional-directive>`
 `text-if-true`
 `endif`
 或者是：
 `<conditional-directive>`
 `<text-if-true>`
 `else`
 `<text-if-false>`
 `endif`

 其中，`<conditional-direction>`表示条件关键字： "ifeq"、"ifneq"、"ifdef"、"ifndef"，每个关键字语法如下：

 ```
 ifeq (<arg1>,<arg2>)
 ifeq '<arg1>' '<arg2>'
 ifeq "<arg1>" "<arg2>"
 ifeq "<arg1>" '<arg2>'
 ifeq '<arg1>' "<arg2>"
 ```
 上述代码比较"arg1"、"arg2"是否相同。

 ```
 ifneq (<arg1>,<arg2>)
 ifneq '<arg1>' '<arg2>'
 ifneq "<arg1>" "<arg2>"
 ifneq "<arg1>" '<arg2>'
 ifneq '<arg1>' "<arg2>"
 ```
 上述代码比较"arg1"、"arg2"是否不相同。

### 八、使用函数

#### 8.1 函数调用语法

函数调用语法如下：
`$(<function> <arguments>)` 或者 `${<function> <arguments>}`
这里，<function>为函数名，<arguments>为函数参数，参数间以逗号，分隔；函数名和参数之间用空格分隔。下面举个:chestnut::

```
 comma:=,
 empty:=
 space:=$(empty) $(empty)
 foo：=a b c
 bar:=$(subst $(space), $(comma), $(foo))
```
本例中，`bar`的值为`a,b,c`。

#### 8.2 字符串处理函数

##### 1. subst
`$(subst <from>,<to>,<text>)`
**名称**：字符串替换函数--subst
**功能**：把字符串<text>中的<from>替换成<to>
**返回**：函数返回替换后的字符串
:chestnut::`$(subst ee,EE,feet on the street)`，结果为“fEEt on the strEEt".

##### 2. patsubst
`$(patsubst <pattern>,<replacement>,<text>)`
**名称**：模式字符串替换函数
**功能**：查找<text>单词（以“空格”、“Tab”或"回车"、“换行”分隔）是否符合模式<pattern>，如果符合则以<replacement>替换。这里，<pattern>可以包括通配符"%"，表示任意长度字符串。如果<replacement>中包含"%"，那么<replacement>中的%将是<pattern>中%所代表的字符串。
**返回**：函数返回替换后的字符串。
:chestnut::`$(patsubst %.c,%.o,x. c.c bar.c)`，结果为“x. c.o bar.o".

**:warning:Warning**: `$(var:<pattern>=<replacement>)`相当于`$(patsubst <pattern>,<replacement>,$(var))`;
:chestnut:**Example**:如果`objects = foo.o bar.o baz.o`，则`$(object:.o=.c)`和`$(patsubst %.o, %.c, $(objects))`是一样的。

##### 3. strip
`$(strip <string>)`
**名称**：去空格函数--strip
**功能**：去掉<string>字符串开头和结尾的空字符
**返回**：去掉空格的字符串值。
:chestnut::`$(strip a b c )`，结果为“a b c".

##### 4. findstring

`$(findstring <find>,<in>)`
**名称**： 查找字符串函数--findstring。
**功能**： 在字符串<in>中查找<find>字串。
***返回***： 如果找到，则返回<find>，否则返回空字符串。
:chestnut::`$(findstring a,a b c)` 返回'a';
:chestnut::`$(findstring a,b c)`返回空字符串。

##### 5. filter

`$(filter <pattern...>,<text>)`
**名称**： 过滤函数--filter。
**功能**： 以<pattern>模式过滤<text>字符串中的单词，保留符合模式的单词，可以有多个模式。
***返回***： 返回符合<pattern>的字符串。
:chestnut::`sources := foo.c bar.c baz.s ugh.h`
​      `           foo: $(sources)`
​      `           cc $(filter %.c %.s, $(sources)) -o foo`
这里`$(filter %.c %.s, $(sources))`返回'foo.c bar.c baz.s';

##### 6.filter-out

`$(filter-out <pattern...>,<text>)`
**名称**： 反过滤函数--filter-out。
**功能**： 以<pattern>模式过滤<text>字符串中的单词，去除符合模式的单词，可以有多个模式。
***返回***： 返回不符合<pattern>的字符串。
:chestnut::`objects := mainl.c foo.o main2.o bar.o`
​      `           mains=main1.o main2.o`
​      `           $(filter-out $(mains), $(objects))`
返回'foo.o bar.o';

##### 7. sort

`$(sort<list>)`
**名称**： 排序函数--sort。
**功能**： 给字符串<list>中的单词排序（升序）。
**返回**： 返回排序后的字符串。
:chestnut::`（sort foo bar lose）`返回`bar foo lose`
:warning:**Warning**:sort会去掉<list>中相同的单词。

##### 8. word

`$(word <n>,<text>)`
**名称**： 取单词函数--word。
**功能**： 取字符串<text>中第n个单词。
**返回**： 返回<text>中的第n个单词。如果<n>比<text>中单词数要大，则返回空字符串。
:chestnut::`（words 2， foo bar baz）`返回"bar".

##### 9. wordlist

`$(wordlist <s>,<e>,<text>)`
**名称**： 取单词串函数--wordlist。
**功能**： 从字符串<text>中取自<s>开始，到<e>结束的单词串，<s><e>皆为数字。
**返回**： 返回取出的单词串。如果<s>大于<text>单词数，则返回空字符；若<e>大于<text>单词数，则返回从<s>开始的所有单词串。
:chestnut::`(wordlist 2, 3, foo bar baz）`返回"bar baz"。

##### 10. words

`$(words <text>)`
**名称**： 单词个数统计函数--words。
**功能**： 统计<text>中字符串中的单词个数。
**返回**： 返回<text>中的单词数。
:chestnut::`$（words foo bar baz）`返回3.
:bulb::若要取<text>中最后一个单词，可用命令`$(word $(words <text>),<text>)`

##### 11. firstword

`$(firstword <text>)`
**名称**： 首单词函数--firstword。
**功能**： 取字符串<text>中第一个单词。
**返回**： 返回<text>中的第一个单词数。
:chestnut::`（firstword foo bar baz）`返回"foo".

##### 12. 字符串综合实例:chestnut:

混合上述各种命令可以实现比较复杂的功能。例如，make使用"VPATH"变量来制定“依赖文件”的搜索路径。则，通过字符串函数可以利用这个搜索路径来指定编译器对头文件的搜索路径参数CFLAGS：
```
override CFLAGS += $(patsubst %， -I%, $(subst :, ,$(VPATH)))
```
如果$(VPATH)的值为"src:../headers"，则上述命令返回值为"-Isrc -I../headers"，正是cc或者gcc搜索路径。

#### 8.3 文件名操作函数

##### 1. dir
`$(dir <names...>)`
**名称**： 取目录函数--dir
**功能**： 从文件名序列<name>中取出目录部分。目录部分是指最后一个反斜杠"/"之前的部分，如果没有反斜杠，则返回"./"。
**返回**： 返回<names...>中的目录部分。
:chestnut::`（dir src/foo.c hacks）`返回"src/ ./".

##### 2. notdir
`$(notdir <names...>)`
**名称**： 取文件函数--notdir
**功能**： 从文件名序列<name>中取出非目录部分。非目录部分是指最后一个反斜杠"/"之后的部分。
**返回**： 返回<names...>中的非目录部分。
:chestnut::`（notdir src/foo.c hacks）`返回"foo.c hacks".

##### 3. suffix

`$(suffix <names...>)`
**名称**： 取后缀函数--suffix
**功能**： 从文件名序列<name...>中取出各个文件名的后缀。
**返回**： 返回<names...>中的文件名的后缀，如果文件没有后缀则返回空字串。
:chestnut::`（suffix src/foo.c src-1.0/bar.c hacks）`返回".c .c".

##### 4. basename

`$(basename <names...>)`
**名称**： 取前缀函数--basename
**功能**： 从文件名序列<name...>中取出各个文件名的前缀部分。
**返回**： 返回<names...>中的文件名的前缀，如果文件没有前缀则返回空字串。
:chestnut::`（basename src/foo.c src-1.0/bar.c hacks）`返回"src/foo src-1.0/bar hacks".

##### 5. addsuffix

`$(addsuffix <suffix>,<names...>)`
**名称**： 加后缀函数--addsuffix
**功能**： 把后缀<suffix>加到<name...>中每个单词后面。
**返回**： 返回加过后缀的文件名序列。
:chestnut::`（addsuffix .c,foo bar）`返回"foo.c bar.c".


##### 6. addprefix

`$(addprefix <prefix>,<names...>)`
**名称**： 加前缀函数--addprefix
**功能**： 把前缀<prefix>加到<name...>中每个单词前面。
**返回**： 返回加过前缀的文件名序列。
:chestnut::`（presfix src/,foo bar）`返回"src/foo src/bar".


##### 7. join

`$(join <list1>,<list2>)`
**名称**： 连接函数--join
**功能**： 把<list2>中的单词对应地加到<list1>单词后面，如果<list1>单词比<list2>的多，那么多出来的单词将保持原样；如果<list2>的单词比<list1>的多，那么多出来的单词将被复制到<list1>中。
**返回**： 返回连接后的字符序列。
:chestnut::`（join aaa bbb,111 222 333）`返回"aaa111 bbb222 333".

#### 8.4 foreach函数

`$(foreach <var>,<list>,<text>)`
**功能**：把<list>中的单词逐一取出来放到参数<var>所制定的变量中，然后再执行<text>所包含的表达式。每一次<text>会返回一个字符串，循环过程中所返回的每个字符串会以空格分隔，当循环结束，所有返回字符串将是foreach的返回值。
:chestnut::
`names := a b c d`
`files := $(foreach n,$(names),$(n).o)`
上面例子可以看出<var>最好是一个变量名，<list>可以是一个表达式，而<text>一般会使用<var>这个参数来依次枚举<list>中的单词。本例中，`$(names)`中的单词被挨个取出，并存入变量"n"中，"`$(n).o`"每次根据`$(n)`计算出一个值，所有值以空格分隔后作为函数返回值，所以`$(files)`的返回值是"a.o b.o c.o d.o"
**Warning**:warning::foreach中的参数<var>为临时局部变量，函数执行完毕后，参数<var>将不起作用。

#### 8.5 if函数

`$(if <condition>,<then-part>)`
`$(if <condition>,<then-part>,<else-part>)`
**功能**：<condition>参数是if的表达式，若其返回的是非空字符串，则该表达式就相当于真，于是<then-part>会被计算并作为整个if函数的返回值，否则<else-part>会被计算，并作为函数返回值，如果第二种情况下<else-part>没有被定义，则整个函数返回空字符串。

#### 8.6 call函数
`$(call <expression>,<para1>,<para2>,<para3>...)`
**功能**：执行本函数时，<expression>中的变量，如$(1)、$(2)、$(3)等会被参数<para1>、<para2>、<para3>依次取代，而<expression>返回值就是整个函数的返回值。
:chestnut::
`reverse = $(1) $(2)`
`foo = $(call reverse, a, b)`
上式返回值为"a b"

#### 8.7 origin 函数
`$(origin <variable>)`
**功能**：返回变量<variable>来源。<varialbe>为变量名字，不要使用`$`字符。
**返回值含义**：
1. undefined： 没有定义过；
2. default： 默认定义；
3. file： 变量被定义在Makefile中；
4. command line： 被命令行定义；
5. override： 被override指示符重新定义；
6. automatic： 命令运行中的自动化变量。

#### 8.8 shell 函数
`$(shell <command>)`
**功能**：执行操作系统的shell命令后，将命令输出作为函数返回值。
:chestnut:：
`contents := $(shell cat foo)`

#### 8.9 控制make的函数

##### 1. error
产生致命错误，<text>为错误信息。error不会在一被使用后就产生错误信息，如果定义在某个变量中，并在后续脚本中使用该变量，则使用变量时才会触发该错误信息。
:chestnut:1:
`ifdef ERROR_001`
`$(error error is $(ERROR_001))`
`endif`
该脚本在定义ERROR_001之后才会调用error命令。
:chestnut:2:
`ERR = $(error found an error!)`
`.PHONY: err`
`err: ; $(ERR)`
本脚本只有在执行err时才会发生error调用。

##### 2 warning
`$（warning <text...>）`
与error函数类似，唯一区别是不会让make程序退出，仅输出一段警告信息，之后make继续执行。

### 九、make的运行

最简单的就是直接在命令行下输入make命令，make命令会找当前目录的makefile来执行。

#### 9.1 make的退出码
make命令执行后有三个退出码： 0-表示执行成功；1-运行时出现错误；2-使用了make的"-q"选项，并且make使得一些目标不需要更新。

#### 9.2 指定makefile

GNU make的默认规则是在当前目录下依次找GNUmakefile、makefile和Makefile三个文件。一旦找到，就开始读取这个文件并执行。
我们也可以通过make的“-f”或“--file”参数，给make命令指定特殊名字的makefile。:chestnut::
`make -f hchen.mk`
则会执行"hchen.mk"这个文件。

#### 9.3 指定目标

通常make的最终目标是makefile中的第一个目标，其它目标都是由这个目标连带出来的。但是，在make命令后直接跟目标名称就可以指定目标。

makefile中的任何目标都可以被指定成最终目标，但是“-”打头，或者包含“=”的目标除外，因为这些字符会被解析成命令行参数或者变量。

makefile的环境变量“MAKECMDGOALS”中会存放终极目标列表，如果命令行没有指定目标，这个文件为空。通过该变量可以实现一些自动化处理，:chestnut:

`sources = foo.c bar.c`
`ifneq ($(MAKECMDGOALS),clean)`
`include $(sources: .c = .d)`
`endif`
只要输入命令不是"make clean"，则makefile会自动包含"foo.d, bar.d"这两个文件。

在软件发布时，makefile包含了编译、安装、打包等功能，通过上述规则编写makefile可以实现所有这些目标。

- **all**
本为目标是所有目标的目标，其功能是编译所有目标

- **clena**
本伪目标功能是删除所有被make创建的文件。

- **install**
本伪目标功能是安装已经编译好的程序，其功能是把目标执行文件copy到指定目标去。

- **print**
本伪目标功能是列出改变过的源文件

- **tar**
  把源程序打包备份成一个tar文件。

- **dist**
  创建一个压缩文件，一般是把tar文件解压成z文件，或是gz文件

- **TAGS**
  更新所有的目标，以备完整的重新编译使用。

- **check** 和 **test** 
  用来测试makefile流程。

## AntiDrone Signal Processing Algorithm

### spectrum analysis algorithm

The processing process of received signals are listed as following:

1. Down-convert the signal frequency to lower-frequency
2. Zoom transformation, the signals are summed up to resampled, the summing point lengths = Fs/fd, Fs is sampling frequency, fd is analysis band-width.
3. Detect the ratio between spectrum peak of ZFFT result and the mean spectrum value.
4. Calculate the pulse width of the zoom transformed signal, after it passing through low-filter.

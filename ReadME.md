# **2021-22学年第2学期**

## **实 验 报 告**

![zucc](img/zucc.png){width="1.5208333333333333in" height="1.5208333333333333in"}

-   课程名称: <u>编程语言原理与编译</u>
-   实验项目: <u>大作业</u>
-   专业班级: <u>计算1903</u>
-   学生学号: <u>31901083</u>
-   学生姓名: <u>田会文</u>
-   实验指导教师: <u>张芸</u>
-   [github链接](https://github.com/marsgravity/microc)

## 简介

基于microC的编译原理大作业，通过对解释器和编译器的代码开发，实现了一些C语言的语法。

## 结构



- `CLex.fsl`——生成`CLex.fs`词法分析器
- `CPar.fsy`——生成`CPar.fs`语法分析器
- `Absyn.fs` ——抽象语法树
- `Interp.fs`——解释器
- `Comp.fs`——编译器
- `Contcomp.fs`——优化编译器
- `micorcc.fsproj`——项目文件

- `Machine.java`——生成`Machine.class`虚拟机和`Machinetrace.class`堆栈追踪

- `example文件夹`——测试程序

- `img文件夹`——运行结果



## 用法

  #### 1. 解释器

```sh
# 生成扫描器
dotnet "C:\Users\Administrator\.nuget\packages\fslexyacc\10.2.0\build\/fslex/netcoreapp3.1\fslex.dll"  -o "CLex.fs" --module CLex --unicode CLex.fsl

# 生成分析器
dotnet "C:\Users\Administrator\.nuget\packages\fslexyacc\10.2.0\build\/fsyacc/netcoreapp3.1\fsyacc.dll"  -o "CPar.fs" --module CPar CPar.fsy

# 命令行运行程序
dotnet fsi 

#r "nuget: FsLexYacc";;  //添加包引用
#load "Absyn.fs" "Debug.fs" "CPar.fs" "CLex.fs" "Parse.fs" "Interp.fs" "ParseAndRun.fs" ;; 

open ParseAndRun;;    //导入模块 ParseAndRun
fromFile "example\1.c";;    //显示 1.c的语法树
run (fromFile "example\1.c") [17];; //解释执行 1.c
#q;;
```

  #### 2. 编译器

```sh
# 启动fsi
dotnet fsi

#r "nuget: FsLexYacc";;

#load "Absyn.fs"  "CPar.fs" "CLex.fs" "Debug.fs" "Parse.fs" "StackMachine.fs" "Backend.fs" "Comp.fs" "ParseAndComp.fs";;   

# 运行编译器
open ParseAndComp;;
compileToFile (fromFile "example\1.c") "example\1";; 
#q;;
```

#### 3. 优化编译器

```sh
dotnet fsi 

#r "nuget: FsLexYacc";;

#load "Absyn.fs"  "CPar.fs" "CLex.fs" "Debug.fs" "Parse.fs" "StackMachine.fs" "Backend.fs" "Contcomp.fs" "ParseAndContComp.fs";;   

open ParseAndContcomp;;
contCompileToFile (fromFile "example\1.c") "example\1.out";;
#q;;
```

#### 4. 虚拟机构建与运行

```sh
javac Machine.java # 构建 java 虚拟机
java Machine ./example/9.out 3 # 执行虚拟机
java Machinetrace ./example/9.out 0 # 堆栈追踪
```




## 功能实现

  #### 1. for循环

测试样例

```c
void main(int n) {
  int i;
  for (i = 0; i < n; ++i) {
    print i;
  }
}
```

解释执行结果和语法树

![](img\for1.JPG)

编译执行结果（**backwards**编译器）

![](img\for2.JPG)

虚拟机执行并查看堆栈变化

![](img\for3.png)

  #### 2. do while

测试样例

```c
void main(int n)
{
  int i;
  i = 0;
  do {
    print i;
    i = i + 1;
  } while (i < n);
}
```

解释执行结果和语法树

![](img\dowhile1.JPG)

编译执行结果

![](img\dowhile2.JPG)

虚拟机执行并查看堆栈变化

![](img\dowhile3.png)

  #### 3. until

测试样例

```c
void main(int n)
{
  int i;
  i = 0;
  until (i > n) {
    print i;
    i = i + 1;
  } 
}
```

解释执行结果和语法树

![](img\until1.JPG)

编译执行结果

![](img\until2.JPG)

虚拟机执行并查看堆栈变化

![](img\until3.png)

![](img\until4.png)

  #### 4. do until

测试样例

```c
void main(int n)
{
  int i;
  i = 0;
  do {
    print i;
    i = i + 1;
  } until (i > n);
}
```

解释执行结果和语法树

![](img\dountil.JPG)

编译执行结果

![](img\dountil2.JPG)

虚拟机执行并查看堆栈变化

![](img\dountil3.png)

  #### 5. switch case

测试样例

```c
void main(int n) {
  switch(n) {
    case 0: print 0;
    case 1: print 1;
    case 2: print 2;
  }
}
```

解释执行结果和语法树

![](img\switchcase1.JPG)

编译执行结果

![](img\switchcase2.JPG)

虚拟机执行并查看堆栈变化

![](img\switchcase3.png)

  #### 6. 自增自减

测试样例

```c
void main(int n){
  print n;
  ++n;
  print n;
  --n;
  print n;
}
```

解释执行结果和语法树

![](img\++--1.JPG)

编译执行结果（**backwards**编译器）

![](img\++--2.JPG)

虚拟机执行并查看堆栈变化

![](img\++--3.png)

  #### 7. += -= *= /= %=

测试样例

```c
void main(int n){
  n += 2;
  print n;
  n -= 2;
  print n;
  int i;
  i = 2;
  n *= i;
  print n;
  n /= i;
  print n;
  n %= i;
  print n;
}
```

解释执行结果和语法树

![](img\运算=1.JPG)

编译执行结果（**backwards**编译器）

![](img\运算=2.JPG)

虚拟机执行并查看堆栈变化

![](img\运算=3.png)

![](img\运算=4.png)

  #### 8. 三目运算符

测试样例

```c
void main(int n) {
    int i;
    i = n > 5 ? 100 : -100;
    print i;
}
```

解释执行结果和语法树

![](img\三目运算符1.JPG)

编译执行结果

![](img\三目运算符2.JPG)

虚拟机执行并查看堆栈变化

![](img\三目运算符3.png)

## 自评等级 

| 功能           | 评分 |
| -------------- | ---- |
| for 循环       | ⭐⭐⭐⭐ |
| while          | ⭐⭐⭐⭐ |
| do while       | ⭐⭐⭐⭐ |
| until          | ⭐⭐⭐  |
| do until       | ⭐⭐⭐  |
| switch case    | ⭐⭐⭐  |
| 自增自减       | ⭐    |
| += -= *= /= %= | ⭐⭐⭐⭐ |
| 三目运算符     | ⭐⭐⭐⭐ |

## 提交记录及分工

- 分工

  | 姓名   | 分工 |
  | ------ | ---- |
  | 田会文 | 全部 |

- 提交记录截图

  

## 心得体会

​	编译原理是一门非常有意思的课程，能够亲自动手实践完成一个全新的语言，是一件很有成就感的事。同时编译原理对我来说也是一门比较难的课程，其中概念抽象而难理解。因为我能力不足，大作业最终做了在现有代码上进行改进的工作。通过该课程的学习，我对编程原理有了更深的理解，明白了其内部的运行机制。在本次实验过程中，我学习了一种新的语言 F# ，并对之前从未接触过的函数式编程有了一定的了解。这门课程的学习，开阔了我在编写代码上的视野，收获颇丰。
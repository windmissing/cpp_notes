链接就是指把各个独立但不完整的模块组装成一个可执行文件的过程。

```
aaa.o  ----|  链接
bbb.a  ----|-------> 可执行文件
ccc.so  ---|
```

>链接主要要处理的工作是将各个模块之间相互引用的部分都处理里，使得各个模块之间能够正确地衔接。

本文将介绍可重定位目标文件的之间链接，这种链接方式称为静态链接。[什么是可重定位目标文件？](http://blog.csdn.net/mishifangxiangdefeng/article/details/52042291)

静态链接主要包括这些步骤：合并目标文件、虚拟地址分配、符号解析(symbol resolution)和重定位（relocation）

<!-- more -->

#### O、要解决的问题
先来看看可重定位目标文件之间的链接需要解决什么问题  
![](http://img.my.csdn.net/uploads/201607/26/1469501843_7909.jpg)  
图中有两个目标文件，每个目标文件由红色的代码段和绿色的数据段组成。  
由于目标文件还不是可执行文件，没有分配起始地址，那么文件中的每个符号和指令的地址也是未知的。  
代码段中的指令，要访问本文件中的指令（函数调用）和数据（读写变量），也会访问其它文件中的指令（extern函数调用）和数据（extern变量读写）。
访问需要地址，除了访问文件内的指令使用的是相对跳转以外，其它的地址都是未知的。可重定位目标文件之间的链接需要确定这些地址。 

可重定位目标文件之间的链接主要包括这些步骤：合并目标文件、地址、重定位

##### 一、合并目标文件

把各个目标文件相同的内容叠到一起，就形成了一个新的目标文件。  
新目标文件同样主要由代码段和数据段组成，包含了所有目标文件中的指令和数据。  
![](http://img.my.csdn.net/uploads/201607/26/1469502773_2364.jpg)

#### 二、分配地址空间

合成前的目标文件没有虚拟内存地址（VMA）。  
![](http://img.my.csdn.net/uploads/201607/26/1469510256_1639.jpg)  
合成以后的目标文件会分配一个起始地址。这个地址与操作系统的虚拟地址分配策略有关有关。
![](http://img.my.csdn.net/uploads/201607/26/1469510343_3935.jpg)   
有了起始地址以后，文件内的每个指令和数据的地址也就确定了。

##### 三、重定位

在链接之前，符号的地址是不确定的。指令中需要引用符号的地方，使用的都是一个临时地址。  
所有符号的地址都确定以后，就要修正这些地址了。  
![](http://img.my.csdn.net/uploads/201607/26/1469510871_5660.jpg)  
正常情况下，完成重定向以后所有符号都可以正确引用了，新生成的目标文件就是可执行文件。

#### 四、符号冲突

但是本文中的例子不能得到上面的happy endding。因为两个目标文件中定义了同一个符号。  
在合并的过程中发现符号重定义会怎样处理呢？这就是两个符号之间的one-on-one的较量了。  
对于合并目标文件是的符号冲突处理，链接器使用强弱符号原则：  

|symbol in a.cpp|symbol in b.cpp|result|
|---|---|---|
|strong|strong|error|
|strong|weak|the strong one|
|weak|weak|the bigger one|

怎么判断一个符号是强还是弱呢？  
函数和已定义的全局变量是强符号。  
未定义的全局变量是弱符号。

上面例子中冲突的两个都是强符号，因此出现链接错误。

#### 五、符号缺少

在合并的过程中不会发现符号缺少的问题，要到重定义的时候才会有。  
如果出现符号未定义，链接器会报错。  

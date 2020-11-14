# 关于strlen和sizeof

(1)`char a[5] = "1,2,3,4,5";`

strlen(a) = 11

sizeof(a) = 5

(2)`char a[8] = "1,2,3,4,5";`

strlen(a) = 5

sizeof(a) = 8

(3)`string str = "12345";`

sizeof(str) = 16

(4)`string str = "123456789123456789";`

sizeof(str) = 16

总结：

char a[5]是个数组，数组的初始化 是 如果不够就补0 ，char a[5] = "1,2,3,4,5" 刚好 5个 ，strlen 不知道什么时候会停  ，所以这个打印应该是随机的

对字符数组使用strlen，若数组没有满，strlen=数组中元素的个数

对字符数组使用sizeof，sizeof=数组大小，与数组中元素的个案无关

对string使用strlen，语法错误

对string使用sizeof，sizeof=16，要看string是怎么实现的。

# 结构体中成员的存储

## 结构体的大小

下面程序的输出结果为（）

```C
#pragma pack(8)   
union A  
{  
    char a[13];  
    int b;  
};  
int main(void)  
{  
    cout<<sizeof(A)<<endl;  
    return 0;  
}  
```

A、4      B、8       C、16        D、12

答：C

## 结构体成员的存储

1、下面程序的输出结果为（）

```C
struct Test  
{  
    unsigned short int a:5;  
    unsigned short int b:5;  
    unsigned short int c:6;  
};  
int main(void)  
{  
    Test test;  
    test.a=16;  
    test.b=4;  
    test.c=0;  
    int i=*(short*)&test;  
    printf("%d\n",i);  
    return 0;  
}  
```

A、6         B、144            C、5            D、95

答：B

1、程序中':'的作用，如 unsigned short int a:5;表示变量a占了5个bit的空间，这样的话结构体所占的变量空间为5+5+6，暂且表示为000000|00000|00000，对应c|b|a
2、在主程序中对结构体初始化a=16,b=4,c=0,转换为二进制放到上面的空间，000000|00100|10000同样对应a|b|c
3、后面一句int i=*(short*)&test;取结构体test的地址空间，就是上面的000000|00100|10000，转换成short型，也就是144

2、下面程序的输出结果为（）

```C
#include<stdio.h>
union myun
{
  struct
  {
  int x, y, z;
  }u;
  int k;
}a;
 
int main()
{
  a.u.x=4; a.u.y=5; a.u.z=6;
  a.k=0;
  printf("%d\n",a.u.x);
}
```

分析：输出0
x和k共用一个存储单元，语句a.k=0;覆盖掉了a.u.x的值。
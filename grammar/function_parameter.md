# 参数为数值，传参的三种方式

## 1.按值传递

```c++
void swap(int a, int b)
{
	int temp = a;
	a = b;
	b = temp;
}
int main()
{
	int a = 2, b =3;
	swap(a, b);
	return 0;
}
优点：便于阅读

缺点：不能改变主函数中的值
```
 

## 2.按指针传递

```c++
void swap(int *a, int *b)
{
	int temp = *a;
	*a = *b;
	*b = temp;
}
int main()
{
	int a = 2, b =3;
	swap(&a, &b);
	return 0;
}
```

优点：可以改变主函数的值，传参时不需要大量的复制

缺点：不易阅读，容易出错

 
## 3.按别名传递

```c++
void swap(int &a, int &b)
{
	int temp = a;
	a = b;
	b = temp;
}
int main()
{
	int a = 2, b =3;
	swap(a, b);
	return 0;
}
```

优点：能改变主函数的值，易阅读，传参时不需要大量的复制

# 参数为一维数组

传递数组的3种方式

1）void func(int a[]);

2）void func(int a[5]);

3）void func(int *a);

```c++
void func1(int a[], int n);
void func2(int a[5], int n);
void func3(int *a, int n);
int main()
{
	int a[10], i;
	for(i = 0; i < 10; i++)
		a[i] = i + 1;
	func1(a, 10);
	func2(a, 10);
	func3(a, 10);
	return 0;
}
void func1(int a[], int n)
{
	int i;
	for(i = 0; i < n; i++)
		cout<< a[i] << ' ' ;
	cout<<endl;
}
void func2(int a[5], int n)
{
	int i;
	for(i = 0; i < n; i++)
		cout<< a[i] << ' ' ;
	cout<<endl;
}
void func3(int *a, int n)
{
	int i;
	for(i = 0; i < n; i++)
		cout<< a[i] << ' ' ;
	cout<<endl;
}
```

输出：

1 2 3 4 5 6 7 8 9 10
1 2 3 4 5 6 7 8 9 10
1 2 3 4 5 6 7 8 9 10

# 参数不确定

一个函数输入的参数个数不确定，可以使用类似“命令行参数”的方法实现，如下：

```C++
#include <stdio.h>
void proc(char **p);
 
int main()
{
	char s[124] = {0};
	char *str[10] = {0};
	
	gets(s);
	printf("s: %s \n",s );
	int i;
	int j = 0;
	str[0] = s;
	for(i = 0; s[i] != '\0'; i++)
	{
		if(s[i] == ' ')
		{
			j++;
			s[i] = '\0';
			str[j] = &s[i+1];
		}
	}
	
	proc(str);
	
	return 0;
}
 
void proc(char **p)
{
	int i = 0;
	while(1)
	{
		if(p[i] != NULL)
		{
			printf("%s \n", p[i]);
		}
		else
		{
			break;
		}
		i++;
	}
}
```

这个函数要注意的是：

str[j] = &s[i+1];让指针数组的某一项指向数组的某一项，s是在栈里面的，str[j] 指向的是一样栈的空间 ，现在把str传给另一个函数，栈的内容都没有了，proc怎么访问p的内容呢？
答：
栈是先入后出的 先入的肯定 还没有释放掉 ，mian函数调用 proc ， proc都没有返回 mian的 栈 不可能被销毁，这个问题 是调用函数 用到了被调用函数的栈 内地址，被调用函数 用调用函数的 栈地址 肯定是安全的
 

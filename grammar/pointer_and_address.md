# 指针与取地址的组合使用

问：`char (*(*x[3])())[5]`，x是什么类型？  
答：参考 http://topic.csdn.net/u/20120926/10/6b423fa9-e7c9-4584-b7b7-63607657e21d.html?54424

问：`*(short *)&byte[0]`是什么意思？  
答：参考 http://topic.csdn.net/u/20120927/16/03b87825-0650-43c7-b3c1-ef955cfc863c.html?9294

# 函数取地址

```c++
class A  
{
public:
	void f()
	{
		int f = 0;
	}
	void h()
	{
		int h = 0;
	}
	virtual void g() = 0;
};
int main()
{
	cout<<&A::f<<endl; // 输出结果：1
	cout<<&A::h<<endl; // 输出结果：1
	cout<<&A::g<<endl; // 输出结果：1
	return 0;
}
```

解释：代码中输出的不是函数的地址。事实上没有什么通用的方法可以得到成员函数的地址。cout不知道怎么输出成员函数的地址，于是将成员函数的地址转换为另一个类型（即bool型）输出。成员地址均非零，因此输出1。
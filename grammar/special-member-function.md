# 一、什么是特殊的成员函数
 
- 拷贝构造函数：`T::T(const T& rhs)`  
[复制构造函数总结](https://windmissing.github.io/cpp_notes/grammar/2012-01-12-copy-constructor-summarize.html)  
此复制构造函数非我们熟知的构造函数。需要以一个对象为模板创建另一个对象时自动调用。   

- 拷贝赋值函数：`T&T::operator=(const)T& rhs）`  
这是对"操作符="的重载函数，把一个对象的内容赋值给另一个对象时自动调  

- 移动构造函数：`T::T(T&& rhs)`  
- 移动赋值函数： `T& T::operator=(const T&& rhs)`   
- 析构函数 `~T()`  
在释放对象时自动调用，与构造函数相对应  

为描述简单，以下用一些简化写法来指代这些函数：  

costructor：构造函数  
copy constructor：拷贝构造函数  
move constructor：移动构造函数  
move =：移动赋值函数  
copy = ：拷贝赋值函数
delete ：析构函数

# 特殊成员函数的语法规则

1. constructor, copy constructor, move constructor，显式地定义了其中任意一种，则C++不会提供default constuctor。  

2. move constructor, move =, 显式地定义了其中任意一种，则C++不会提供default copy = 。

3. 显式地定义了除default constructor以外的任意一种，则无default move constuctor。把函数定义为`=delete`也算是显式地定义了。  

4. move = 与move consturctor的规则相同

# 特殊成员函数的使用规则

1. 三法则：析构函数、拷贝构造函数、拷贝赋值函数，此三者，用一个则必须三个全用。  
2. 五法则：以上五个函数，如果用了移动相关的函数，则必须五个全用。  
可以只用拷贝，不使用移动。  
3. 基类的delete应定义为virtual  
4. 零法则：自定义的delete、move/copy constructor、move/copy =应只用于处理所有权问题。若无所有权问题，不应该自定义这些函数。**这与传统的说法不一样。但在智能指针与RAII的帮助下，是可以做到的。**

# 二、复制控制成员的特点
## 相同点  
把这三个成员函数归为一类函数，是因为它们有许多相同点：  
 - 每个类都有这三函数，如果作者没有显示地写出来，编译器会提供默认的  
 - 它们只能修改类的非static成员，对于static成员，只能读不能写  
 - 特殊情况下（具体指针类型的成员变量时），通常需要自己重载这三个函数  
 - 当你发现需要自己定义其中一个函数时，通常意味着你还要重载另外两个函数  
 - 它们都被编译器默默地调用的  
## 不同点
 - 赋值操作符与复制构造的区别，只是不用为对象开辟新空间
 - 即使自己定义了析构函数，系统还是会执行默认的析构函数，顺序是先运行自己的析构函数，再运行默认的析构函数

# 复制构造函数
详见：[复制构造函数总结](http://windmissing.github.io/%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80/2012-01/copy-constructor-summarize.html)  

# 赋值操作符函数

赋值操作符也是依次复制每个非static成员，相当于memcpy。  
赋值操作符与复制构造的区别，只是不用为对象开辟新空间

# 析构函数  
## 1.成员被析构的顺序  
成员被析构的顺序与成员被创建对象的逆序，也即声明次序的逆序  
例：  

```c++
class A  
{  
    int x;  
public:  
    A(int i){x = i;cout<<"construct A "<<i<<endl;}  
    ~A(){cout<<"delete A "<<x<<endl;}  
};  
class B  
{  
    A a;  
    A b;  
    A c;  
public:  
    B():c(3),b(2),a(1){cout<<"construct B"<<endl;}  
    ~B(){cout<<"delete B"<<endl;}  
};  
int main()  
{  
    B b;  
    return 0;  
}  
```

输出结果：  

```
construct A 1
construct A 2
construct A 3
construct B
delete B
delete A 3
delete A 2
delete A 1
```

这里特意让初始化列表的顺序不同，可以看出构造与析构的顺序与初始化列表无关，只与声明顺序有关

## 2.和其它函数不同的是，即使定义了自己的析构函数，还是会执行默认的析构函数

先运行自己的析构函数，再运行默认的析构函数

# 为什么要重载复制控制成员？
## 深层复制 vs 浅层复制  
通常情况下，一个对象占据一段连续的内存空间。  
对象的复制只指把这一段空间的数据复制到另一段空间。  
特殊情况下，对象的数据包含另一段空间的地址，那么被指向的那一段空间是否属于这个对象呢？是否有必要复制这一段空间呢？  
**浅层复制**的作法是只复制对象占据的空间，不复制对象指向的空间。那么复制后的对象与原对象对指向同一空间。  
**深层复制**的作法是把对象占据的空间和对象指向的空间都复制一遍，复制后两个对象不太有关联。  
# 复制控制成员  
在复制控制函数中，复制时只复制内容，这种复制称为浅层复制

比如复制指针，只复制指针中的地址，不复制指针指向的内容。

当类中有指针成员时，浅层复制会出错。需要定义自己的深层复制

如果需要定义自己的析构函数，就肯定需要定义自己的所有这也三个控制成员函数

# 定义自己的控制成员函数

```c++
class A  
{  
    int val;  
    int *ptr;  
public:  
    /*构造函数 
    ptr(new int(p))：创建一个int，int的内容与p相同，ptr指向该int*/  
    A(const int &p, int i):ptr(new int(p)),val(i){}  
    /*复制构造函数 
    *orig.ptr：取指针orig.ptr指向的内容 
    ptr(new int(*orig.ptr))：创建一个int，int的内容与*orig.ptr相同，ptr指向该int*/  
    A(const A &orig):ptr(new int(*orig.ptr)),val(orig.val){}  
    /*赋值操作符不需要开辟空间*/  
    A& operator=(const A &orig)  
    {  
        /*是函数内容的意思 
        rig.ptr指向的内容赋给ptr指向的内容*/  
        *ptr = *orig.ptr;  
        val = orig.val;  
        return *this;  
    }  
    //析构函数  
    ~A(){delete ptr;}  
};  
```

需要注意的是，在本例中，this对象可以访问orig对象的私有成员。

对于私有的定义是这样的：只允许类的创建者(在这里是this)和该类的成员函数可以访问

我理解的可以访问是指可以访问创建者的私有成员。

为什么在本例中this对象可以访问orig对象的私有成员？求解释

# 移动

移动方便了资源的传递，也可以让函数返回大的对象和容器。  
例子：  

```c++
string result = string("Hello, ") + name + "."; 
```
在C++98中，没有移动语义，这句话存在大量的拷贝构造与销毁，非常低效  
在C++11中，用移动代表拷贝，没有不必要的额外开销。  

## 在返回值处使用移动构造

- 错误写法：  

```c++
T&& wrong_move()
{
	T t;
    return std::move(t);
}
```

这种写法返回一个栈上空间的引用，是未定义的行为

- 不正确的写法

```c++
T bad_move()
{
    T t;
    return std::move(t);
}
```

这种写法没有语法错误，但是不推荐，因为这里的move会禁止NRVO。  

- 正确写法

```c++
T correct_move()
{
    T t;
    return t;
}
```

如果T定义了移动语义，C++会自动调用移动构造而不是拷贝构造。  

## 在参数中使用移动构造

1. 例子

```c++
void func(T && rhs)
{
}

func(create());
```

create()返回一个T对象的右值。  
将右值移动到func的函数中

2. 例子

```c++
void func(T && rhs)
{
    func2(rhs);
}
```

func的形参rhs是在一个右值的引用变量，但到了func2的实参中，就变了一个左值

3. 例子

```c++
void func(T && rhs)
{
    func2(std::move(rhs));
}
```

为了保持rhs的右值性，在func2中增加std::move()将右值转为左值。  

4. 通用模板函数  
如果例子3中的func是一个通用模板函数，那么rhs有可能是一个右值的引用，则需要std::move。rhs也有可能是左值的引用，则不能使用std::move。如何将这两种情况统一?  
解决方法：  
使用std::forward<T>(x)代替std::move<T>(x)可保持x的引用类型。  

# noexcept

关键字noexcept用于标记函数不会抛异常  
以下函数建议使用些关键字：  
析构函数  
移动构造  
移动赋值  
交换(swap)

# notes

如果类中有指针成员，复制控制函数一定要自己写。

有时候，复制控制的应用不是很明显，容易被忽略。

所以发现类中有指针成员时，一定要仔细分析类的使用，不能忽视任意一处可能的复制控制的使用

最保险的方法就是，只要看到类中有指针成员，就自己写复制控制

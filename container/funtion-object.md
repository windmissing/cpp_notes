函数类是指在struct或class中实现了`operator()`的类

# C++提供的两个函数类

## less

less的定义：

```c++
template<class T>
struct less : binary_function<T, T, bool>
{
    bool operator()(const T& x, const T& y) const
    {
        return x < y;
    }
}
```

less的用法：

```c++
std::less()
```

less是C++提供的用于容器元素互相比较的默认比较函数。  
也可以自己实现一个类似less的函数类，并设置到容器中。  

## hash

# 函数类的使用

sort可以接收函数指针作为参数，而map只能接收函数类。  

例子：

```c++
bool my_greater(int x, int y)
{
    return x > y;
}
```

sort的使用

```c++
vector<int> v{5, 7, 3, 9, 2};

// sort使用函数指针
sort(v.begin(), v.end(), my_greater);
// sort使用函数类
sort(v.begin(), v.end(), std::less<int>());

// set使用函数指针
std::set<int, my_greater> s{5, 7, 3, 9, 2}; //编译出错
// set使用函数类
std::set<int, std::less> s{5, 7, 3, 9, 2};
```
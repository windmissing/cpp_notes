# 测试用例为什么要参数化？

一个用例同时测试多个数据，测试用例与测试数据的解耦

例如：

```c++
TEST(NumberTest, IsEven)
{
	EXPECT_TRUE(IsEven(0));
	EXPECT_TRUE(IsEven(2));
	EXPECT_TRUE(IsEven(4));
}

TEST(NumberTest, IsOdd)
{
	EXPECT_FALSE(IsOdd(0));
	EXPECT_FALSE(IsOdd(2));
	EXPECT_FALSE(IsOdd(4));
}
```

# 参数化的方法

1. 定义一个测试类，该类继承`::testing::TestWithParam<int>`，表示该测试类支持测试数据的参数化。其中`int`为参数的类型。对类的实现没有要求。  
2. 定义测试数据。必须使用关键字`INSTANTIATE_TEST_CASE_P`。
3. 测试用例使用关键字`TEST_P`，表示这里面会用到预定义的参数。
4. 在测试用例中，使用`int n = GetParam();`代替原先的测试数据。

```c++
class NumberTest : public ::testing::TestWithParam<int>
{};

INSTANTIATE_TEST_CASE_P(anyname, //测试用例的前缀，可以任意取
                        NumberTest, // 测试类的名字。一组参数只供一个测试类使用
                        test::Values(0,2,4) // 参数生成器，用于设置测试数据
                       );

TEST_P(NumberTest, IsEven)
{
	int n = GetParam();
	EXPECT_TRUE(IsEven(n));
}

TEST_P(NumberTest, IsOdd)
{
	int n = GetParam();
	EXPECT_False(IsOdd(n));
}
```

# 参数生成器

上文中使用了参数生成器`test::Values(0,2,4)`，这是一种基于指定数据生成参数的方法。还有很多自动生成有规律的参数的参数生成器。

||||
|---|---|---|
|列表|`Values(v1, v2, v3, ..., vn)`||
|范围|`Range(begin, end[, step]`|范围在begin~end之间，步长为step，不包括end|
|真假|`Bool()`|取`false` 和 `true` 两个值|
|数组、容器|`ValuesIn(array)`|从一个C类型的数组或是STL容器取值|
|迭代器|`ValuesIn(begin, end)`||
|组合|`Combine(g1, g2, ..., gn)`|它将g1,g2,...gN进行排列组合，g1,g2,...gN本身是一个参数生成器，每次分别从g1,g2,..gN中各取出一个值，组合成一个元组(Tuple)作为一个参数。<br>说明：这个功能只在提供了`<tr1/tuple>`头的系统中有效。gtest会自动去判断是否支持`tr/tuple`，如果你的系统确实支持，而gtest判断错误的话，你可以重新定义宏`GTEST_HAS_TR1_TUPLE=1e`|


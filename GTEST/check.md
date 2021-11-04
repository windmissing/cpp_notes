# 致命断言 *VS.* 非致命断言

`ASSERT_XXX`: 致命断言  
`EXPECT_XXX`: 非致命断言  
以下都以非致命断言为例。

# 基本断言

`EXPECT_TRUE(条件)`

# 大小比较

`EXPECT_EQ/NE/LT/LE/GT/GE(期望值，实际值)`

# 字符串比较

`EXPECT_STREQ/STRNE/STRCASEEQ/STRCASENE(期望值，实际值)`

# 浮点数检查

```
EXPECT_FLOAT_EQ()
EXPECT_DOUBLE_EQ()
EXPECT_NEAR(..., ..., abs_error)
```

# 其它断言功能

- 显式返回成功或失败
- 异常检查
- 类型检查
- predicate检查
- windows HRESULT检查
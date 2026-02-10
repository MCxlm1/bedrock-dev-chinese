---
title: 共享构造
description: JSON 构造可以在附加包系统的多个位置表达。
mentions:
    - Ciosciaa
    - ThomasOrs
    - QuazChick
---

JSON 构造可以在附加包系统的多个位置表达。

## 方块描述符

方块描述符可用于检查符合指定属性的方块。
例如，方块描述符用于方块[放置过滤器](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/blocks/block-components#placement-filter)以确定自定义方块可以在哪些类型的方块上存活。

<CodeHeader>标识符对象示例</CodeHeader>

```json
{
    "name": "wiki:my_block"
}
```

<CodeHeader>置换对象示例</CodeHeader>

```json
{
    "name": "wiki:my_block",
    "states": {
        "wiki:my_state": 5
    }
}
```

<CodeHeader>标签对象示例</CodeHeader>

```json
{
    "tags": "q.any_tag('minecraft:is_axe_item_destructible', 'wiki:my_tag')"
}
```

## 范围对象

范围对象定义了两个数字之间的范围。

<CodeHeader>范围对象示例</CodeHeader>

```json
{
    "min": 2,
    "max": 4
}
```

提供时，将在最小值和最大值之间（包含边界）选择一个随机值。随机数不会被保留；每次使用范围对象时都会重新生成一个新的随机值。最大值不能小于最小值，但它们可以相等，以将随机数固定为特定值。

## 分数对象

分数对象使用分子和分母定义一个分数。

<CodeHeader>分数对象示例</CodeHeader>

```json
{
    "numerator": 3,
    "denominator": 5
}
```

在对象位置使用的值将是计算的除法结果，`numerator` ÷ `denominator`。分子和分母都必须至少为`1`，且分母不能等于分子。
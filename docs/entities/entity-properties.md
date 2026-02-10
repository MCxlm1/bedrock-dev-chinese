---
title: 实体属性
description: 实体属性允许在实体上存储数据，而无需在实体的服务端使用组件或属性（如`minecraft:variant`），类似于方块状态。
category: General
license: true
mentions:
    - SirLich
    - sermah
    - MedicalJewel105
    - Lufurrius
    - stirante
    - TheItsNameless
    - SmokeyStack
    - QuazChick
---

实体属性（也称为角色属性）允许在实体上存储数据，而无需在实体的服务端使用组件或属性（如`minecraft:variant`），类似于方块状态。

## 定义属性

实体属性在BP实体的`description`对象中定义：

<CodeHeader>BP/entities/properties_example.json</CodeHeader>

```json
{
    "format_version": "1.21.50",
    "minecraft:entity": {
        "description": {
            "identifier": "wiki:properties_example",
            "properties": {
                "wiki:bool_property_example": {
                    "type": "bool",
                    "default": false
                },
                "wiki:enum_property_example": {
                    "type": "enum",
                    "values": ["first", "second", "third"],
                    "default": "first"
                },
                "wiki:float_property_example": {
                    "type": "float",
                    "range": [0, 3.14],
                    "default": 2.5
                },
                "wiki:int_property_example": {
                    "type": "int",
                    "range": [0, 4],
                    "default": 4
                }
            }
        },
        "components": { ... },
        "events": { ... }
    }
}
```

### 默认值

默认值由`default`参数确定，可以定义为特定值或Molang表达式。

字符串值将被视为Molang表达式，除非它是枚举值。
Molang表达式在实体首次生成时进行求值。

<CodeHeader>minecraft:entity > description > properties</CodeHeader>

```json
"example:enum_property_example": {
    "type": "enum",
    "values": ["first", "second", "third"],
    "default": "math.random_integer(0, 1) ? 'first' : 'second'"
}
```

### 客户端同步

要从资源包（客户端）访问属性，`client_sync`参数必须为`true`。
默认情况下，此值为`false`。

<CodeHeader>minecraft:entity > description > properties</CodeHeader>

```json
"wiki:is_sad": {
    "type": "bool",
    "default": false,
    "client_sync": true
}
```

让我们设置一个变量为这个属性的值，以便在动画中使用！

<CodeHeader>minecraft:client_entity > description</CodeHeader>

```json
"scripts": {
    "pre_animation": [
        "v.is_sad = q.property('wiki:is_sad');"
    ]
}
```

## 获取属性值

### Molang查询函数

属性值由`property`查询函数返回。

<CodeHeader>Molang表达式</CodeHeader>

```molang
!q.property('wiki:bool_property_example')
```

### 实体过滤器测试

<CodeHeader>实体过滤器</CodeHeader>

```json
{
    "test": "bool_property",
    "domain": "wiki:bool_property_example",
    "operator": "==",
    "value": true
}
```

### 命令选择器参数

<CodeHeader>命令</CodeHeader>

```c
testfor @e[has_property={wiki:enum_property_example="second"}]
```

### 脚本API

[`Entity.getProperty()`](https://learn.microsoft.com/minecraft/creator/scriptapi/minecraft/server/entity#getproperty)方法允许您获取不同属性的当前值。

<CodeHeader>脚本</CodeHeader>

```js
customEntity.getProperty("wiki:int_property_example") === 2;
```

## 设置属性值

### 实体事件响应

使用实体事件，您可以通过`set_property`事件响应将实体属性设置为一个值：

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:set_entity_property": {
    "set_property": {
        "wiki:integer_property_example": 2,
        "wiki:enum_property_example": "second",
        "wiki:boolean_property_example": "!q.property('wiki:bool_property_example')"
    }
}
```

### 脚本API

[`Entity.setProperty()`](https://learn.microsoft.com/minecraft/creator/scriptapi/minecraft/server/entity#setproperty)方法允许您设置属性的值。

<CodeHeader>脚本</CodeHeader>

```js
customEntity.setProperty("wiki:int_property_example", 2);
```
---
title: 物品简介
description: 制作物品的"Hello world"指南。了解物品格式以及如何创建基本的自定义物品。
nav_order: 1
tags:
    - guide
    - beginner
mentions:
    - SirLich
    - solvedDev
    - Joelant05
    - yanasakana
    - destruc7ion
    - aexer0e
    - stirante
    - ChibiMango
    - MedicalJewel105
    - Sprunkles137
    - mark-wiemer
    - TheItsNameless
    - s1050613
    - SmokeyStack
    - QuazChick
---

Minecraft基岩版允许我们在世界中添加具有各种原版样式的自定义物品

本教程将介绍如何为Minecraft稳定版创建基本物品。

## 注册物品

物品定义的结构类似于实体：它们包含描述和组件列表，这些组件定义了物品的行为。

以下是将自定义物品放入创造模式物品栏所需的**最小**行为端代码。

<CodeHeader>BP/items/custom_item.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:custom_item",
            "menu_category": {
                "category": "items"
            }
        },
        "components": {} // 即使为空也必须存在！
    }
}
```

### 物品描述

-   定义物品的标识符 - 格式为`namespace:identifier`的唯一ID。
-   配置物品放置到哪个`menu_category`中。
    -   还接受可选参数`group`和`is_hidden_in_commands`。

## 添加组件

目前，我们的自定义物品正在使用默认组件值（可在[此处](/items/item-components)找到）。

让我们配置自己的功能！

<CodeHeader>BP/items/custom_item.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:custom_item",
            "menu_category": {
                "category": "construction"
            }
        },
        "components": {
            "minecraft:damage": 10,
            "minecraft:durability": {
                "max_durability": 36
            },
            "minecraft:hand_equipped": true
        }
    }
}
```

在此处浏览更多物品组件[点击这里](/items/item-components)！

## 应用纹理

我们需要创建一个纹理简称，将其链接到`RP/textures/item_texture.json`中的图像。

<CodeHeader>RP/textures/item_texture.json</CodeHeader>

```json
{
    "resource_pack_name": "wiki",
    "texture_name": "atlas.items",
    "texture_data": {
        "wiki:custom_item": {
            "textures": "textures/items/custom_item"
        }
    }
}
```

在我们的物品文件中，我们将添加`minecraft:icon`组件来应用纹理。

<CodeHeader>BP/items/custom_item.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:custom_item",
            "menu_category": {
                "category": "construction"
            }
        },
        "components": {
            "minecraft:icon": "wiki:custom_item"
        }
    }
}
```

:::tip 方块图标
或者，[方块放置器](/items/item-components#block-placer)物品可以省略`minecraft:icon`组件，以使用方块的3D图标显示物品。
:::

## 定义名称

最后，让我们这样定义我们的物品名称：

<CodeHeader>RP/texts/en_US.lang</CodeHeader>

```lang
item.wiki:custom_item=自定义物品
```

## 结果

在本页中，您已了解以下内容：

-   [x] 物品的基本功能
-   [x] 如何应用纹理
-   [x] 如何使用`item_texture.json`中的简称链接纹理
-   [x] 如何在语言文件中定义名称
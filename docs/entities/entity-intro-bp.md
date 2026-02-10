---
title: 实体行为包简介
category: General
nav_order: 1
tags:
    - guide
    - beginner
mentions:
    - SirLich
    - solvedDev
    - stirante
    - Joelant05
    - destruc7ion
    - MedicalJewel105
    - ChibiMango
    - SmokeyStack
    - ThomasOrs
description: 实体行为包介绍。
---

三种主要结构构成了行为包实体文件的基础。本文档将解释它们各自的含义以及如何使用它们。

混淆组件组和组件是错误的常见来源。请注意以理解它们之间的区别。

## 组件

组件是构成Minecraft实体的逻辑构建块。所有组件均由Mojang编写并提供给我们使用。组件可以执行各种操作，比如设置实体的大小或赋予它游泳的能力。完整的组件列表可以在[此处](https://bedrock.dev/docs/stable/Entities)找到。

创建自己的组件是_不可能的_。整个组件列表都是硬编码的，由Microsoft提供。

当您想要为实体添加行为时，您可以将`components`添加到`minecraft:entity`对象的`components`对象中。例如，如果我们想让实体具有攀爬能力，我们可以添加此组件：`"minecraft:can_climb": {}`。

所有组件的格式都类似`"minecraft:<name>": { <setting> }`。每个组件接受不同类型的设置。

下面是实体内部一些组件的示例：

<CodeHeader>BP/entities/example.json#minecraft:entity</CodeHeader>

```json
"components": {
    "minecraft:type_family": {
        "family": [
            "player"
        ]
    },
    "minecraft:collision_box": {
        "width": 0.6,
        "height": 1.8
    },
    "minecraft:can_climb": {}
}
```

注意`components`列表_仅_包含组件。

## 组件组

组件组是组件的"文件夹"。它们对组件进行分组，可以使用`events`添加或移除以创建自定义游戏玩法。

下面是一个例子：

<CodeHeader>BP/entities/example.json#minecraft:entity</CodeHeader>

```json
"component_groups": {

    //组件组的名称
    "minecraft:cat_persian": {

        //有效组件列表。可根据需要添加任意数量。
        "minecraft:variant": {
            "value": 6
        },
        "minecraft:physics": {}
    },

    //第二个组件组的名称
    "wiki:example_group": {
        "minecraft:type_family": {
            "family": [
                "wiki_is_awesome!"
            ]
        }
    }
}
```

所有组件组都是自定义创建的。您不能在您的实体中使用其他实体的组件组。

在原版Minecraft实体中，组件组使用`minecraft:`命名空间，如上面的`minecraft:cat_persian`。但重要的是要记住它们不是_组件_。当您创建组件组时，可以使用任何想要的名称/命名空间：名称组合。例如，上面的`wiki:example_group`。[这里](/concepts/namespaces)有更多关于命名空间的信息。

当组件被放入组中时，它不会自动添加到您的实体中。在添加组之前，它完全不会产生任何作用。当组被添加时，组件将变为活跃状态并开始影响实体的行为。您也可以同时添加多个组件组。

## 事件

事件是一种特殊的语法，用于添加和删除组件组，可以在组件满足特定条件时调用。通过添加/删除组，我们可以为实体创建动态行为。

一个例子：

<CodeHeader>BP/entities/example.json#minecraft:entity#events</CodeHeader>

```json
"minecraft:ageable_grow_up": { //事件名称
    "remove": { //要移除的组件组列表
        "component_groups": [
            "minecraft:cat_baby"
        ]
    },
    "add": {
        "component_groups": [
            "minecraft:cat_adult" //要添加的组件组列表。
        ]
    }
},
```

与组件组一样，事件在每个实体内部100%是自定义创建的。您不能在自己的实体中使用其他实体的事件。不要试图在自己的实体中使用`"minecraft:ageable_grow_up"`。如果您想要成长功能，您需要自己定义组件组和事件。

您可以从实体中添加/移除的唯一东西是`component groups`。尽管直接尝试添加/移除组件很诱人，但这是不可能的。

当满足某些条件时，在某些组件内部激活事件。下面是一个例子：

<CodeHeader>BP/entities/example.json#minecraft:entity</CodeHeader>

```json
"components": {
    "minecraft:interact": {
        "interactions": [
            {
                "on_interact": {
                    "filters": [
                        {
                            "test":"is_family",
                            "subject": "other",
                            "value": "player"
                        }
                    ],
                    "target": "self",
                    "event": "wiki:on_interact"
                }
            }
        ]
    }
},
"component_groups": {
    "wiki:interacted": {
        "minecraft:scale": {
            "value": 2
        }
    }
},
"events":{
    "wiki:on_interact":{
        "add": {
            "component_groups": [ "wiki:interacted" ]
        }
    }
}
```

在这里，当实体被玩家互动时，将激活`"wiki:on_interact"`事件。然后事件将添加组件组`"wiki:interacted"`。这将应用组件`"minecraft:scale"`。

有关事件可以做什么的更深入教程，请查看我们的实体事件页面。

<Button link="/entities/entity-events">实体事件</Button>

## 原版用法

组件组和事件是原版实体用来创建自定义和适应性行为的主要工具。以下是一些使用组件组和事件创建的原版功能：

-   僵尸被编程为如果在水中待太久就会变成溺尸。

-   狐狸有两个组件组`minecraft:fox_red`和`minecraft:fox_active`，以根据它们的生成地点拥有两种颜色变体。

-   当玩家看向末影人时，末影人将对玩家变得具有攻击性。
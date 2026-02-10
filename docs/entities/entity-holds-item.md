---
title: 实体持物
category: Tutorials
tags:
    - intermediate
mentions:
    - pieterdefour
    - SirLich
    - solvedDev
    - stirante
    - Joelant05
    - destruc7ion
    - Dreamedc2015
    - sermah
    - 7dev7urandom
    - legopitstop
description: 在本教程中，您将学习让实体生成时手中持有物品。
---

::: tip
本教程假设您对实体、战利品表和Blockbench有基本了解。
:::

在本教程中，您将学习让实体生成时手中持有物品。我将使用自定义的`mandalorian_armorer`实体和自定义的`hammer`物品作为示例。

## 模型

首先，您需要在Blockbench中有一个名为`rightArm`的地图。在此地图中，需要有一个名为'rightItem'的子地图。
现在设置此子地图的枢轴点位置，使其位于您希望实体握住物品的位置。

![](/assets/images/tutorials/entity-holds-item/blockbench.png)

## 行为包端

现在您需要在实体的组件列表中添加一个`minecraft:equipment`组件，并添加一个包含所需物品的战利品表。

在我们的示例中，它看起来像这样：

<CodeHeader>BP/entity/mandolorian.json#components</CodeHeader>

```json
"minecraft:equipment": {
    "table": "loot_tables/entities/gear/mandolorian.json"
}
```

## 战利品表

最后，为您的实体添加战利品表。它需要在行为包中的`loot_tables/entities/<your_loot_table_name>.json`。在我们的案例中，它被称为`mandolorian.json`。

:::warning
这不是与实体死亡时掉落的相同战利品表。所以确保它有不同的名称。
:::

要让实体总是以相同的物品生成，请添加以下战利品表：

<CodeHeader>BP/loot_tables/entities/gear/mandolorian.json</CodeHeader>

```json
{
    "pools": [
        {
            "rolls": 1,
            "entries": [
                {
                    "type": "item",
                    "name": "dd:hammer",
                    "weight": 1
                }
            ]
        }
    ]
}
```

如果一切顺利，您将得到如下所示的结果：

![](/assets/images/tutorials/entity-holds-item/finished_result.png)

## 常见问题

-   持有的物品未显示

您的实体包含多个几何变体。
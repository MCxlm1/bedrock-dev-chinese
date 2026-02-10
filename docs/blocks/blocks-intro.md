---
title: 方块简介
description: 制作方块的Hello World指南。了解方块格式以及如何创建基本的自定义方块。
category: General
nav_order: 1
tags:
    - guide
    - beginner
mentions:
    - SirLich
    - solvedDev
    - Joelant05
    - Dreamedc2015
    - sermah
    - yanasakana
    - aexer0e
    - SmokeyStack
    - MedicalJewel105
    - stirante
    - ChibiMango
    - Hatchibombotar
    - fraysdev
    - Ciosciaa
    - Sprunkles137
    - ThomasOrs
    - QuazChick
---

:::tip 格式版本 1.21.130
此页面讨论基本方块功能。您可以在此处[了解](/blocks/block-components)有关其他方块组件的更多信息。
:::
:::danger 注意
原版方块是硬编码的。您可能无法覆盖或访问它们。
:::

Minecraft基岩版允许我们在世界中添加具有各种原版样式的自定义方块。自定义方块可以有多个阶段（如植物）、方向朝向和其他有用的功能。

本教程将介绍如何为Minecraft稳定版创建基本方块。

## 注册方块

方块定义的结构类似于实体：它们包含描述和组件列表，这些组件定义了方块的行为。

与实体不同，方块没有资源定义，只在`RP/blocks.json`中有定义。

以下是将自定义方块放入创造模式物品栏所需的**最小**行为端代码。

<CodeHeader>BP/blocks/custom_block.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:block": {
        "description": {
            "identifier": "wiki:custom_block",
            "menu_category": {
                "category": "construction", // 方块所在的创造模式物品栏或配方书标签
                "group": "minecraft:itemGroup.name.concrete", // 方块所属的可展开分组。（可选）
                "is_hidden_in_commands": false // 是否在命令中隐藏该方块？（可选）
            }
        },
        "components": {} // 即使为空也必须存在！
    }
}
```

### 方块描述

-   定义方块的`identifier` - 格式为`<namespace>:<identifier>`{lang=xml}的唯一ID。
-   配置方块放置到哪个`menu_category`中。
    -   还接受可选参数`group`和`is_hidden_in_commands`。

_方块描述也是[状态](/blocks/block-states)和[特征](/blocks/block-traits)的归属地，这些内容在其各自的页面中有详细介绍。_

## 添加组件

目前，我们的自定义方块正在使用默认组件值（可在[此处](/blocks/block-components)找到）。

让我们配置自己的功能！

<CodeHeader>BP/blocks/custom_block.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:block": {
        "description": {
            "identifier": "wiki:custom_block",
            "menu_category": {
                "category": "construction"
            }
        },
        "components": {
            "minecraft:destructible_by_mining": {
                "seconds_to_destroy": 3
            },
            "minecraft:destructible_by_explosion": {
                "explosion_resistance": 3
            },
            "minecraft:map_color": "#ffffff",
            "minecraft:light_dampening": 0,
            "minecraft:light_emission": 4,
            "minecraft:loot": "loot_tables/blocks/custom_block.json"
        }
    }
}
```

-   [`minecraft:destructible_by_mining`](/blocks/block-components#destructible-by-mining)定义玩家需要挖掘多长时间才能破坏方块。目前，无法为不同工具设置不同的破坏时间。
-   [`minecraft:destructible_by_explosion`](/blocks/block-components#destructible-by-explosion)定义爆炸抗性。值越高，被破坏的可能性越低。
-   [`minecraft:map_color`](/blocks/block-components#map-color)是在Minecraft地图上代表此方块显示的十六进制颜色代码。`"#ffffff"`{lang=json}表示白色。您可以[在此](https://www.google.com/search?q=hex+color+picker)获取其他颜色的十六进制代码。
-   [`minecraft:light_dampening`](/blocks/block-components#light-dampening)定义光线通过时会被阻挡多少。
-   [`minecraft:light_emission`](/blocks/block-components#light-emission)定义方块将输出的光照等级。
-   [`minecraft:loot`](/blocks/block-components#loot)定义方块掉落物的战利品表路径。如果删除此项，则方块会掉落自身。您可以[在此](/loot/loot-tables)了解更多关于战利品表的信息。

_浏览更多方块组件[点击这里](/blocks/block-components)！_

## 应用纹理

应该使用[几何体](/blocks/block-components#geometry)和[材质实例](/blocks/block-components#material-instances)组件来确定方块外观。

对于我们的基本16&times;16&times;16像素方块，将使用[原版模型](/blocks/vanilla-block-models)`minecraft:geometry.full_block`。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:geometry": "minecraft:geometry.full_block",
"minecraft:material_instances": {
    "*": {
        "texture": "wiki:custom_block"
    }
}
```

现在，我们需要在`RP/textures/terrain_texture.json`中将纹理简称链接到图像文件路径：

<CodeHeader>RP/textures/terrain_texture.json</CodeHeader>

```json
{
    "resource_pack_name": "wiki",
    "texture_name": "atlas.terrain",
    "texture_data": {
        // 我们的纹理简称：
        "wiki:custom_block": {
            "textures": "textures/blocks/custom_block" // 链接到图像文件名
        }
    }
}
```

### 每面纹理

纹理也可以按面应用。例如，自定义"指南针方块"可以使用以下✨惊艳的✨纹理：

-   `textures/blocks/compass_block_down.png`

    <WikiImage
        src="/assets/images/blocks/blocks-intro/compass_block_down.png"
        pixelated
        width="64"
    />

-   `textures/blocks/compass_block_up.png`

    <WikiImage src="/assets/images/blocks/blocks-intro/compass_block_up.png" pixelated width="64" />

-   `textures/blocks/compass_block_north.png`

    <WikiImage
        src="/assets/images/blocks/blocks-intro/compass_block_north.png"
        pixelated
        width="64"
    />

-   `textures/blocks/compass_block_east.png`

    <WikiImage
        src="/assets/images/blocks/blocks-intro/compass_block_east.png"
        pixelated
        width="64"
    />

-   `textures/blocks/compass_block_south.png`

    <WikiImage
        src="/assets/images/blocks/blocks-intro/compass_block_south.png"
        pixelated
        width="64"
    />

-   `textures/blocks/compass_block_west.png`

    <WikiImage
        src="/assets/images/blocks/blocks-intro/compass_block_west.png"
        pixelated
        width="64"
    />

[材质实例](/blocks/block-components#material-instances)应如下所示：

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:material_instances": {
    "down": {
        "texture": "wiki:compass_block_down" // 此纹理出现在破坏粒子效果中
    },
    "up": {
        "texture": "wiki:compass_block_up"
    },
    "north": {
        "texture": "wiki:compass_block_north"
    },
    "east": {
        "texture": "wiki:compass_block_east"
    },
    "south": {
        "texture": "wiki:compass_block_south"
    },
    "west": {
        "texture": "wiki:compass_block_west"
    }
}
```

以下是相应的`terrain_texture.json`数据：

<CodeHeader>RP/textures/terrain_texture.json</CodeHeader>

```json
{
    "resource_pack_name": "wiki",
    "texture_name": "atlas.terrain",
    "texture_data": {
        "wiki:compass_block_down": {
            "textures": "textures/blocks/compass_block_down"
        },
        "wiki:compass_block_up": {
            "textures": "textures/blocks/compass_block_up"
        },
        "wiki:compass_block_north": {
            "textures": "textures/blocks/compass_block_north"
        },
        "wiki:compass_block_east": {
            "textures": "textures/blocks/compass_block_east"
        },
        "wiki:compass_block_west": {
            "textures": "textures/blocks/compass_block_west"
        },
        "wiki:compass_block_south": {
            "textures": "textures/blocks/compass_block_south"
        }
    }
}
```

## 应用声音

自定义方块的挖掘声、脚步声、破坏声和放置声可以通过`RP/blocks.json`中的`sound`参数确定。

在此处[了解](/blocks/block-sounds)有关方块声音的更多信息！

<CodeHeader>RP/blocks.json</CodeHeader>

```json
{
    "format_version": "1.21.40",
    "wiki:custom_block": {
        "sound": "grass"
    }
}
```

## 定义名称

最后，让我们这样定义我们的方块名称：

<CodeHeader>RP/texts/en_US.lang</CodeHeader>

```lang
tile.wiki:custom_block.name=自定义方块
tile.wiki:compass_block.name=指南针方块
```

您可以在此处[了解](/text/text-intro)有关翻译的更多信息。

## 结果

在此页面中，您已经学习了以下内容：

-   [x] 方块的基本功能
-   [x] 如何为所有方块面应用纹理
-   [x] 如何按面应用纹理

...但这只是开始，请参阅下面的内容了解您可以做的其他事情！

## 下一步是什么？

<CardGrid>
<Card title="添加功能" image="/assets/images/icons/crafting_table.png">

了解可用的方块[组件](/blocks/block-components)来制作独特的游戏玩法。

为什么不使用[几何体](/blocks/block-components#geometry)组件为您的方块提供自定义模型？
您还可以配置自己的[碰撞箱](/blocks/block-components#collision-box)和[选择框](/blocks/block-components#selection-box)来匹配！

</Card>
<Card title="创建变体" image="/assets/images/icons/levers.png">

利用方块[状态](/blocks/block-states)和[排列](/blocks/block-permutations)来有条件地启用方块上的组件。

例如，您可以向自定义储罐方块添加液体深度级别，并支持多种液体类型。

</Card>
<Card title="复制原版" image="/assets/images/icons/diamond_ore.png">

在**原版重现**类别中浏览现有方块的完整副本。

从简单的[自定义玻璃方块](/blocks/custom-glass-blocks)开始，利用[材质实例](/blocks/block-components#material-instances)！

</Card>
</CardGrid>
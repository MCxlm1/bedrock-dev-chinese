---
title: 世界生成介绍
description: 了解如何修改世界生成。
category: General
nav_order: 1
tags:
    - guide
mentions:
    - SirLich
    - solvedDev
    - Dreamedc2015
    - destruc7ion
    - MedicalJewel105
    - aexer0e
    - retr0cube
    - SmokeyStack
    - Supernova3695
---

:::warning
此页面有些过时，信息有限。如需最新、最全面的信息，请查看本节中的其他页面。
:::

您可以通过附加包更改世界的生成。行为包中需要的文件夹有：

`structures`、`features`、`feature_rules`、`biomes`和`worldgen`。这相当直观：您可以将结构方块的.mcstructure文件存储在`structures`中，生物群系文件在`biomes`中，拼图结构文件在`worldgen`及其子文件夹中，地形特征（如矿石）在`features`中，它们的生成规则在`feature_rules`中。让我们首先添加自定义生物群系。

_注意：使用bridge.（链接和联系中也有提及）创建生物群系可能更容易，这是一个可视化附加包创建软件（也列在链接和联系中），因为官方文档相当不完整。您也可以生成所有原版生物群系、特征和特征规则的示例文件以供参考，如下所示：_

![](/assets/images/guide/gen_coal_ore.png)
_使用bridge.生成煤_矿石特征。_

然而，bridge.不是必需的。

---

## 自定义生物群系

<CodeHeader>BP/biomes/cold_biome.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:biome": {
        "description": {
            "identifier": "wiki:cold_biome"
        },
        "components": {
            "minecraft:climate": {
                "downfall": 0.7,
                "snow_accumulation": [0.6, 0.9],
                "temperature": 15.0
            },
            "minecraft:surface_parameters": {
                "sea_floor_depth": 7,
                "sea_floor_material": "minecraft:blue_ice",
                "foundation_material": "minecraft:cobblestone",
                "mid_material": "minecraft:minecraft:concrete",
                "top_material": "minecraft:glass",
                "sea_material": "minecraft:water"
            },
            "minecraft:replace_biomes": {
                "replacements": [
                    {
                        "amount": 0.5,
                        "noise_frequency_scale": 50,
                        "dimension": "minecraft:overworld",
                        "targets": [
                            "minecraft:plains"
                        ]
                    }
                ]
            },
            "minecraft:tags": {
                "tags": [
                    "cold_biome"
                ]
            }
        }
    }
}
```

-   将`format_version`设置为1.21.130：这是当前版本的最新生物群系文件版本。
-   `description`只接受一个值：`identifier`。
-   `components`就是您所期望的：默认应用于生物群系的内容。让我们来看看它们：
-   `minecraft:climate`控制所有气候相关的内容。
-   `downfall`是指下雨或下雪的频率。0.0表示完全不下雨（如沙漠），1.0则意味着持续下雨。
-   `temperature`用于定义诸如水结冰和雨变成雪之类的特性。

**您可以使用bridge.生成默认生物群系文件以供参考。**

-   `overworld_surface`控制生成的方块。
-   `floor_depth`是指湖泊和河流向下延伸的深度（以方块为单位）。
-   `sea_floor_material`定义生成河流和湖底时使用的材料。
-   `foundation_material`是在y=5到y=50之间大约使用的材料。例如，对于沙漠来说，它是石头。
-   `sea_material`是在湖泊、河流、海洋等中显示液体所使用的材料。例如，在所有主世界生物群系中，这被设置为"minecraft:water"。
-   `top_material`定义最高层的材料。例如对于平原来说是草。
-   `mid_material`是'top'和'foundation'之间的层。对于平原来说是泥土。
-   `overworld_height`定义生物群系的地形外观。

请勿同时使用`noise_type`和`noise_params`。`noise_params`是生物群系中允许的顶层噪声和底层噪声的数组。

![](/assets/images/guide/non_smooth_noise_transition.jpg)
_使用noise_params为[0.1, 0,1]然后是[1.0, 1.0]生成的相同生物群系之间的非平滑过渡。_

-   然而，如果您想使用`noise_type`，您将会看到一些预生成的噪声类型。您可能从原版游戏中知道其中一些的样子。这里是列表：

`beach, default, extreme, taiga, ocean, mountains, default_mutated, deep_ocean, lowlands, less_extreme, stone_beach, swamp, river, mushroom`。

-   `minecraft:replace_biomes`是所有组件中最重要的。它告诉游戏在多大百分比下用自定义生物群系替换原版生物群系。

-   最后但同样重要的是，生物群系标签！它们非常简单，但很有用。您可以通过在此格式中添加它们来设置任意数量的原版或自定义标签，格式如下`minecraft:tags`组件：

<CodeHeader>minecraft:biome > components</CodeHeader>

```json
"minecraft:tags": {
    "tags": [
        "overworld",
        "wiki:custom_tag"
    ]
}
```

然后，您可以在_环境传感器_、_过滤器_、_has_biome_测试、_生成规则_等中测试您的标签。

您的自定义生物群系现在已经完成！

---

## 特征和特征规则

特征和特征规则用于生成从矿石到草和花、植被到花岗岩或粘土块的一切。
甚至可以使用这些来创建自定义结构，但由于这非常繁琐，并且在提到的更新后会更容易，所以我们暂时不讨论这个问题。

现在，值得提一下生成自定义结构的最简单方法是[MACHINE_BUILDER](https://www.youtube.com/channel/UC8FBQgo4AWwKFX97h60NKOQ)的这个[自动生成器](https://machine-builder.itch.io/frg-v2)，您应该注意此工具有一个功能有限的免费版本，要享受完整功能请使用付费版本。不过，我们仍将在这里学习一些其他的手动特征生成，因为像矿石这样的某些东西生成为`ore_feature`而不是`structure_template_feature`更高效。

为了教程的目的，让我们让我们的`wiki:blocky`自定义方块生成为矿石。我会用简单的方法来做：

1. 打开_bridge._，选择您的附加包。
1. _添加新文件>特征>钻石矿石和添加新文件>特征规则>钻石矿石_。
1. 现在我将保存文件并在代码编辑器中打开它们进行必要的修改。

_您可以轻松地从零开始编写文件或从某处复制它们，而无需使用bridge._以防您在安装时遇到困难。**一个找到原版文件的地方是[示例包](https://www.minecraft.net/en-us/addons)，另一个更完整的则是[bridge.的仓库](https://github.com/bridge.-core/bridge../tree/master/static/vanilla)**._

## 特征

特征位于`BP/features`中，基本上是一组存储在游戏文件中的方块，可以用_feature_rule_放置。_feature_的**文件名**必须与**标识符**匹配。

您可以在[bedrock.dev/r/Features](https://bedrock.dev/r/Features)上找到它们的文档

<CodeHeader>BP/features/blocky_ore_feature.json</CodeHeader>

```json
{
    "format_version": "1.13.0",
    "minecraft:ore_feature": {
        "description": {
            "identifier": "blocky_ore_feature"
        },
        "count": 8,
        "places_block": "wiki:blocky",
        "may_replace": [
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "andesite"
                }
            },
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "andesite_smooth"
                }
            },
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "diorite"
                }
            },
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "diorite_smooth"
                }
            },
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "granite"
                }
            },
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "granite_smooth"
                }
            },
            {
                "name": "minecraft:stone",
                "states": {
                    "stone_type": "stone"
                }
            }
        ]
    }
}
```

-   `minecraft_ore_feature`是自动放置矿石的特定特征类型。每种特征类型都有自己的特定语法。（还有`single_block_feature`，它放置单个方块而不是矿石组等）
-   `identifier`在此场景中不需要命名空间。命名空间是可选的，不应添加到文件名中。
-   `count`是矿石"簇"的最大大小，或一起生成的实际矿石方块的数量。
-   `places_block`接收要作为值放置的方块的标识符。
-   `may_replace`接收特征可以替换的所有方块作为参数。如果它在未包含在此处的方块之上生成，则该方块将保留在原处而不被替换。

## 特征规则

**特征规则**控制_where_和_how_（以及未来，_结构_）被放置。

<CodeHeader>BP/feature_rules/overworld_underground_blocky_ore_feature.json</CodeHeader>

```json
{
    "format_version": "1.13.0",
    "minecraft:feature_rules": {
        "description": {
            "identifier": "overworld_underground_blocky_ore_feature",
            "places_feature": "blocky_ore_feature"
        },
        "conditions": {
            "placement_pass": "underground_pass",
            "minecraft:biome_filter": [
                {
                    "any_of": [
                        {
                            "test": "has_biome_tag",
                            "operator": "==",
                            "value": "overworld"
                        },
                        {
                            "test": "has_biome_tag",
                            "operator": "==",
                            "value": "overworld_generation"
                        }
                    ]
                }
            ]
        },
        "distribution": {
            "iterations": 100,
            "coordinate_eval_order": "zyx",
            "x": {
                "distribution": "uniform",
                "extent": [0, 16]
            },
            "y": {
                "distribution": "uniform",
                "extent": [0, 16]
            },
            "z": {
                "distribution": "uniform",
                "extent": [0, 16]
            }
        }
    }
}
```

-   `description`
    -   `identifier`不需要命名空间，但文件名需要匹配。
    -   `places_feature`接收由此规则控制的特征的标识符作为值。
-   `conditions`
    -   `placement_pass`特征将如何传递。
    -   `biome_filter`测试特征将生成的生物群系标签，就像生成规则一样。
-   `distribution`
    -   `iterations`基本上是机会。我将其设置为100以使其在各处生成，但对于钻石矿石，这被设置为1。
    -   接下来的四个组件显示了矿石将在哪个方向上被"拖拽"。
        _需要进一步澄清_

测试您的矿石是否生成的最简单方法是在低Y层使用此命令：/fill ~15 ~5 ~15 ~-15 ~-15 ~-15 air 0 replace stone。这将保留选定区域内的所有非石头方块，如下所示：

![](/assets/images/guide/ore_gen_sans_stone.jpg)

是的，100作为"iterations"绝对是太多了 ;)

最好继续查看其他特征和特征规则原版文件以学习进一步的技术。但是，刚才介绍的这些足以创建大多数需要生成的内容。

---

## 自定义结构


### 结构模板特征
截至MCBE v1.16.20，**自定义生成结构成为可能**
生成结构的一种简单方法是MACHINE_BUILDER之前提到的[自动生成器](https://machine-builder.itch.io/frg-v2)。它为您的结构生成所有三个必需文件：`feature_rules/mystructure.feature_rule.json`、`feature_rules/mystructure.feature.json`和`structures/mystructure.mcstructure.`。您可以在此处[了解](/nbt/mcstructure)在Minecraft本身中使用结构方块定义.mcstructures的更多信息。

---

现在您有了您的.mcstructure，是时候编写您的`feature`和`feature rule`了。最后一个是与矿石相同的（参见本文开头）。让我们继续**Feature**。

-   记住命名空间不是必需的，并且文件名必须与标识符匹配（不包括命名空间）。所以如果您的特征规则标识符为`wiki:myfeaturerule`或简单地`myfeaturerule`，文件名在两种情况下都可以是`myfeaturerule.json`。

这是来自[特征文档](https://bedrock.dev/r/Features#minecraft:structure_template_feature)的代码示例：

<CodeHeader></CodeHeader>

```json
{
    "format_version": "1.13.0",
    "minecraft:structure_template_feature": {
        "description": {
            "identifier": "wiki:hot_air_balloon_feature"
        },
        "structure_name": "wiki:hot_air_balloon",
        "adjustment_radius": 8,
        "facing_direction": "random",
        "constraints": {
            "unburied": {},
            "block_intersection": {
                "block_whitelist": ["minecraft:air"]
            }
        }
    }
}
```

-   `structure_name`是结构的标识符，即您通过结构方块保存的那个。

### 拼图结构

拼图结构可以很小也可以很大，最多可达256个方块宽！

拼图允许生成大型随机结构，如村庄或遗迹，这些结构可以在世界各地生成，并且可以使用/locate定位，这与结构特征不同。
它们非常强大，有关它们的更多信息可在此处[找到](/world-generation/jigsaw-structures)。

差不多就是这样！现在您可以生成自己的自定义结构到世界中了。

## 您到目前为止的进度

**您已完成：**

-   [x] 创建了您的第一个生物群系。
-   [x] 使您的第一个矿石自然生成。
-   [x] 学会使用bridge.生成和引用原版文件。
-   [x] 了解其他自定义生成方法。
-   [x] 创建自定义结构。
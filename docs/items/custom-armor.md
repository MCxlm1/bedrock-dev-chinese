---
title: 自定义盔甲
description: 了解如何制作一套自定义盔甲。
mentions:
    - SirLich
    - Dreamedc2015
    - sermah
    - yanasakana
    - Joelant05
    - MedicalJewel105
    - aexer0e
    - Brougud
    - XxPoggyisLitxX
    - LeGend077
    - SmokeyStack
    - QuazChick
---

::: tip
强烈建议您在处理这些章节之前先浏览新手指南中的[Blockbench建模和纹理](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/guide/blockbench)部分。
:::

制作自定义盔甲出奇地简单，您需要做一些调整，因为需要添加一些文件，并且可能会涉及一些纹理处理，但您可以在这里按自己的意愿做多做少。

## 胸甲

创建一件胸甲：

<CodeHeader>BP/items/my_chest.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:my_chest",
            // 注意我们给它分配了装备类别
            "menu_category": {
                "category": "equipment",
                "group": "minecraft:itemGroup.name.chestplate"
            }
        },
        "components": {
            // 我们在INVENTORY中想要使用的图标
            "minecraft:icon": "wiki:my_chest",
            // 我们给它一个名字
            "minecraft:display_name": {
                "value": "我的自定义盔甲"
            },
            // 我们不希望它堆叠
            "minecraft:max_stack_size": 1,
            // 我们确保它只能接受胸部盔甲的附魔
            "minecraft:enchantable": {
                "value": 10,
                "slot": "armor_torso"
            },
            // 我们希望它可以修复，以及使用什么来修复
            "minecraft:repairable": {
                "repair_items": [
                    {
                        "items": ["minecraft:stick"],
                        "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
                        // 一些复杂的molang；只需复制即可
                    }
                ]
            },
            // 标记它为可穿戴的，并且放在胸部槽位
            "minecraft:wearable": {
                "slot": "slot.armor.chest",
                "protection": 5
            },
            // 提供其耐久度
            "minecraft:durability": {
                "max_durability": 200
            }
        }
    }
}
```

此时，您只需在`RP/textures/item_texture.json`中添加一个键为`my_chest`的物品纹理，就可以开始了。如果您只想跟随教程，我们在这里附上了一个盔甲的默认物品纹理。

![](/assets/images/tutorials/custom-armor/custom_chestplate.png)

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-armor/custom_chestplate.png">
    下载纹理
</Button>

## 添加附着物

此时，您的物品会在游戏中出现并且可以穿戴，但它没有任何外观。这是因为我们需要告诉它如何处理附着物装备并为其提供显示纹理。

首先，您需要在RP中创建一个`attachables`文件夹（您可能已经有了）。

<CodeHeader>RP/attachables/my_chest.json</CodeHeader>

```json
{
    "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "wiki:my_chest",
            // 这2个是默认的，也是必需的
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                // 这是我们CUSTOM盔甲纹理，接下来需要制作
                "default": "textures/models/armor/custom_main",
                // 这个纹理包含在原版RP中，用于附魔闪烁效果
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            // 我们告诉它为胸甲使用什么几何形状
            "geometry": {
                "default": "geometry.player.armor.chestplate"
            },
            // 我们告诉它隐藏胸部图层，因为我们将在上方显示我们的盔甲
            "scripts": {
                "parent_setup": "v.chest_layer_visible = 0.0;"
            },
            // 我们告诉它使用什么控制器（默认盔甲控制器）
            "render_controllers": ["controller.render.armor"]
        }
    }
}
```

此时我们需要确保为模型创建纹理，这些纹理位于`RP/textures/models/armor`中。然而实际上我们需要2个纹理，一个是整个盔甲一起穿戴时的，另一个是腿部单独穿戴时的，通常会覆盖一些靴子区域。

如果您没有创意，我们提供了重新着色的钻石盔甲皮肤供本教程使用。只需`另存为`并将它们放入文件夹中。

![](/assets/images/tutorials/custom-armor/custom_main.png)

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-armor/custom_main.png">
    下载纹理
</Button>

![](/assets/images/tutorials/custom-armor/custom_legs.png)

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-armor/custom_legs.png">
    下载纹理
</Button>

在实际情况下，您可能想使用Blockbench或某些照片编辑程序来编辑纹理，并在将它们添加到附加包之前理想地查看它们在模型上的效果。
如果您现在进入游戏并检查您制作的内容，您应该能够穿上胸甲并为自己完成的工作感到自豪。

![](/assets/images/tutorials/custom-armor/armor-item-image.jpg)

![](/assets/images/tutorials/custom-armor/armor-model-image.jpg)

## 护腿

虽然单独的胸甲很棒，但您可能想要一整套，所以从这里开始，如果您为靴子制作另一个物品json，如下所示。

<CodeHeader>BP/items/my_leggings.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:my_leggings",
            "menu_category": {
                "category": "equipment",
                "group": "minecraft:itemGroup.name.leggings"
            }
        },
        "components": {
            // 给它一个适用的ITEM纹理
            "minecraft:icon": "wiki:my_leggings",
            "minecraft:display_name": {
                "value": "我的自定义护腿"
            },
            "minecraft:max_stack_size": 1,
            // 确保附魔适用于腿部
            "minecraft:enchantable": {
                "value": 10,
                "slot": "armor_legs"
            },
            "minecraft:repairable": {
                "repair_items": [
                    {
                        "items": ["minecraft:stick"],
                        "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
                    }
                ]
            },
            // 确保可穿戴槽位是腿部
            "minecraft:wearable": {
                "slot": "slot.armor.legs",
                "protection": 3
            },
            "minecraft:durability": {
                "max_durability": 200
            }
        }
    }
}
```

这很棒，和之前一样，您需要添加自己的物品纹理，如果您只想继续，这里有现成的。

![](/assets/images/tutorials/custom-armor/custom_leggings.png)

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-armor/custom_leggings.png">
    下载纹理
</Button>

完成这里后，我们需要创建附着物文件，如下所示：

<CodeHeader>RP/attachables/my_leggings.json</CodeHeader>

```json
{
    "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "wiki:my_leggings",
            // 注意这和之前是一样的
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                // 和之前一样
                "enchanted": "textures/misc/enchanted_actor_glint",
                // 这个不同，因为我们使用专门的护腿纹理
                "default": "textures/models/armor/custom_legs"
            },
            // 告诉它使用护腿几何体
            "geometry": {
                "default": "geometry.humanoid.armor.leggings"
            },
            // 隐藏腿部图层，因为我们将渲染在其上方
            "scripts": {
                "parent_setup": "v.leg_layer_visible = 0.0;"
            },
            // 和之前一样
            "render_controllers": ["controller.render.armor"]
        }
    }
}
```

鉴于我们已经放入了所需的纹理，我们可以运行它并立即看到我们的护腿。

## 头盔

这就像胸甲一样，只是我们更改了一些类别和槽位，如下所示。

<CodeHeader>BP/items/my_helm.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:my_helm",
            "menu_category": {
                "category": "equipment",
                "group": "minecraft:itemGroup.name.helmet"
            }
        },
        "components": {
            "minecraft:icon": "wiki:my_helm",
            "minecraft:display_name": {
                "value": "我的自定义头盔"
            },
            "minecraft:max_stack_size": 1,
            // 头盔附魔槽位
            "minecraft:enchantable": {
                "value": 10,
                "slot": "armor_head"
            },
            "minecraft:repairable": {
                "repair_items": [
                    {
                        "items": ["minecraft:stick"],
                        "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
                    }
                ]
            },
            // 可穿戴头部槽位
            "minecraft:wearable": {
                "slot": "slot.armor.head",
                "protection": 3
            },
            "minecraft:durability": {
                "max_durability": 200
            }
        }
    }
}
```

正如您可以看到的，没有什么太大变化，我们只是更新了类别/槽位以适应头盔的正确选项，然后我们添加附着物文件（如果您需要的话，这里有物品纹理）。

![](/assets/images/tutorials/custom-armor/custom_helmet.png)

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-armor/custom_helmet.png">
    下载纹理
</Button>

<CodeHeader>RP/attachables/my_helm.json</CodeHeader>

```json
{
    "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "wiki:my_helm",
            // 这2个是默认的，也是必需的
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                // 这是我们CUSTOM盔甲纹理，接下来需要制作
                "default": "textures/models/armor/custom_main",
                // 这个纹理实际上不存在于我们的RP中
                // 但没有它会导致崩溃，所以保留它
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            // 我们告诉它为头盔使用什么几何形状
            "geometry": {
                "default": "geometry.player.armor.helmet"
            },
            // 我们告诉它隐藏头盔图层，因为我们将在上方显示我们的盔甲
            "scripts": {
                "parent_setup": "v.helmet_layer_visible = 0.0;"
            },
            // 我们告诉它使用什么控制器（默认盔甲控制器）
            "render_controllers": ["controller.render.armor"]
        }
    }
}
```

就是这样，您现在有了一套完整的3/4，我们也可以了解一下靴子，这样您就知道所有类别等等。

## 靴子

您已经知道模式了，所以让我们制作物品和附着物json文件。

<CodeHeader>BP/items/my_boots.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:my_boots",
            "menu_category": {
                "category": "equipment",
                "group": "minecraft:itemGroup.name.boots"
            }
        },
        "components": {
            "minecraft:icon": "wiki:my_boots",
            "minecraft:display_name": {
                "value": "我的自定义靴子"
            },
            "minecraft:max_stack_size": 1,
            // 可附魔脚部
            "minecraft:enchantable": {
                "value": 10,
                "slot": "armor_feet"
            },
            "minecraft:repairable": {
                "repair_items": [
                    {
                        "items": ["minecraft:stick"],
                        "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
                    }
                ]
            },
            // 脚部槽位
            "minecraft:wearable": {
                "slot": "slot.armor.feet",
                "protection": 3
            },
            "minecraft:durability": {
                "max_durability": 200
            }
        }
    }
}
```

如果您需要，这是自定义靴子纹理。

![](/assets/images/tutorials/custom-armor/custom_boots.png)

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-armor/custom_boots.png">
    下载纹理
</Button>

<CodeHeader>RP/attachables/my_boots.json</CodeHeader>

```json
{
    "format_version": "1.8.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "wiki:my_boots",
            // 这2个是默认的，也是必需的
            "materials": {
                "default": "armor",
                "enchanted": "armor_enchanted"
            },
            "textures": {
                // 这是我们CUSTOM盔甲纹理，接下来需要制作
                "default": "textures/models/armor/custom_main",
                // 这个纹理实际上不存在于我们的RP中
                // 但没有它会导致崩溃，所以保留它
                "enchanted": "textures/misc/enchanted_actor_glint"
            },
            // 我们告诉它为靴子使用什么几何形状
            "geometry": {
                "default": "geometry.player.armor.boots"
            },
            // 我们告诉它隐藏靴子图层，因为我们将在上方显示我们的盔甲
            "scripts": {
                "parent_setup": "v.boot_layer_visible = 0.0;"
            },
            // 我们告诉它使用什么控制器（默认盔甲控制器）
            "render_controllers": ["controller.render.armor"]
        }
    }
}
```

就是这样，您现在有一整套自定义盔甲可以炫耀了，并且可以以此为基础制作游戏中的其他盔甲。

> 值得注意的是，我们在这里使用了2个单独的纹理，您可能会为每个附着物使用一个纹理，但每个新纹理都会消耗内存，所以最好尽可能少使用。
> 所以这就是您应该得到的结果，另外还有一个奖励部分，关于使用过滤器制作套装效果，这有点高级，但这是很有趣的事情。

![](/assets/images/tutorials/custom-armor/custom-set-image.jpg)

## 奖励 - 制作套装效果

这有点高级，但假设您希望自定义盔甲表现得像RPG游戏中的套装。我们可以添加一些代码来检查我们是否装备了套装，并对其做一些很棒的操作。

注意，对于效果，您可以使用tick.json和带有hasitem选择器参数的函数来避免使用player.json。

在这个例子中，我们只是添加一个传送攻击者到附近某个位置的机会，并在控制台上打印一段文字增加趣味性。

由于我们希望在角色受到攻击时触发，我们需要向`player.json`文件添加一些逻辑。这是一个巨大的文件，不幸的是我们需要确保其中包含所有默认内容，因为它会覆盖默认的玩家组件等。

所以与其包含整个`player.json`，我只包含您需要添加到`components`和`events`部分的部分。如果您不知道`player.json`是什么，请查看原版行为包并找到它，然后将其复制到您的项目中。

首先让我们在组件部分添加伤害传感器组件，它监听您受到伤害的时刻，并让您从中引发事件。

<CodeHeader>BP/entities/player.json#components</CodeHeader>

```json
"minecraft:damage_sensor": {
    "triggers": {
        "on_damage": {
            "filters": {
                "all_of": [
                    {
                        "test": "has_equipment",
                        "subject": "self",
                        // 域在此情况下是身体部位
                        "domain": "head",
                        "operator": "==",
                        // 我们要检查的物品标识符
                        "value": "wiki:my_helm"
                    },
                    {
                        "test": "has_equipment",
                        "subject": "self",
                        "domain": "torso",
                        "operator": "==",
                        // 值得注意的是，您可以省略minecraft内置物品的前缀，即stick
                        "value": "wiki:my_chest"
                    },
                    {
                        "test": "has_equipment",
                        "subject": "self",
                        "domain": "leg",
                        "operator": "==",
                        "value": "wiki:my_leggings"
                    },
                    {
                        "test": "has_equipment",
                        "subject": "self",
                        "domain": "feet",
                        "operator": "==",
                        "value": "wiki:my_boots"
                    }
                ]
            },
            // 如果过滤器中的所有触发器匹配，则引发事件
            "event": "wiki:armor_sets.my_custom.taken_damage"
        },
        // 这意味着如果它匹配检查，仍然应用伤害
        // 对于忽略团队伤害或类似场景很好
        "deals_damage": true
    }
}
```

从注释可以看出，有很多内容，但实际上我们要做的就是监听某些事件，然后确保我们只过滤我们关心的结果，然后触发一个事件。

> 事件可以叫任何名称，但通常最好让它更具体，以防您最终有多个类似的事件等，而且它可以帮助查找是否有多个部分属于它，例如我可以搜索"armor_sets"并找到所有与之相关的事件。
> 然后当您完成后，在同一文件中我们决定如何处理事件，将其放入我们的`events`部分。

<CodeHeader>BP/entities/player.json#events</CodeHeader>

```json
"wiki:armor_sets.my_custom.taken_damage": {
    "randomize": [
        {
            "weight": 1,
            // 我们在这里执行一个序列，因为我们想在一个实体上应用一个命令
            // 在我们自己身上应用另一个命令
            "sequence": [
                {
                    // 这将在当时的情境中拿攻击者/其他实体
                    // 在damage_sensor中引发事件时
                    "queue_command": {
                        // 将实体从我们身边移开
                        "command": "spreadplayers ~~ 5 20 @s",
                        // 在攻击者而不是我们身上运行命令
                        "target": "other"
                    }
                },
                {
                    "queue_command": {
                        "command": "tellraw @s{\"rawtext\":[{\"text\":\"§a你的盔甲发光，敌人消失了\"}]}"
                    }
                }
            ]
        },
        {
            // 假权重，以便它偶尔发生
            "weight": 20
        }
    ]
}
```

就是这样，您可以根据自己的需要调整各个部分，但最终您拥有所有零件来为盔甲应用效果，检查您是否装备了整套，或者检查其他装备。

您还可以将装备检查从self改为other，检查攻击您的角色是否装备了某些东西，甚至检查您是否正在攻击某种方块/实体，并基于此执行不同效果。我们在这里没有直接涉及，但这已经足够好的起点，可以让您开始并发挥创意。
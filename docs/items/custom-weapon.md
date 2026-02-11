---
title: 自定义武器
description: 了解如何制作自定义武器。
tags:
    - easy
mentions:
    - SirLich
    - solvedDev
    - MedicalJewel105
    - aexer0e
    - PepijnMC
    - ThomasOrs
    - Xterionix
    - QuazChick
---

跟随本指南创建一个自定义剑物品。自己修改以创建任何你想要的武器！

如果你想为你的武器添加更多功能，请查看[击中实体](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-events#hit-entity)事件。

## 自定义剑物品

与其他物品教程一样，我们将从制作一个简单的自定义剑开始。

<CodeHeader>BP/items/my_sword.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:my_sword",
            "menu_category": {
                "category": "equipment",
                "group": "minecraft:itemGroup.name.sword"
            }
        },
        "components": {
            "minecraft:max_stack_size": 1,
            // 使物品在第三人称中具有正确的定位
            "minecraft:hand_equipped": true,
            "minecraft:durability": {
                "max_durability": 600
            },
            // 此武器对实体造成的额外伤害
            "minecraft:damage": 10,
            // 我们也让它能在"sword"槽位附魔
            "minecraft:enchantable": {
                "value": 10,
                "slot": "sword"
            },
            // 此纹理用于物品栏和手持模型
            "minecraft:icon": "wiki:my_sword",
            "minecraft:display_name": {
                "value": "我的自定义剑"
            },
            // 允许用木棍修复剑
            "minecraft:repairable": {
                "repair_items": [
                    {
                        "items": ["minecraft:stick"],
                        "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"
                    }
                ]
            }
        }
    }
}
```

所以至少这足以将一把剑放入游戏中，我们仍需要在资源包中注册图标，但这不是什么大问题，因为我们只需要进入我们的资源包文件夹并像这样输入即可。

<CodeHeader>RP/textures/item_texture.json</CodeHeader>

```json
{
    "resource_pack_name": "custom-weapon",
    "texture_name": "atlas.items",
    "texture_data": {
        "wiki:my_sword": {
            // 确保您已在此处放置名为my_sword.png的图标纹理
            "textures": "textures/items/my_sword"
        }
    }
}
```

如果您没有自己的纹理，这里有一个示例纹理，只需"另存为"并将其放入`RP/textures/items`目录。

<WikiImage
    src="/assets/images/tutorials/custom-weapons/my_sword.png"
    alt="剑纹理"
    pixelated="true"
    width="128"
    class="my-4 mr-4"
/>

<Button link="https://raw.githubusercontent.com/Bedrock-OSS/bedrock-wiki/wiki/docs/public/assets/images/tutorials/custom-weapons/my_sword.png">
    下载纹理
</Button>

## 游戏中

现在我们有一个包含物品JSON定义的行为包和一个包含纹理的资源包，我们可以创建一个新世界，确保应用我们的附加包。

完成以上所有步骤后，进入创造模式，你应该能够通过名称找到你的剑，或在剑类别中找到，如图所示。

![](/assets/images/tutorials/custom-weapons/custom_sword.jpg)

然后，如果你把它放在手中，你应该在游戏中看到这样的效果。

![](/assets/images/tutorials/custom-weapons/held_sword.jpg)

现在这并不太难，对吧？你现在可以制作任意数量的自定义剑，但是如果你愿意，从这里开始还有很多有趣的事情可以做。

## 工具功能

你还可以混合搭配其他组件，如`minecraft:digger`，让你更快地穿过蜘蛛网或竹子，如下所示：

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:digger": {
    "use_efficiency": true,
    "destroy_speeds": [
        {
            "block": "minecraft:web",
            "speed": 15
        },
        {
            "block": "minecraft:bamboo",
            "speed": 10
        }
    ]
}
```

## 物品配方

你可能应该为它制作一个配方，这在前面的章节中有介绍，因为那里没有太多新内容，但如果你不确定，这里有一个示例，用末影之眼和末影珍珠制作剑。

<CodeHeader>BP/recipes/my_sword.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:recipe_shaped": {
        "description": {
            "identifier": "wiki:my_sword"
        },
        "tags": ["crafting_table"],
        "pattern": ["e", "E", "#"],
        "key": {
            "#": {
                "item": "minecraft:stick"
            },
            "E": {
                "item": "minecraft:ender_eye"
            },
            "e": {
                "item": "minecraft:ender_pearl"
            }
        },
        "result": {
            "item": "wiki:my_sword"
        },
        "unlock": [
            {
                "item": "minecraft:ender_eye"
            }
        ]
    }
}
```

![](/assets/images/tutorials/custom-weapons/sword_recipe.jpg)

如果你将其添加进去，你现在就可以在游戏中制作你的剑，希望能去制作任何你喜欢的其他自定义剑，甚至弓或三叉戟。
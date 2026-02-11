---
title: 自定义食物
description: 了解如何创建自定义食物，当被消费时会给玩家带来效果（比如金苹果）。
tags:
    - easy
    - scripting
license: true
mentions:
    - KawEduh-dv
    - QuazChick
---

:::tip 格式版本 1.21.130
此页面需要基本理解自定义物品。
开始之前请查看[物品指南](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/items-intro)!
:::

在本页面上，您将学习如何创建自定义食物，当被消费时会给玩家带来效果（比如金苹果）。

## 基础物品JSON

<CodeHeader>BP/items/custom_food.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:custom_food",
            "menu_category": {
                "category": "equipment",
                "group": "minecraft:itemGroup.name.miscFood"
            }
        },
        "components": {
            "minecraft:icon": "wiki:custom_food",
            "minecraft:food": {
                "nutrition": 4,
                "saturation_modifier": 0.6
            },
            "minecraft:use_animation": "eat",
            "minecraft:use_modifiers": {
                "use_duration": 1.6,
                "movement_modifier": 0.33
            },
            "minecraft:tags": {
                "tags": [
                    "minecraft:is_food",
                    "minecraft:is_meat", // 仅当食物是肉类时包含
                    "minecraft:is_cooked" // 仅当食物是熟食时包含
                ]
            }
        }
    }
}
```

如果您已经知道如何将纹理放在正确路径中，可以跳过，但如果不知道，仅仅将纹理文件放在`RP/textures/items`文件夹中是不够的。

我们需要在资源包的`RP/textures/item_texture.json`文件中创建一个名为`wiki:custom_<thing>`的对象，如下面的示例。

<CodeHeader>RP/textures/item_texture.json</CodeHeader>

```json
{
    "texture_name": "atlas.items",
    "texture_data": {
        "wiki:custom_food": {
            "textures": "textures/items/custom_food"
        }
    }
}
```

## 应用效果

为了在食物被消费时对玩家应用效果，我们需要使用[自定义组件](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-events)。

在本教程中，我们的自定义组件将是`wiki:food_effects`。
确保将命名空间更改为唯一标识您的附加包的内容。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"wiki:food_effects": [
    {
        "name": "wither",
        "duration": 600, // 30秒（以刻度计）。
        "amplifier": 1
    },
    {
        "name": "slowness",
        "duration": 600,
        "amplifier": 2
    }
]
```

### 自定义组件脚本

在脚本文件中，使用特定物品后的单一事件，使用此物品后玩家将获得一个或多个效果，如以下代码所示。

<CodeHeader>BP/scripts/main.js</CodeHeader>

```js
import { system } from "@minecraft/server";

const ItemFoodEffectsComponent = {
    onConsume({ source }, { params }) {
        // 遍历组件数组中的每个对象。
        for (const { name, duration, amplifier } of params) {
            source.addEffect(name, duration, { amplifier });
        }
    },
};

system.beforeEvents.startup.subscribe(({ itemComponentRegistry }) => {
    // 注册自定义组件以在物品JSON文件中使用：
    itemComponentRegistry.registerCustomComponent("wiki:food_effects", ItemFoodEffectsComponent);
});
```
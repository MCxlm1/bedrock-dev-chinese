---
title: 工具耐久度
tags:
    - experimental
    - intermediate
    - scripting
mentions:
    - MedicalJewel105
    - TheDoctor15
    - napstaa967
description: 为自定义工具添加类似原版的耐久度。
hidden: true
---

## 介绍

1.21.10+物品与1.10和1.16物品有不同的耐久度机制。
现在您需要定义何时物品会受到耐久度损伤，以及执行损伤的事件。
本页将讨论的内容：

-   耐久度组件
-   更新耐久度的事件
-   对实体造成伤害
-   方块破坏
-   `repair_amount`值
-   `on_tool_used`事件

### 组件

<CodeHeader>BP/items/my_item.json#components</CodeHeader>

```json
"minecraft:durability": {
    "max_durability": 200
}
```

`minecraft:durability`将为您的物品提供设定的最大耐久度

## 事件

### 物品事件

<CodeHeader>BP/items/my_item.json#events</CodeHeader>

```json
"durability_update": {
    "damage": {
        "type": "none",
        "amount": 1,
        "target": "self"
    }
}
```

当调用此事件时，物品（`self`目标）将受到耐久度损伤。
看起来很简单，不是吗？

### 脚本事件

对于脚本方法，我们将使用一个函数来损坏我们的物品

此函数支持物品上的耐久强化附魔

<CodeHeader>BP/scripts/main.js</CodeHeader>

```js
function damage_item(item) {
    // 获取耐久度
    const durabilityComponent = item.getComponent("durability");
    var unbreaking = 0;
    // 获取耐久强化等级
    if (item.hasComponent("enchantments")) {
        unbreaking = item.getComponent("enchantments").enchantments.getEnchantment("unbreaking");
        if (!unbreaking) {
            unbreaking = 0;
        } else {
            unbreaking = unbreaking.level;
        }
    }
    // 应用损伤
    if (durabilityComponent.damage == durabilityComponent.maxDurability) {
        return;
    }
    durabilityComponent.damage += Number(
        Math.round(Math.random() * 100) <= durabilityComponent.getDamageChance(unbreaking)
    );
    return item;
}
```

## 对实体造成伤害

### 使用脚本

:::warning 实验性脚本

此脚本使用`@minecraft/server 1.9.0-beta`，将在下次Minecraft更新中更改。
:::

对于格式版本1.20.40及以上版本，`on_hurt_entity`不再有效。

这提供了一种使用脚本来损坏武器的方法

<CodeHeader>BP/scripts/main.js</CodeHeader>

```js
// 将您的物品ID添加到此数组中
const my_items = ["wiki:silver_dagger"];

world.afterEvents.entityHurt.subscribe((event) => {
    // 如果没有来源实体，则跳过
    if (!event.damageSource.damagingEntity) return;

    // 获取装备的武器
    const equipment = event.damageSource.damagingEntity.getComponent("minecraft:equippable");
    if (!equipment) return;
    const weapon = equipment.getEquipment(EquipmentSlot.Mainhand);

    // 如果没有武器，则跳过
    if (!weapon) return;

    // 如果物品不在我们的物品ID列表中，则跳过
    if (!my_items.includes(weapon.typeId)) return;
    let newItem = damage_item(weapon);
    equipment.setEquipment(EquipmentSlot.Mainhand, newItem);
    if (!newItem) {
        if (event.damageSource.damagingEntity instanceof Player) {
            event.damageSource.damagingEntity.playSound("random.break");
        }
    }
});
```

### on_hurt_entity

:::warning

格式版本1.20.40中移除了`on_hurt_entity`
:::

`on_hurt_entity`可以在"minecraft:weapon"组件中定义。它告诉游戏当玩家使用此物品伤害实体时应该发生什么事件。

<CodeHeader>BP/items/my_item.json#components</CodeHeader>

```json
"minecraft:weapon": {
    "on_hurt_entity": {
        "event": "durability_update"
    }
}
```

## 方块破坏

### 使用脚本

:::warning 实验性脚本

此脚本使用`@minecraft/server 1.9.0-beta`，将在下次Minecraft更新中更改。
:::

对于格式版本1.20.20及以上版本，`on_dig`不再有效。

这提供了一种使用脚本来损坏挖掘物品的方法

<CodeHeader>BP/scripts/main.js</CodeHeader>

```js
// 将您的物品ID添加到此数组中
const my_items = ["wiki:obsidian_pickaxe"];

world.afterEvents.playerBreakBlock.subscribe((event) => {
    // 如果没有物品，则跳过
    if (!event.itemStackAfterBreak) return;
    // 如果物品不在我们的物品ID列表中，则跳过
    if (!my_items.includes(event.itemStackAfterBreak.typeId)) return;

    // 如果玩家处于创造模式，则跳过
    if (
        world
            .getPlayers({
                gameMode: GameMode.creative,
            })
            .includes(event.player)
    )
        return;
    const newItem = damage_item(event.itemStackAfterBreak);
    event.player.getComponent("minecraft:equippable").setEquipment(EquipmentSlot.Mainhand, newItem);
    if (!newItem) {
        event.player.playSound("random.break");
    }
});
```

### on_dig

:::warning

格式版本1.20.20中移除了`on_dig`
:::

`on_dig`可以在"minecraft:digger"组件中定义。它告诉游戏当玩家使用此物品挖掘方块时应该发生什么事件。

<CodeHeader>BP/items/my_item.json#components</CodeHeader>

```json
"minecraft:digger": {
    "use_efficiency": true,
    "destroy_speeds": [
        {
            "block": {
                "tags": "q.any_tag('wood')"
            },
            "speed": 8,
            "on_dig": {
                // 定义挖掘带有wood标签的方块时应发生的事件。
                "event": "durability_update"
            }
        }
    ],
    "on_dig": {
        // 定义破坏任何方块时应发生的事件。
        "event": "durability_update"
    }
}
```

## repair_amount

`repair_amount`可以在"minecraft:repairable"组件中定义。它告诉游戏在修复物品时应该恢复多少耐久度。

<CodeHeader>BP/items/my_item.json#components</CodeHeader>

```json
"minecraft:repairable": {
    "repair_items": [
        {
            "repair_amount": "context.other->q.remaining_durability + 0.05 * context.other->q.max_durability",
            "items": [
                "bs:silver",
                "bs:silver_axe"
            ]
        }
    ]
}
```

公式解释：

`"context.other->q.remaining_durability + 0.05 * context.other->q.max_durability"`

最终耐久度将是第一把斧头的耐久度 + 第二把斧头的耐久度 + 第二把斧头最大耐久度的5%。

## on_tool_used

（这可能现在不起作用）
`on_tool_used`是可以使用标签调用的特殊事件。
标签的工作方式类似于实体的运行时标识符。
已知标签：

| 标签 | 效果 | 如何调用 |
| -------------------- | -------------- | -------------------------------------------------- |
| minecraft:is_axe | 剥木头 | 通过与斧头可交互的方块互动 |
| minecraft:is_hoe | 制造耕地 | 通过与锄头可交互的方块互动 |
| minecraft:is_pickaxe | 未知 | 未知 |
| minecraft:is_sword | 未知 | 未知 |

您可以这样应用这些标签：

<CodeHeader>BP/items/my_item.json#components</CodeHeader>

```json
"tag:minecraft:is_axe": {}
}
```
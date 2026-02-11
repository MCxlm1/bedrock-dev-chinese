---
title: 物品事件
description: 当满足某些条件时触发物品事件。创建者可以挂钩这些事件以在事件触发时修改游戏世界。
nav_order: 4
tags:
    - scripting
license: true
mentions:
    - SmokeyStack
    - QuazChick
---

:::tip 格式版本 1.21.130
在创建自定义物品时使用最新格式版本可以访问新功能和改进。本维基旨在分享有关自定义物品的最新信息，当前针对格式版本 1.21.130。
:::

## 注册自定义组件

当满足某些条件时会触发物品事件，并且可以在脚本中注册的**自定义组件**中"监听"这些事件，这些脚本在世界加载之前注册。

在每个自定义组件中，事件处理函数（如 [`onBeforeDurabilityDamage`](#before-durability-damage)）被列出，以配置在每个事件触发时您希望发生的事情。

_此示例防止物品在击中实体时受到耐久度损伤：_

<CodeHeader>BP/scripts/unbreakable.js</CodeHeader>

```js
import { system } from "@minecraft/server";

/** @type {import("@minecraft/server").ItemCustomComponent} */
const ItemUnbreakableComponent = {
    onBeforeDurabilityDamage(event) {
        event.durabilityDamage = 0;
    },
};

system.beforeEvents.startup.subscribe(({ itemComponentRegistry }) => {
    itemComponentRegistry.registerCustomComponent("wiki:unbreakable", ItemUnbreakableComponent);
});
```

## 应用自定义组件

要将自定义组件绑定到物品，只需在物品JSON的`components`中列出它。

<CodeHeader>minecraft:item</CodeHeader>

```json
"components": {
    "wiki:unbreakable": {}
}
```

## 事件列表

### 耐久度损伤前

当包含此组件的物品击中实体并即将受到耐久度损伤时，将调用此事件。

<CodeHeader>自定义组件</CodeHeader>

```js
onBeforeDurabilityDamage(event) {
    event.attackingEntity // 攻击实体。
    event.durabilityDamage // 事件发生时应用于物品耐久度的损伤。
    event.hitEntity // 被击中的实体。
    event.itemStack // 用来击中实体的物品堆叠。
}
```

### 使用完成

:::tip 依赖项
完成使用事件需要在您的物品上激活[`minecraft:use_modifiers`](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-components#use-modifiers)组件才能触发。
:::

当包含此组件的物品的使用持续时间完成时，将调用此事件。

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:use_modifiers": {
    "use_duration": 5
}
```

<CodeHeader>自定义组件</CodeHeader>

```js
onCompleteUse(event) {
    event.itemStack // 返回已完成充电的物品堆叠。
    event.source // 返回触发此物品事件的源实体。
}
```

### 消耗

当包含此组件的物品被实体吃掉时，将调用此事件。

:::tip 依赖项
完成使用事件需要在您的物品上激活[`minecraft:use_modifiers`](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-components#use-modifiers)和[`minecraft:food`](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-components#food)组件才能触发。
:::

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:food": {},
"minecraft:use_modifiers": {
    "use_duration": 5
}
```

<CodeHeader>自定义组件</CodeHeader>

```js
onConsume(event) {
    event.itemStack // 被消耗的物品堆叠。
    event.source // 消耗物品的源实体。
}
```

### 击中实体

当使用包含此组件的物品击中另一个实体时，将调用此函数。

<CodeHeader>自定义组件</CodeHeader>

```js
onHitEntity(event) {
    event.attackingEntity // 攻击实体。
    event.hadEffect // 击中是否成功或有任何效果。
    event.hitEntity // 被击中的实体。
    event.itemStack // 用来击中实体的物品堆叠。
}
```

### 挖掘方块

当使用包含此组件的物品挖掘方块时，将调用此函数。

<CodeHeader>自定义组件</CodeHeader>

```js
onMineBlock(event) {
    event.block // 受此事件影响的方块。
    event.itemStack // 用来挖掘方块的物品堆叠。
    event.minedBlockPermutation // 被挖掘的方块排列。
    event.source // 挖掘方块的实体。
}
```

### 使用

当玩家使用包含此组件的物品时，将调用此函数。

<CodeHeader>自定义组件</CodeHeader>

```js
onUse(event) {
    event.itemStack // 使用物品时的物品堆叠。
    event.source // 使用物品的玩家。
}
```

### 在方块上使用

当在方块上使用包含此组件的物品时，将调用此函数。

<CodeHeader>自定义组件</CodeHeader>

```js
onUseOn(event) {
    event.source // 在方块上使用物品的实体。
    event.usedOnBlockPermutation // 物品被使用的方块排列。
}
```
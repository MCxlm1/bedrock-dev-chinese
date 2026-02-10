---
title: AOE云介绍
description: 学习如何使用和操控区域效果云。
category: Tutorials
tags:
    - intermediate
mentions:
    - Sprunkles137
    - MedicalJewel105
    - Nytreon
---

**区域效果云**，在内部也被称为AOE云和`minecraft:area_effect_cloud`，是具有许多独特特性的特殊实体。通常这些实体是通过投掷滞留药水创建的，但通过结构和一些NBT编辑技巧，我们可以在制图方面以非常强大的方式操控它们。

## 概述

AOE云有几项我们可以利用的特殊功能：

-   作为[虚拟实体](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/dummy-entities)，AOE云性能极高。它们被优化为与世界或其他实体没有碰撞。这使得它们非常适合围绕玩家或其他实体的情况。
-   AOE云完全静止，只能通过命令移动。这对于精确定位很重要的情况非常理想。
-   AOE云可以配置为施加药水效果。持续时间可以设置到刻度，以及效果是否环境友好、是否在屏幕上显示、是否发出粒子等。
-   具有`minecraft:area_effect_cloud`运行时标识符的实体继承了这些相同属性。

## 放置AOE云

要创建AOE云，我们首先需要创建一个结构来容纳它。AOE云必须具有特定的NBT数据才能正确存在。

### NBT编辑器

推荐使用以下NBT编辑器之一来编辑结构文件：

-   [NBT Studio](https://github.com/tryashtar/nbt-studio)（由tryashtar制作的独立程序）
-   [NBT Viewer](https://marketplace.visualstudio.com/items?itemName=Misodee.vscode-nbt)（由Misode制作的Visual Studio Code扩展）

### 结构文件

为方便起见，本文包含一个预制的结构文件，您可以下载并使用。其中包含一个具有最小可能半径（0.5个方块）的AOE云，存在最长时间（`2^31 - 1`刻度，大约29,826小时）。

<Button link="/assets/packs/entities/aec/aec.mcstructure" download>
    下载MCSTRUCTURE
</Button>

请参考本文了解如何编辑结构文件：[.mcstructure](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/nbt/mcstructure)

### NBT格式

| 标签                  | 类型    | 描述                                                                                                                       |
| -------------------- | ------- | --------------------------------------------------------------------------------------------------------------------------------- |
| Duration             | 整数 | 云存在多久后消失，以刻度为单位。值为0将导致云瞬间消失。              |
| DurationOnUse        | 整数 | 应用效果时持续时间应如何改变。                                                                     |
| InitialRadius        | 浮点数   | 云创建时的半径大小。小于0.5的值会导致云瞬间消失。                              |
| RadiusChangeOnPickup | 浮点数 | 玻璃瓶拾取时效果半径改变多少。这用于末影龙吐息云。 |
| RadiusOnUse          | 浮点数 | 应用效果时半径应如何改变。                                                                       |
| RadiusPerTick        | 浮点数   | 每刻度半径改变多少。                                                                                           |
| ParticleColor        | 整数 | 粒子效果的颜色，以十进制存储。                                                                              |
| ParticleId           | 整数 | 用于存储组件粒子ID的遗留值。无效果。                                                                  |
| PotionId             | 短整数   | 创建时此云的药水效果ID。无效果。                                                                        |
| ReapplicationDelay   | 整数 | 效果可以应用的间隔，以刻度为单位。                                                                           |
| mobEffects           | 列表    | 描述应施加什么药水效果。                                                                                  |

以下是`mobEffects`标签的参数。

| 标签                             | 类型    | 描述                                                                                                                                                                |
| ------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Ambient                         | 字节    | 定义此效果的粒子是否应该是半透明的。                                                                                                      |
| Amplifier                       | 字节    | 此药水效果的强度。                                                                                                                                        |
| DisplayOnScreenTextureAnimation | 字节    | 应用效果时显示屏幕动画。这用于坏兆头、图腾和村庄英雄以在获得效果时显示浮动图标。 |
| Duration                        | 整数 | 此效果应用的时间量，以刻度为单位。                                                                                                                   |
| DurationEasy                    | 整数 | 简单模式下效果消失前的刻度数。                                                                                                                 |
| DurationNormal                  | 整数 | 普通模式下效果消失前的刻度数。                                                                                                               |
| DurationHard                    | 整数 | 困难模式下效果消失前的刻度数。                                                                                                                 |
| Id                              | 字节    | 此效果的药水效果ID。                                                                                                                                      |
| ShowParticles                   | 字节    | 定义此效果的粒子是否应该出现。                                                                                                              |

## 使用AOE云

一旦放置了AOE云，我们就可以像使用任何其他标记/虚拟实体一样使用它。

使用AOE云时需要考虑的一些事项：

-   它们使用粒子发射器`minecraft:mobspell_lingering`。为了避免看到粒子，资源包必须修改此粒子发射器。
-   实体`minecraft:area_effect_cloud`不向客户端发送其位置的更新；这意味着它将固定在生成的位置。仍然可以使用`/teleport`移动它。

### 脚本实用工具

为了更容易操控AOE云，提供了以下实用工具。它导出一个函数`registerOnEntityLoad`，在实体加载时调用回调。这可用于在将生成推迟到结构管理器后访问AOE云。

<Button link="/assets/packs/entities/aec/entity_load_handler.js" download>
    下载实用工具
</Button>

<CodeHeader>示例用法：</CodeHeader>

```js
import { world, DimensionLocation } from "@minecraft/server";
import { registerOnEntityLoad } from "./entity_load_handler.js";

/**
 * @param {DimensionLocation} location
 */
function spawnMarker(location) {
    const Structure_Location = {
        x: Math.floor(location.x),
        y: Math.floor(location.y),
        z: Math.floor(location.z),
    };
    world.structureManager.place("mystructure:aec", location.dimension, Structure_Location);
    registerOnEntityLoad("minecraft:area_effect_cloud", function initializeMarker(entity) {
        const Entity_Location = {
            x: location.x,
            y: location.y,
            z: location.z,
        };
        entity.teleport(Entity_Location);
        entity.addTag("origin");
        // 等等。
    });
}
```
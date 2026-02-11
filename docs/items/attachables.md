---
title: 附着物
description: 物品附着物文档。
tags:
    - beginner
mentions:
    - Sprunkles137
    - MedicalJewel105
    - AdamRaichu
    - Lufurrius
    - TheItsNameless
---

::: tip
本文档假设您对Molang、渲染控制器、动画和客户端实体定义有基本了解。请确保您熟悉[客户端实体](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/entity-intro-rp)的基础知识！
:::

## 介绍

当我们设计自定义物品或方块时，Minecraft会从模板构建一个模型，以便在手持时显示物品。这采用物品精灵作为拉伸纹理网格的形式，或方块用其模型显示。通过使用称为**附着物**的系统，我们可以在持有这些物品时设计自己的模型来显示。

一直想要木棒看起来像望远镜吗？或者挥舞一个带有旋转链条的大电锯？附着物就是实现这一目标的方法！

本文档涵盖创建附着物的**两种不同方式**，取决于所使用的几何体是如何构建的。

## 概述

附着物是在装备物品或方块时渲染实体模型的系统。这意味着物品握在主手、副手或装备在装甲槽中。

附着物定义在设计上与客户端实体定义非常相似；它们让我们定义纹理、材质、几何体和动画来显示附着物。

### 文件结构

附着物定义位于'attachables'文件夹中。文件布局与自定义实体完全相同。

<FolderView :paths="[
    'RP/animations/my_item.animation.json',
    'RP/attachables/my_item.entity.json',
    'RP/models/entity/my_item.geo.json',
    'RP/textures/entity/my_item.png',
    'RP/manifest.json'
]" />

### 附着物定义

这是一个附着物的基本示例。

<CodeHeader>RP/attachables/stick.entity.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "minecraft:attachable": {
        "description": {
            "identifier": "minecraft:stick",
            "materials": {
                "default": "entity",
                "enchanted": "entity_alphatest_glint"
            },
            "textures": {
                "default": "textures/entity/steve",
                "enchanted": "textures/misc/enchanted_item_glint"
            },
            "geometry": {
                "default": "geometry.wiki.steve_head"
            },
            "animations": {
                "hold_first_person": "animation.steve_head.hold_first_person",
                "hold_third_person": "animation.steve_head.hold_third_person"
            },
            "scripts": {
                "animate": [
                    {
                        "hold_first_person": "context.is_first_person == 1.0"
                    },
                    {
                        "hold_third_person": "context.is_first_person == 0.0"
                    }
                ]
            },
            "render_controllers": ["controller.render.item_default"]
        }
    }
}
```

需要注意此附着物定义的一些要点：

-   标识符与现有方块或物品ID匹配。这将在装备物品时激活附着物，并替换手持时显示的原始模型。
-   列出了用于附魔闪光的材质和纹理。如果您的物品在附魔时应具有闪光，则保留此功能很重要。

制作附着物比制作客户端实体文件稍微复杂一些。我们需要正确连接几何体的骨架，以便在装备时看起来正确。

## 方法1 - 附加到骨架

<Tag name="beginner" />

在此第一种方法中，我们将通过将您的模型附加到玩家骨骼之一来使用玩家骨架的副本构建附着物。

这种解决方案非常适合专为单个类型的生物/实体设计的模型，尤其是玩家；并且仅涉及一个装备槽。在Blockbench中查看模型的外观很容易。

### 设置骨架

我们需要重建玩家的骨架，以便我们的模型能被绑定到正确的骨骼上，否则它将不会绑定到任何骨骼，并会自由漂浮在玩家身上。

使用文本编辑器，从提供的玩家骨架文件中提取骨骼并复制到您的几何体文件中，然后将`rightItem`骨骼设置为模型中立方体的父级。将此几何体保存到您的资源包中。

为方便起见，这里准备了一个这样的模型。玩家模型中的立方体已经被删除：

<Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_one/steve_head.geo.json?raw=true">
    📄 几何体文件
</Button>

### 显示设置

让您的模型漂浮在玩家脚下并不理想。我们的下一步是创建动画，以便我们能够在玩家身上正确显示模型。

创建两个新动画，一个用于第一人称持握物品，另一个用于第三人称持握。选择您的第三人称动画，并将其定位到您想要的位置。将此动画保存到您的资源包中。

以下是此类动画的示例。这也包括第一人称动画——制作方法详见下面的章节。

<Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_one/steve_head.animation.json?raw=true">
    📄 动画文件
</Button>

### 第一人称动画

为了更容易创建第一人称动画，我们需要模仿手臂在第一人称中的定位。

:::tip
要为玩家的手添加动画，您需要使用玩家动画，而不是附着物动画。
:::

使用以下引导动画并将其导入Blockbench。它对右臂骨骼应用(95, -45, 115)的旋转和(13.5, -10, 12)的平移，完美模仿了第一人称中的手臂定位。

<Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_one/attachable_guide.animation.json?raw=true">
    📄 附着物引导文件
</Button>

:::warning 注意
这就是事情变得棘手的地方。两个动画都需要同时播放；您的第一人称动画和引导的第一人称动画。

确保在进行更改时正在编辑您的动画。先选择它，然后在其上播放引导的第一人称动画。
:::

### 结论

完成所有设置后，删除玩家骨架中可能存在的任何_立方体_，但保留骨骼。在游戏中查看模型！

## 方法2 - 绑定到骨骼

<Tag name="intermediate" />

在第二种方法中，附着物几何体将使用模型绑定构建。这允许模型直接附加到生物几何体中与其装备槽对应的骨骼上。Minecraft使用模型绑定其附着物项目，包括三叉戟、望远镜、弓和盾牌。

虽然此方法允许附着物动态应用到其他生物和装备槽，但模型绑定也有奇怪的特点，如下所示。一些开发者可能发现此方法更难使其正常工作。

### 模型绑定

我们的第一步是将模型文件格式版本升级到"1.16.0"（如果尚未如此）。如果是旧版文件，则在继续之前转换它；Blockbench有工具可以做到这一点（文件→转换项目）。

接下来是修改我们几何体的根骨骼，将其绑定到物品放置的装备槽。注意此摘录自骨架头几何文件的第4行：

<CodeHeader>RP/models/entity/skeleton_head.geo.json</CodeHeader>

```json
// 一块骨骼
{
    "name": "skeleton_head",
    "binding": "q.item_slot_to_bone_name(context.item_slot)",
    "pivot": [0, 4, 0],
    "cubes": [
        {
            "origin": [-4, 0, -4],
            "size": [8, 8, 8],
            "uv": [0, 0]
        }
    ]
}
```

骨骼中的`"parent"`键接受一个字符串，输入的任何骨骼名称都将设置为当前骨骼的父级；子骨骼保持其位置，但相对于父骨骼移动。

另一方面，`"binding"`键接受Molang，输入的任何骨骼名称的枢轴点都被设置为子骨骼及其子项应该继承的_根位置_。

对于`"binding"`的值，我们使用Molang查询`q.item_slot_to_bone_name`，它将槽名称转换为骨骼名称，使用上下文变量`context.item_slot`作为参数。这将此物品所在的装备槽名称转换为其在玩家几何体中对应的骨骼名称。转换如下：

-   `'main_hand'` → "rightitem"
-   `'off_hand'` → "leftitem"

将模型绑定应用到您的骨骼，并将几何体保存到您的资源包中。

这里提供了一个应用此绑定的示例模型：

<Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_two/skeleton_head.geo.json?raw=true">
    📄 几何体文件
</Button>

### 显示设置

完成后，下一步是设置动画以在第一人称和第三人称中显示模型。

创建两个新动画，一个用于第一人称持握物品，另一个用于第三人称持握。

为了更容易创建这些动画，请执行以下操作：

-   下载以下玩家骨架模型。我们将使用此作为定位模型的视觉辅助工具。

    <Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_two/player_skeleton.geo.json?raw=true">
        📄 玩家骨架文件
    </Button>

-   使用文本编辑器，将您模型中的骨骼和立方体添加到玩家骨架模型中，然后将玩家骨架模型导入Blockbench。
-   将您的模型的根骨骼设置为玩家骨架中'rightItem'骨骼的子级。
-   下载以下动画文件，导入`wiki.third_person_guide`动画。这将在稍后用于简化定位。

    <Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_two/attachable_guide.animation.json?raw=true">
        📄 附着物引导文件
    </Button>

这些引导动画有一个显著特点：它们对右项骨骼的y位置应用-24偏移，以抵消Minecraft对绑定骨骼应用的类似-24 y位置偏移。我们目前不确定为什么会发生这种情况。

:::warning 注意
类似于方法一，**两个**动画需要同时播放以实现正确定位。

确保在进行更改时正在编辑您的动画。先选择它，然后在其上播放引导动画。
:::

播放两个动画，并按您想要的方式定位您的模型。将动画保存到您的资源包中。

此定位的示例动画文件：

<Button link="https://github.com/Bedrock-OSS/bedrock-wiki/blob/wiki/docs/public/assets/packs/tutorials/attachables/method_two/skeleton_head.animation.json?raw=true">
    📄 动画文件
</Button>

### 第一人称动画

类似于第三人称动画，查看附着物引导文件并导入`wiki.first_person_guide`动画到Blockbench。同时播放您的动画和引导的第一人称动画，然后进行更改并保存文件。

## 示例包

如果您遇到困难或只是想看到一个工作示例，这些方法已汇编成一个您可以参考的示例包。

<Button link="https://github.com/Bedrock-OSS/bedrock-examples/releases/download/download/attachable-example.mcpack">
    💾 示例包
</Button>
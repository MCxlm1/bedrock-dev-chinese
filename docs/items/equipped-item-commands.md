---
title: 基于装备的命令
description: 当特定物品被装备时运行命令。
tags:
    - intermediate
mentions:
    - Chikorita-Lover
    - MedicalJewel105
    - Lufurrius
    - TheItsNameless
    - QuazChick
---

对于附加包来说，实现具有独特效果的新盔甲套装（如海龟壳）是一个常见的概念。
物品没有用于在特定条件下施加生物效果、发射粒子等的组件。
然而，使用`hasitem`命令选择器或服务端动画，这可以轻松实现！

## 命令选择器方法

[`hasitem`](https://minecraft.wiki/w/Target_selectors#Selecting_targets_by_item)选择器参数可用于基于实体装备槽中的物品来定位实体。

您可以在[Minecraft Wiki](https://minecraft.wiki/w/Slot#Bedrock_Edition)上查看附加槽位标识符的列表。

<CodeHeader>目标选择器</CodeHeader>

```c
@e[hasitem={item=wiki:custom_helmet,location=slot.armor.head}]
```

通过使用在`tick.json`文件中列出的函数，我们可以每tick使用此选择器运行命令。

例如，要让自定义头盔为其佩戴者提供跳跃提升，可以使用以下函数：

<CodeHeader>RP/functions/wiki/custom_helmet_effects.mcfunction</CodeHeader>

```c
effect @e[hasitem={item=wiki:custom_helmet,location=slot.armor.head}] jump_boost 10
```

<CodeHeader>BP/functions/tick.json</CodeHeader>

```json
{
    "values": ["wiki/custom_helmet_effects"]
}
```

## 服务端动画方法

请记住，这需要修改玩家行为，这是许多附加包的常见主题；因此，如果您想这样做，您的附加包可能与其他附加包不兼容。

第一步是创建一个服务端动画，这是一种在特定关键帧运行命令或事件的文件。虽然客户端动画在资源包中，但服务端动画在行为包中。您可以在此处阅读更多[内容](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/timers#animation-based-timers)。我们可以使用以下模板开始：

<CodeHeader>BP/animations/player.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animations": {
        "animation.player.emerald_armor": {
            "animation_length": 0.05,
            "loop": true,
            "timeline": {
                "0.0": []
            }
        }
    }
}
```

让我们来看看这个模板中的内容及其功能：

-   `animation.player.emerald_armor`是我们的动画标识符；您可以将其更改为其他内容，例如`animation.player.phantom_armor`。
-   `animation_length`是动画持续时间；我们将使用0.05秒，因为那是游戏内tick的长度。
-   `loop`非常直截了当；将其设置为true使动画循环。
-   `timeline`在给定的关键帧运行命令和事件。

我们可以在时间线中的`0.0`数组中添加命令来执行，比如一个`/effect`命令，如下所示：

```json
{
    "0.0": ["/effect @s speed 1 0"]
}
```

当然，我们不仅限于`/effect`。如果您想使用其他命令，比如`/function`或`/particle`，请随意！

在这之后，我们在服务端动画中就完成了，然后我们将进入物品的行为文件进行快速添加。

### 应用物品标签

要实际检查我们的物品是否被装备，我们可以使用Molang查询来检查物品标签。

如果以下情况之一成立，您可以跳过此部分：

-   您想要检查原版物品，例如通过`minecraft:iron_tier`标签检查铁质盔甲部件
-   您想通过`q.is_item_name_any`检查物品，该方法检查任意槽位中的物品标识符

在我们的物品行为中，我们必须添加一个标签。
例如，如果我们想添加`wiki:emerald_tier`标签，我们会将以下内容添加到标签组件中：

<CodeHeader>minecraft:item > components</CodeHeader>

```json
"minecraft:tags": {
    "tags": ["wiki:emerald_tier"]
}
```

就是这样，现在您的物品有了您分配给它的任何标签！如果您愿意，可以添加更多标签，但这就是我们正在做的事情所需要的全部。

### 玩家行为

最后，我们需要修改玩家行为以运行服务端动画。我们将在`description`中完全工作。

首先，我们需要为我们的动画设置一个短名称。如果您有客户端动画的经验，这个过程会非常相似。将`animations`添加到`description`中，并设置一个短名称，如下所示：

<CodeHeader>BP/entities/player.json > minecraft:entity > description</CodeHeader>

```json
{
    "identifier": "minecraft:player",
    "spawn_category": "creature",
    "is_spawnable": false,
    "is_summonable": false,
    "animations": {
        "emerald_armor": "animation.player.emerald_armor"
    }
}
```

现在设置了短名称，我们可以运行我们的动画。

将`scripts`添加到`description`中，并设置一个Molang查询来运行。要检查物品，我们可以使用以下之一：

-   `q.is_item_name_any`，用于检查任意槽位中给定的物品标识符。此示例将检查`wiki:totem_of_retreat`是否在任一手中：

```molang
q.is_item_name_any('slot.weapon.mainhand', 'wiki:totem_of_retreat') || q.is_item_name_any('slot.weapon.offhand', 'wiki:totem_of_retreat')
```

-   `q.equipped_item_any_tag`，用于检查给定槽位中是否有任何给定标签中的至少一个。此示例将允许翡翠或幻翼级别的盔甲部件使用：

```molang
q.equipped_item_any_tag('slot.armor.head', 'wiki:emerald_tier', 'wiki:phantom_tier')
```

-   `q.equipped_item_all_tags`，用于检查给定槽位中的所有给定标签。此示例将仅允许既是翡翠级又是古代级的盔甲部件：

```molang
q.equipped_item_all_tags('slot.armor.head', 'wiki:ancient_tier', 'wiki:emerald_tier')
```

让我们看看使用`q.equipped_item_any_tag`的一个示例：

<CodeHeader>BP/entities/player.json > minecraft:entity > description</CodeHeader>

```json
{
    "identifier": "minecraft:player",
    "spawn_category": "creature",
    "is_spawnable": false,
    "is_summonable": false,
    "animations": {
        "emerald_armor": "animation.player.emerald_armor"
    },
    "scripts": {
        "animate": [
            {
                "emerald_armor": "q.equipped_item_any_tag('slot.armor.head', 'wiki:emerald_tier')"
            }
        ]
    }
}
```

如果在头盔槽中装备了翡翠级物品，此示例将运行一个名为`emerald_armor`的短名称的服务端动画。您可以更改Molang字段以匹配您的物品标签，使用不同的查询，或添加额外的查询。

您可以在[Minecraft Wiki](https://minecraft.wiki/w/Slot#Bedrock_Edition)上查看附加槽位标识符的列表。

### 结论

通过设置服务端动画、玩家行为和物品标签，您的装备物品现在可以运行命令了！这种技术允许更大的物品自定义，而不局限于物品组件。如果您想为效果或附加包添加更多内容，请查看下一节；否则，恭喜，您已完成！

### 补充内容

#### 多个必需物品

如果您想在装备多件盔甲套装部件时运行命令，我们可以扩展之前的Molang：

<CodeHeader>BP/entities/player.json > minecraft:entity > description > scripts</CodeHeader>

```json
"animate": [
    {
        "emerald_armor": "q.equipped_item_any_tag('slot.armor.head', 'wiki:emerald_tier') && q.equipped_item_any_tag('slot.armor.chest', 'wiki:emerald_tier') && q.equipped_item_any_tag('slot.armor.legs', 'wiki:emerald_tier') && q.equipped_item_any_tag('slot.armor.feet', 'wiki:emerald_tier')"
    }
]
```

此示例将检查四个盔甲槽中的翡翠级盔甲，如果全部装备则运行动画。

#### 更多条件

海龟壳并不总是施加水下呼吸，而是在玩家首次进入水中时持续10秒。如果我们要让翡翠盔甲仅在生命值较低时运行我们的动画，可以在Molang中添加另一个查询：

<CodeHeader>BP/entities/player.json > minecraft:entity > description > scripts</CodeHeader>

```json
"animate": [
    {
        "emerald_armor": "q.equipped_item_any_tag('slot.armor.head', 'wiki:emerald_tier') && q.health <= 5"
    }
]
```

此示例将在剩余2.5颗心或更少时运行动画，允许玩家在危险时快速逃脱。

我们也可以将其应用于需要多个盔甲部件的情况，使用更长的Molang：

<CodeHeader>BP/entities/player.json > minecraft:entity > description > scripts</CodeHeader>

```json
{
    "animate": [
        {
            "emerald_armor": "q.equipped_item_any_tag('slot.armor.head', 'wiki:emerald_tier') && q.equipped_item_any_tag('slot.armor.chest', 'wiki:emerald_tier') && q.equipped_item_any_tag('slot.armor.legs', 'wiki:emerald_tier') && q.equipped_item_any_tag('slot.armor.feet', 'wiki:emerald_tier') && q.health <= 5"
        }
    ]
}
```

您可以在[bedrock.dev](https://bedrock.dev/docs/stable/Molang#List%20of%20Entity%20Queries)上查看记录的Molang查询列表。

#### 带有特效的多种物品

如果您想添加更多具有独特效果的物品，不用担心；这很容易做到。您可以创建一个新的服务端动画文件，或者添加到之前的文件中，如下所示：

<CodeHeader>BP/animations/player.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animations": {
        "animation.player.emerald_armor": {
            "timeline": {
                "0.0": ["..."]
            },
            "animation_length": 0.05,
            "loop": true
        },
        "animation.player.phantom_armor": {
            "timeline": {
                "0.0": ["..."]
            },
            "animation_length": 0.05,
            "loop": true
        }
    }
}
```

在我们的玩家行为中，您也需要添加到`animations`和`scripts`中。

<CodeHeader>BP/entities/player.json > minecraft:entity > description</CodeHeader>

```json
{
    "identifier": "minecraft:player",
    "spawn_category": "creature",
    "is_spawnable": false,
    "is_summonable": false,
    "animations": {
        "emerald_armor": "animation.player.emerald_armor",
        "phantom_armor": "animation.player.phantom_armor"
    },
    "scripts": {
        "animate": [
            {
                "emerald_armor": "q.equipped_item_any_tag('slot.armor.head', 'wiki:emerald_tier')"
            },
            {
                "phantom_armor": "q.equipped_item_any_tag('slot.armor.head', 'wiki:phantom_tier')"
            }
        ]
    }
}
```
---
title: 自定义陶片
description: 了解如何制作自定义陶片。
tags:
    - easy
license: true
mentions:
    - SmokeyStack
    - QuazChick
---

:::tip 格式版本 1.21.130
此页面需要对自定义物品有基本了解。
开始之前请查看[物品指南](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/items-intro)和[物品标签](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-tags)！
:::

Minecraft基岩版允许我们添加自定义陶片，这些陶片可用于制作装饰罐。
本文将介绍如何创建它们。

## 注册陶片

陶片由[物品标签](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/item-tags)控制！
要允许物品用于制作装饰罐，我们需要在物品上应用`minecraft:decorated_pot_sherds`标签。

:::warning 物品名称

显示用于制作装饰罐的陶片的工具提示将假定物品的本地化键格式为`item.<identifier>.name`{lang=xml}。
因此，您需要将物品的显示名称更改为这种格式。

<CodeHeader>RP/texts/en_US.lang</CodeHeader>

```lang
item.wiki:custom_pottery_sherd.name=自定义陶片
```

:::

<CodeHeader>BP/items/custom_pottery_sherd.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:custom_pottery_sherd",
            "menu_category": {
                "category": "items",
                "group": "minecraft:itemGroup.name.potterySherds"
            }
        },
        "components": {
            "minecraft:icon": "wiki:custom_pottery_sherd", // 在"RP/textures/item_texture.json"中定义的短名称
            "minecraft:tags": {
                "tags": ["minecraft:decorated_pot_sherds"] // 允许物品用于制作装饰罐
            },
            "minecraft:display_name": {
                "value": "item.wiki:custom_pottery_sherd.name"
            }
        }
    }
}
```

现在我们已经注册了我们的陶片，我们可以通过将陶片放入合成台来测试它。
如果您正确应用了标签，合成台应该输出一个装饰罐！

![自定义陶片物品被用于制作装饰罐](/assets/images/items/custom-pottery-sherds/crafting.png)

但是，装饰罐仍然不会显示陶片本身。这是因为我们还没有告诉装饰罐要显示什么图像！

## 定义图案纹理

这是我们将在本教程中使用的示例陶器图案纹理：

<WikiImage
    src="/assets/images/items/custom-pottery-sherds/custom_pottery_pattern.png"
    caption="RP/textures/blocks/custom_pottery_pattern.png"
    pixelated
/>

要告诉游戏显示纹理，我们需要编辑装饰罐的客户端实体文件。
将以下JSON文件添加到您的资源包：

<CodeHeader>RP/entity/decorated_pot.json</CodeHeader>

```json
{
    "format_version": "1.8.0",
    "minecraft:client_entity": {
        "description": {
            "identifier": "minecraft:decorated_pot",
            "textures": {
                // 此处列出的每个短名称应该是您的物品标识符但不带命名空间。
                "custom_pottery_sherd": "textures/blocks/custom_pottery_pattern" // 自定义罐子纹理路径
            }
        }
    }
}
```

如果您一切都做对了，您的装饰罐现在将显示您的陶片，而不是空白罐子！

![装饰罐，其中一个面上有自定义图案](/assets/images/items/custom-pottery-sherds/decorated_pot.png)

## 附加说明

**问题**：编辑客户端实体文件会使我的附加包与其他添加陶片的附加包不兼容吗？

**答案**：不会！客户端实体文件实际上会合并定义。
只要您的物品标识符是唯一的，其他附加包就不会覆盖它们！
如果您担心没有命名空间的物品标识符不够唯一，请在您的标识符前加上您的命名空间，例如`wiki:wiki_custom_pottery_sherd`。
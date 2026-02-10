---
title: 物品故障排除
description: 物品的故障排除指南。
category: General
tags:
    - help
mentions:
    - SmokeyStack
    - yanasakana
    - SirLich
    - MedicalJewel105
    - TheDoctor15
    - ThomasOrs
    - QuazChick
---

:::tip
此页面包含关于_物品_的故障排除信息。在继续阅读此处之前，您应该阅读我们的[全局故障排除](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/guide/troubleshooting)文档。
:::

## 从这里开始

> "我跟着教程做了，或者尝试制作自己的物品，但有些地方不对！"

不用惊慌！此页面将帮助调试常见问题。

### 物品不存在

-   确认您的包确实已应用于世界
-   确认您的物品确实在`BP/items/`文件夹中
-   确认您的物品是有效的，根据[jsonlint](https://jsonlint.com/)验证。
-   确认您的标识符都是小写，并且看起来类似于这样：`wiki:my_item`

### 缺少纹理

导航到您的`item_texture.json`文件。确保它被正确命名并在正确的文件夹中。一些错误的命名示例：

-   ⚠️ `texture/item_texture.json`
-   ⚠️ `textures/Item_texture.json`
-   ⚠️ `textures/item_textures.json`

以下是一个对比示例文件：

<CodeHeader>RP/textures/item_texture.json</CodeHeader>

```json
{
    "resource_pack_name": "wiki",
    "texture_name": "atlas.items",
    "texture_data": {
        "wiki:your_item_icon": {
            "textures": "textures/items/your_item_icon"
        }
    }
}
```

接下来，导航到您的物品BP文件。在组件部分下的物品文件中放置`minecraft:icon`组件。确保它被正确命名。

<CodeHeader>BP/items/your_item.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:item": {
        "description": {
            "identifier": "wiki:your_item",
            "menu_category": {
                "category": "items"
            }
        },
        "components": {
            "minecraft:icon": "wiki:your_item_icon" // 确保此字符串与您在item_texture.json中放置的短名称匹配
        }
    }
}
```

如果您正确遵循了此操作，您的物品现在应该有纹理了。

## 现在怎么办？

您已经到达指南的末尾。如果您仍然有任何问题，请随时[加入Discord服务器](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/discord)并在那里提出您的问题。
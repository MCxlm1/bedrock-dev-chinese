---
title: 高分辨率物品
description: 了解如何正确制作高分辨率纹理的物品。
category: Tutorials
mentions:
    - BlazeDrake
---

::: tip
本教程使用附着物。如果您不知道什么是附着物，请先阅读此页面：[附着物](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/items/attachables)
:::

## 介绍

大多数时候创建物品时，标准的16x16分辨率就足够了。但有时，您希望拥有更详细的物品。不过，您可能已经注意到高分辨率物品的问题：它们最终看起来更大，而不是更详细！

<WikiImage
    src="/assets/images/items/high-resolution-items/large_item_broken_thirdperson.png"
    alt="替代文本"
    width=1080
/>

解决方案是使用附着物在手持时缩小物品。进行此类计算可能需要相当多的调整，因此此页面提供了创建附着物以将物品缩放到正常大小所需的代码！虽然不是完美的，但它会看起来非常接近普通物品，只是在旋转和用它击打时的动画上有一些细微差别。其主要思想是使用动画将物品的大小缩小到应有的尺寸

## 文件

对于此修复，我们需要一个几何体、一个渲染控制器、一个附着物和一个动画。

这是几何体文件。它所做的就是使用纹理网格读取在您的附着物中使用的纹理，并基于此创建一个几何体，而无需费力地建模立方体来匹配物品！此文件不应需要任何编辑

<CodeHeader>RP/models/entity/large_item.geo.json</CodeHeader>

```json
{
    "format_version": "1.16.0",
    "minecraft:geometry": [
        {
            "description": {
                "identifier": "geometry.large_item",
                "texture_width": 16,
                "texture_height": 16,
                "visible_bounds_width": 2,
                "visible_bounds_height": 1.5,
                "visible_bounds_offset": [0, 0.25, 0]
            },
            "bones": [
                {
                    "name": "rightitem",
                    "pivot": [0, 0, 0],
                    "texture_meshes": [
                        {
                            "texture": "default",
                            "position": [0, 0, 0],
                            "local_pivot": [8, 0, 8]
                        }
                    ]
                }
            ]
        }
    ]
}
```

这是渲染控制器文件的样子。这个也很简单，因此不应该需要任何编辑。

<CodeHeader>RP/render_controllers/large_item.render_controllers.json</CodeHeader>

```json
{
    "format_version": "1.8.0",
    "render_controllers": {
        "controller.render.large_item": {
            "geometry": "Geometry.default",
            "materials": [{ "*": "variable.is_enchanted ? Material.enchanted : Material.default" }],
            "textures": ["Texture.default", "Texture.enchanted"]
        }
    }
}
```

这是附着物文件的样子。请注意，您需要将`<identifier>`更改为匹配您的物品标识符，并将`<path>`替换为您的物品纹理的文件路径（与`item_texture.json`中使用的相同）

<CodeHeader>RP/attachables/large_item.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "minecraft:attachable": {
        "description": {
            //将<identifier>替换为您的物品的完整标识符
            "identifier": "<identifier>",
            "materials": {
                "default": "entity_alphatest",
                "enchanted": "entity_alphatest_glint"
            },
            "textures": {
                //将<path>替换为您的物品纹理的文件路径。它应与item_texture.json中给出的文件路径匹配
                "default": "<path>",
                "enchanted": "textures/misc/enchanted_item_glint"
            },
            "geometry": {
                "default": "geometry.large_item"
            },
            "animations": {
                "hold": "animation.large_item.hold"
            },
            "scripts": {
                "animate": ["hold"]
            },
            "render_controllers": ["controller.render.large_item"]
        }
    }
}
```

现在我们有了这些文件，我们可以创建所有神奇效果的动画！此动画将旋转您的物品，使其与原版物品在手持时的旋转方式密切匹配。它还将您的物品缩小到合适的大小。

<CodeHeader>RP/animations/large_item.animation.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animations": {
        "animation.large_item.hold": {
            "loop": true,
            "bones": {
                "rightitem": {
                    //这些动画将其定位在正确位置
                    "position": [
                        "c.is_first_person ? -6 : 1",
                        "c.is_first_person ? 0 : -1",
                        "c.is_first_person ? -1 : -6"
                    ],
                    "rotation": [
                        "c.is_first_person ? 45 : 15",
                        "c.is_first_person ? -15 : 0",
                        "c.is_first_person ? 30 : -165"
                    ],
                    "scale": [
                        "c.is_first_person ? 1 : 0.5",
                        "c.is_first_person ? 1 : 0.5",
                        "c.is_first_person ? 1 : 0.5"
                    ]
                }
            }
        }
    }
}
```

## 结果

有了所有这些文件，您的物品应该看起来好多了！例如，这是我用于第一张图像的物品，当我将本教程中的所有文件添加到资源包中，并将所有适当的值替换为我在物品中使用的值时：

<WikiImage
    src="/assets/images/items/high-resolution-items/large_item_fixed_thirdperson.png"
    alt="替代文本"
    width=1080
/>
---
title: 实体资源包简介
category: General
nav_order: 2
tags:
    - guide
    - beginner
mentions:
    - SirLich
    - MedicalJewel105
    - Overload1252
    - ChibiMango
    - Lufurrius
    - TheItsNameless
    - SmokeyStack
    - ThomasOrs
description: 实体资源包介绍。
---

资源包实体文件包含形成实体视觉效果的资产引用。此外，它还包含如何以及何时渲染这些视觉效果的信息。

本页面将分解实体文件的每个部分并解释每一部分。有关制作自己的实体的演练，您可以查看我们的[初学者指南](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/guide/custom-entity)。

## 文件概要

<CodeHeader>RP/entity/example.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "minecraft:client_entity": {
        "description": {
            "identifier": "wiki:example",
            "materials": {...},
            "textures": {...},
            "geometry": {...},
            "render_controllers": [...],

            "animations": {...},
            "scripts": {...},

            "sound_effects": {...},
            "particle_effects": {...},

            "spawn_egg": {...},
            "enable_attachables": false,
            "hide_armor": false
        }
    }
}
```

虽然看起来令人不知所措，但其中许多部分只是_简称定义_。简称定义是我们将资产（如纹理路径或几何标识符）分配给简称的地方，然后我们可以在后面引用它。这意味着如果我们稍后更改资产的位置，只需要在一个地方更改。此外，它可以让我们的代码更简洁，无需写出冗长的路径位置或标识符。

## 材质

材质描述纹理如何被渲染。例如，骷髅有一种材质，允许纹理透明，而末影人有一种材质，允许它们的眼睛发光。有许多材质可以直接使用，而无需自己创建。

<CodeHeader>RP/entity/spider.entity.json#minecraft:client_entity/description</CodeHeader>

```json
"materials": {
    "default": "spider",
    "invisible": "spider_invisible"
},

```

这里材质是`spider`和`spider_invisible`，简称分别是`default`和`invisible`。记住这个键只是_定义_哪个材质附加到简称，我们的实体仍然不知道何时使用每一个。
有关预设材质列表，您可以查看我们的[页面](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/documentation/materials)。
有关制作自己的材质的指南，您可以查看这个[页面](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/visuals/materials)。请注意，这相当高级。

## 纹理

纹理是映射到我们几何体上的图像。每个实体都有不同的纹理。与材质类似，这个键也是一个简称定义，但这里，引用的是纹理路径。

<CodeHeader>RP/entity/bee.entity.json#minecraft:client_entity/description</CodeHeader>

```json
"textures": {
    "default": "textures/entity/bee/bee",
    "angry": "textures/entity/bee/bee_angry",
    "nectar": "textures/entity/bee/bee_nectar",
    "angry_nectar": "textures/entity/bee/bee_angry_nectar"
}

```

如前所述，我们可以定义多个纹理。如果我们要为实体的不同变体，比如上面的蜜蜂，这可能很有用。此外，我们可以使用多个纹理来将不同纹理层叠在不同基础上，就像村民有不同的生物群系基础和不同的职业层。您可以查看我们关于渲染控制器的页面[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/render-controllers)了解有关如何层叠纹理的更多详情。

## 几何体

几何体是一个文件，定义了一组_骨骼_，组成我们的实体形状。这个文件可以使用像Blockbench这样的应用程序自动制作。您可以查看我们的[指南](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/guide/blockbench)了解如何制作自己的模型的更多细节。

<CodeHeader>RP/entity/creeper.entity.json#minecraft:client_entity/description</CodeHeader>

```json
"geometry": {
    "default": "geometry.creeper",
    "charged": "geometry.creeper.charged"
}
```

这里我们的简称引用了我们的几何体标识符。

<CodeHeader>RP/entity/creeper.entity.json#minecraft:client_entity/description</CodeHeader>

```json
{
	"format_version" : "1.12.0",
	"minecraft:geometry" : [
		{
			"description" : {
				"identifier" : "geometry.creeper",
                ...
            }
        }
}
```

同样，我们可以有多个几何体，比如苦力怕，它为其充能和非充能形态有两个模型。

:::tip
通常，如果您的视觉效果出现问题，可能是由于实体简称拼写错误。请务必仔细检查。
:::

## 渲染控制器

渲染控制器简单地说控制了您的实体如何被渲染。这个文件采用您的材质、纹理和几何体的简称，使用它们定义何时渲染每个元素。

<CodeHeader>RP/render_controllers/example.rc.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "render_controllers": {
        "controller.render.example": {
            "geometry": "geometry.default",
            "materials": [
                {
                    "*": "material.default"
                }
            ],
            "textures": ["texture.default"]
        }
    }
}
```

这里，这个渲染控制器表示始终使用`default`材质、纹理和几何体。您可以获得更复杂的渲染控制器，允许切换纹理或使几何体的某些部分不可见。这在我们的渲染控制器页面[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/entities/render-controllers)有解释。

要告诉我们的实体使用哪个渲染控制器，我们只需将渲染控制器标识符添加到我们的文件中。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"render_controllers": [
    "controller.render.example"
]
```

最基本的实体文件需要这4个键才能正确渲染实体。

## 动画

动画描述我们的实体如何移动。这可以包括行走动画、攻击或实体如何看向玩家的方式。它们由代码定义，描述几何体在特定时间如何移动或使用数学方程。您需要几何体才能使动画工作。

<CodeHeader>RP/animations/example.a.json</CodeHeader>

```json
{
	"format_version" : "1.8.0",
	"animations" : {
		"animation.example.walk" : {...},
        "animation.example.attack" : {...}
	}
}
```

每个动画由其标识符定义。我们的动画键是动画的另一个简称定义。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"animations": {
    "walk": "animation.example.walk",
    "attack": "animation.example.attack",
    "attack_controller": "controller.animation.example"
}
```

在这里您会注意到我们也引用了一个动画控制器。这控制何时播放特定动画。这允许我们定义动画之间的不同转换。

此控制器使用在动画键中定义的动画简称。我们还额外定义了我们的控制器，以便能够在后面引用何时运行此控制器。我建议查看我们的指南，了解动画控制器结构的一些更多信息。

:::tip 重要
请记住，这个键只是为我们的动画_定义_一个简称，而不会运行我们的动画。如果您只有这个键，您的动画在游戏中将不会运行。
:::

## 脚本

脚本键定义实体在特定时间运行的某些脚本。这允许我们运行动画、设置变量，甚至控制实体的大小。此外，我们可以使用_Molang_来定义这些。要深入了解Molang，您可以查看我们的页面[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/concepts/molang)。总的来说，Molang本质上是一种使用变量的数学方程。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"scripts": {
    "initialize": [...],
    "pre_animation": [...],
    "animate": [...],

    "scale": "1",
}
```

Molang中一些有用的东西是：

-   查询。这些是根据条件变化的值。例如，`query.time_of_day`或`q.time_of_day`查询返回一天中的时间。
-   变量。这些是可以编辑的值，用于保存稍后使用的值。例如，您可以将变量`variable.my_number`或`v.my_number`设置为2。
-   计算。在Molang中，我们可以使用运算符返回值。例如，我们可以返回值1，如果`q.time_of_day`大于`v.my_number`，通过写入`q.time_of_day > v.my_number`。

### 初始化

此脚本在实体首次初始化时运行，即当它生成时和每次加载时。这意味着每次登录世界时，它都会运行此脚本中的任何内容。这对于设置自定义变量的默认值很有用。

### 预动画

此脚本在动画播放前每帧运行。这对于计算动画中将使用的变量很有用，这些变量需要在动画运行前计算。

### 动画

此脚本在`pre_animation`之后每帧运行。这是您运行动画和动画控制器的地方。每帧此键中的每个动画或动画控制器都将运行。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"scripts": {
    "animate": [
        "attack_controller",
        {
            "walk": "q.modified_move_speed"
        }
    ],
}
```

在这里，`attack_controller`是我们的动画控制器的简称。每帧它将运行我们的动画控制器和控制器中发生的任何转换。此外，在动画中，我们可以使用Molang定义动画播放的速度。请记住，Molang会计算为一个值，这里我们有一个查询，`q.modified_move_speed`。此查询返回实体移动的速度，正常步行速度返回`1`。因此，使用此设置，我们的`walk`动画根据实体移动的速度播放。

如果我们改为使用`"walk": 2`，那么行走动画将始终以两倍速度播放。我们也可以在动画控制器内定义这个，它可以提供更多关于何时播放这些动画的控制。有关使用Molang和动画的更多信息，请查看我们的页面[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/visuals/math-based-animations)。

如果您的动画没有播放，那么检查它们是否使用正确的简称在`animate`中定义是个好主意。

### 缩放

缩放控制模型的大小。这与您可以在实体行为文件中定义的组件`minecraft:scale`略有不同。组件`minecraft:scale`缩放实体的模型和碰撞箱，而实体资源文件中的`scale`仅缩放模型。根据您的情况，任何一个都可能更有用。

`scale`的另一个区别是您可以使用Molang，还有3个附加组件：`scaleX`、`scaleY`和`scaleZ`。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"scripts": {
    "scale": "q.variant",
    "scaleX": 2,
    "scaleY": 0.5
}
```

在这里，我们的实体将根据`minecraft:variant`组件确定的变体进行缩放。如果您想保持碰撞箱相同但让实体看起来更大，这可能很有益。
此外，模型将在`y`方向上被压缩2倍，在`x`方向上被拉伸2倍。

这种情况的一个用例是拥有一个具有随机大小的大气实体。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"scripts": {
    "initialize": [
        "v.scale = math.random_integer(1, 5);"
    ],
    "scale": "v.scale"
}
```

此代码将使每次加载实体时，它都具有我们选择的值之间的随机大小。这里`math.random_integer`是一个Molang函数，在提供的数字之间选择一个随机整数。

## 音效

音效是可以在游戏中特定时间播放的声音文件。此键再次定义实体在动画中可以使用的音效简称。当实体与世界交互时，这可用于创建更动态的声音。例如，您可能希望实体在攻击时播放3个声音。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"sound_effects": {
    "attack_1": "mob.entity.attack_1",
    "attack_2": "mob.entity.attack_2",
    "attack_3": "mob.entity.attack_3"
}
```

在这里，简称引用了在`sound_definitions.json`文件中定义的声音简称。当在其他地方使用声音时，例如在命令中，您会使用`mob.entity.attack_1`，但在实体中定义的动画中，您会使用`attack_1`。

## 粒子效果

粒子效果是包含许多小粒子运动信息的文件，以创建烟雾或火焰等效果。与音效类似，此键定义动画中使用的粒子效果的简称。例如，幻翼在其飞行时翅膀上有粒子效果。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"particle_effects": {
    "smoke": "wiki:smoke_particle"
}
```

在这里，简称引用了粒子的标识符。有关粒子和制作自己的粒子的更多信息，请查看我们的粒子页面[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/particles/particles)。有关在动画中使用音效和粒子效果的更多信息，您也可以查看我们的页面[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/visuals/animation-effects)。

## 生成蛋

生成蛋键允许我们为我们的实体生成一个生成蛋。使用时将生成我们的实体，并自动添加到创造模式库存中。对于您的生成蛋外观有两种选择：彩色和纹理。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"spawn_egg": {
    "base_color": "#db7500",
    "overlay_color": "#242222"
}
```

使用`base_color`和`overlay_color`将创建一个类似于原版的纹理蛋，使用提供的颜色。

<CodeHeader>RP/entity/example.json#minecraft:client_entity/description</CodeHeader>

```json
"spawn_egg": {
    "texture": "wiki:example",
}
```

`texture`键采用在`item_texture.json`中定义的图像的纹理简称，用作生成蛋的图像。如果省略此键，则不会生成生成蛋。

## 附加设置

`enable_attachments`确定实体是否可以使用附件。例如，将此设置为false意味着实体不能持有剑或弓等武器。

`hide_armor`允许实体穿装甲，但它不会被渲染。
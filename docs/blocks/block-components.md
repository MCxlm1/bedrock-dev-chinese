---
title: 方块组件
description: 方块组件用于更改方块在世界中的外观和功能。
category: General
nav_order: 2
license: true
mentions:
    - SirLich
    - solvedDev
    - yanasakana
    - SmokeyStack
    - MedicalJewel105
    - aexer0e
    - Chikorita-Lover
    - Lufurrius
    - TheDoctor15
    - XxPoggyisLitxX
    - TheItsNameless
    - ThomasOrs
    - Kaioga5
    - QuazChick
---

:::tip 格式版本 1.21.130
在创建自定义方块时使用最新格式版本可以访问新功能和改进。
本维基旨在分享有关自定义方块的最新信息，当前针对格式版本1.21.130。
:::
:::danger 覆盖组件
每个组件一次只能激活**一个**实例。
重复的组件将被最新的[`permutations`](/blocks/block-permutations)数组条目覆盖。
:::

方块组件用于更改方块在世界中的外观和功能。

## 应用组件

方块组件可以直接应用于[permution](/blocks/block-permutations)的`minecraft:block`的`components`子项中，或按方块应用。

<CodeHeader>BP/blocks/lamp.json</CodeHeader>

```json
{
    "format_version": "1.21.130",
    "minecraft:block": {
        "description": {
            "identifier": "wiki:lamp",
            "menu_category": {
                "category": "items"
            }
        },
        "components": {
            "minecraft:light_dampening": 0,
            "minecraft:light_emission": 15,
            "minecraft:map_color": [210, 200, 190],
            "minecraft:geometry": {
                "identifier": "geometry.lamp",
                "culling": "wiki:culling.lamp"
            },
            "minecraft:material_instances": {
                "*": {
                    "texture": "wiki:lamp"
                },
                "shade": {
                    "texture": "wiki:lamp_shade"
                }
            }
        }
    }
}
```

## 原版组件列表

### 碰撞箱

定义实体和粒子与之发生碰撞的区域。

_需要格式版本[1.19.50](/blocks/block-format-history#_1-19-50)或更高版本。_

#### 布尔定义 {#collision-box-boolean}

-   当为`true`{lang=json}（默认）时，使用16×16×16的碰撞箱。
-   当为`false`{lang=json}时，禁用方块与实体的碰撞。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:collision_box": true
```

#### 对象定义 {#collision-box-object}

-   `origin` — 向量`[X, Y, Z]`{lang=js}
    -   碰撞箱的底部西北角。
    -   从方块单位底部中心测量的像素。
    -   必须在范围`[-8, 0, -8]`{lang=json}到`[8, 16, 8]`{lang=json}之间。
-   `size` — 向量`[X, Y, Z]`{lang=js}
    -   碰撞箱各边的大小。
    -   从碰撞箱的`origin`测量的像素。
    -   `origin`和`size`的总和必须在范围`[-8, 0, -8]`{lang=json}到`[8, 16, 8]`{lang=json}之间。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:collision_box": {
    "origin": [-8, 0, -8],
    "size": [16, 16, 16]
}
```

### 工作台

将你的方块变成工作台，在交互时打开功能性的合成界面。

_需要格式版本[1.19.50](/blocks/block-format-history#_1-19-50)或更高版本。_

#### 对象定义 {#crafting-table-object}

-   `crafting_tags` — 数组
    -   根据标签字符串数组确定哪些配方可以由该工作台使用。
    -   具有任意定义标签的配方都可以在工作台中使用。
    -   自定义标签应包含命名空间（例如`"wiki:workbench"`{lang=json}），最多可达64个字符。
    -   最多可包含64个标签。
-   `table_name` — 字符串（可选）
    -   指定语言文件键，映射到工作台界面中合成网格上方显示的文本。
    -   如果给定字符串的翻译无法解析，则会显示原始字符串。
    -   省略时，默认显示方块的[显示名称](#display-name)。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:crafting_table": {
    "table_name": "Wiki Workbench",
    "crafting_tags": [
        "crafting_table",
        "wiki:workbench"
    ]
}
```

### 可被爆炸破坏

确定方块是否可以被爆炸摧毁。

_需要格式版本[1.19.20](/blocks/block-format-history#_1-19-20)或更高版本。_

#### 布尔定义 {#destructible-by-explosion-boolean}

-   当为`true`{lang=json}（默认）时，方块的爆炸抗性为0。
-   当为`false`{lang=json}时，方块不能被爆炸摧毁。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:destructible_by_explosion": false
```

#### 对象定义 {#destructible-by-explosion-object}

-   `explosion_resistance` — 浮点数
    -   定义方块抵抗爆炸摧毁的能力。
    -   更高的抗性降低了方块在爆炸附近被摧毁的机会。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:destructible_by_explosion": {
    "explosion_resistance": 20
}
```

### 可被挖掘破坏

确定玩家挖掘方块所需的时间。

_需要格式版本[1.19.20](/blocks/block-format-history#_1-19-20)或更高版本。_

#### 布尔定义 {#destructible-by-mining-boolean}

-   当为`true`{lang=json}（默认）时，挖掘时方块将立即被摧毁。
-   当为`false`{lang=json}时，无法通过挖掘来摧毁方块。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:destructible_by_mining": false
```

#### 对象定义 {#destructible-by-mining-object}

-   `seconds_to_destroy` — 浮点数
    -   确定方块的"硬度"值。
    -   更大的数值导致更长的挖掘时间。

:::danger 秒数至摧毁
`seconds_to_destroy`参数的名称具有误导性。
虽然名称暗示此参数决定摧毁方块需要多少秒，但实际上该参数设置的是方块的"硬度"值。

默认情况下，实际摧毁自定义方块所需的秒数是此处设定值的1.5倍。
:::

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:destructible_by_mining": {
    "seconds_to_destroy": 20
}
```

### 破坏粒子

确定敲击、摧毁、踩踏和掉落到方块上时产生的粒子外观。

_需要格式版本[1.21.90](/blocks/block-format-history#_1-21-90)或更高版本。_

#### 对象定义 {#destruction-particles-object}

-   `particle_count` — 整数（可选）
    -   确定摧毁方块时创建多少粒子（`0-255`{lang=json}）。
    -   默认情况下，创建`100`{lang=json}个粒子。
-   `texture` — 字符串（可选）
    -   指定从`RP/textures/terrain_texture.json`使用的[纹理图集](/concepts/texture-atlases)简称。
    -   默认情况下，粒子将使用`down`材质实例的纹理（如果未指定则使用`*`）。
-   `tint_method` — 字符串（可选）
    -   指定根据方块放置的生物群系来调整`texture`色调的[着色方法](/blocks/block-tinting#tint-methods)。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:destruction_particles": {
    "texture": "wiki:particle_texture",
    "tint_method": "grass"
}
```

### 显示名称

语言文件键，确定当您悬停在物品栏和快捷栏中的方块上时将显示的文本。

-   如果给定字符串没有翻译，则显示原始字符串。
-   在某些场景下，Minecraft可能会恢复使用`tile.<identifier>.name`{lang=xml}，例如在"可放置于"提示中。

_需要格式版本[1.19.60](/blocks/block-format-history#_1-19-60)或更高版本。_

#### 字符串定义 {#display-name-string}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:display_name": "tile.wiki:custom_block.name"
```

<CodeHeader>RP/texts/en_US.lang</CodeHeader>

```lang
tile.wiki:custom_block.name=自定义方块
```

### 嵌入视觉效果

:::tip 仅限根级
此组件只能在方块的根`components`对象中定义，因此不能按排列指定。
:::

确定方块在花盆中显示的方式。
要使方块能够放置在花盆中，还必须应用[花盆可放置](#flower-pottable)组件。

#### 对象定义 {#embedded-visual-object}

-   `geometry` — 字符串/对象
    -   显示的[几何形状](#geometry)组件。
-   `material_instances` — 对象
    -   显示的[材质实例](#material-instances)组件。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:embedded_visual": {
    "geometry": "minecraft:geometry.full_block",
    "material_instances": {
        "*": {
            "texture": "wiki:block_texture"
        }
    }
}
```

### 实体跌落

当实体跌落到方块上时，触发[实体跌落](/blocks/block-events#entity-fall-on)事件。

_需要格式版本[1.21.10](/blocks/block-format-history#_1-21-10)或更高版本。_

#### 对象定义 {#entity-fall-on-object}

-   `min_fall_distance` — 浮点数
    -   实体必须跌落以触发事件的最小距离（以方块为单位）。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:entity_fall_on": {
    "min_fall_distance": 5
}
```

### 易燃

确定方块的易燃程度。

_需要格式版本[1.19.10](/blocks/block-format-history#_1-19-10)或更高版本。_

#### 布尔定义 {#flammable-boolean}

当为`true`时，方块可以从邻近方块自然起火。
当为`false`（默认）时，方块不会从邻近方块自然起火，但仍可以直接点燃。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:flammable": true
```

#### 对象定义 {#flammable-object}

-   `catch_chance_modifier` — 整数
    -   影响当此方块在火旁边时着火的机会。
    -   当为`0`{lang=json}时，方块上的火最终会熄灭。
    -   当大于`0`{lang=json}时，火将继续燃烧直到方块被摧毁（或者如果`destroy_chance_modifier`为`0`{lang=json}，则会永远燃烧）。
    -   默认设置为`5`{lang=json}，这与原版木板相同。
-   `destroy_chance_modifier` — 整数
    -   影响方块着火时被火焰摧毁的机会。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:flammable": {
    "catch_chance_modifier": 5,
    "destroy_chance_modifier": 20
}
```

### 花盆可放置

:::tip 仅限根级
此组件只能在方块的根`components`对象中定义，因此不能按排列指定。
:::

允许将方块放置在花盆中。
[嵌入视觉效果](#embedded-visual)组件可用于更改方块在花盆中的外观。

#### 对象定义 {#flower-pottable-object}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:flower_pottable": {}
```

### 摩擦力

确定方块对于在上面移动的实体有多滑（`0.0-0.9`{lang=js}）。
较低的值意味着方块更滑。

:::warning 移动速度
此组件有些问题。虽然它在船上工作正常，但更高的摩擦值会导致其他实体加速而不是减速。
:::

_需要格式版本[1.19.20](/blocks/block-format-history#_1-19-20)或更高版本。_

#### 浮点数定义 {#friction-float}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:friction": 0.4
```

### 几何形状

确定此方块的几何形状和应应用的任何剔除规则。
方块的几何形状也可以设置为任何[原版方块模型](/blocks/vanilla-block-models)。

**自定义方块模型限制：**

1.  您的方块大小限制为30×30×30像素。

2.  在每个轴上，方块至少要有1像素位于16×16×16方块单元内。

3.  位置的绝对边界为原点周围每个方向30像素。
    您的方块可以在这些边界内的任何位置放置，只要遵守规则#2即可。

_需要格式版本[1.21.90](/blocks/block-format-history#_1-21-90)或更高版本。_

#### 字符串定义 {#geometry-string}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:geometry": "geometry.example_block"
```

#### 对象定义 {#geometry-object}

-   `identifier` — 字符串
    -   几何形状的标识符。
-   `bone_visibility` — 对象（可选）
    -   键值映射，确定模型中的哪些骨骼可见。
    -   键表示骨骼名称（这些不支持通配符 — 必须指定确切的骨骼名称）
    -   值可以是布尔值或Molang表达式字符串，确定骨骼是否可见
        -   所有骨骼默认都是可见的，这意味着值为`true`{lang=json}是多余的。
        -   Molang表达式必须遵循[排列条件](/blocks/block-permutations#permutation-conditions)限制。
-   `culling` — 字符串（可选）
    -   确定应应用于几何形状的剔除规则标识符。
    -   剔除标识符应采用`<namespace>:culling.<name>`{lang=xml}形式。
-   `culling_layer` — 字符串（可选）
    -   确定剔除规则中[相同剔除层](/blocks/block-culling#same-culling-layer)条件检查的剔除层标识符。
    -   剔除层标识符应采用`<namespace>:culling_layer.<name>`{lang=xml}形式。
    -   使用`minecraft`命名空间时，只允许以下值：
        -   `"minecraft:culling_layer.undefined"`{lang=json}是没有定义的方块的剔除层。
        -   `"minecraft:culling_layer.leaves"`{lang=json}是所有原版树叶的剔除层，应在创建自定义树叶时使用。
-   `uv_lock` — 数组/布尔值（可选）
    -   确定应用来自[变换](#transformation)组件的旋转时，UV是否应锁定到其原始旋转。
    -   当为`false`{lang=json}（默认）时，模型中的所有UV都会跟随方块的旋转。
    -   当为`true`{lang=json}时，模型中的所有UV都将锁定旋转。
    -   当使用数组时，列出的骨骼中的所有UV都将锁定旋转。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:geometry": {
    "identifier": "geometry.example_block",
    "culling": "wiki:culling.example_block",
    "bone_visibility": {
        "wiki_bone": false,
        "conditional_bone": "q.block_state('wiki:example_state') == 3",
        "another_bone": true
    },
    "uv_lock": ["locked_bone"]
}
```

### 物品视觉

确定此方块作为物品时的显示方式。

_需要格式版本[1.21.60](/blocks/block-format-history#_1-21-60)或更高版本。_

#### 对象定义 {#item-visual-object}

-   `geometry` — 对象/字符串
    -   显示的[几何形状](#geometry)组件。
-   `material_instances` — 对象
    -   显示的[材质实例](#material-instances)组件。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:item_visual": {
    "geometry": "minecraft:geometry.full_block",
    "material_instances": {
        "*": {
            "texture": "wiki:block_texture"
        }
    }
}
```

### 光照衰减

确定光线穿过方块时被衰减的最大光照等级数（`0-15`{lang=js}）。

_需要格式版本[1.19.10](/blocks/block-format-history#_1-19-10)或更高版本。_

#### 整数定义 {#light-dampening-integer}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:light_dampening": 15
```

### 光源发射

确定方块发出的整数光照等级（`0-15`{lang=js}）。

_需要格式版本[1.19.20](/blocks/block-format-history#_1-19-20)或更高版本。_

#### 整数定义 {#light-emission-integer}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:light_emission": 10
```

### 液体检测

确定此方块与不同类型液体的行为方式。

_需要格式版本[1.21.60](/blocks/block-format-history#_1-21-60)或更高版本。_

#### 对象定义 {#liquid-detection-object}

-   `detection_rules` — 数组
    -   `liquid_type` — 字符串
        -   此规则适用于哪种类型的液体。目前仅支持`"water"`{lang=json}。
    -   `can_contain_liquid` — 布尔值
        -   液体类型是否可以与该方块占据同一空间，例如水淹。
    -   `on_liquid_touches` — 字符串（可选）
        -   基于以下值之一确定当液体类型流入方块时会发生什么：
            -   `"blocking"`{lang=json}（默认）阻止液体流动。
            -   `"broken"`{lang=json}导致方块被破坏。
            -   `"no_reaction"`{lang=json}允许液体流过方块。
            -   `"popped"`{lang=json}导致方块弹出，掉落其战利品。
    -   `stops_liquid_flowing_from_direction` — 数组（可选）
        -   确定液体不能从此方块流出的方向数组。
        -   如果`on_liquid_touches`设置为`"no_reaction"`{lang=json}，此数组还确定液体不能从此方块流入的方向。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:liquid_detection": {
    "detection_rules": [
        {
            "liquid_type": "water",
            "can_contain_liquid": true, // 可水淹
            "on_liquid_touches": "no_reaction" // 水像空气一样流过方块
        }
    ]
}
```

### 战利品

摧毁方块时掉落的战利品表路径（使用带有"精准采集"附魔的工具时忽略）。
如果省略，则方块作为物品掉落。

#### 字符串定义 {#loot-string}

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:loot": "loot_tables/blocks/custom_block.json"
```

### 地图颜色

定义在地图上查看时方块的外观。
如果省略，地图会忽略方块。

#### 字符串定义 {#map-color-string}

十六进制字符串（以`#`开头）定义方块在地图上的`#RRGGBB`颜色。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:map_color": "#FFFFFF"
```

#### 数组定义 {#map-color-array}

3个整数的数组（`0-255`{lang=js}）定义方块在地图上的`[R, G, B]`{lang=js}颜色。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:map_color": [255, 255, 255]
```

#### 对象定义 {#map-color-object}

-   `color` — 字符串
    -   十六进制字符串（以`#`开头）定义方块在地图上的`#RRGGBB`颜色。
-   `tint_method` — 字符串（可选）
    -   指定根据方块放置的生物群系来调整`color`色调的[着色方法](/blocks/block-tinting#tint-methods)。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:map_color": {
    "color": "#FFFFFF",
    "tint_method": "grass"
}
```

### 材质实例

方块渲染的配置，包括纹理和光照。

_需要格式版本[1.19.40](/blocks/block-format-history#_1-19-40)或更高版本。_

**已知问题：**

-   周围方块的环境光遮蔽会在自定义方块上造成不自然的光照。
    当方块模型与周围方块相交时，导致面变暗，这一点尤其明显。
-   在用户界面中，面暗化在方块模型中的`item_display_transforms`旋转之前应用。

#### 对象定义 {#material-instances-object}

每个键是材质实例的名称，每个值是一个材质实例对象。
`*`实例是所有立方体面的默认实例，但如果所有面都分别定义了材质实例，则不是必需的。

-   `<name>`{lang=xml}: 对象
    -   `texture` — 字符串
        -   指定从`RP/textures/terrain_texture.json`使用的[纹理图集](/concepts/texture-atlases)简称。
    -   `render_method` — 字符串（可选）
        -   渲染使用材质实例的面时使用的[渲染方法](#render-methods)。
        -   **所有材质实例必须使用相同的渲染方法。**
        -   默认情况下，使用`opaque`渲染方法。
    -   `tint_method` — 字符串（可选）
        -   指定根据方块放置的生物群系来调整`texture`色调的[着色方法](/blocks/block-tinting#tint-methods)。
    -   `ambient_occlusion` — 布尔值/浮点数（`0.0-10.0`{lang=json}）（可选）
        -   确定是否对使用材质实例的面应用"平滑光照"。
        -   浮点数值可用于确定环境光遮蔽强度。
        -   默认情况下，对于发光方块为`false`{lang=json}（或`0.0`{lang=json}），对于不发光方块为`true`{lang=json}（或`1.0`{lang=json}）。
    -   `face_dimming` — 布尔值（可选）
        -   确定使用材质实例的面是否根据其方向变暗。
        -   默认情况下，对于发光方块为`false`{lang=json}，对于不发光方块为`true`{lang=json}。
    -   `isotropic` — 布尔值（可选）
        -   确定使用材质实例的面的UV是否基于方块在世界中的位置随机旋转。
        -   默认情况下，纹理不会随机旋转。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:material_instances": {
    // 实例名称"up"、"down"、"north"、"east"、"south"和"west"是内置的。
    "*": {
        "texture": "wiki:texture_name", // 在"RP/textures/terrain_texture.json"中定义的简称。
        "render_method": "blend", // 支持纹理透明度
        "isotropic": true // 随机旋转纹理
    }
}
```

#### 渲染方法

渲染方法本质上控制方块在世界中的外观，就像实体材质一样。以下是每种类型的关键属性：

| 渲染方法 | _透明度_ | _半透明_ | _背面剔除_ | _远距离剔除_ | 原版示例 |
| ------------------------- | :------------: | :------------: | :----------------: | :---------------: | ------------------------------ |
| `alpha_test` | ✔️ | ❌ | ❌ | ✔️ | 梯子、刷怪笼、藤蔓 |
| `alpha_test_single_sided` | ✔️ | ❌ | ✔️ | ✔️ | 门、树苗、活板门 |
| `blend` | ✔️ | ✔️ | ✔️ | ❌ | 玻璃、信标、蜂蜜块 |
| `double_sided` | ❌ | ❌ | ❌ | ❌ | 细雪 |
| `opaque` _(默认)_ | ❌ | ❌ | ✔️ | ❌ | 泥土、石头、混凝土 |

-   **_透明度_** - 完全透视的区域。
-   **_半透明_** - 半透明的区域。
-   **_背面剔除_** - 从后面看时面变得不可见。
-   **_远距离剔除_** - 达到一半渲染距离后，面变得不可见。

##### 基于距离的渲染方法

| 渲染方法 | _近距离外观_ | _远距离外观_ | 原版示例 |
| ----------------------------------- | ------------------------- | ---------------- | ---------------- |
| `alpha_test_to_opaque` | `alpha_test` | `opaque` | 树叶 |
| `alpha_test_single_sided_to_opaque` | `alpha_test_single_sided` | `opaque` | 海带、甘蔗 |
| `blend_to_opaque` | `blend` | `opaque` | N/A |

-   **_近距离外观_** - 达到一半渲染距离之前使用的渲染方法。
-   **_远距离外观_** - 达到一半渲染距离之后使用的渲染方法。

#### 自定义材质实例名称

:::tip
可以通过右键单击立方体并打开其`材质实例`在Blockbench中定义自定义材质实例名称。

在此处[了解](/blocks/block-models#changing-material-instances)更多关于应用不同材质实例的信息！
:::

可以在材质实例中定义自定义实例名称，并且可以引用内置实例名称或在方块模型中引用。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:material_instances": {
    "*": {
        "texture": "wiki:texture_name",
        "render_method": "blend" // 必须与其他实例匹配
    },
    // 自定义实例名称
    "end": {
        "texture": "wiki:texture_name_end",
        "render_method": "blend" // 必须与其他实例匹配
    },
    "up": "end",
    "down": "end",
    // 模型中定义的实例名称：
    "flower": {
        "texture": "wiki:texture_name_flower",
        "render_method": "blend" // 必须与其他实例匹配
    }
}
```

### 可移动

确定方块如何被活塞移动。

_需要格式版本[1.21.100](/blocks/block-format-history#_1-21-100)或更高版本。_

#### 对象定义 {#movable-object}

-   `movement_type` — 字符串
    -   可以是以下值之一：`"immovable"`{lang=json}、`"popped"`{lang=json}、`"push"`{lang=json}或`"push_pull"`{lang=json}（默认）。
-   `sticky` — 字符串（可选）
    -   可以设置为`"same"`{lang=json}以复制粘液块/蜂蜜块功能。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:movable": {
    "movement_type": "popped" // 方块被活塞推动时破碎。
}
```

### 放置过滤器

确定方块必须处于的放置条件才能生存。
如果不满足任何指定条件，方块将不会被放置；或者如果方块已经放置，它将作为物品弹出。

_需要格式版本[1.19.60](/blocks/block-format-history#_1-19-60)或更高版本。_

#### 对象定义 {#placement-filter-object}

-   `conditions` — 数组
    -   指定方块可以放置和生存而不会作为物品弹出的条件对象列表。
    -   最多可包含64个条件。
    -   `allowed_faces` — 数组（可选）
        -   方块可以放置的方块面
        -   可以包括`"all"`{lang=json}、`"side"`{lang=json}、`"down"`{lang=json}、`"up"`{lang=json}、`"north"`{lang=json}、`"south"`{lang=json}、`"west"`{lang=json}和`"east"`{lang=json}。
    -   `block_filter` — 数组（可选）
        -   [方块描述符](/documentation/shared-constructs#block-descriptors)数组，方块可以在`allowed_faces`方向放置在这些方块旁边。
        -   请参阅[此](/blocks/block-tags)页面获取原版标签和相关方块列表。
        -   最多可包含64个方块描述符。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:placement_filter": {
    "conditions": [
        {
            "allowed_faces": ["up"], // 必须放置在以下任意方块顶部
            "block_filter": [
                "minecraft:dirt",
                {
                    "name": "minecraft:sand",
                    "states": {
                        "sand_type": "red"
                    }
                },
                {
                    "tags": "!q.any_tag('minecraft:crop', 'wiki:tag')"
                }
            ]
        }
    ]
}
```

### 降水交互

确定方块如何与降水（雨和雪）交互。

_需要格式版本[1.21.120](/blocks/block-format-history#_1-21-120)或更高版本。_

#### 对象定义 {#precipitation-interactions-object}

-   `precipitation_behavior`: 字符串
    -   `"obstruct_rain_accumulate_snow"`{lang=json}（默认）防止雨水透过方块，而是使其在方块顶部飞溅，并在下雪时在方块上方堆积雪花层。
    -   `"obstruct_rain"`{lang=json}防止雨水透过方块，而是使其在方块顶部飞溅。
    -   `"none"`{lang=json}允许雨水和雪透过方块。

<CodeHeader>minecraft:block > components</CodeHeader>

```json
"minecraft:precipitation_interactions": {
    "precipitation_behavior": "none"
}
```

### 随机偏移

根据方块在世界中的位置应用随机偏移，影响方块的碰撞箱、选择框和几何形状。

**偏移模型不得超过[方块几何限制](#geometry)。**

_需要格式版本[1.21.100](/blocks/block-format-history#_1-21-100)或更高版本。_

#### 对象定义 {#random-offset-object}

-   `<axis>`{lang=xml}: 对象（可选）
    -   以下偏移参数分别针对`x`、`y`和`z`轴定义。
    -   `range` — [范围](/documentation/shared-constructs#range-objects)
        -   确定每个轴上偏移可以有多大。
    -   `steps` — 整数
        -   确定在`range`范围内可以选择多少个等距的随机值。
```

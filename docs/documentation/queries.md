---
title: Molang 查询
description: 一些 MoLang 查询的解释。
outline_depth: 2
mentions:
    - SirLich
    - solvedDev
    - stirante
    - SmokeyStack
    - Dreamedc2015
    - Ultr4Anubis
    - MedicalJewel105
    - TreaBeane
    - r4isen1920
    - ChillRx
    - Lufurrius
    - TheItsNameless
    - ThomasOrs
---

基岩版的 MoLang 文档臭名昭著地糟糕。此页面将尝试通过提供各个查询的额外细节来补救这个问题，_在可能的情况下_。此页面旨在被搜索，而不是全文阅读。使用侧边栏，或使用 `ctrl-f` 进行导航。

:::tip
这不是一份详尽的列表！它只包含我们为额外信息编写的查询。完整查询列表可在[这里](https://bedrock.dev/docs/stable/Molang#List%20of%20Entity%20Queries)找到！
:::

## query.armor_texture_slot

格式如下：`query.armor_texture_slot(x) = y`。

其中 `x` 和 `y` 都是整数参数，来自下表：

### X

| 参数 | 槽位 |
| -------- | ---------- |
| 0 | 头盔 |
| 1 | 胸甲 |
| 2 | 护腿 |
| 3 | 靴子 |

### Y

| 参数 | 类型 |
| -------- | --------------------- |
| -1 | 无 |
| 0 | 皮革护甲部件 |
| 1 | 链锁护甲部件 |
| 2 | 铁质护甲部件 |
| 3 | 钻石护甲部件 |
| 4 | 金质护甲部件 |
| 5 | 鞘翅 |
| 6 | 海龟头盔 |
| 7 | 下界合金护甲部件 |

### 马的Y值

| 参数 | 类型 |
| -------- | ------------------- |
| 1 | 皮革护甲部件 |
| 2 | 铁质护甲部件 |
| 3 | 金质护甲部件 |
| 4 | 钻石护甲部件 |

### 示例

`query.armor_texture_slot(3) == 1`：查询铁质靴子。

## query.armor_material_slot

格式如下：`query.armor_material_slot(x) = y`。

其中 `x` 和 `y` 都是整数参数，来自下表：

### X

| 参数 | 槽位 |
| -------- | ---------- |
| 0 | 头盔 |
| 1 | 胸甲 |
| 2 | 护腿 |
| 3 | 靴子 |

### Y

未知，可能为：

| 参数 | 槽位 |
| -------- | -------------------------- |
| 0 | 默认护甲材质 |
| 1 | 附魔护甲材质 |
| 2 | 皮革护甲材质 |
| 3 | 皮革附魔材质 |

## query.armor_color_slot

_注意：从版本 `1.16.100.51` 开始，此查询会导致 Minecraft 崩溃。在较新版本中可能会修复。_

格式如下：`color = query.armor_color_slot(slot, channel)`。

其中 `slot` 和 `channel` 都是整数参数，来自下表：

### 槽位

| 参数 | 槽位 |
| -------- | ---------- |
| 0 | 头盔 |
| 1 | 胸甲 |
| 2 | 护腿 |
| 3 | 靴子 |

### 通道

| 参数 | 槽位 |
| -------- | ------------- |
| 0 | 红色通道 |
| 1 | 绿色通道 |
| 2 | 蓝色通道 |
| 3 | 透明通道 |

### 颜色

查询返回指定通道的颜色值。

## query.get_equipped_item_name

:::warning
**已弃用查询：** 如果可能，建议使用新查询（`query.is_item_name_any`），因为它是此查询的更新版本。但是，出于向后兼容性，此查询在未来仍将继续工作。
:::

格式如下：`query.get_equipped_item_name('main_hand') = 'item_name'`

接受一个可选的手部槽位作为参数（0 或 'main_hand' 为主手，1 或 'off_hand' 为副手），以及第二个参数（0=默认）用于获取装备的物品或任何非零数字以获取当前渲染的物品，如果有的话，则返回请求槽位中的物品名称（如果未提供参数则默认为主手），否则返回 ''。

其中 `item_name` 是你要测试的物品。无需命名空间，请注意引号。

示例："query.get_equipped_item_name == 'diamond'"

**你能测试库存中的物品吗？是的！使用新查询 `query.is_item_name_any`。**

## query.get_name

:::warning
**已弃用查询：** 如果可能，建议使用新查询（`query.is_name_any`），因为它是此查询的更新版本。但是，出于向后兼容性，此查询在未来仍将继续工作。
:::

格式如下：`query.get_name == 'Name'`

如果实际游戏中显示的名称与名称匹配（使用 OnixClient 查看第三人称视图中的名称），则返回 true。
需要在特殊条件下使用。

<Spoiler title="显示">

<CodeHeader>animation_controllers/ac.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.ac": {
            "initial_state": "default",
            "states": {
                "default": {
                    "transitions": [
                        {
                            "active": "query.is_alive"
                        }
                    ]
                },
                "active": {
                    "transitions": [
                        {
                            "default": "(1.0)"
                        }
                    ],
                    "animations": [
                        {
                            "anim": "query.get_name == '...'" // 你只能在这里使用它！
                        }
                    ]
                }
            }
        }
    }
}
```

</Spoiler>

## query.is_name_any

格式如下：`query.get_name('Name1', 'Name2')`。
接受一个或多个参数。
如果实际游戏中显示的名称与给定名称之一匹配，则返回 true。
需要在特殊条件下使用。

<Spoiler title="显示">

<CodeHeader>animation_controllers/ac.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.ac": {
            "initial_state": "default",
            "states": {
                "default": {
                    "transitions": [
                        {
                            "active": "query.is_alive"
                        }
                    ]
                },
                "active": {
                    "transitions": [
                        {
                            "default": "(1.0)"
                        }
                    ],
                    "animations": [
                        {
                            "anim": "query.is_name_any(...)" // 你只能在这里使用它！
                        }
                    ]
                }
            }
        }
    }
}
```

</Spoiler>

## query.is_item_name_any

格式如下：`query.is_item_name_any('slot.weapon.mainhand', 0, 'namespace:item_name')`

首先接受装备槽名称，然后是槽索引值，最后是带有命名空间的物品名称列表。

可能的装备槽如下：

| 槽名称 | 槽计数 | 描述 |
| ---------------------- | ----------- | ----------------------------------------------------------------------------------- |
| `slot.weapon.mainhand` | 0 | 通常任何手持物品都在这里 |
| `slot.weapon.offhand` | 0 | 副手槽，用于 `Shield`、`Totem of Undying` 或 `Map` 等 |
| `slot.armor.head` | 0 | 头部护甲部件 |
| `slot.armor.chest` | 0 | 胸甲护甲部件 |
| `slot.armor.legs` | 0 | 护腿护甲部件 |
| `slot.armor.feet` | 0 | 靴子护甲部件 |
| `slot.armor` | 0 | 马护甲 |
| `slot.saddle` | 0 | 鞍槽 |
| `slot.hotbar` | 0 到 8 | 玩家快捷栏槽 |
| `slot.inventory` | 0+（不同） | 有库存的实体，如玩家、箱子矿车、驴子等。 |
| `slot.enderchest` | 0 到 26 | 仅限玩家的末影箱库存 |

### 测试玩家库存中的物品

格式如下：`t.val = 0; t.i = 0; loop(27, {t.val = q.is_item_name_any('slot.inventory', t.i, 'namespace:item_name'); t.val ? break; t.i = t.i+1;});`

将 `namespace:item_name` 替换为你想检查的任何物品。这只需循环遍历库存的所有 27 个槽位，并在找到具有指定物品的任何槽位时返回 `1.0`。请注意，快捷栏与主库存槽位不同，因此你需要单独检查。

## query.is_enchanted

格式如下：`is_enchanted = query.is_enchanted`。

根据实体是否附魔返回 1.0 或 0.0。

_目前，只能在材质中使用。_

## query.is_eating

此查询跟踪某些实体何时"进食"。它不用于玩家。要触发，请使用以下组件之一：
- `minecraft:behavior.eat_carried_item`
- `minecraft:behavior.snacking`

## query.is_ghost

格式如下：`is_ghost = query.is_ghost`。

根据实体是否是幽灵返回 1.0 或 0.0。

_目前，仅对守卫者幽灵返回 1.0，并由其渲染器使用。_

## query.is_grazing

格式如下：`is_grazing = query.is_grazing`。

根据实体是否在吃方块返回 1.0 或 0.0。

_目前，仅对羊和使用羊运行时标识符的实体返回 1.0。_

## query.is_jumping

格式如下：`is_jumping = query.is_jumping`。

根据实体是否跳跃返回 1.0 或 0.0。

对于玩家，激活条件为：

- 按下跳跃按钮（包括在水中和攀爬脚手架时）
- 或者自动跳跃被触发
- 或者使用自动跳跃游泳
- 或者为可骑乘实体充能跳跃

## query.modified_move_speed

格式如下：`modified_move_speed = query.modified_move_speed`。

返回实体当前行走速度，受诸如 is_baby 或 on_fire 等状态标志的修改

值示例：

- 玩家正在行走：约 0.86
- 玩家正在冲刺：1.0
- 玩家冲刺并跳跃：0.35
- 玩家着火时行走：1.0
- 玩家着火时冲刺：1.0
- 玩家着火时冲刺并跳跃：0.525

## query.log

内容日志不是调试日志，它们是不同的文件。`query.log` 仅输出到调试日志。

## query.on_fire_time

格式如下：`on_fire_time = query.on_fire_time`。

返回实体开始或停止着火以来的刻度时间，否则返回 0.0

值示例：

- 实体被召唤：值为 0
- 实体被点燃：值为 0 并开始每刻度递增 1
- 实体着火已有 2 秒：值为 40 并继续每刻度递增 1
- 实体停止着火：值重置为 0 并继续每刻度递增 1，尽管不再着火
- 实体第二次被点燃：值重置为 0 并继续每刻度递增 1
- 实体第二次停止着火：值重置为 0 并继续每刻度递增 1，尽管不再着火

基本上这是一个计时器，从实体第一次被点燃后开始计时，并在每次从/到着火状态改变时重置。

## query.scoreboard

格式如下：`query.scoreboard('objective_name') > 0`

如果查询的值在指定范围内，则返回 1.0 或 0.0。或基于分数计数、molang 运算符和数字。

请注意，有时它可能不起作用，原因未知。其中之一是这不能查询带有大写字母的计分板目标名称。在这种情况下，例如，目标 `testfoo` 将工作，但**不**是 `testFoo`。

## query.structural_integrity

格式如下：`structural_integrity = query.structural_integrity`。

用于船只和矿车的销毁。攻击实体时会减少，并随时间恢复。
可能不能用于船和矿车以外的任何东西。

## variable.attack_time

### 解释

此变量设置为好像它是一个查询。换句话说，它可以在任何实体上使用，无论客户端还是服务器，无论你是否正确设置/定义了变量。

### 对于实体

该变量跟踪实体何时挥动攻击。不攻击时，返回 0.0，攻击时，范围从 0.0 到总攻击时间，这可能是 0.3 或类似的值。对于玩家，此值范围从 0.0 到 1.0。该变量返回实体攻击进度的百分比，以小数形式。例如，如果实体的攻击挥动到了一半，那么变量将返回 0.5。它线性递增。

### 对于玩家

对于玩家，该变量将跟踪手臂骨骼何时摆动，这包括：

- 放置方块
- 放置实体
- 交互（当启用了摆动时）
- 近战攻击

## query.is_roaring

当发生 `knockback_roar` 攻击时评估为真。

## query.head_x_rotation

格式如下：`query.head_x_rotation(x)`

其中 `x` 指定实体的头部。对于除凋灵以外的任何实体都不是特别相关。

返回头部俯仰角。抬头返回 `-89.9`，低头到底返回 `89.9`。

## query.head_y_rotation

格式如下：`query.head_y_rotation(x)`

其中 `x` 指定实体的头部。对于除凋灵以外的任何实体都不是特别相关。

返回头部偏航角，范围从 `-179.9` 到 `179.9`。值会环绕，所以如果你在 `-179.9` 处稍微转一点，它会立即跳到 `179.9`。

## query.target_x_rotation 和 query.target_y_rotation

与各自的 `query.head_*_rotation` 相同，但没有选择头部的可选参数。

## query.time_of_day

返回实体所在维度的日期时间（午夜=0.0，日出=0.25，中午=0.5，日落=0.75）。
白天时间通过以下公式计算：

`f(x) = (x*0.25/2400)mod 1`

query.time_of_day - 白天时间表

<Spoiler title="显示">

| `query.time_of_day` | 白天时间 |
| ------------------- | -------- |
| 0.00 | 18000 |
| 0.01 | 18240 |
| 0.02 | 18480 |
| 0.03 | 18720 |
| 0.04 | 18960 |
| 0.05 | 19200 |
| 0.06 | 19440 |
| 0.07 | 19680 |
| 0.08 | 19920 |
| 0.09 | 20162 |
| 0.10 | 20400 |
| 0.11 | 20640 |
| 0.12 | 20880 |
| 0.13 | 21120 |
| 0.14 | 21360 |
| 0.15 | 21602 |
| 0.16 | 21840 |
| 0.17 | 22080 |
| 0.18 | 22322 |
| 0.19 | 22560 |
| 0.20 | 22800 |
| 0.21 | 23040 |
| 0.22 | 23280 |
| 0.23 | 23520 |
| 0.24 | 23760 |
| 0.25 | 0 |
| 0.26 | 240 |
| 0.27 | 480 |
| 0.28 | 720 |
| 0.29 | 960 |
| 0.30 | 1202 |
| 0.31 | 1440 |
| 0.32 | 1680 |
| 0.33 | 1922 |
| 0.34 | 2160 |
| 0.35 | 2400 |
| 0.36 | 2642 |
| 0.37 | 2880 |
| 0.38 | 3120 |
| 0.39 | 3360 |
| 0.40 | 3600 |
| 0.41 | 3840 |
| 0.42 | 4080 |
| 0.43 | 4320 |
| 0.44 | 4560 |
| 0.45 | 4800 |
| 0.46 | 5040 |
| 0.47 | 5280 |
| 0.48 | 5520 |
| 0.49 | 5760 |
| 0.50 | 6000 |
| 0.51 | 6240 |
| 0.52 | 6480 |
| 0.53 | 6720 |
| 0.54 | 6960 |
| 0.55 | 7200 |
| 0.56 | 7440 |
| 0.57 | 7680 |
| 0.58 | 7920 |
| 0.59 | 8160 |
| 0.60 | 8402 |
| 0.61 | 8640 |
| 0.62 | 8880 |
| 0.63 | 9120 |
| 0.64 | 9360 |
| 0.65 | 9600 |
| 0.66 | 9842 |
| 0.67 | 10080 |
| 0.68 | 10320 |
| 0.69 | 10560 |
| 0.70 | 10800 |
| 0.71 | 11040 |
| 0.72 | 11282 |
| 0.73 | 11520 |
| 0.74 | 11760 |
| 0.75 | 12000 |
| 0.76 | 12240 |
| 0.77 | 12480 |
| 0.78 | 12720 |
| 0.79 | 12962 |
| 0.80 | 13200 |
| 0.81 | 13440 |
| 0.82 | 13680 |
| 0.83 | 13920 |
| 0.84 | 14160 |
| 0.85 | 14402 |
| 0.86 | 14640 |
| 0.87 | 14880 |
| 0.88 | 15120 |
| 0.89 | 15360 |
| 0.90 | 15600 |
| 0.91 | 15842 |
| 0.92 | 16080 |
| 0.93 | 16320 |
| 0.94 | 16560 |
| 0.95 | 16800 |
| 0.96 | 17040 |
| 0.97 | 17282 |
| 0.98 | 17520 |
| 0.99 | 17760 |
| 1.00 | 18000 |

致谢：[Analysis of query.time_of_day](https://gist.github.com/DoubleF3lix/a03afde0a979dfa41e8525ee92f12ca5)

</Spoiler>

## query.eye_target_x_rotation 和 query.eye_target_y_rotation

对玩家无效。不太确定它的用途是什么。

## variable.short_arm_offset_right

返回玩家右臂骨骼与默认皮肤几何体相比的偏移因子。当细臂（3像素宽）皮肤装备到玩家身上时将返回 `0.5`。当普通（4像素宽）皮肤装备到玩家身上时将返回 `0.0`。注意：玩家必须至少进入一次第一人称视角，此变量才会被初始化并在实体的其他地方可用。

## variable.short_arm_offset_left

与 `variable.short_arm_offset_right` 行为相同，只是它引用玩家左臂骨骼。

## query.movement_direction

返回标准化实体移动向量的 3 个分量之一，这意味着向量的大小/模/长度在 0 到 1 之间。

**注意**：在编写文档时，从任何轴返回的值将根据实体的速度而改变（如果实体在地面上，值将小于实体在空中时的值，即使它在相同方向移动）。

要获得实体移动的实际标准化速度向量，你必须标准化这些值。这里是 Molang 设置：

```molang
variable.mag = math.sqrt( math.pow( query.movement_direction(0), 2 ) + math.pow( query.movement_direction(1), 2) + math.pow( query.movement_direction(2), 2));
variable.xNorm = query.movement_direction(0) / variable.mag;
variable.yNorm = query.movement_direction(1) / variable.mag;
variable.zNorm = query.movement_direction(2) / variable.mag;
```

有关标准化向量的更多信息，你可以使用这个 <a href=https://www.desmos.com/calculator/hhoamwgve2>Desmos 图表</a> 进行实验

| 参数 | 轴 |
| -------- | ---- |
| 0 | X |
| 1 | Y |
| 2 | Z |

## query.block_neighbor_has_any_tag 和 query.relative_block_has_any_tag

需要 `实验性 Molang 功能` 才能使用。根据文档 `接受相对位置和一个或多个标签名称，并根据该位置的方块是否有任何提供的标签返回 0 或 1`。这对于使用连接方块或检测实体很有用。

`query.block_neighbor_has_any_tag` - 接受方块位置
`query.relative_block_has_any_tag` - 接受实体位置

其语法为 `q.block_neighbor_has_any_tag(x,y,z,'tag_name')` 和 `q.relative_block_has_any_tag(x,y,z,'tag_name')`。

示例：

- `q.relative_block_has_any_tag(0,-1,0,'grass')` 将尝试检测实体下方一个方块处带有草标签的方块。
- `q.block_neighbor_has_any_tag(0,-1,0,'grass')` 将尝试检测方块下方一个方块处带有草标签的方块。

要进行多个标签，你将使用 `q.correct_query(0,-1,0,'grass', 'plant')`，其中 `correct_query` 被正确的查询替换。

请注意，这也可以检测自定义标签和[原版标签](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/blocks/block-tags)
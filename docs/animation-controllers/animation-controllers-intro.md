---
title: 动画控制器介绍
nav_order: 1
tags:
    - guide
mentions:
    - SirLich
    - solvedDev
    - Joelant05
    - MedicalJewel105
    - stirante
    - cda94581
    - ThijsHankelMC
    - MetalManeMc
    - ThomasOrs
description: 动画控制器介绍。
---

动画控制器（AC）是可以在资源包和行为包中使用的状态机。在资源包中，动画控制器（RPAC）用于播放动画，而在行为包（BPAC）中，它们用于播放命令和命令"动画"。

## 什么是状态机？

状态机是一种特殊的逻辑管理方式，依赖于一系列状态。每个状态都有两个属性：

-   在当前状态下要做什么
-   如何转移到其他状态

状态机在各处广泛使用，特别是在经典编程中。它们不仅存在于minecraft中！您可以在[此处](https://www.itemis.com/en/yakindu/state-machine/documentation/user-guide/overview_what_are_state_machines)了解有关状态机的更多信息。

状态机一次只能处于一个状态。当状态机"运行"时，您可以将其视为从一个状态移动到另一个状态，执行内部逻辑，然后沿着`transitions`（转换）到其他状态。

## 状态机示例

状态机之所以有用，是因为它们允许我们自然地将动画分解为逻辑流程，其中每个状态处理自己的动画_和_自己的逻辑。

例如，想象一下您想要为直升机的旋转叶片制作动画 - 但仅当直升机在地面时。您有两种状态：

-   `地面状态`
-   `飞行状态`

我们可以用上述两段信息来注释这些状态：

-   `地面状态`:
    -   不播放动画
    -   如果在空中则转移到`飞行状态`
-   `飞行状态`:
    -   播放飞行动画
    -   如果在地面则转移到`地面状态`

这里是状态机的流程图表示：

![](/assets/images/concepts/animation-controllers/two_state_FSM.png)

在此流程图中，状态由矩形表示，箭头表示从一个状态到另一个状态的_转换_。

流程图是可视化多状态有限状态机的好方法，因为它允许您跟踪动画的逻辑_流程_。让我们看一个更详细的示例，添加第三个`爆炸`状态：

![](/assets/images/concepts/animation-controllers/three_state_FSM.png)

如您所见，状态可以同时转到多个状态。状态也可能是死胡同（因为直升机已损坏，不再需要进一步动画）。动画控制器的分支流程是它们强大之处的重要组成部分。

## 什么是动画控制器？

动画控制器是Minecraft状态机，允许我们播放动画和运行命令。动画控制器始终位于`animation_controllers`文件夹下，位于RP或BP中。

### 将控制器附加到实体

动画控制器在自己的文件中定义，必须在执行任何操作之前"附加"到实体。要将AC附加到您的实体，您必须做两件事：

-   为动画控制器定义一个简称
-   通过`scripts`运行动画控制器

这是一个示例`description`，展示了如何首先在`animations`中定义AC，然后在`scripts/animate`中播放。

<CodeHeader>RP/entity/helicopter.ce.json 或 BP/entities/helicopter.se.json</CodeHeader>

```json
"description": {
	"identifier": "wiki:helicopter",
	"animations": {
		"blade_controller": "controller.animation.helicopter.blade"
	},
	"scripts": {
		"animate": [
			"blade_controller"
		]
	}
}
```

如果您想有条件地播放动画控制器，可以提供一个可选的molang参数。如果参数计算结果为真，则控制器将播放：

<CodeHeader>RP/entity/helicopter.ce.json 或 BP/entities/helicopter.se.json</CodeHeader>

```json
"scripts": {
	"animate": [
		{
			// 仅当直升机有骑手时才播放blade_controller。
			"blade_controller": "q.has_rider"
		}
	]
}
```

### RP动画控制器

RP动画控制器位于RP中，可以附加到RP实体。它们允许您播放骨骼动画。

### BP动画控制器

BP动画控制器位于BP中，可以附加到BP实体。它们允许您播放命令并向实体发送事件。

## 动画控制器示例

让我们看一下上面状态机示例中的简单动画控制器：

### 简单示例

<CodeHeader>RP/animation_controllers/helicopter.ac.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.helicopter.blade": {
            "initial_state": "ground",
            "states": {
                "ground": {
                    "transitions": [
                        {
                            "flying": "!q.is_on_ground"
                        }
                    ]
                },
                "flying": {
                    "animations": ["flying"],
                    "transitions": [
                        {
                            "ground": "q.is_on_ground"
                        }
                    ]
                }
            }
        }
    }
}
```

这里有很多内容。让我们一步一步地分解。当我们这样做时，请记住两件事：

-   动画控制器是_状态列表_
-   每个状态包含两条信息：_在任何给定状态下要做什么_，以及_如何转换到新状态_。

因此，这个特定示例包含两种状态：

-   `ground`（地面）
-   `flying`（飞行）

您可以注意到`"initial_state": "ground"`意味着我们的动画控制器将从`ground`状态开始。

<CodeHeader>
    RP/animation_controllers/helicopter.ac.json#animation_controllers/controller.animation.helicopter.blade/states
</CodeHeader>

```json
"ground": {
    "transitions": [
        {
            "flying": "!q.is_on_ground"
        }
    ]
}
```

`ground`状态包含一个_转换_列表，这是我们到达其他状态的方式。在此示例中，默认状态表示：_当`q.is_on_ground`不为真时移动到`flying`状态_。换句话说 - 当我们飞到空中时开始飞行动画！

<CodeHeader>
    RP/animation_controllers/helicopter.ac.json#animation_controllers/controller.animation.helicopter.blade/states
</CodeHeader>

```json
"flying": {
    "animations": [
        "flying"
    ],
    "transitions": [
        {
            "ground": "q.is_on_ground"
        }
    ]
}
```

`flying`状态也包含一个转换列表。在这种情况下，它包含相反的转换：_当`q.is_on_ground`为真时移动到`ground`状态_。换句话说 - 当我们降落在地面上时回到默认状态！

除了`transition`（转换）列表之外，还有一个在此状态下播放的`animations`（动画）列表。在这种情况下，播放`flying`动画。此动画需要在此实体的实体定义文件中定义。

### 完整示例

下面是上面第二个状态机的代码，这次有三个状态。此示例说明了一些新概念：

-   具有多个转换的状态
-   没有转换的状态

<CodeHeader>RP/animation_controllers/helicopter.ac.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.helicopter.blade": {
            "initial_state": "ground",
            "states": {
                "ground": {
                    "transitions": [
                        {
                            "flying": "!q.is_on_ground"
                        },
                        {
                            "explode": "!q.is_alive"
                        }
                    ]
                },
                "flying": {
                    "animations": ["flying"],
                    "transitions": [
                        {
                            "ground": "q.is_on_ground"
                        },
                        {
                            "explode": "!q.is_alive"
                        }
                    ]
                },
                "explode": {
                    "animations": ["explode"]
                }
            }
        }
    }
}
```

## RP动画控制器

资源包动画控制器也可以运行声音和粒子等内容。
在动画控制器中调用声音或粒子之前，您需要在客户端实体文件中定义它们。

<CodeHeader>RP/entities/custom_tnt.json#minecraft:client_entity/description</CodeHeader>

```json
"sound_effects": {
    "explosion": "wiki.custom_tnt.explosion" //其中wiki.custom_tnt.explosion是在sound_definitions中定义的声音，就像动画声音一样。
},
"particle_effects": {
    "fuse_lit": "wiki:tnt_fuse_lit_particle"
}
```

然后您才能在动画控制器中调用它们：

<CodeHeader>
    RP/animation_controllers/custom_tnt.animation_controllers.json#controller.animation.custom_tnt
</CodeHeader>

```json
"states":{
    "default":{
        "transitions":[
            {
                "explode_state":"q.mark_variant == 1"
            }
        ]
    },
    "explode_state":{
        "sound_effects":[
            {
                "effect":"explosion"
            }
        ],
		"particle_effects": [
			{
				"effect": "fuse_lit"
				// "locator": "<bone>" 定位器也可以放在这里
			}
		],
        "transitions":[
            {
                "default":"q.mark_variant == 0"
            }
        ]
    }
}
```

:::warning 警告！并非每个粒子都能在那里工作。如果您有问题，请考虑尝试另一个粒子。例如，使用烈焰人动画控制器中的一个。
:::

## BP动画控制器

行为包动画控制器使用与RP动画控制器相同的通用格式，不同之处在于不是触发动画，它们允许您触发命令、事件或执行Molang代码。一般来说，它们引入了两个新字段：

-   `on_entry`：进入状态时播放的命令
-   `on_exit`：退出状态时播放的命令

在这种情况下，命令指三件不同的事情：

-   斜杠命令，如`/say 你好！`
-   实体上的事件触发器，例如：`@s wiki:transform_into_plane`
-   任意Molang表达式，如`v.tickets += 1;`（这也适用于资源包动画控制器）

这是一个BP动画控制器示例，展示了一些这种行为：

<CodeHeader>BP/animation_controllers/helicopter.ac.json</CodeHeader>

```json
{
    "format_version": "1.10.0",
    "animation_controllers": {
        "controller.animation.helicopter.commands": {
            "initial_state": "ground",
            "states": {
                "ground": {
                    "on_entry": ["/say 我现在在地面上了！"],
                    "transitions": [
                        {
                            "flying": "!q.is_on_ground"
                        }
                    ]
                },
                "flying": {
                    "on_entry": ["/say 我现在在空中了！"],
                    "transitions": [
                        {
                            "ground": "q.is_on_ground"
                        }
                    ]
                }
            }
        }
    }
}
```

## 动画控制器流程

通过示例，希望您开始了解动画控制器流程是如何工作的。在本节中，我将更明确地解释它。

### 加载

当实体加载到世界中时，它将进入每个附加动画控制器中的默认动画控制器状态。如果未定义`initial_state`，则使用名为`default`的状态。如果缺少此状态，AC将生成内容日志。

运行时，AC将在每个tick中执行以下操作：

1. 运行当前状态中的任何动画（如果设置为循环将循环播放，否则只会播放一次）。如果刚刚进入状态，则运行`on_entry`中的任何命令。
2. 检查所有转换以查看是否有任何有效转换。从列表的顶部到底部搜索，并移动到第一个有效转换。如果找到转换，将播放`on_exit`命令。

由于动画控制器的设置方式，它最多每tick仅从一个状态移动到另一个状态。

### 重置

当实体重新加载时（玩家加入/离开、区块重新加载等），动画控制器将"重置"。这意味着它将"跳回"到默认状态。您应该始终在默认状态中包含能够重启任何关键动画的逻辑。

## 注释

您也可以在动画控制器中创建变量（并重新映射它们的值）！

```json
{
    "format_version": "1.17.30",
    "animation_controllers": {
        "controller.animation.sheep.move": {
            "states": {
                "default": {
                    "variables": {
                        "ground_speed_curve": {
                            "input": "q.ground_speed",
                            "remap_curve": {
                                "0.0": 0.2,
                                "1.0": 0.7
                            }
                        }
                    },
                    "animations": [
                        "wiggle_nose",
                        {
                            "walk": "v.ground_speed_curve"
                        }
                    ]
                }
            }
        }
    }
}
```
---
title: 实体事件
category: General
mentions:
    - ChibiMango
    - SirLich
    - Joelant05
    - MedicalJewel105
    - aexer0e
    - SmokeyStack
    - ThomasOrs
    - QuazChick
tags:
    - beginner
description: 学习实体最重要的内容之一 - 事件。
---

实体事件是与组件和组件组并列的基本构建块之一。它们作为组件组的控制中心，可以从组件、动画、动画控制器和其他事件中调用。本页面旨在涵盖如何在实体内部和其他实体中调用事件以及事件的格式。

## 事件响应

事件允许我们执行操作，例如从我们的实体中添加和移除组件组，允许我们在满足特定条件时改变实体的行为。之所以称为事件，是因为我们可以在事件发生时激活它们，例如计时器超时、玩家与实体交互或环境变化发生。当触发事件时，它将执行所有列出的事件响应。

### 添加/移除

事件最本质和常见的用途是直接添加和/或移除组件组。这些几乎总是在您的事件中使用，并与其他键一起使用。以下名为`wiki:ranged_attacker`的事件添加了"attacker"和"ranged"两个组件组，并移除了"standby"和"melee"组：

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:ranged_attacker": {
    "add": {
        "component_groups": [
            "attacker",
            "ranged"
        ]
    },
    "remove":{
        "component_groups": [
            "standby",
            "melee"
        ]
    }
}
```

:::tip
当您添加一个组件组时，如果当前活跃的组件组中有相同的组件，它将被最近添加的组覆盖。
:::

### 队列命令

将命令排队以在tick结束时由目标执行。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:execute_event": {
    "queue_command": {
        "target": "self", // 可选 - 'self'是默认值（目标实体）
        "command": "summon pig"
    }
}
```

可以使用数组来排队多个命令：

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:execute_event": {
    "queue_command": {
        "target": "self", // 可选 - 'self'是默认值（目标实体）
        "command": [
            "summon pig",
            "say Everybody welcome the pig!"
        ]
    }
}
```

### 随机化

随机化是可以在实体事件内部使用的一个参数，用于基于加权随机化添加或移除组件组。当应该基于随机机会添加不同的组件组时，这是一个非常有用的工具。

牛内部的`minecraft:entity_spawned`事件使用随机化来给予95%的机会使牛以成年形式生成，5%的机会以幼年形式生成（组件组`minecraft:cow_adult`和`minecraft:cow_baby`）。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"minecraft:entity_spawned": {
    "randomize": [
        {
            "weight": 95,
            "add": {
                "component_groups": [
                    "minecraft:cow_adult"
                ]
            }
        },
        {
            "weight": 5,
            "add": {
                "component_groups": [
                    "minecraft:cow_baby"
                ]
            }
        }
    ]
}
```

请注意，`randomize`只会从选项池中选择一个选项。

### 序列/过滤器

序列是一个可以在实体事件内部使用的参数，用于基于过滤器添加或移除组件组。过滤器允许我们创建条件事件，只有在满足条件时才会添加/移除组件组。僵尸内部的`minecraft:convert_to_drowned`事件使用`sequence`参数基于僵尸是否为幼年来添加不同的组件组。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"minecraft:convert_to_drowned": {
    "sequence": [
        {
            "filters": {
                "test": "has_component",
                "operator": "!=",
                "value": "minecraft:is_baby"
            },
            "add": {
                "component_groups": [
                    "minecraft:convert_to_drowned"
                ]
            },
            "remove": {
                "component_groups": [
                    "minecraft:start_drowned_transformation"
                ]
            }
        },
        {
            "filters": {
                "test":"has_component",
                "value":"minecraft:is_baby"
            },
            "add": {
                "component_groups": [
                    "minecraft:convert_to_baby_drowned"
                ]
            },
            "remove": {
                "component_groups": [
                    "minecraft:start_drowned_transformation"
                ]
            }
        }
    ]
}
```

此外，`sequence`允许我们按顺序运行多个参数。它同时评估每个部分，如果有效，将应用它。

:::tip
序列中的条目不是互斥的；如果其中一个条目的过滤器通过，它不会阻止其他条目运行。在上面的情况下，第一个条目中没有过滤器，因此它自动运行。这不会阻止检查其他条目并在有效时随后运行。
:::

下面是使用序列结合过滤器、随机化和添加与移除组件组的综合示例：

<Spoiler title="序列示例">

当实体被玩家或抛射物击中时运行此事件。有60%的概率什么都不发生，40%的概率激活攻击序列。这个攻击序列选择随机攻击，权重由实体当前生命值（当实体低于一半生命值时，更强的攻击被赋予更高概率）和最近玩家的距离（当玩家距离较远时远程攻击优先级更高）决定。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:on_hit": {
    "randomize":[
        // 60%概率什么都不发生
        {
            "weight": 60
        },
        // 40%概率运行此条目
        {
            "weight": 40,
            "sequence": [
                // 运行所有攻击都需要的独立事件
                {
                    "trigger": "attack_event"
                },
                // 在实体未被剪毛时运行（实体在低于一半生命值时被剪毛）
                {
                    "filters": {
                        "test": "has_component",
                        "operator": "!=",
                        "value": "minecraft:is_sheared"
                    },
                    "sequence": [
                        // 在玩家在5格以内时运行
                        {
                            "filters": {
                                "test": "distance_to_nearest_player",
                                "operator": "<=",
                                "value": 5.0
                            },
                            "randomize": [
                                {
                                    "weight": 10,
                                    "add": {
                                        "component_groups": [
                                            "explode"
                                        ]
                                    }
                                },
                                {
                                    "weight": 60,
                                    "add": {
                                        "component_groups": [
                                            "attack"
                                        ]
                                    }
                                },
                                {
                                    "weight": 20,
                                    "add": {
                                        "component_groups": [
                                            "range_attack"
                                        ]
                                    }
                                },
                                {
                                    "weight": 10
                                }
                            ]
                        },
                        // 在玩家距离超过5格且实体仍有目标时运行
                        {
                            "filters": {
                                "all_of": [
                                    {
                                        "test": "distance_to_nearest_player",
                                        "operator": ">",
                                        "value": 5.0
                                    },
                                    {
                                        "test": "has_target",
                                        "operator": "equals",
                                        "value": true
                                    }
                                ]
                            },
                            "randomize": [
                                {
                                    "weight": 30,
                                    "add": {
                                        "component_groups": [
                                            "attack"
                                        ]
                                    }
                                },
                                {
                                    "weight": 60,
                                    "add":{
                                        "component_groups": [
                                            "range_attack"
                                        ]
                                    }
                                },
                                {
                                    "weight": 10
                                }
                            ]
                        }
                    ]
                },
                // 在实体被剪毛时运行（低于一半生命值）
                {
                    "filters": {
                        "test": "has_component",
                        "value": "minecraft:is_sheared"
                    },
                    "sequence": [
                        // 在玩家在5格以内时运行
                        {
                            "filters": {
                                "test": "distance_to_nearest_player",
                                "operator": "<=",
                                "value": 5.0
                            },
                            "randomize": [
                                {
                                    "weight": 20,
                                    "add":{
                                        "component_groups": [
                                            "explode"
                                        ]
                                    }
                                },
                                {
                                    "weight": 60,
                                    "add": {
                                        "component_groups": [
                                            "strong_attack"
                                        ]
                                    }
                                },
                                {
                                    "weight": 20,
                                    "add": {
                                        "component_groups": [
                                            "strong_range_attack"
                                        ]
                                    }
                                }
                            ]
                        },
                        // 在玩家距离超过5格且实体仍有目标时运行
                        {
                            "filters": {
                                "all_of": [
                                    {
                                        "test": "distance_to_nearest_player",
                                        "operator": ">",
                                        "value": 5.0
                                    },
                                    {
                                        "test": "has_target",
                                        "operator": "equals",
                                        "value": true
                                    }
                                ]
                            },
                            "randomize": [
                                {
                                    "weight": 60,
                                    "add": {
                                        "component_groups": [
                                            "strong_range_attack"
                                        ]
                                    }
                                },
                                {
                                    "weight": 40,
                                    "randomize": [
                                        {
                                            "weight": 30,
                                            "trigger": "rapid_fire"
                                        },
                                        {
                                            "weight": 70,
                                            "add": {
                                                "component_groups": [
                                                    "strong_blast"
                                                ]
                                            }
                                        }
                                    ]
                                }
                            ]
                        }
                    ]
                }
            ]
        }
    ]
}
```

</Spoiler>

### 设置属性

设置实体属性值（每个值都可以设置为Molang表达式字符串的返回值）。

<CodeHeader>minecraft:block > events</CodeHeader>

```json
"wiki:change_properties": {
    "set_property": {
        "wiki:boolean_property_example": false,
        "wiki:integer_property_example": "q.property('wiki:integer_property_example') + 1",
        "wiki:string_property_example": "red"
    }
}
```

### 触发

触发是一个可以在实体事件内部使用的参数，用于基于过滤器向选定目标运行其他事件。
我们可以使用它在实体内部触发另一个事件，将其与`sequence`结合使用可以让我们整齐地组织我们的事件。

我们还能够为事件指定过滤器和目标。目标参数将在后面详细介绍。如果以下事件由`minecraft:interact`组件调用，那么如果交互的实体具有`pig`家族标签，它将在与实体交互的玩家中运行`wiki:interacted`事件。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:on_interact": {
    "trigger": {
        "filters": {
            "test": "is_family",
            "subject": "self",
            "value": "pig"
        },
        "event": "wiki:interacted",
        "target": "other"
    }
}
```

:::tip
事件能够保留从它们被调用的组件中获得的实体上下文。例如，如果使用`minecraft:interact`组件触发事件，我们能够将过滤器应用于与实体交互的玩家。然而，如果调用事件的方法没有此上下文，使用目标将不起作用。
:::

将其与序列参数结合使用，这允许我们在多个实体中运行事件，只要存在上下文即可。我们将在目标部分对此进行更多讨论。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"wiki:on_interact": {
    "sequence": [
        {
            "trigger": {
                "event": "wiki:interacted",
                "target": "other"
            }
        },
        {
            "trigger": {
                "event": "wiki:interacted_with",
                "target": "self"
            }
        }
    ]
}
```

## 调用事件

为了让事件运行，我们需要知道如何激活它，这是通过调用事件来完成的。有五种主要方法可以做到这一点：

-   在组件内部
-   在动画内部
-   在动画控制器内部
-   在另一个事件内部
-   使用命令

一些组件允许玩家根据设定的参数调用事件。在这里，当参数满足时，我们输入要运行的事件。例如，僵尸中使用`minecraft:environment_sensor`组件在实体在水下时调用`minecraft:start_transforming`事件。

<CodeHeader>minecraft:entity > components</CodeHeader>

```json
"minecraft:environment_sensor": {
    "triggers": {
        "filters": {
            "test": "is_underwater",
            "operator": "==",
            "value": true
        },
        "event": "minecraft:start_transforming"
    }
}
```

我们还可以在动画和动画控制器中直接在实体上运行事件。
此基于行为的动画用于在10秒后调用`wiki:start_pouncing`事件。

<CodeHeader></CodeHeader>

```json
"animation.entity.pounce_timer": {
    "timeline": {
        "10.0": "@s wiki:start_pouncing"
    },
    "animation_length": 10.1
}
```

此基于行为的动画控制器用于在转换到"run"状态时调用`wiki:running`事件。

<CodeHeader></CodeHeader>

```json
"controller.animation.entity.movement":{
    "initial_state":"walk",
    "states":{
        "walk":{
            "transitions":[
                {
                    "run":"q.is_sheared"
                }
            ]
        },
        "run":{
            "on_entry":[
                "@s wiki:running"
            ],
            "transitions":[
                {
                    "walk":"!q.is_sheared"
                }
            ]
        }
    }
}
```

这里的`@s`用于将事件应用到实体自身。动画控制器功能强大，可以用来创建更多自定义行为，尽管它们更高级。查看我们[这里](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/animation-controllers/animation-controllers-intro)的页面以获取更多信息。

在事件中，除了添加和移除组件组，我们还可以`trigger`其他事件的发生。
猪灵内部的此事件从`minecraft:entity_born`事件调用`spawn_baby`事件。

<CodeHeader>minecraft:entity > events</CodeHeader>

```json
"minecraft:entity_born": {
    "trigger": "spawn_baby"
}
```

我们也可以使用命令`/event`在实体上激活事件。以下命令将向所有猪添加组件组`wiki:example`。
`/event entity @e[type=minecraft:pig] wiki:example`。

### 在其他实体中调用事件

一些组件，例如伤害传感器，可以在调用事件时针对"self"以外的实体。特别设计用于在其他实体中调用事件的组件是：`minecraft:behavior.send_event`。我们将首先讨论此组件。

唤魔者内部使用`minecraft:behavior.send_event`组件在其激活范围内对任何蓝色羊调用名为`wololo`的事件。

<CodeHeader></CodeHeader>

```json
"minecraft:behavior.send_event": {
    "priority": 3,
    "event_choices": [
        {
            "min_activation_range": 0.0,
            "max_activation_range": 16.0,
            "cooldown_time": 5.0,
            "cast_duration": 3.0,
            "particle_color": "#FFB38033",
            "weight": 3,
            "filters": {
                "all_of": [
                    {
                        "test": "is_family",
                        "subject": "other",
                        "value": "sheep"
                    },
                    {
                        "test": "is_color",
                        "subject": "other",
                        "value": "blue"
                    }
                ]
            },
            "start_sound_event": "cast.spell",
            "sequence": [
                {
                    "base_delay": 2.0,
                    "event": "wololo",
                    "sound_event": "prepare.wololo"
                }
            ]
        }
    ]
}
```

您还可以在生成实体时调用事件。要做到这一点，在可以召唤实体的组件的字符串末尾添加`<my:event_name>`。

<CodeHeader>BP/entities/zombie.json#component_groups/minecraft:convert_to_drowned</CodeHeader>

```json
"minecraft:transformation": {
    "into": "minecraft:drowned<minecraft:as_adult>",
    "transformation_sound": "convert_to_drowned",
    "drop_equipment": true,
    "delay": {
        "value":15
    }
}
```

### 目标

掠夺者内部的`minecraft:damage_sensor`组件在杀死它的玩家上调用`minecraft:gain_bad_omen`事件。注意事件的目标是如何设置为"other"的。

<CodeHeader></CodeHeader>

```json
"minecraft:damage_sensor": {
    "triggers": {
        "on_damage": {
            "filters": {
                "all_of": [
                    {
                        "test": "has_damage",
                        "value": "fatal"
                    },
                    {
                        "test": "is_family",
                        "subject": "other",
                        "value": "player"
                    }
                ]
            },
            "event": "minecraft:gain_bad_omen",
            "target": "other"
        }
    }
}
```

一些组件有这些`targets`，每个都有特定的可用目标。例如，`minecraft:interact`可以将目标设置为`self`或`other`，其中other是与实体交互的实体。所有有效的组件都应该有`self`和`target`作为选项，其中target是目标实体。

### 内置事件

一般来说，使用来自原版生物的组件组将不起作用。例如，除非您使用上述方法之一来调用它，否则`minecraft:convert_to_drowned`不会在您的实体中被调用。然而，有几个事件在满足条件时会自动调用：

-   `minecraft:entity_spawned`：在实体生成时调用。对于设置初始组件组很有用。
-   `minecraft:entity_born`：通过繁殖生成实体时调用。
-   `minecraft:entity_transformed`：当另一个实体转变为这个实体时调用。
-   `minecraft:on_prime`：当实体的引信被点燃并准备爆炸时调用。

这些使用的好例子是牛。这显示了我们如何确保牛在生成/转换后立即具有`minecraft:cow_adult`或`minecraft:cow_baby`。

<CodeHeader>BP/entities/cow.json#events</CodeHeader>

```json
"events": {
    "minecraft:entity_spawned": {
        "randomize": [
            {
                "weight": 95,
                "add": {
                    "component_groups": ["minecraft:cow_adult"]
                }
            },
            {
                "weight": 5,
                "add": {
                    "component_groups": ["minecraft:cow_baby"]
                }
            }
    ]
    },
    "minecraft:entity_born": {
        "add": {
            "component_groups": ["minecraft:cow_baby"]
        }
    },
    "minecraft:entity_transformed": {
        "add": {
            "component_groups": ["minecraft:cow_adult"]
        }
    }
}
```
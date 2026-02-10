---
title: 物品格式历史
description: 每个Minecraft版本中数据驱动物品格式的所有更改列表。
category: Documentation
outline_depth: 2
license: true
mentions:
    - QuazChick
---

:::info <Label color="green">版本化</Label> 更改
必须更新文件的`format_version`字段才能使版本化更改生效。
:::

:::info <Tag name="experimental" /> 更改
必须在您的世界中启用实验才能使实验性更改生效。
:::

## 1.26.0

### 组件

<Label color="green">版本化</Label>

-   更新了`minecraft:damage`
    -   现在正确支持超过`255`{lang=json}的值，最大可达`32767`{lang=json}。

## 1.21.130

### 组件

-   添加了`minecraft:kinetic_weapon`
    -   使物品在使用时对玩家移动方向上的每个实体造成伤害（以及击落并施加击退效果）。
    -   包含`delay`，确定开始应用动能伤害和效果前的持续时间（以刻度为单位）。
    -   包含`hitbox_margin`，确定每个目标的碰撞箱需要距离玩家视线方向多近才能受到动能攻击的影响。
        -   默认情况下，没有额外的命中框边距。
    -   包含`reach`，确定实体必须距离玩家多远才能受到动能攻击影响的范围（以方块为单位）。
        -   默认情况下，距离玩家0到3个方块的实体将受到动能攻击的影响。
    -   包含`creative_reach`，确定玩家在创造模式下应用的`reach`。
        -   默认情况下，创造模式下的玩家将被限制在正常的`reach`范围内。
    -   包含`damage_multiplier`，基础动能攻击伤害乘以该值以获得倍增的伤害值。
    -   包含`damage_modifier`，添加到倍增的伤害值以获得最终伤害值施加给每个目标。
    -   包含`damage_conditions`，列出造成伤害需要满足的条件。
    -   包含`dismount_conditions`，列出每个目标需要满足的条件才能通过动能攻击从其所骑乘的实体上击落。
        -   默认情况下，实体永远不会被击落。
    -   包含`knockback_conditions`，列出每个目标需要满足的条件才能从动能攻击中受到击退。
        -   默认情况下，永远不会应用击退。
-   添加了`minecraft:piercing_weapon`
    -   使物品在攻击时对玩家视线方向直线上的所有实体造成伤害。
    -   如果玩家和其他实体之间有方块碰撞，伤害将被阻挡。
    -   防止物品用于挖掘方块。
    -   包含`hitbox_margin`，确定每个实体的碰撞箱需要距离玩家视线方向多近才能受到伤害。
        -   默认情况下，没有额外的命中框边距。
    -   包含`reach`，确定实体必须距离玩家多远才能受到伤害的范围（以方块为单位）。
        -   默认情况下，距离玩家0到3个方块的实体将受到伤害。
    -   包含`creative_reach`，确定玩家在创造模式下应用的`reach`。
        -   默认情况下，创造模式下的玩家将被限制在正常的`reach`范围内。
-   添加了`minecraft:swing_sounds`
    -   确定玩家手持物品攻击时触发的原版声音事件。
    -   包含`attack_miss`，确定未击中实体或未造成伤害时触发的声音事件。
    -   包含`attack_hit`，确定击中实体并造成非致命伤害时触发的声音事件。
    -   包含`attack_critical_hit`，确定击中实体并造成致命伤害时触发的声音事件。
-   更新了`minecraft:cooldown`
    -   添加了`type`参数，确定冷却影响以下哪种类型的输入：
        -   `use`（默认）在使用物品时触发，并在冷却期间防止使用物品。
        -   `attack`在玩家手持物品攻击时触发，并在冷却期间防止使用物品进行攻击。
-   更新了`minecraft:use_modifiers`
    -   添加了`start_sound`参数，确定物品开始使用时触发的原版声音事件。

## 1.21.120

### 描述

<Label color="green">版本化</Label>

-   更新了`menu_category`
    -   没有类别的物品现在可以在命令中使用。
    -   如果`is_hidden_in_commands`设置为`true`，物品在命令中仍然是无效的。

### 组件

-   添加了`minecraft:swing_duration`
    -   确定玩家在挖掘、攻击或使用物品时挥动动画的持续时间（以秒为单位）。
-   更新了`minecraft:use_modifiers`
    -   添加了`emit_vibrations`参数，确定物品开始和停止使用时是否发出震动。

## 1.21.110

### 组件

-   添加了`minecraft:fire_resistant`
    -   确定物品是否不能被火和熔岩销毁。

## 1.21.90

### 组件

-   更新了`minecraft:wearable`
    -   添加了`hides_player_location`参数，确定穿戴物品的玩家是否会从定位器栏和定位器地图中隐藏。
    -   默认情况下，可穿戴物品不会隐藏玩家。

<Label color="green">版本化</Label>

-   移除了`minecraft:custom_components`

    -   自定义组件现在应以与原版组件相同的方式应用，作为`components`对象中的条目：

        <CodeHeader>minecraft:block</CodeHeader>

        ```json
        "components": {
            "minecraft:glint": true,
            "wiki:custom_component": {
                "parameter": "value"
            }
        }
        ```

## 1.21.80

### 组件

<Tag name="experimental" />
<Label color="red">自定义组件V2</Label>
<Label color="green">版本化</Label>

-   移除了`minecraft:custom_components`
    -   自定义组件现在应以与原版组件相同的方式应用，作为`components`对象中的条目。

## 1.21.60

### 描述

<Label color="green">版本化</Label>

-   更新了`menu_category`
    -   更新了`group`参数，需要命名空间，例如`minecraft:itemGroup.name.concrete`

### 组件

<Label color="green">版本化</Label>

-   添加了`minecraft:storage_weight_limit`
    -   确定存储物品容器中所有物品的最大允许总重量。
    -   替换了`max_weight_limit`存储物品参数。
-   添加了`minecraft:storage_weight_modifier`
    -   确定物品在存储物品内部时增加的额外重量。
    -   值为0意味着该物品不允许在存储物品内部。
    -   替换了`weight_in_storage_item`存储物品参数。
-   将`minecraft:compostable`从实验中发布
-   更新了`minecraft:block_placer`
    -   将`replace_block_item`参数从实验中发布
-   更新了`minecraft:storage_item`
    -   移除了`max_weight_limit`参数
    -   移除了`weight_in_storage_item`参数

## 1.21.50

### 组件

<Tag name="experimental" />
<Label color="blue">即将到来的创作者功能</Label>

-   添加了`minecraft:compostable`
    -   允许此物品在堆肥箱中使用。
    -   包含`composting_chance`，确定堆肥水平增加的百分比概率（0-100）。

## 1.21.40

<Label color="green">版本化</Label>

-   数字不能再用于表示布尔值。
-   浮点数不能再用于表示整数值。

### 组件

<Label color="green">版本化</Label>

-   将`minecraft:bundle_interaction`从实验中发布
-   将`minecraft:storage_item`从实验中发布

<Tag name="experimental" />
<Label color="blue">即将到来的创作者功能</Label>

-   更新了`minecraft:block_placer`
    -   如果未应用`minecraft:icon`组件，现在将指定的`block`用作物品图标。
    -   添加了`replace_block_item`参数，用此物品替换指定`block`的默认物品。
        -   为此功能正常工作，物品和方块标识符必须相同。
        -   在[此处](file:///c/Users/MCxlm/Downloads/bedrock-wiki-wiki/bedrock-wiki-wiki/docs/blocks/blocks-as-items#replacing-block-items)了解更多关于替换方块物品的信息！

## 1.21.30

### 组件

<Label color="green">版本化</Label>

-   添加了`minecraft:dyeable`
    -   允许物品通过炼药锅水染色。
    -   包含`default_color`，确定物品染色前使用的可选颜色。
    -   一旦染色，物品将显示`minecraft:icon`组件中定义的`dyed`纹理而非`default`。
-   添加了`minecraft:rarity`
    -   定义物品的基础稀有度（由物品名称的颜色显示）。
    -   接受以下稀有度等级：`common`、`uncommon`、`rare`和`epic`。
    -   当物品被附魔时，其稀有度将尽可能提升到下一个级别。
    -   此组件没有功能效果，可以用`minecraft:hover_text_color`覆盖。

<Tag name="experimental" />
<Label color="orange">捆绑</Label>

-   添加了`minecraft:bundle_interaction`
    -   在物品上启用捆绑界面和功能。
    -   包含`num_viewable_slots`，确定
    -   需要应用`minecraft:storage_item`组件。
-   添加了`minecraft:storage_item`
    -   允许此物品包含其他物品。
    -   包含`allow_nested_storage_items`，确定其他存储物品是否可以放入容器中。
    -   包含`allowed_items`，定义容器中独占允许的物品。
        -   如果为空，则容器中允许所有物品。
    -   包含`banned_items`，定义容器中不允许的物品。
    -   包含`max_slots`，定义容器中的槽位数。
    -   包含`max_weight_limit`，定义容器中所有物品的最大允许总重量。
    -   包含`weight_in_storage_item`，定义物品在另一个存储物品内部时增加的额外重量。
        -   值为0意味着该物品不允许在另一个存储物品内部。

## 1.21.20

### 组件

<Label color="green">版本化</Label>

-   添加了`minecraft:damage_absorption`
    -   使物品吸收原本会对穿戴者造成的伤害。
    -   包含`absorbable_causes`，确定可以被物品吸收的伤害原因（如`entity_attack`和`magma`）列表。
    -   需要`minecraft:durability`和`minecraft:wearable`组件。
-   添加了`minecraft:durability_sensor`
    -   使物品在受到伤害时发出效果。
    -   包含`durability_thresholds`，确定耐久度阈值以及达到每个阈值时发出的效果。

## 1.21.10

### 组件

<Label color="green">版本化</Label>

-   将`minecraft:custom_components`从实验中发布

## 1.21.0

<Tag name="experimental" />
<Label color="red">假日创作者功能</Label>
<Label color="green">版本化</Label>

-   移除了`events`

### 组件

<Tag name="experimental" />
<Label color="yellow">Beta API</Label>

-   添加了`minecraft:custom_components`
    -   列出应应用于此方块的所有自定义组件。

### 事件

<Tag name="experimental" />
<Label color="red">假日创作者功能</Label>
<Label color="green">版本化</Label>

-   移除了`add_mob_effect`响应
-   移除了`damage`响应
-   移除了`decrement_stack`响应
-   移除了`remove_mob_effect`响应
-   移除了`run_command`响应
-   移除了`teleport`响应
-   移除了`transform_item`响应

## 1.20.60

### 组件

<Label color="green">版本化</Label>

-   更新了`minecraft:icon`
    -   移除了`texture`参数
    -   添加了`textures`参数，列出物品可在其图标中使用的不同纹理，包括盔甲装饰。
        -   包含`default`，替换了`texture`参数。

## 1.20.50

### 组件

<Label color="green">版本化</Label>

-   添加了`minecraft:tags`
    -   列出应用于此物品的标签。
    -   替换`tag:*`组件。
-   添加了`minecraft:use_modifiers`
    -   包含`use_duration`，确定完整使用物品的长度（以秒为单位）。
        -   替换`minecraft:use_duration`组件。
    -   包含`movement_modifier`，在使用物品时对持有者的速度应用倍数。
-   移除了`minecraft:use_duration`
-   移除了使用`tag:*`应用标签的功能

<Tag name="experimental" />
<Label color="red">假日创作者功能</Label>
<Label color="green">版本化</Label>

-   移除了`minecraft:chargeable`
-   更新了`minecraft:digger`
    -   移除了`on_dig`触发参数

## 1.20.40

### 组件

<Label color="green">版本化</Label>

-   更新了`minecraft:icon`
    -   单字符串值现在可以再次使用，例如`"minecraft:icon": "stick"`。

<Tag name="experimental" />
<Label color="red">假日创作者功能</Label>
<Label color="green">版本化</Label>

-   移除了`minecraft:on_use`触发器
-   移除了`minecraft:on_use_on`触发器
-   移除了`minecraft:weapon`

## 1.20.30

### 组件

<Label color="green">版本化</Label>

-   添加了`minecraft:interact_button`
    -   启用并设置触摸控制时显示的按钮文本。
    -   如果设置为`true`，按钮显示"使用物品"。
-   将`minecraft:allow_off_hand`从实验中发布
-   将`minecraft:damage`从实验中发布
-   将`minecraft:digger`从实验中发布
-   将`minecraft:enchantable`从实验中发布
-   将`minecraft:food`从实验中发布
-   将`minecraft:hand_equipped`从实验中发布
-   将`minecraft:liquid_clipped`从实验中发布
-   将`minecraft:should_despawn`从实验中发布
-   将`minecraft:stacked_by_data`从实验中发布
-   将`minecraft:use_animation`从实验中发布
-   将`minecraft:use_duration`从实验中发布

## 1.20.10

### 组件

<Label color="green">版本化</Label>

-   将`minecraft:block_placer`从实验中发布
-   将`minecraft:can_destroy_in_creative`从实验中发布
-   将`minecraft:cooldown`从实验中发布
-   将`minecraft:hover_text_color`从实验中发布
-   将`minecraft:max_stack_size`从实验中发布
-   将`minecraft:projectile`从实验中发布
-   将`minecraft:record`从实验中发布
-   将`minecraft:repairable`从实验中发布
-   将`minecraft:shooter`从实验中发布
-   将`minecraft:throwable`从实验中发布

<Tag name="experimental" />
<Label color="red">假日创作者功能</Label>
<Label color="green">版本化</Label>

-   移除了`minecraft:render_offsets`

## 1.20.0

### 组件

<Label color="green">版本化</Label>

-   将`minecraft:display_name`从实验中发布
-   将`minecraft:durability`从实验中发布
-   将`minecraft:fuel`从实验中发布
-   将`minecraft:entity_placer`从实验中发布
-   将`minecraft:icon`从实验中发布

<Tag name="experimental" />
<Label color="red">假日创作者功能</Label>
<Label color="green">版本化</Label>

-   移除了`minecraft:dye_powder`
-   更新了`minecraft:repairable`
    -   移除了`on_repaired`触发参数

---

:::danger 未记录
`1.20.0`版本之前的格式更改尚未列出。
:::
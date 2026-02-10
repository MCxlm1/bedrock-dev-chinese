---
title: 命令方块简介
category: General
mentions:
    - BedrockCommands
    - zheaEvyline
    - jordanparki7
nav_order: 1
description: MCBE命令方块介绍。
---

## 简介

[由Bedrock Commands Community (BCC) Discord提供来源](https://bedrockcommands.org/)

命令方块是Minecraft中的特殊方块。您在聊天中输入的相同命令（作弊码）可以使用命令方块自动运行，并且可以重复使用而无需再次全部输入。

只有拥有创造模式中操作员权限的玩家才能放置或销毁它们。

## 获取

1. 打开您的世界设置。
2. 在作弊码下，将"激活作弊码"设置切换为开启。
3. 在聊天中运行`/give @s command_block`。

## 命令方块UI

![命令方块Ui](/assets/images/commands/intro-to-command-blocks/command-block-ui.png)

## 命令方块类型

![脉冲命令方块](/assets/images/commands/intro-to-command-blocks/command-block.impulse.png) **脉冲** 每次供电时**一次**运行命令。

![连锁命令方块](/assets/images/commands/intro-to-command-blocks/command-block.chain.png) **连锁** 按顺序运行命令，即仅在连接的上一个命令方块运行后才运行。

![循环命令方块](/assets/images/commands/intro-to-command-blocks/command-block.repeat.png) **循环** 每个游戏刻度运行命令。大约每秒有20个刻度。可以应用延迟来调整命令执行的频率，如下[所述](/commands/intro-to-command-blocks#command-block-tick-delay)。

## 命令方块条件

**条件**命令方块仅在连接的上一个命令方块有成功输出(`true`)时运行命令。

> 条件命令方块状态通过命令方块箭头纹理上的小凹痕显示，如下所示：
>
> -   ![脉冲条件命令方块](/assets/images/commands/intro-to-command-blocks/command-block.impulse-conditional.png) 脉冲条件命令方块
> -   ![连锁条件命令方块](/assets/images/commands/intro-to-command-blocks/command-block.chain-conditional.png) 连锁条件命令方块
> -   ![循环条件命令方块](/assets/images/commands/intro-to-command-blocks/command-block.repeat-conditional.png) 循环条件命令方块

**无条件**命令方块无论结果如何都会运行命令。无论连接的上一个命令方块是否有成功输出(`true`)或失败(`false`)，命令方块仍会运行命令。

## 命令方块红石状态

**需要红石**命令方块只能通过红石能量激活。使用按钮、拉杆、红石火把等。

**始终激活**命令方块在关闭命令方块UI时立即激活。

## 命令方块刻度延迟

在此选项中，您可以指定命令方块运行命令前应有多少延迟。

刻度指的是Minecraft游戏刻度。**刻度**只是游戏中时间的测量单位。现实生活中1秒大约等于Minecraft中的20个游戏刻度。

:::tip
![命令在游戏刻度中运行的大致位置](/assets/images/commands/intro-to-command-blocks/game-tick.png)
:::

## 命令方块悬停说明

此选项允许您在命令方块上放置悬停文本。在处理多个命令方块链时，这对于给予简短名称以便轻松识别很有用。

当运行命令时，如果启用了gamerule `commandblockoutput`，悬停说明将与输出一起显示在聊天中。
![命令方块悬停说明](/assets/images/commands/intro-to-command-blocks/command-block-hover-note.png)

## 粘贴按钮

![命令方块粘贴按钮](/assets/images/commands/intro-to-command-blocks/command-block-paste-button.png)

粘贴按钮允许您将先前复制的坐标粘贴到'命令输入'框中。

## 命令方块输出

-   在命令方块UI中切换'上一个输出'按钮以查看命令输出和方块详情。

-   您在完整命令前输入的`/`在命令方块中不是必需的，但这样做不会导致任何错误。

-   红石比较器可以读取命令方块输出。如果输出成功，它将返回1到15个红石信号，具体取决于输出强度。

-   您可以在聊天中运行它来检查命令方块输出是否为`true`/`false`。红色文本输出或`0`输出表示语法错误或失败(`false`)输出。白色文本输出表示命令已成功运行。

-   您还可以通过检查动作是否执行来判断命令是否为`true`/`false`。

### 在聊天中禁用命令消息

在聊天中运行：

-   `/gamerule commandblockoutput false`以在聊天中禁用命令方块消息。
-   `/gamerule sendcommandfeedback false`以禁用从聊天中输入的命令的反馈。

## 命令方块放置

在链中放置命令方块（排列为按顺序工作）用于任何系统时，请确保连续的命令方块从上一个命令方块箭头的头部连接/开始。

可以从侧面的命令方块纹理观察箭头/朝向。

**✅ 正确放置**
![正确命令方块放置](/assets/images/commands/intro-to-command-blocks/command-block-placement.correct.png)

**❌ 错误放置**
![错误命令方块放置](/assets/images/commands/intro-to-command-blocks/command-block-placement.incorrect.png)

## 命令方块故障排除

-   在世界设置中，在**作弊码**下，确保命令方块未被禁用。

-   确保gamerule `maxcommandchainlength`未设置为0

-   确保没有任何不需要的红石能量干扰命令方块。它可能来自红石粉、拉杆、红石火把等。

-   尝试在'始终激活'和'需要红石'之间切换。

-   仔细检查方块类型、条件和命令语法。供电后，再次检查'上一个输出'。

-   就像红石一样，命令方块也必须在加载的区块内才能工作。当玩家不在附近时，您可以使用tick区域使其保持加载。有关更多信息，请参阅[/tickingarea](https://learn.microsoft.com/en-us/minecraft/creator/documents/tickingareacommand)命令文档。

如果没有效果，只需打破并更换该命令方块即可。

## 您学到的内容

:::tip 您学到的内容：

-   如何在游戏中获得命令方块。
-   不同类型的命令方块的行为及其外观。
-   不同命令方块选项是什么（包括条件、状态和延迟。）
-   命令方块如何通过红石和聊天消息输出数据。
-   如何正确放置命令方块链。
-   如何解决'命令方块不工作'

:::

> 设置codeblock/mcfunction命令方块系统时，请始终记住，第一个命令方块将是![循环命令方块](/assets/images/commands/intro-to-command-blocks/command-block.repeat.png) **`无条件始终激活`**，其余命令方块将是![连锁命令方块](/assets/images/commands/intro-to-command-blocks/command-block.chain.png) **`无条件始终激活`**。除非另有说明，否则全部设置为0刻度延迟。
>
> ![四个命令方块链](/assets/images/commands/command-block-chain/4.png)

## 接下来是什么？

<CardGrid>
<Card title="基础系统" image="/assets/images/icons/command_block.png">

为了将您学到的知识付诸实践，尝试制作这个简单的**[玩家首次加入](/commands/on-first-join)**系统，或**[实体计数器](/commands/entity-counter)**系统。

</Card>

<Card title="理解选择器" link="/commands/selectors" image="/assets/images/icons/parrot.png">

学习在命令中使用目标选择器以及如何过滤它们。

</Card>
</CardGrid>
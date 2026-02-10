---
title: 在任意空间之间转换点（世界、实体、骨骼）
category: Tutorials
tags:
    - intermediate
mentions:
    - Johnb003
    - SmokeyStack
description: 在本页面中我们将讨论如何在Minecraft中转换不同的坐标系。
---

## 概述

在本页面中我们将讨论如何在Minecraft中转换不同的坐标系。您可能希望这样做的原因有很多：

-   如果您想要使用细长方块构建3D线来可视化某些3D空间，您需要从世界坐标转换到实体骨骼。
-   如果您想要准确地进行头部跟踪，并想要测量从实体上某个特定关节到目标的角度。
-   如果您想要从武器尖端发射抛射物。
-   如果您想要解决肢体的逆运动学链(IK)以匹配地面接触点。

## 背景知识

在我们进入指南之前，有一些背景话题值得介绍。

### 矩阵

通常当我们把所有内容发送到显卡时，我们会将它们转换为矩阵，因为这样可以非常高效地通过一组矩阵变换许多顶点（比如网格的顶点）。游戏通常使用其他表示方法，比如四元数，或者在Minecraft的情况下使用欧拉旋转，但首先最好对矩阵有扎实的理解。

如果您看到一个充满各种值的4x4矩阵，如果您不习惯处理它们，可能需要特殊能力才能看透它们，就像这样。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-1.png" />

但是，通常我们处理的3D变换矩阵实际上只是一个位置偏移和一个由"轴向量"表示的旋转，这些轴向量描述了X、Y和Z轴，每个都是一个3分量向量。

当写入3x3矩阵时，方向向量是单位长度向量（除非有缩放），这些向量就像您习惯看到的任何方向向量一样，它们有x、y、z分量定义该空间的轴向方向。

<WikiImage src="/assets/images/tutorials/entity-transforms/image.png" />

在3x3矩阵中（尤其是在教科书中），它们沿列排列，如下所示：

```
[ XAxis.x  YAxis.x  ZAxis.x ]
[ XAxis.y  YAxis.y  ZAxis.y ]
[ XAxis.z  YAxis.z  ZAxis.z ]
```

在程序中，您总是会将数据存储在内存中，格式为`<XAxis.x, XAxis.y, XAxis.z, YAxis.x, YAxis.y, YAxis.z, ZAxis.x, ZAxis.y, ZAxis.z>`，无论您是行主序还是列主序([关于行与列主序的维基](https://en.wikipedia.org/wiki/Row-_and_column-major_order))，这意味着如果您是行主序，您将XAxis沿行放置。如果您的数学库遵循规则，那么它会影响**事物相乘的顺序**：

对于所有的矩阵乘法，总是行×列。

所以，对于**行主序**，要进行向量和矩阵的乘法：row_vector * matrix = row_vector：

<WikiImage src="/assets/images/tutorials/entity-transforms/image-2.png" />

对于**列主序**，要进行与行主序相同的乘法，您需要颠倒顺序：

<WikiImage src="/assets/images/tutorials/entity-transforms/image-3.png" />

以上区分确实很重要，特别是如果您在线阅读一些材料时，能够理解经此变换的数据受影响的顺序。

所以这意味着，如果我们有一个相对于右手的位置，想知道它在世界中的位置，我们首先需要用右手变换它，然后是右肘，然后是右肩、脊柱、骨盆、根部、实体等，直到它在世界空间中。用列主序写出来就是：

```
Entity * RootBone * Pelvis * Spine0 .. SpineN * RShoulder * RightElbow * RightHand * point;
```

如果您涉及更复杂的变换，强烈建议将变换命名以反映它们从哪个空间变换到哪个空间。因此对于上面的例子，我们会使用类似这样的：

```
World_To_Entity * Entity_To_Root * Root_To_Pelvis * Pelvis_To_Spine0 .. Spine(N-1)_To_SpineN * SpineN_To_RShoulder * RShoulder_To_RElbow * RElbow_To_RHand * RHand_point;
```

我们就不容易混淆我们处于哪个"空间"。上面描述的是"局部空间"。每个关节相对于父关节。但如果我们将从`Root`到`RHand`的所有内容相乘，得到的变换仍然是`RHand`变换，只是在"角色或实体空间"中。要将其转换到世界空间，我们必须乘以`World_To_Entity * Entity_To_RHand = World_To_RHand`

从技术上讲，这种"To"命名有点混乱，因为实体变换应用到一个点上实际上是变换`EntityToWorld`，但它是从右到左应用的，所以`World_To_Entity`从右到左读就是`Entity_To_World`。我也见过：`WorldFromEntity * EntityFromRoot`。无论使用"From vs To"哪种，使用这种相对而非绝对命名的好处是，当您看到它写出来时，您可以验证数学是否正确：

```
A_To_B = A_To_Something * Something_To_B
              ^^^^^^^^^^^^^^^^^^^^^
```

标记区域：`^` 应该始终保持一致。结合并删除中间部分会给您它实际功能的自然名称。

最后，转向Minecraft。在撰写本文时，Minecraft没有可以相乘的变换，所以它们没有推断列或行主序符号。您只需获得一个变换，如何应用它取决于您。但是，选择了上面的列主序后，我们可以写下操作的工作顺序，只要按顺序应用，就没问题。

此外，无论我们使用TRS（变换-旋转-缩放组合对象）、四元数、矩阵还是欧拉角，当涉及旋转时，顺序确实重要。先旋转A再旋转B，与先旋转B再旋转A是不同的。

## 开始之前先了解世界

让我们想想在未旋转的标准情况下这些轴向量。实际上我们可以确定正X、Y和Z的方向，通过在世界中移动。当您第一次进入游戏时，您面朝正北，游戏称之为北。如果您向前走，您会看到这增加了您的Z值。如果您跳跃，您会看到Y增加。剩下X，这不对！不，这没错，而是左边！当您继续面朝北时，如果您向左平移，这会增加您的X值。这是一个右手坐标系（如果您指向某个轴，弯曲手指到下一个连续轴，您的拇指指向第三个轴。XY->Z，YZ->X，Z的循环：ZX->Y）。

## 最后！！让我们从一个实体开始

创建实体时，我建议从blockbench开始，制作的第一个实体是一个简单的3轴框架，如下所示：

<WikiImage src="/assets/images/tutorials/entity-transforms/image-4.png" />

此时需要注意几个奇怪的事情。请密切关注Blockbench标记的"北、东、南、西"方向。

1. 首要注意的是，这与Minecraft世界坐标系旋转了180度。您应该让您的实体面朝"北"，这相对于游戏世界坐标来说是负Z。
2. 这变得更奇怪了。如果您在正X方向移动盒子，盒子坐标显示为正值，但如果您创建骨骼并转到动画选项卡并尝试调整骨骼位置，移动手柄仍然如预期，但现在在正方向拖动手柄实际上会给X带来负值。动画中的+X向西，而+Y仍向上，+Z仍向南。因此与Minecraft世界相比，我们必须翻转Z，并开始在实体中使用左手坐标系。
3. 实体中有16倍的缩放因子。世界中的"方块"或1个单位，实际上在实体中是16个单位。

让我们尝试将我们的3轴东西放在一个骨骼下。
然后复制该组，这样我们有一个保留在实体上，一个可以移动到世界位置。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-5.png" />

1. 转到动画选项卡。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-6.png" />

2. 创建新动画。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-7.png" />

3. 为移动器添加位置关键帧。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-8.png" />

4. 尝试在X轴上移动并确认事情很奇怪。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-9.png" />

6. 现在让我们使用变量设置位置，我们将在`pre_animation`脚本中设置。

<WikiImage src="/assets/images/tutorials/entity-transforms/image-11.png" />

7. 您可以从最小机器人示例中混合搭配，了解如何开始使用您的实体。您还将保存blockbench几何体、动画。在行为方面，目前您不需要太多。也许只是：

```json
"minecraft:physics": {},
"minecraft:collision_box": {},
```

在实体方面，您只需要播放动画的最低要求：

```json
"animations": {
    "myAnim": "animation.tut_transform.move"
},
"scripts": {
    "pre_animation": [
        "// TODO -- 我们接下来将填写这个。"
    ],
    "animate": [
        "myAnim"
    ]
}
```

最后对于脚本，我们可以这样做：

```json
"
v.target.x = 10;
v.target.y = q.position(1);
v.target.z = 10;

v.target.x = v.target.x - q.position(0);
v.target.y = v.target.y - q.position(1);
v.target.z = v.target.z - q.position(2);

t.cos_yaw = math.cos(q.body_y_rotation);
t.sin_yaw = math.sin(q.body_y_rotation);
t.x = v.target.x;
v.target.x=t.cos_yaw * t.x + t.sin_yaw * v.target.z;
v.target.z=-t.sin_yaw * t.x + t.cos_yaw * v.target.z;

v.target.x = v.target.x * 16;
v.target.y = v.target.y * 16;
v.target.z = -v.target.z * 16;
"
```

让我们稍微分解一下：

pre_animation在动画之前运行，并运行此脚本。脚本的目的是给定一个位置，也许您的插件会通过脚本从行为中通过参数提供输入？

目前您可以将这些硬编码为世界位置10,y,10，其中y与实体的高度相同。

```molang
v.target.x = 10;
v.target.y = q.position(1);
v.target.z = 10;
```

所以一般来说，我们正在应用"TRS"变换、旋转和缩放，从一个空间转换到另一个空间。

从技术上讲，请记住我们会这样写变换堆栈：

```
Translation * RotationZ * RotationY * RotationX * Scale * point;
```

但在我们的情况下，不是从实体到世界进行变换，而是从世界到实体进行变换。当我们有这种可逆的非交换数学运算时，应用此逆变换的方法如下：

```
inverse(A*B) = inverse(B) * inverse(A)
```

这意味着，对操作取反，并以相反顺序执行。所以我们按此顺序对向量应用这些操作。

1. inverse(Translation)
2. inverse(RotationZ)
3. inverse(RotationY)
4. inverse(RotationX)
5. inverse(Scale)

数学上看起来像：

```
Inverse(Scale) * Inverse(RotationX) * Inverse(RotationY) * Inverse(RotationZ) * Inverse(Translation) * point;
```

这是从右到左进行的。

#### 1: Inverse(Translation)

如果通常（向前方向）您从实体到世界，您会取实体的相对位置（比如关节位置）并加上实体的。所以在反向中，我们减去实体的位置。

```molang
v.target_x = v.target_x - q.position(0);
v.target_y = v.target_y - q.position(1);
v.target_z = v.target_z - q.position(2);
```

#### 2: Inverse(RotationZ)

目前实体似乎只能通过控制器调整俯仰和偏航。所以没有"Z"。所以我们跳过这一步。

#### 3: Inverse(RotationY)

要查询实体的偏航，有一个查询方法：q.body_y_rotation。我们将使用这个，但要考虑正旋转应该使角色向左转。用角度旋转向量是一个相当简单的公式，使用sin和cos，但正确设置符号很重要。想想如果您有一个最初面向世界+z的向量，然后向左旋转，+X轴最初会变成正还是负？在我们的情况下，X左实际上是世界空间，所以X左是正的。同样，如果我们有一个面向正+X（向左）的向量，然后开始向左旋转，Z轴会开始变成什么？... 是的，负数。您只否定正弦项。最后一点注意，t.x是一个临时变量，用于保存目标值

```molang
t.cos_yaw = math.cos(q.body_y_rotation);
t.sin_yaw = math.sin(q.body_y_rotation);
t.x = v.target_x;
v.target_x=t.cos_yaw * t.x + t.sin_yaw * v.target_z;
v.target_z=-t.sin_yaw * t.x + t.cos_yaw * v.target_z;
```

注意，通常我喜欢这样写：

```
new_first_axis = cos(angle) * first_axis - sin(angle) * second_axis;
new_second_axis = sin(angle) * first_axis + cos(angle) * second_axis;
```

其中第一轴和第二轴是旋转轴的两个垂直轴，但按右手顺序。所以：XY、YZ或ZX。

换句话说，这里是另一种替代方法，将与其他欧拉角更一致，XY和YZ。

```molang
t.cos_yaw = math.cos(q.body_y_rotation);
t.sin_yaw = math.sin(q.body_y_rotation);
t.z = v.target.z;
v.target.z=t.cos_yaw * t.z - t.sin_yaw * v.target.x;
v.target.x=t.sin_yaw * t.z + t.cos_yaw * v.target.x;
```

#### 4: Inverse(RotationX)

我认为实体确实可以俯仰，但这在实践中我没有见过。所以我跳过了。

这是读者的练习，不过稍后在涉及骨骼变换时会有更多信息，因此您可以从该部分获得更多上下文（当我讲到那里时）。

#### 5: Inverse(Scale)

最后一步是从世界到实体应用缩放。实体需要用较小的单位表示，所以在这个方向的操作是乘法。如果我们从实体到世界，那就是除以16。

这里还包括一个偷偷摸摸的小否定，正如指出的，从技术上讲，X在Blockbench动画时与看起来应该的相反，但这实际上与世界坐标系对齐，而Z仍然相反。所以在缩放步骤中我们只需翻转Z。

```molang
v.target.x = v.target.x * 16;
v.target.y = v.target.y * 16;
v.target.z = -v.target.z * 16;
```
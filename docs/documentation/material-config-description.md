---
title: 材质配置说明
tags:
    - expert
mentions:
    - MedicalJewel105
    - SmokeyStack
description: 本文将详细介绍材质文件的结构和配置。
---

:::warning
材质并不适合初学者。请准备好应对潜在的崩溃、内容日志错误和长时间加载。
:::

## 前言

本文翻译自 https://mc.163.com/dev/mcmanual/mc-dev/mcguide/ - 由网易（中国版开发者）提供。文章将详细介绍材质文件的结构和配置。

## 材质文件

我们将解释原生微软的材质文件。首先，目录下的文件基本上是带有".material"后缀的文件。此外，还有三个重要的json文件，分别是common.json、fancy.json、sad.json。

让我们先看看sad.json和fancy.json。它们用于控制图像质量表现。每个文件都定义了材质文件列表。fancy.json通常比sad.json定义了更多的材质文件，并且可能为一些材质文件添加了一些额外的宏，着色器可以通过判断这些宏来进行特殊处理：

<CodeHeader>sad.json</CodeHeader>

```json
[
    { "path": "materials/sad.material" },
    { "path": "materials/entity.material" },
    { "path": "materials/terrain.material" },
    { "path": "materials/portal.material" },
    { "path": "materials/barrier.material" },
    { "path": "materials/wireframe.material" }
]
```

<CodeHeader>fancy.json</CodeHeader>

```json
[
    { "path": "materials/fancy.material", "+defines": ["FANCY"] },
    { "path": "materials/entity.material", "+defines": ["FANCY"] },
    { "path": "materials/terrain.material", "+defines": ["FANCY"] },
    { "path": "materials/hologram.material" },
    { "path": "materials/portal.material", "+defines": ["FANCY"] },
    { "path": "materials/barrier.material" },
    { "path": "materials/wireframe.material" }
]
```

可以看出，fancy.json比sad.json定义了更多的fancy.material和hologram.material材质文件，并且还为多个材质文件定义了FANCY宏。游戏中设置/视频/精致纹理的开关就是控制sad和fancy之间的切换。当开启精致纹理开关时，fancy.json中的材质文件会生效，关闭时，sad.json中的材质文件会生效。

为了获得更好的性能，fancy.json中的材质文件通常具有更复杂的操作，而sad.json中的材质通常牺牲一些渲染性能以换取更好的性能。如果开发者需要编写更复杂的着色器，建议同时编写一个低成本版本，然后分别在fancy和sad中定义。让玩家通过游戏中的精致纹理选项控制是否开启相应效果。

<CodeHeader>common.json</CodeHeader>

```json
[
    { "path": "materials/particles.material" },
    { "path": "materials/shadows.material" },
    { "path": "materials/sky.material" },
    { "path": "materials/ui.material" },
    { "path": "materials/ui3D.material" },
    { "path": "materials/portal.material" },
    { "path": "materials/barrier.material" },
    { "path": "materials/wireframe.material" }
]
```

与sad和fancy可以相互切换不同，common.json中定义的材质文件将在进入游戏后加载。除了在common.json、sad.json、fancy.json中声明的材质文件外，其他材质文件均不会被加载。

## 材质语法

我们使用材质文件中的一个实体材质文件entity.material来解释，打开文件，可以看到文件以materials开头，然后定义版本号version为1.0.0，这些都是固定格式，它们标识了此材质文件的解析方式，我们可以暂时忽略它而不修改它。

可以看到材质中每个字段的定义都是键值对的形式，例如：

```json
[
	"vertexShader": "shaders/entity.vertex",
]
```

冒号左侧表示键vertexShader，右侧表示值shaders/entity.vertex；

还有列表形式的定义：

```json
[
	"vertexFields": [
        { "field": "Position" },
        { "field": "Normal" },
        { "field": "UV0" }
    ],
]
```

用符号[ ]声明的是列表，然后里面是每个子元素的json定义。

## 材质所有属性字段概述

### 渲染状态

#### `states`

配置渲染环境，它可以有以下值：

-   `EnableAlphaToCoverage`：一种用于半透明物体的独立于顺序的渲染方法。此开关仅在支持MSAA的环境中有效。启用后，物体边缘将根据透明度更准确地软化和过渡。它也可以用于一些具有大量网格重叠的复杂场景。

-   `Wireframe`：绘制线框模式

-   `Blending`: 启用颜色混合模式，通常用于渲染半透明对象。声明此选项后，通常需要声明混合因子blendSrc, blendDst

-   `DisableColorWrite`：不将颜色值写入颜色缓冲区，RGBA通道都不写入

-   `DisableAlphaWrite`：不将透明度alpha值写入颜色缓冲区，允许写入RGB值

-   `DisableRGBWrite`：不将透明度RGB值写入颜色缓冲区，允许写入alpha值

-   `DisableDepthTest`：关闭深度测试

-   `DisableDepthWrite`：关闭深度写入

-   `DisableCulling`: 同时渲染正面和背面

-   `InvertCulling`：使用正面剔除。默认是背面剔除。声明此选项后，渲染背面并剔除正面。

-   `StencilWrite`: 启用模板掩码写入

-   `EnableStencilTest`：启用模板掩码测试

### 着色器路径

#### `vertexShader`

顶点着色器的路径，通常为shaders/XXX.vertex。

#### `vrGeometryShader`或`geometryShader`

几何着色器的路径，通常为shaders/XXX.geometry，在移动端不使用，不需要修改。

#### `fragmentShader`

片段着色器的路径，通常为shaders/XXX.fragment。

### 着色器宏定义

#### `defines`

为使用的着色器定义宏。为了代码复用，我们使用相同的着色器用于许多不同的材质。这时，如果你想根据当前材质在着色器中的某处执行不同的逻辑，可以通过材质defines声明的宏来判断。我们可以使用材质entity_for_skeleton作为例子。在这里我们可以看到定义了三个宏USE_SKINNING、USE_OVERLAY和NETEASE_SKINNING。

```json
"entity_for_skeleton": {
	"vertexShader": "shaders/entity.vertex",
	"vrGeometryShader": "shaders/entity.geometry",
	"fragmentShader": "shaders/entity.fragment",
	"+defines": [ "USE_SKINNING", "USE_OVERLAY", "NETEASE_SKINNING" ],
	"vertexFields": [
		{ "field": "Position" },
		{ "field": "Normal" },
		{ "field": "BoneId0" },
		{ "field": "UV0" }
	],
	"msaaSupport": "Both",
	"+samplerStates": [
		{
			"samplerIndex": 0,
			"textureFilter": "Point"
		}
	]
}
```

查看顶点着色器entity.vertex，会有#ifdef、#else、#endif来判断宏并执行不同的逻辑分支。这些宏的判断语句在编译时处理，不像传统的着色器中的if。Else，编译时处理的逻辑分支在实际运行中不会生成，而且由于分支的存在也不会降低性能。此外，如下所示，宏也可以进行多层判断。首先判断NETEASE_SKINNING宏，然后在内部执行逻辑中判断LARGE_VERTEX_SHADER_UNIFORMS宏：

```glsl
#ifdef NETEASE_SKINNING
		MAT4 boneMat = transpose(mat3x4ToMat4(BONES_70[int(BONEID_0)]));
		entitySpacePosition = boneMat * POSITION;
		entitySpaceNormal = boneMat * NORMAL;
#else
	#if defined(LARGE_VERTEX_SHADER_UNIFORMS)
		entitySpacePosition = BONES[int(BONEID_0)] * POSITION;
		entitySpaceNormal = BONES[int(BONEID_0)] * NORMAL;
	#else
		entitySpacePosition = BONE * POSITION;
		entitySpaceNormal = BONE * NORMAL;
	#endif
#endif
```

### 运行时状态

#### 深度测试

##### `depthFunc`

深度检测通过函数，可以使用以下值：

-   `Always`: 始终通过

-   `Equal`：当深度值等于缓冲区值时通过

-   `NotEqual`：当深度值不等于缓冲区值时通过

-   `Less`：当深度值小于缓冲区值时通过

-   `Greater`：当深度值大于缓冲区值时通过

-   `GreaterEqual`：当深度值大于或等于缓冲区值时通过

-   `LessEqual`：当深度值小于或等于缓冲区值时通过

关联状态渲染环境配置：

-   `DisableDepthTest`：关闭深度测试

-   `DisableDepthWrite`：关闭深度写入

#### 模板掩码测试

##### `stencilRef`

用于与掩码缓冲区比较或写入掩码缓冲区的值

##### `stencilRefOverride`

是否使用缓冲区的当前值作为stencilRef，支持0或1：

-   `1`：使用配置的stencilRef。如果配置了stencilRef，则stencilRefOverride将自动取1

-   `0`：使用缓冲区的当前值作为stencilRef，在这种情况下不配置stencilRef

##### `stencilReadMask`

掩码缓冲区值和stencilRef值在比较前与stencilReadMask进行按位与操作

##### `stencilWriteMask`

stencilRef值在写入掩码缓冲区前与stencilWriteMask进行按位与操作

##### `frontFace`和`backFace`

配置在网格的正面或背面使用哪种掩码测试函数。此外，判断顺序是先掩码检测，然后深度检测。需要配置以下操作：

<!-- Test if this looks ok -->

-   `stencilFunc`: stencilRef与掩码缓冲区比较时使用的方法，支持以下值：

    -   `Always`: 始终通过
    -   `Equal`：当stencilRef等于缓冲区值时通过
    -   `NotEqual`：当stencilRef不等于缓冲区值时通过
    -   `Less`：当stencilRef小于缓冲区值时通过
    -   `Greater`：当stencilRef大于缓冲区值时通过
    -   `GreaterEqual`：当stencilRef大于或等于缓冲区值时通过
    -   `LessEqual`：当stencilRef小于或等于缓冲区值时通过

-   `stencilFailOp`：当stencilFunc比较函数返回失败时执行的处理，支持以下值：

    -   `Keep`：保持缓冲区原始值
    -   `Replace`：将stencilRef位与stencilWriteMask的值写入缓冲区

-   `stencilDepthFailOp`：stencilFunc比较函数返回成功，但深度测试失败时执行的处理，支持以下值：

    -   `Keep`：保持缓冲区原始值
    -   `Replace`：将stencilRef位与stencilWriteMask的值写入缓冲区

-   `stencilPassOp`: stencilFunc比较函数返回成功，且深度测试成功时执行的处理，支持以下值：
    -   `Keep`：保持缓冲区原始值
    -   `Replace`：将stencilRef位与stencilWriteMask的值写入缓冲区

关联状态渲染环境配置：

-   `StencilWrite`：启用掩码写入

-   `EnableStencilTest`: 启用掩码测试

最后，让我们看一个例子：

```json
    "shadow_back": {
      "+states": [
        "StencilWrite",
        "DisableColorWrite",
        "DisableDepthWrite",
        "InvertCulling",
        "EnableStencilTest"
      ],

      "vertexShader": "shaders/position.vertex",
      "vrGeometryShader": "shaders/position.geometry",
      "fragmentShader": "shaders/flat_white.fragment",

      "frontFace": {
        "stencilFunc": "Always",
        "stencilFailOp": "Keep",
        "stencilDepthFailOp": "Keep",
        "stencilPassOp": "Replace"
      },

      "backFace": {
        "stencilFunc": "Always",
        "stencilFailOp": "Keep",
        "stencilDepthFailOp": "Keep",
        "stencilPassOp": "Replace"
      },

      "stencilRef": 1,
      "stencilReadMask": 255,
      "stencilWriteMask": 1,

      "vertexFields": [
        { "field": "Position" }
      ],
      "msaaSupport": "Both"
    }
```

在示例中，StencilWrite表示支持写入掩码缓冲区，EnableStencilTest表示开启掩码测试，frontFace的配置表示在渲染正面时掩码测试始终通过。如果深度测试失败，缓冲区值保持不变。如果也通过，则将模板位和stencilWriteMask的值写入缓冲区，即1 & 1 = 1值。backFace的配置也类似。

#### Blend 半透明对象颜色混合

半透明对象的渲染需要配置混合因子。最终输出rgb颜色值 = 当前颜色值 _ 源混合因子 + 缓冲区中的颜色值 _ 目标混合因子

##### `blendSrc`

源混合因子

##### `blendDst`

目标混合因子

##### `alphaSrc`

计算alpha时的源混合因子，通常不配置以采用默认值

##### `alphaDst`

计算alpha时的目标混合因子，通常不配置以采用默认值

总共来说，混合因子可以取以下值：

-   `DestColor`：缓冲区颜色值

-   `SourceColor`：当前颜色值

-   `Zero`：(0,0,0)

-   `One`：(1,1,1)

-   `OneMinusDestColor`: (1,1,1) - 缓冲区颜色值

-   `OneMinusSrcColor`: (1,1,1) - 当前颜色值

-   `SourceAlpha`：当前颜色中的alpha值

-   `DestAlpha`：缓冲区颜色中的alpha值

-   `OneMinusSrcAlpha`：1 - 当前颜色值中的alpha值

在引擎中，默认为：

-   `blendSrc`：SourceAlpha

-   `blendDst`：OneMinusSrcAlpha

-   `alphaSrc`：One

-   `alphaDst`：OneMinusSrcAlpha

关联状态渲染环境配置：

-   `Blending`: 启用颜色混合模式，通常用于渲染半透明对象。声明此选项后，通常需要声明混合因子blendSrc, blendDst

-   `DisableColorWrite`：不将颜色值写入颜色缓冲区，RGBA通道都不写入

-   `DisableAlphaWrite`：不将透明度alpha值写入颜色缓冲区，允许写入RGB值

-   `DisableRGBWrite`：不将透明度RGB值写入颜色缓冲区，允许写入alpha值

#### Sample 纹理采样

##### `samplerStates`

配置采样状态，值为列表，根据要采样的纹理数量配置每个纹理。通常，如果在顶点属性中声明了UV0和UV1，意味着需要采样两个纹理，这里需要配置两个元素。让我们看看子元素的定义：

```json
{
    "samplerIndex": 0,
    "textureFilter": "Point",
    "textureWrap": "Repeat"
}
```

每个属性定义如下：

##### `samplerIndex`

数字，表示当前正在设置的纹理属性，从0开始

##### `textureFilter`

纹理过滤模式（默认为Point），当实际显示的纹理贴图与原图相比放大或缩小时，新分辨率图与原分辨率图上的像素之间的映射关系可以有以下值：

-   `Point`：点采样

-   `Bilinear`: 双线性采样

-   `Trilinear`: 三线性采样

-   `MipMapBilinear`：MipMap双线性采样

-   `TexelAA`：Texel抗锯齿（并非所有设备都支持，不推荐）

-   `PCF`：按比较函数采样（并非所有设备都支持，不推荐）

##### `textureWrap`

纹理包裹模式，控制当uv超出[0,1]时应该采样什么样的纹理。它可以有以下值：

-   `Repeat`：重复，即对值取模到[0, 1]进行采样

-   `Clamp`：边缘采样，采样最近边缘的值，即如果1.1更接近1，则取1；如果-0.1更接近0，则取0。

#### 顶点

##### `vertexFields`

顶点属性，用于声明使用此材质渲染的网格的每个顶点持有哪些属性。在美术制作资源时确定。可能使用以下值：

-   `Position`：模型空间坐标

-   `Color`：颜色

-   `Normal`：法线

-   `UV0`: 纹理采样坐标

-   `UV1`：纹理采样坐标

-   `UV2`：纹理采样坐标

-   `BoneId0`：骨骼ID，用于骨骼模型

#### 光栅化器环境配置

##### `msaaSupport`

配置MSAA（多重采样抗锯齿）支持（引擎中默认为NonMSAA）

-   `NonMSAA`: 当MSAA未启用时允许材质

-   `MSAA`: 当MSAA启用时允许材质

-   `Both`：无论是否启用MSAA都允许材质。通常只使用此值。

##### `depth offset`

深度偏移主要用于解决z-fighting问题，即当两个物体的深度相似时，某些帧可能显示这个物体，而另一些帧显示另一个物体。深度偏移的原理是在较大或较小深度方向上偏移其中一个物体，使它们的深度不再相同。可以配置以下四个变量：

-   depthBias

-   slopeScaledDepthBias

-   depthBiasOGL

-   slopeScaledDepthBiasOGL

具体偏移深度为：

`offset = (slopeScaledDepthBias * m) + (depthBias * r)`

在OGL平台上为：

`offset = (slopeScaledDepthBiasOGL * m) + (depthBiasOGL * r)`

m是多边形深度斜率的最大值（在光栅化阶段计算）。多边形与近裁剪平面越平行，m越接近0。r是在窗口坐标系中产生可辨识深度值差异的最小值，r是由实现OpenGL的平台指定的常量。

关联状态渲染环境配置：

-   `Wireframe`：绘制线框模式

-   `DisableCulling`: 同时渲染正面和背面

-   `InvertCulling`：使用正面剔除。默认是背面剔除。声明此选项后，渲染背面并剔除正面。

#### 图元

##### `primitiveMode`

图元渲染模式（引擎中默认为TriangleList）：

-   `None`：不渲染，通常不使用

-   `QuadList`：四边形模式

-   `TriangleList`: 每三个顶点绘制一个三角形的模式，例如第一个三角形使用顶点v0、v1、v2，第二个使用v3、v4、v5

-   `TriangleStrip`: 每个顶点将与前两个顶点形成一个三角形，结构稍微复杂一些，但会节省数据量

-   `LineList`: 每两个顶点绘制一条线段

-   `Line`: 每个顶点与之前出现的顶点形成一条线段。

### 材质变体

#### `variants`

用于基于大部分相同定义快速实现多个子材质。参见下面entity_static的实际示例：

```json
    "entity_static": {
      "vertexShader": "shaders/entity.vertex",
      "vrGeometryShader": "shaders/entity.geometry",
      "fragmentShader": "shaders/entity.fragment",
      "vertexFields": [
        { "field": "Position" },
        { "field": "Normal" },
        { "field": "UV0" }
      ],
      "variants": [
        {
          "skinning": {
            "+defines": [ "USE_SKINNING" ],
            "vertexFields": [
              { "field": "Position" },
              { "field": "BoneId0" },
              { "field": "Normal" },
              { "field": "UV0" }
            ]
          }
        },
        {
          "skinning_color": {
            "+defines": [ "USE_SKINNING", "USE_OVERLAY" ],
            "+states": [ "Blending" ],
            "vertexFields": [
              { "field": "Position" },
              { "field": "BoneId0" },
              { "field": "Color" },
              { "field": "Normal" },
              { "field": "UV0" }
            ]
          }
        }
      ],
      "msaaSupport": "Both",
      "+samplerStates": [
        {
          "samplerIndex": 0,
          "textureFilter": "Point"
        }
      ]
    }
```

Variants是材质变体的声明。上述声明定义了两个子变体，skinning和skinning_color。在子变体中重写了外部字段。在实际使用中，相当于快速定义了两种材质。主体和变体之间用点"."连接。两种材质是entity_static.skinning和entity_static.skinning_color。

此外，如果有其他材质继承自entity_static，如entity_dynamic，此材质也会继承这两个变体，entity_dynamic.skinning和entity_dynamic.skinning_color。

## 材质合并规则

当在不同目录文件中声明相同的材质时，加载后将按照以下规则进行合并：1. 通常情况下，后加载的文件的材质字段将覆盖先前加载的。2. 以下字段是特殊的。支持使用"+"添加属性和使用"-"删除属性的操作：

-   defines
-   states
-   samplerStates

举例说明，例如在包体文件中声明了这样一个材质（无关代码省略），定义了三个宏：

```json
"testMat": {
	"defines": [ "MACRO_1", "MACRO_2", "MACRO_3" ],
}
```

此时，一个MOD也声明了这个材质，定义了另外三个宏：

```json
"testMat": {
	"defines": [ "MACRO_4", "MACRO_5", "MACRO_6" ],
}
```

在上述情况下，最终运行时相当于defines字段被覆盖，实际运行时生效的宏仅为：MACRO_4, MACRO_5, MACRO_6

如果在MOD中定义时使用"+"符号：

```json
"testMat": {
	"+defines": [ "MACRO_4", "MACRO_5", "MACRO_6" ],
}
```

相当于在原有基础上添加定义，实际运行时生效的宏为：MACRO_1, MACRO_2, MACRO_3, MACRO_4, MACRO_5, MACRO_6

如果在MOD中定义时使用"-"符号：

```json
"testMat": {
	"-defines": [ "MACRO_3"],
}
```

相当于在原有基础上删除一些定义，运行时生效的宏仅为：MACRO_1, MACRO_2

如果多个文件定义了相同的材质，并且涉及覆盖、添加和删除操作，它们生效的顺序为：首先执行所有覆盖操作，然后执行所有添加操作，最后执行所有删除操作。

即如果其中一个材质文件声明了删除MACRO_3操作：

```json
"testMat": {
	"-defines": [ "MACRO_3"],
}
```

那么无论其他文件如何覆盖、添加MACRO_3，此材质在最终合成后都不得有MACRO_3宏。
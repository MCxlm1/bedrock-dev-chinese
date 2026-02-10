---
title: 脚本介绍
category: General
nav_order: 1
mentions:
    - cda94581
    - Herobrine643928
    - JaylyDev
    - SmokeyStack
    - kumja1
    - realfeatherdev
    - QuazChick
    - jeanmajid
    - AndroAGENT
description: 学习如何通过JavaScript文件修改您的世界。
---

::: tip
此脚本API页面假设您具备附加包设置的基本知识。有关如何设置行为包的信息，请参阅[附加包介绍](/guide/introduction)页面。
:::

::: warning
脚本API目前正在积极开发中，经常会有重大变化。本页面假定为Minecraft 1.21.114格式。
:::

## 概述

脚本API（前身为GameTest框架）是一项功能，允许创建新的创作种类，这些创作使用行为包文件夹中的JavaScript文件构建。脚本API的大部分功能都不是实验性的。

本页面将为您介绍日常使用中会用到的Minecraft创作者API概念的80%。

## 创建您的第一个项目

目前，脚本只能在行为包中使用。

在行为包清单中，您需要添加一个`script`模块并为脚本项目设置一个`entry`入口点。目前，仅支持`"javascript"`作为有效的语言。

<CodeHeader>BP/manifest.json#modules[0]</CodeHeader>

```json
{
    "uuid": "239c134f-67bf-4738-9bcc-8c69d31b1f72",
    "version": "1.0.0",
    "type": "script",
    "language": "javascript",
    "entry": "scripts/main.js"
}
```

此外，需要根据所使用的模块设置依赖关系。要导入和使用脚本API模块，必须使用`module_name`和`version`指定依赖关系。在此示例中，使用了`@minecraft/server`模块。

<CodeHeader>BP/manifest.json</CodeHeader>

```json
{
    "format_version": 2,
    "header": {
        "name": "Bedrock Add-ons",
        "description": "Script API Template",
        "uuid": "<UUID>",
        "version": "1.0.0",
        "min_engine_version": [1, 21, 114]
    },
    "modules": [
        {
            "uuid": "<UUID>",
            "version": "1.0.0",
            "type": "script",
            "language": "javascript",
            // 您的入口文件；Minecraft将从此处读取您的代码。
            "entry": "scripts/main.js"
        }
    ],
    // 在代码中启用eval()和Function()（不推荐），如不需要请删除
    // "capabilities": ["script_eval"],
    "dependencies": [
        {
            // 启用@minecraft/server模块，版本为2.2.0（可用的最新稳定版本）。
            "module_name": "@minecraft/server",
            "version": "2.3.0"
        }
    ]
}
```

如果您的项目需要其他模块来运行您的代码，请添加遵循上述格式的其他依赖项。

**稳定API模块**，这些不需要开启Beta APIs实验。大多数功能都包含在稳定API中，并且当Minecraft更新时不会被破坏或更改。

-   `@minecraft/server`:

    -   [`2.3.0`](https://www.npmjs.com/package/@minecraft/server/v/2.3.0) (最新版本)
    -   [`2.2.0`](https://www.npmjs.com/package/@minecraft/server/v/2.2.0)
    -   [`2.1.0`](https://www.npmjs.com/package/@minecraft/server/v/2.1.0)
    -   [`2.0.0`](https://www.npmjs.com/package/@minecraft/server/v/2.0.0)
    -   [`1.19.0`](https://www.npmjs.com/package/@minecraft/server/v/1.19.0)
    -   [`1.18.0`](https://www.npmjs.com/package/@minecraft/server/v/1.18.0)
    -   [`1.17.0`](https://www.npmjs.com/package/@minecraft/server/v/1.17.0)
    -   [`1.16.0`](https://www.npmjs.com/package/@minecraft/server/v/1.16.0)
    -   [`1.15.0`](https://www.npmjs.com/package/@minecraft/server/v/1.15.0)
    -   [`1.14.0`](https://www.npmjs.com/package/@minecraft/server/v/1.14.0)
    -   [`1.13.0`](https://www.npmjs.com/package/@minecraft/server/v/1.13.0)
    -   [`1.12.0`](https://www.npmjs.com/package/@minecraft/server/v/1.12.0)
    -   [`1.11.0`](https://www.npmjs.com/package/@minecraft/server/v/1.11.0)
    -   [`1.10.0`](https://www.npmjs.com/package/@minecraft/server/v/1.10.0)
    -   [`1.9.0`](https://www.npmjs.com/package/@minecraft/server/v/1.9.0)
    -   [`1.8.0`](https://www.npmjs.com/package/@minecraft/server/v/1.8.0)
    -   [`1.7.0`](https://www.npmjs.com/package/@minecraft/server/v/1.7.0)
    -   [`1.6.0`](https://www.npmjs.com/package/@minecraft/server/v/1.6.0)
    -   [`1.5.0`](https://www.npmjs.com/package/@minecraft/server/v/1.5.0)
    -   [`1.4.0`](https://www.npmjs.com/package/@minecraft/server/v/1.4.0)
    -   [`1.3.0`](https://www.npmjs.com/package/@minecraft/server/v/1.3.0)
    -   [`1.2.0`](https://www.npmjs.com/package/@minecraft/server/v/1.2.0)
    -   [`1.1.0`](https://www.npmjs.com/package/@minecraft/server/v/1.1.0)
    -   [`1.0.0`](https://www.npmjs.com/package/@minecraft/server/v/1.0.0)

-   `@minecraft/server-ui`:

    -   [`2.0.0`](https://www.npmjs.com/package/@minecraft/server-ui/v/2.0.0) (最新版本，需要`@minecraft/server@2.0.0`或更高版本)
    -   [`1.3.0`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.3.0) (需要`@minecraft/server@1.3.0`或更高版本)
    -   [`1.2.0`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.2.0) (需要`@minecraft/server@1.2.0`或更高版本)
    -   [`1.1.0`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.1.0) (需要`@minecraft/server@1.2.0`或更高版本)
    -   [`1.0.0`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.0.0) (需要`@minecraft/server@1.2.0`)

**Beta API模块**需要在世界设置中开启"Beta APIs"实验。这些API可能会在很少通知的情况下被更改、删除或添加，容易出现破坏性变更。请注意！

::: tip
从v1.21.120开始，您现在可以将依赖项`version`属性设置为`"beta"`{lang=json}以始终选择每个原生模块的最新beta版本。
:::

-   `@minecraft/server`:

    -   [`2.3.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/2.3.0-beta.1.21.114-stable) (1.21.114)
    -   [`2.2.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/2.2.0-beta.1.21.102-stable) (1.21.102)
    -   [`2.1.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/2.1.0-beta.1.21.95-stable) (1.21.95)
    -   [`2.0.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/2.0.0-beta.1.21.84-stable) (1.21.84)
    -   [`1.18.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.18.0-beta.1.21.62-stable) (1.21.62)
    -   [`1.17.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.17.0-beta.1.21.51-stable) (1.21.51)
    -   [`1.16.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.16.0-beta.1.21.44-stable) (1.21.44)
    -   [`1.15.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.15.0-beta.1.21.31-stable) (1.21.31)
    -   [`1.14.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.14.0-beta.1.21.23-stable) (1.21.23)
    -   [`1.12.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.12.0-beta.1.21.3-stable) (1.21.3)
    -   [`1.11.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.11.0-beta.1.20.80-stable) (1.21.80)
    -   [`1.10.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.10.0-beta.1.20.70-stable) (1.20.70)
    -   [`1.9.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.9.0-beta.1.20.60-stable) (1.20.60)
    -   [`1.8.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.8.0-beta.1.20.50-stable) (1.20.50)
    -   [`1.7.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.7.0-beta.1.20.40-stable) (1.20.40)
    -   [`1.6.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.6.0-beta.1.20.30-stable) (1.20.30)
    -   [`1.4.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.4.0-beta.1.20.10-stable) (1.20.10)
    -   [`1.3.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.3.0-beta.1.20.0-stable) (1.20.0)
    -   [`1.2.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.2.0-beta.1.19.80-stable) (1.19.80)
    -   [`1.1.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.1.0-beta.1.19.70-stable) (1.19.70)
    -   [`1.0.0-beta`](https://www.npmjs.com/package/@minecraft/server/v/1.0.0-beta.1.19.40-stable) (1.19.40)

-   `@minecraft/server-ui`:

    -   [`2.1.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/2.1.0-beta.1.21.114-stable) (1.21.114)
    -   [`2.0.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/2.0.0-beta.1.21.84-stable) (1.21.84)
    -   [`1.4.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.4.0-beta.1.21.62-stable) (1.21.62)
    -   [`1.3.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.3.0-beta.1.21.23-stable) (1.21.23)
    -   [`1.2.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.2.0-beta.1.21.3-stable) (1.21.3)
    -   [`1.1.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.1.0-beta.1.20.0-stable) (1.20.0)
    -   [`1.0.0-beta`](https://www.npmjs.com/package/@minecraft/server-ui/v/1.0.0-beta.1.19.80-stable) (1.21.23)

-   `@minecraft/server-gametest`:

    -   [`1.0.0-beta`](https://www.npmjs.com/package/@minecraft/server-gametest/v/1.0.0-beta.1.21.114-stable) (1.21.114)

-   `@minecraft/server-net`:

    -   [`1.0.0-beta`](https://www.npmjs.com/package/@minecraft/server-net/v/1.0.0-beta.1.21.114-stable) (1.21.114; 仅限基岩版专用服务器模块，必须在`permission.json`中启用，因为它默认未启用)

-   `@minecraft/server-admin`:

    -   [`1.0.0-beta`](https://www.npmjs.com/package/@minecraft/server-admin/v/1.0.0-beta.1.21.114-stable) (1.21.114; 仅限基岩版专用服务器模块)

-   `@minecraft/debug-utilities`:

    -   [`1.0.0-beta`](https://www.npmjs.com/package/@minecraft/debug-utilities/v/1.0.0-beta.1.21.114-stable) (1.21.114)

要在您的代码中使用`eval()`函数或`Function()`构造函数，可以在清单功能中添加以下内容：

<CodeHeader>BP/manifest.json</CodeHeader>

```json
{
    "capabilities": ["script_eval"]
}
```

入口点文件可以包含脚本和/或导入到其他脚本文件。只能指定一个入口点文件。

## 使用JS编写脚本

Minecraft的脚本引擎只运行JavaScript，就像任何其他JavaScript项目一样。请查看[使用TypeScript进行脚本编写](/scripting/typescript#script-api)以直接将TS编译为JavaScript。

<CodeHeader>BP/scripts/main.js</CodeHeader>

```js
// 此文件通过
// 在聊天中反复发送"Hello World"来演示代码正在工作

// 从"@minecraft/server"导入world和system组件，用于世界和游戏逻辑。
import { world, system } from "@minecraft/server";

// 创建并运行一个在每个Minecraft刻度时被调用的间隔
system.runInterval(() => {
    // 使用API的world.sendMessage函数向聊天发送"Hello World"
    world.sendMessage("Hello World");
}, 1);
```

## 脚本API 2.0.0

随着脚本API版本2的发布，脚本现在默认在世界加载之前执行。这意味着我们现在必须等到世界加载完成后才能运行大多数函数。

<CodeHeader>BP/scripts/main.js</CodeHeader>

```js
import { world } from "@minecraft/server";

// 等待世界加载完成
world.afterEvents.worldLoad.subscribe(() => {
    // 在聊天中发送一条消息
    world.sendMessage("World has loaded");

    // 导入其他js文件
    import("./path/to/file.js");
});
```

## 参考文档

官方文档托管在Microsoft Learn上，可在此处找到：

-   [`@minecraft/server`](https://learn.microsoft.com/minecraft/creator/scriptapi/mojang-minecraft/mojang-minecraft)
-   [`@minecraft/server-gametest`](https://learn.microsoft.com/minecraft/creator/scriptapi/mojang-gametest/mojang-gametest)
-   [`@minecraft/server-ui`](https://learn.microsoft.com/minecraft/creator/scriptapi/minecraft/server-ui/minecraft-server-ui)
-   [`@minecraft/server-admin`](https://learn.microsoft.com/minecraft/creator/scriptapi/mojang-minecraft-server-admin/mojang-minecraft-server-admin)
-   [`@minecraft/server-net`](https://learn.microsoft.com/minecraft/creator/scriptapi/mojang-net/mojang-net)
-   [`@minecraft/debug-utilities`](https://learn.microsoft.com/en-us/minecraft/creator/scriptapi/minecraft/debug-utilities/minecraft-debug-utilities)

Minecraft预览版中最新Beta API模块的官方typescript声明可在此处找到：

-   [`@minecraft/server`](https://www.npmjs.com/package/@minecraft/server/v/beta)
-   [`@minecraft/server-gametest`](https://www.npmjs.com/package/@minecraft/server-gametest/v/beta)
-   [`@minecraft/server-ui`](https://www.npmjs.com/package/@minecraft/server-ui/v/beta)
-   [`@minecraft/server-admin`](https://www.npmjs.com/package/@minecraft/server-admin/v/beta)
-   [`@minecraft/server-net`](https://www.npmjs.com/package/@minecraft/server-net/v/beta)
-   [`@minecraft/debug-utilities`](https://www.npmjs.com/package/@minecraft/debug-utilities/v/beta)

这些在编辑器中使用时可以提供增强的自动补全和验证。

-   bridge. v2: 内置GameTest支持。
-   Visual Studio Code: 安装Node.js和npm，然后在命令行中运行以下命令：

最新beta API模块：

```bash
npm i @minecraft/server@2.3.0-beta.1.21.114-stable
npm i @minecraft/server-ui@2.1.0-beta.1.21.114-stable
npm i @minecraft/server-gametest@1.0.0-beta.1.21.114-stable
npm i @minecraft/server-admin@1.0.0-beta.1.21.114-stable
npm i @minecraft/server-net@1.0.0-beta.1.21.114-stable
npm i @minecraft/debug-utilities@1.0.0-beta.1.21.114-stable
```

最新稳定API模块：

```bash
npm i @minecraft/server
npm i @minecraft/server-ui
```
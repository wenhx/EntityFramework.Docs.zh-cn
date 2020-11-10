---
title: 设计时服务-EF Core
description: Entity Framework Core 设计时服务的信息
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431228"
---
# <a name="design-time-services"></a>设计时服务

这些工具使用的某些服务仅在设计时使用。 这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。 若要替代其中某个服务 (例如，服务) 生成迁移文件，请将的实现添加 `IDesignTimeServices` 到启动项目。

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>引用 Microsoft.entityframeworkcore

Microsoft.entityframeworkcore 是 DevelopmentDependency 包。 这意味着依赖关系不会以可传递方式流向其他项目，并且你不能在默认情况下引用其类型。

为了引用其类型并重写设计时服务，请在项目文件中更新 PackageReference 项的元数据。

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

如果正在通过 Microsoft.entityframeworkcore 以可传递方式对包进行引用，则需要将显式 PackageReference 添加到包并更改其元数据。

## <a name="list-of-services"></a>服务列表

下面是设计时服务的列表。

服务                                                                              | 说明
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | 为相应的模型批注生成代码。
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | 有助于生成 c # 代码。
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | 为和单数形式单词。
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | 为迁移生成代码。
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | 用于管理迁移文件的主类。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | 从数据库创建数据库模型。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | 为模型生成代码。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | 生成 OnConfiguring 代码。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | 基架反向工程模型的主类。

## <a name="using-services"></a>使用服务

这些服务也可用于创建自己的工具。 例如，当你想要自动执行部分设计时工作流时。

可以使用 AddEntityFrameworkDesignTimeServices 和 AddDbContextDesignTimeServices 扩展方法生成包含这些服务的服务提供程序。

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]

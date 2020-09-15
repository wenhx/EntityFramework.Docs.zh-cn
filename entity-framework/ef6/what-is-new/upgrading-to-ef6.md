---
title: 升级到实体框架 6-EF6
description: 升级到实体框架6
author: divega
ms.date: 10/23/2016
uid: ef6/what-is-new/upgrading-to-ef6
ms.openlocfilehash: 118853ad9b53fe2157f50b329bddc31d3135d75a
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074037"
---
# <a name="upgrading-to-entity-framework-6"></a>升级到实体框架6

在以前版本的 EF 中，代码是在核心库之间拆分的 (主要 System.Data.Entity.dll) 作为 .NET Framework 和带外 (OOB) 库的一部分提供， (主要 EntityFramework.dll 在 NuGet 包中提供的) 。 EF6 采用核心库中的代码，并将其合并到 OOB 库中。 此操作是必需的，以便使 EF 成为开源，使其能够以不同于 .NET Framework 的步调发展。 这样做的结果是需要针对已移动的类型重新生成应用程序。

对于使用 EF 4.1 和更高版本中随附的 DbContext 的应用程序，这应该非常简单。 对于使用 ObjectContext 但仍不难执行的应用程序，还需要执行其他一些工作。

下面是将现有应用程序升级到 EF6 所需执行的操作清单。

## <a name="1-install-the-ef6-nuget-package"></a>1. 安装 EF6 NuGet 包

需要升级到新的实体框架6运行时。

1. 右键单击项目，然后选择 "**管理 NuGet 包 ...** "  
2. 在 "**联机**" 选项卡中选择**EntityFramework** ，然后单击 "**安装**"  
   > [!NOTE]
   > 如果安装了 EntityFramework NuGet 包的以前版本，则会将其升级到 EF6。

或者，你可以从包管理器控制台运行以下命令：

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2. 确保删除对 System.Data.Entity.dll 的程序集引用

安装 EF6 NuGet 包应会自动从你的项目中删除对 System.object 的任何引用。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3. 将任何 EF 设计器替换 (EDMX) 模型以使用 EF 1.x 代码生成

如果使用 EF 设计器创建了任何模型，则需要更新代码生成模板以生成 EF6 兼容代码。

> [!NOTE]
> 目前只有适用于 Visual Studio 2012 和2013的 EF 1.x DbContext 生成器模板。

1. 删除现有代码生成模板。 这些文件通常名为** \<edmx_file_name\> . tt**和** \<edmx_file_name\> 。Context.tt** ，并将其嵌套在解决方案资源管理器中的 edmx 文件下。 可以在解决方案资源管理器中选择模板，然后按 **Del** 键将它们删除。  
   > [!NOTE]
   > 在网站项目中，模板不会嵌套在 edmx 文件下，但会在解决方案资源管理器中列出。  

   > [!NOTE]
   > 在 VB.NET 项目中，你将需要启用 "显示所有文件" 才能查看嵌套的模板文件。
2. 添加适当的 EF 1.x 代码生成模板。 在 EF 设计器中打开模型，右键单击设计图面，然后选择 "**添加代码生成项 ...** "
    - 如果使用的是 DbContext API (建议) 则在 "**数据**" 选项卡下将提供**EF 1.x DbContext 生成器**。  
      > [!NOTE]
      > 如果使用的是 Visual Studio 2012，则需要安装 EF 6 工具才能使用此模板。 有关详细信息，请参阅 [获取实体框架](xref:ef6/fundamentals/install) 。  

    - 如果你使用的是 ObjectContext API，则需要选择 " **联机** " 选项卡，然后搜索 **EF EntityObject 生成器**。  
3. 如果对代码生成模板应用了任何自定义，则需要将其重新应用到更新的模板。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4. 更新正在使用的任何 core EF 类型的命名空间

DbContext 和 Code First 类型的命名空间尚未更改。 这意味着，对于使用 EF 4.1 或更高版本的许多应用程序，你无需更改任何内容。

之前在 System.Data.Entity.dll 中的类型（如 ObjectContext）已移动到新命名空间。 这意味着你可能需要更新 *使用* 或 *导入* 指令，以针对 EF6 进行构建。

命名空间更改的一般规则是将 System.web. * 中的任何类型都移到 "system.string"。 换言之，只需插入 **Entity。** 在 System.object 之后。 例如：

- EntityException => System.object。**Entity。** EntityException  
- System.web. ObjectContext => System.object。**Entity。** 对象。 ObjectContext  
- Dataclasses.dll. RelationshipManager => 的数据。**Entity。** Dataclasses.dll. RelationshipManager  

这些类型位于 *核心* 命名空间中，因为它们不能直接用于大多数基于 DbContext 的应用程序。 作为 System.Data.Entity.dll 的一部分的某些类型仍通常会直接用于基于 DbContext 的应用程序，因此不会移动到 *核心* 命名空间中。 它们是：

- EntityState => System.object。**实体**。EntityState  
- Dataclasses.dll. EdmFunctionAttribute => 的数据。**DbFunctionAttribute**  
  > [!NOTE]
  > 此类已重命名;具有旧名称的类仍然存在并且工作正常，但现在标记为过时。  
- EntityFunctions = =>。**DbFunctions**  
  > [!NOTE]
  > 此类已重命名;具有旧名称的类仍然存在并且工作正常，但现在标记为过时。 )   
- 空间类 (例如，DbGeography、DbGeometry) 已从 System.web => 系统移动。**实体**。空间

> [!NOTE]
> System.web 命名空间中的某些类型不是 EF 程序集 System.Data.dll。 这些类型不会移动，因此它们的命名空间保持不变。

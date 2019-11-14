---
title: 升级到实体框架 6-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 29958ae5-85d3-4585-9ba6-550b8ec9393a
ms.openlocfilehash: 4395a9c117a6cf38e7fc08f11ee689d6fffa6fed
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182102"
---
# <a name="upgrading-to-entity-framework-6"></a>升级到实体框架6

在以前版本的 EF 中，代码在作为 NuGet 包附带的 .NET Framework 和带外（OOB）库（主要是 EntityFramework）的一部分随附的核心库（主要为 System.web .dll）中拆分。 EF6 采用核心库中的代码，并将其合并到 OOB 库中。 此操作是必需的，以便使 EF 成为开源，使其能够以不同于 .NET Framework 的步调发展。 这样做的结果是需要针对已移动的类型重新生成应用程序。

对于使用 EF 4.1 和更高版本中随附的 DbContext 的应用程序，这应该非常简单。 对于使用 ObjectContext 但仍不难执行的应用程序，还需要执行其他一些工作。

下面是将现有应用程序升级到 EF6 所需执行的操作清单。

## <a name="1-install-the-ef6-nuget-package"></a>1.安装 EF6 NuGet 包

需要升级到新的实体框架6运行时。

1. 右键单击项目，然后选择 "**管理 NuGet 包 ...** "  
2. 在 "**联机**" 选项卡中选择**EntityFramework** ，然后单击 "**安装**"  
   > [!NOTE]
   > 如果安装了 EntityFramework NuGet 包的以前版本，则会将其升级到 EF6。

或者，你可以从包管理器控制台运行以下命令：

``` powershell
Install-Package EntityFramework
```

## <a name="2-ensure-that-assembly-references-to-systemdataentitydll-are-removed"></a>2.确保已删除对 System.Data.Entity.dll 的程序集引用

安装 EF6 NuGet 包应会自动从你的项目中删除对 System.object 的任何引用。

## <a name="3-swap-any-ef-designer-edmx-models-to-use-ef-6x-code-generation"></a>3.交换任何 EF 设计器（EDMX）模型以使用 EF 1.x 代码生成

如果使用 EF 设计器创建了任何模型，则需要更新代码生成模板以生成 EF6 兼容代码。

> [!NOTE]
> 目前只有适用于 Visual Studio 2012 和2013的 EF 1.x DbContext 生成器模板。

1. 删除现有代码生成模板。 通常，这些文件将命名为 **\<edmx_file_name\>.tt**和 **\<edmx_file_name @ no__t。Context.tt** ，并将其嵌套在解决方案资源管理器中的 edmx 文件下。 可以在解决方案资源管理器中选择模板，然后按**Del**键将它们删除。  
   > [!NOTE]
   > 在网站项目中，模板不会嵌套在 edmx 文件下，但会在解决方案资源管理器中列出。  

   > [!NOTE]
   > 在 VB.NET 项目中，你将需要启用 "显示所有文件" 才能查看嵌套的模板文件。
2. 添加适当的 EF 1.x 代码生成模板。 在 EF 设计器中打开模型，右键单击设计图面，然后选择 "**添加代码生成项 ...** "
    - 如果使用的是 DbContext API （推荐），则 "**数据**" 选项卡下将提供**EF 1.x DbContext 生成器**。  
      > [!NOTE]
      > 如果使用的是 Visual Studio 2012，则需要安装 EF 6 工具才能使用此模板。 有关详细信息，请参阅[获取实体框架](~/ef6/fundamentals/install.md)。  

    - 如果你使用的是 ObjectContext API，则需要选择 "**联机**" 选项卡，然后搜索**EF EntityObject 生成器**。  
3. 如果对代码生成模板应用了任何自定义，则需要将其重新应用到更新的模板。

## <a name="4-update-namespaces-for-any-core-ef-types-being-used"></a>4.更新正在使用的任何 core EF 类型的命名空间

DbContext 和 Code First 类型的命名空间尚未更改。 这意味着，对于使用 EF 4.1 或更高版本的许多应用程序，你无需更改任何内容。

之前在 system.exception 中的类型（如 ObjectContext）已移动到新命名空间。 这意味着你可能需要更新*使用*或*导入*指令，以针对 EF6 进行构建。

命名空间更改的一般规则是将 System.web. * 中的任何类型都移到 "system.string"。 换言之，只需插入**Entity。** 在 System.object 之后。 例如：

- System.Data.EntityException = > System.Data.**Entity.Core**.EntityException  
- System.Data.Objects.ObjectContext = > System.Data.**Entity.Core**.Objects.ObjectContext  
- System.Data.Objects.DataClasses.RelationshipManager = > System.Data.**Entity.Core**.Objects.DataClasses.RelationshipManager  

这些类型位于*核心*命名空间中，因为它们不能直接用于大多数基于 DbContext 的应用程序。 作为 DbContext 的一部分的某些类型仍经常用于基于的应用程序，因此尚未移入*核心*命名空间。 这些是：

- System.Data.EntityState = > System.Data.**实体**.EntityState  
- System.Data.Objects.DataClasses.EdmFunctionAttribute = > System.Data.**Entity.DbFunctionAttribute**  
  > [!NOTE]
  > 此类已重命名;具有旧名称的类仍然存在并且工作正常，但现在标记为过时。  
- System.Data.Objects.EntityFunctions = > System.Data.**Entity.DbFunctions**  
  > [!NOTE]
  > 此类已重命名;具有旧名称的类仍然存在并且工作正常，但现在标记为已过时。）  
- 空间类 （例如，DbGeography，DbGeometry） 已从 System.Data.Spatial = > System.Data.**实体**.空间

> [!NOTE]
> System.web 命名空间中的某些类型不是 EF 程序集。 这些类型不会移动，因此它们的命名空间保持不变。

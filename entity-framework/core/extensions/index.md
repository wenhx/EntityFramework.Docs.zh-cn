---
title: 工具和扩展 - EF Core
author: ErikEJ
ms.date: 01/07/2019
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: d432ca36c166f7baf71253709bf58b1f5428a11a
ms.sourcegitcommit: 8f801993c9b8cd8a8fbfa7134818a8edca79e31a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2019
ms.locfileid: "59562502"
---
# <a name="ef-core-tools--extensions"></a>EF Core 工具和扩展

这些工具和扩展为 Framework Core 2.0 及更高版本提供了附加功能。

> [!IMPORTANT]  
> 扩展由各种源构建，不作为 Entity Framework Core 项目的一部分进行维护。 考虑使用第三方扩展时，请务必评估其质量、授权、兼容性和支持等因素，确保其符合要求。

## <a name="tools"></a>工具

### <a name="llblgen-pro"></a>LLBLGen Pro

LLBLGen Pro 是一种实体建模解决方案，包含对 Entity Framework 和 Entity Framework Core 的支持。 借助它可轻松通过 Database First 或 Model First 定义实体模型并将其映射到数据库中，使你可以立即开始编写查询。

[网站](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a>Devart Entity Developer

Devart Entity Developer 是一种用于 ADO.NET 实体框架、NHibernate、LinqConnect、Telerik 数据访问和 LINQ to SQL 的强大 ORM 设计器。 它支持 EF Core 模型的直观设计、使用“模型优先”或“数据库优先”的方式，还支持 C# 或 Visual Basic 代码生成。 

[网站](https://www.devart.com/entitydeveloper/)

### <a name="ef-core-power-tools"></a>EF Core Power Tools

EF Core Power Tools 是一种 Visual Studio 2017 扩展，它在简单用户界面中公开各种 EF Core 设计时任务。 其中包括对现有数据库和 [SQL Server DACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) 中的 DbContext 和实体类的反向工程、对数据库迁移的管理，以及模型可视化效果。

[GitHub wiki](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a>实体框架可视化编辑器

Entity Framework Visual Editor 是一种 Visual Studio 扩展，其中增添了 ORM 设计器用于 EF 6 和 EF Core 类的可视化设计。 代码是通过 T4 模板生成的，因此可自定义来满足任意需求。 它支持继承、单向和双向关联，支持枚举，还能用颜色标识类并添加文本块来解释潜在不可预测的设计部分。

[市场](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a>CatFactory

CatFactory 是一种面向 .NET Core 的基架引擎，它可自动基于 SQL Server 数据库生成 DbContext 类、实体、映射配置和存储库类。

[GitHub 存储库](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a>LoreSoft 的 Entity Framework Core 生成器

Entity Framework Core Generator (efg) 是一种 .NET Core CLI 工具，可基于现有数据库生成 EF Core 模型，其功能与 `dotnet ef dbcontext scaffold` 很相似，但它还支持通过区域替换或分析映射文件来实现安全代码的[重新生成](https://efg.loresoft.com/en/latest/regeneration/)。 此工具支持生成视图模型、验证和对象映射器代码。 

[教程](http://www.loresoft.com/Generate-ASP-NET-Web-API)
[文档](https://efg.loresoft.com/en/latest/)

## <a name="extensions"></a>扩展

### <a name="microsoftentityframeworkcoreautohistory"></a>Microsoft.EntityFrameworkCore.AutoHistory

一个插件库，它可用于将 EF Core 执行的数据更改自动记录到历史记录表中。

[GitHub 存储库](https://github.com/Arch/AutoHistory/)

### <a name="microsoftentityframeworkcoredynamiclinq"></a>Microsoft.EntityFrameworkCore.DynamicLinq

System.Linq.Dynamic 的一个 .NET Core/.NET Standard 端口，其中包含 EF Core 异步支持。
System.Linq.Dynamic 最初用作一个 Microsoft 示例，它展示了如何基于字符串表达式（而非代码）动态构造 LINQ 查询。

[GitHub 存储库](https://github.com/StefH/System.Linq.Dynamic.Core/)

### <a name="efsecondlevelcachecore"></a>EFSecondLevelCache.Core

一个扩展，它可将 EF Core 查询的结果存储到二级缓存中，使后续执行相同查询时无需访问数据库，而是直接从缓存中检索数据。

[GitHub 存储库](https://github.com/VahidN/EFSecondLevelCache.Core/)

### <a name="entityframeworkcoreprimarykey"></a>EntityFrameworkCore.PrimaryKey

借助此库，可从任何用作字典的实体中检索主键（包括复合键）的值。

[GitHub 存储库](https://github.com/NickStrupat/EntityFramework.PrimaryKey/)

### <a name="entityframeworkcoretypedoriginalvalues"></a>EntityFrameworkCore.TypedOriginalValues

借助此库，可对实体属性的原始值进行强类型访问。 

[GitHub 存储库](https://github.com/NickStrupat/EntityFramework.TypedOriginalValues/)

### <a name="geco"></a>Geco

Geco（生成器控制台）是一个基于控制台项目的简单代码生成器，它在 .NET Core 上运行并使用 C# 内插字符串来生成代码。 Geco 提供面向 EF Core 的反向模型生成器，并支持复数形式、单数形式和可编辑的模板。 它还支持种子数据脚本生成器、脚本运行器和数据库清理器。

[GitHub 存储库](https://github.com/iQuarc/Geco)

### <a name="linqkitmicrosoftentityframeworkcore"></a>LinqKit.Microsoft.EntityFrameworkCore

LinqKit.Microsoft.EntityFrameworkCore 是 LINQKit 库的 EF Core 兼容版本。 LINQKit 是 LINQ 的一组免费扩展，面向的是 SQL 和 Entity Framework 超级用户。 它可实现高级功能，例如谓词表达式的动态构建，以及在子查询中使用表达式变量。  

[GitHub 存储库](https://github.com/scottksmith95/LINQKit/)

### <a name="neinlinqentityframeworkcore"></a>NeinLinq.EntityFrameworkCore

NeinLinq 扩展了 Entity Framewor 等 LINQ 提供程序，让用户能够使用可转换谓词和选择器重复使用函数、重新编写查询并构建动态查询。

[GitHub 存储库](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a>Microsoft.EntityFrameworkCore.UnitOfWork

Microsoft.EntityFrameworkCore 的一个插件，它支持存储库、工作模式单元，并支持多个具有具有所支持分布式事务的数据库。

[GitHub 存储库](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a>EFCore.BulkExtensions

用于批量操作（插入、更新和删除）的 EF Core 插件。

[GitHub 存储库](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a>Bricelam.EntityFrameworkCore.Pluralizer

将设计时复数形式添加到 EF Core。

[GitHub 存储库](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="pomelofoundationpomeloentityframeworkcoreextensionstosql"></a>PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql

一个简单扩展方法，它获取 EF Core 将在简单方案中为给定 LINQ 查询生成的 SQL 语句。 仅可在简单方案中使用 ToSql 方法，因为 EF Core 可为一个 LINQ 查询生成多个 SQL 语句，而具体的 SQL 语句由参数值而定。

[GitHub 存储库](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.Extensions.ToSql)

### <a name="toolbeltentityframeworkcoreindexattribute"></a>Toolbelt.EntityFrameworkCore.IndexAttribute

恢复 EF Core 的 [Index] 属性（带有用于模型构建的扩展）。

[GitHub 存储库](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a>EfCore.InMemoryHelpers

提供一个面向 EF Core 内存中数据库提供程序的包装器。 使其功能与关系提供程序更类似。

[GitHub 存储库](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a>EFCore.TemporalSupport

EF Core 临时支持的一个实现。

[GitHub 存储库](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="entityframeworkcorecacheable"></a>EntityFrameworkCore.Cacheable

EF Core 的高性能二级查询缓存。

[GitHub 存储库](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

---
title: 数据种子设定-EF Core
description: 使用数据种子使用 Entity Framework Core 填充包含初始数据集的数据库
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: 1d7adbe45c4cbc64a39485c76d8f516e32ffeba5
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071597"
---
# <a name="data-seeding"></a>数据种子设定

数据种子是用初始数据集填充数据库的过程。

可以通过多种方式在 EF Core 中完成此操作：

* 模型种子数据
* 手动迁移自定义
* 自定义初始化逻辑

## <a name="model-seed-data"></a>模型种子数据

> [!NOTE]
> 此功能是 EF Core 2.1 中的新增功能。

与在 EF6 中不同，在 EF Core 中，种子设定数据可以作为模型配置的一部分与实体类型相关联。 然后 EF Core [迁移](xref:core/managing-schemas/migrations/index) 可以自动计算在将数据库升级到新版本的模型时需要应用的插入、更新或删除操作。

> [!NOTE]
> 迁移仅在确定应该执行哪种操作以使种子数据进入所需状态时才考虑模型更改。 因此，在迁移外部执行的数据更改可能会丢失或导致错误。

例如，这将为中的配置种子数据 `Blog` `OnModelCreating` ：

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

若要添加具有关系的实体，需要指定外键值：

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

如果实体类型具有隐藏状态的任何属性，则可以使用匿名类提供值：

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

拥有的实体类型可以采用类似的方式进行种子设定：

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

有关更多上下文，请参阅 [完整的示例项目](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/DataSeeding) 。

将数据添加到模型后，应使用 [迁移](xref:core/managing-schemas/migrations/index) 来应用更改。

> [!TIP]
> 如果需要在自动部署中应用迁移，可以创建一个可在执行前预览的 [SQL 脚本](xref:core/managing-schemas/migrations/index#generate-sql-scripts) 。

或者，您可以使用 `context.Database.EnsureCreated()` 创建包含种子数据的新数据库，例如，对于测试数据库，或使用内存中提供程序或任何非关系数据库时。 请注意，如果数据库已存在， `EnsureCreated()` 将不会更新数据库中的架构或种子数据。 对于关系数据库， `EnsureCreated()` 如果计划使用迁移，则不应调用。

### <a name="limitations-of-model-seed-data"></a>模型种子数据的限制

此类型的种子数据由迁移管理，需要在不连接到数据库的情况下生成用于更新数据库中已存在的数据的脚本。 这会施加一些限制：

* 主键值需要指定，即使它通常由数据库生成。 它用于检测迁移间的数据更改。
* 如果以任何方式更改了主键，则将删除以前的种子数据。

因此，此功能最适用于不需要在迁移外更改的静态数据，并且不依赖于数据库中的任何其他内容（例如，邮政编码）。

如果你的方案包括以下任何一种情况，则建议使用上一部分中所述的自定义初始化逻辑：

* 用于测试的临时数据
* 依赖于数据库状态的数据
* 需要由数据库生成的键值的数据，包括使用替代密钥作为标识的实体
* 需要自定义转换 (的数据，该转换不) [值转换](xref:core/modeling/value-conversions) 处理，如某些密码哈希
* 需要调用外部 API 的数据，例如 ASP.NET Core 标识角色和用户创建

## <a name="manual-migration-customization"></a>手动迁移自定义

添加迁移时，对指定的数据所做的更改将 `HasData` 转换为对 `InsertData()` 、和的调用 `UpdateData()` `DeleteData()` 。 解决某些限制的一种方法 `HasData` 是手动将这些调用或 [自定义操作](xref:core/managing-schemas/migrations/operations) 添加到迁移。

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>自定义初始化逻辑

执行数据种子设定的一种简单而有效的方法是在 [`DbContext.SaveChanges()`](xref:core/saving/index) 主应用程序逻辑开始执行之前使用。

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> 种子设定代码不应是正常应用执行的一部分，因为这可能会导致多个实例运行时出现并发性问题，并且还要求应用有权修改数据库架构。

根据部署的约束，可以通过不同的方式执行初始化代码：

* 在本地运行初始化应用程序
* 将初始化应用与主应用一起部署，调用初始化例程，禁用或删除初始化应用。

通常可以使用 [发布配置文件](/aspnet/core/host-and-deploy/visual-studio-publish-profiles)自动完成此配置。

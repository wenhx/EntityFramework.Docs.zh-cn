---
title: 自定义迁移操作-EF Core
description: 通过 Entity Framework Core 管理数据库架构管理的自定义和原始 SQL 迁移
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429827"
---
# <a name="custom-migrations-operations"></a>自定义迁移操作

借助 MigrationBuilder API，你可以在迁移过程中执行多种不同的操作，但远远超出了这一过程。 不过，API 也可通过扩展来定义自己的操作。 可以通过两种方法来扩展 API：使用 `Sql()` 方法，或通过定义自定义 `MigrationOperation` 对象。

为了说明这一点，让我们看一下如何实现使用每种方法创建数据库用户的操作。 在我们的迁移中，我们希望能够编写以下代码：

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>使用 MigrationBuilder ( # A1

实现自定义操作的最简单方法是定义调用的扩展方法 `MigrationBuilder.Sql()` 。 下面是生成相应 Transact-sql 的示例。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> `EXEC`当语句必须是 SQL 批处理中的第一个或唯一一个语句时，请使用函数。 还可能需要解决在引用的列当前不存在于表中时可能发生的幂等迁移脚本中的分析器错误。

如果你的迁移需要支持多个数据库提供程序，则可以使用 `MigrationBuilder.ActiveProvider` 属性。 下面是同时支持 Microsoft SQL Server 和 PostgreSQL 的示例。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

此方法仅在知道将应用自定义操作的每个提供程序时才有效。

## <a name="using-a-migrationoperation"></a>使用 MigrationOperation

若要将自定义操作与 SQL 分离，可以定义自己的 `MigrationOperation` 来表示它。 然后，将操作传递给提供程序，以便它可以确定要生成的相应 SQL。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

利用此方法，扩展方法只需将其中一个操作添加到 `MigrationBuilder.Operations` 。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

此方法要求每个提供程序知道如何在其服务中为此操作生成 SQL `IMigrationsSqlGenerator` 。 下面是一个示例，用于重写 SQL Server 的生成器来处理新操作。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

将默认迁移 sql 生成器服务替换为已更新的。

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]

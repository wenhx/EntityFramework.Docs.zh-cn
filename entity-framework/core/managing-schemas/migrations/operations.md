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
# <a name="custom-migrations-operations"></a><span data-ttu-id="4670b-103">自定义迁移操作</span><span class="sxs-lookup"><span data-stu-id="4670b-103">Custom Migrations Operations</span></span>

<span data-ttu-id="4670b-104">借助 MigrationBuilder API，你可以在迁移过程中执行多种不同的操作，但远远超出了这一过程。</span><span class="sxs-lookup"><span data-stu-id="4670b-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="4670b-105">不过，API 也可通过扩展来定义自己的操作。</span><span class="sxs-lookup"><span data-stu-id="4670b-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="4670b-106">可以通过两种方法来扩展 API：使用 `Sql()` 方法，或通过定义自定义 `MigrationOperation` 对象。</span><span class="sxs-lookup"><span data-stu-id="4670b-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="4670b-107">为了说明这一点，让我们看一下如何实现使用每种方法创建数据库用户的操作。</span><span class="sxs-lookup"><span data-stu-id="4670b-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="4670b-108">在我们的迁移中，我们希望能够编写以下代码：</span><span class="sxs-lookup"><span data-stu-id="4670b-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="4670b-109">使用 MigrationBuilder ( # A1</span><span class="sxs-lookup"><span data-stu-id="4670b-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="4670b-110">实现自定义操作的最简单方法是定义调用的扩展方法 `MigrationBuilder.Sql()` 。</span><span class="sxs-lookup"><span data-stu-id="4670b-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="4670b-111">下面是生成相应 Transact-sql 的示例。</span><span class="sxs-lookup"><span data-stu-id="4670b-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="4670b-112">`EXEC`当语句必须是 SQL 批处理中的第一个或唯一一个语句时，请使用函数。</span><span class="sxs-lookup"><span data-stu-id="4670b-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="4670b-113">还可能需要解决在引用的列当前不存在于表中时可能发生的幂等迁移脚本中的分析器错误。</span><span class="sxs-lookup"><span data-stu-id="4670b-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="4670b-114">如果你的迁移需要支持多个数据库提供程序，则可以使用 `MigrationBuilder.ActiveProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="4670b-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="4670b-115">下面是同时支持 Microsoft SQL Server 和 PostgreSQL 的示例。</span><span class="sxs-lookup"><span data-stu-id="4670b-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="4670b-116">此方法仅在知道将应用自定义操作的每个提供程序时才有效。</span><span class="sxs-lookup"><span data-stu-id="4670b-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="4670b-117">使用 MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="4670b-117">Using a MigrationOperation</span></span>

<span data-ttu-id="4670b-118">若要将自定义操作与 SQL 分离，可以定义自己的 `MigrationOperation` 来表示它。</span><span class="sxs-lookup"><span data-stu-id="4670b-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="4670b-119">然后，将操作传递给提供程序，以便它可以确定要生成的相应 SQL。</span><span class="sxs-lookup"><span data-stu-id="4670b-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="4670b-120">利用此方法，扩展方法只需将其中一个操作添加到 `MigrationBuilder.Operations` 。</span><span class="sxs-lookup"><span data-stu-id="4670b-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="4670b-121">此方法要求每个提供程序知道如何在其服务中为此操作生成 SQL `IMigrationsSqlGenerator` 。</span><span class="sxs-lookup"><span data-stu-id="4670b-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="4670b-122">下面是一个示例，用于重写 SQL Server 的生成器来处理新操作。</span><span class="sxs-lookup"><span data-stu-id="4670b-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="4670b-123">将默认迁移 sql 生成器服务替换为已更新的。</span><span class="sxs-lookup"><span data-stu-id="4670b-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]

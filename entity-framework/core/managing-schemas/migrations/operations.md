---
title: 自定义迁移操作-EF Core
description: 通过 Entity Framework Core 管理数据库架构管理的自定义和原始 SQL 迁移
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 708894d8d567a4644be3a4ace98cc837465710e0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617952"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="55e7e-103">自定义迁移操作</span><span class="sxs-lookup"><span data-stu-id="55e7e-103">Custom Migrations Operations</span></span>

<span data-ttu-id="55e7e-104">借助 MigrationBuilder API，你可以在迁移过程中执行多种不同的操作，但远远超出了这一过程。</span><span class="sxs-lookup"><span data-stu-id="55e7e-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="55e7e-105">不过，API 也可通过扩展来定义自己的操作。</span><span class="sxs-lookup"><span data-stu-id="55e7e-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="55e7e-106">可以通过两种方法来扩展 API：使用 `Sql()` 方法，或通过定义自定义 `MigrationOperation` 对象。</span><span class="sxs-lookup"><span data-stu-id="55e7e-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="55e7e-107">为了说明这一点，让我们看一下如何实现使用每种方法创建数据库用户的操作。</span><span class="sxs-lookup"><span data-stu-id="55e7e-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="55e7e-108">在我们的迁移中，我们希望能够编写以下代码：</span><span class="sxs-lookup"><span data-stu-id="55e7e-108">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="55e7e-109">使用 MigrationBuilder ( # A1</span><span class="sxs-lookup"><span data-stu-id="55e7e-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="55e7e-110">实现自定义操作的最简单方法是定义调用的扩展方法 `MigrationBuilder.Sql()` 。</span><span class="sxs-lookup"><span data-stu-id="55e7e-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="55e7e-111">下面是生成相应 Transact-sql 的示例。</span><span class="sxs-lookup"><span data-stu-id="55e7e-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="55e7e-112">如果你的迁移需要支持多个数据库提供程序，则可以使用 `MigrationBuilder.ActiveProvider` 属性。</span><span class="sxs-lookup"><span data-stu-id="55e7e-112">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="55e7e-113">下面是同时支持 Microsoft SQL Server 和 PostgreSQL 的示例。</span><span class="sxs-lookup"><span data-stu-id="55e7e-113">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

<span data-ttu-id="55e7e-114">此方法仅在知道将应用自定义操作的每个提供程序时才有效。</span><span class="sxs-lookup"><span data-stu-id="55e7e-114">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="55e7e-115">使用 MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="55e7e-115">Using a MigrationOperation</span></span>

<span data-ttu-id="55e7e-116">若要将自定义操作与 SQL 分离，可以定义自己的 `MigrationOperation` 来表示它。</span><span class="sxs-lookup"><span data-stu-id="55e7e-116">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="55e7e-117">然后，将操作传递给提供程序，以便它可以确定要生成的相应 SQL。</span><span class="sxs-lookup"><span data-stu-id="55e7e-117">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="55e7e-118">利用此方法，扩展方法只需将其中一个操作添加到 `MigrationBuilder.Operations` 。</span><span class="sxs-lookup"><span data-stu-id="55e7e-118">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

<span data-ttu-id="55e7e-119">此方法要求每个提供程序知道如何在其服务中为此操作生成 SQL `IMigrationsSqlGenerator` 。</span><span class="sxs-lookup"><span data-stu-id="55e7e-119">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="55e7e-120">下面是一个示例，用于重写 SQL Server 的生成器来处理新操作。</span><span class="sxs-lookup"><span data-stu-id="55e7e-120">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

<span data-ttu-id="55e7e-121">将默认迁移 sql 生成器服务替换为已更新的。</span><span class="sxs-lookup"><span data-stu-id="55e7e-121">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

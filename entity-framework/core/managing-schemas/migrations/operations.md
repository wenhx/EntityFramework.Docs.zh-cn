---
title: 自定义迁移操作-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: bd2bfdc24977a47eaf7a6756a88b758b563d818a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414325"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="a6192-102">自定义迁移操作</span><span class="sxs-lookup"><span data-stu-id="a6192-102">Custom Migrations Operations</span></span>

<span data-ttu-id="a6192-103">借助 MigrationBuilder API，你可以在迁移过程中执行多种不同的操作，但远远超出了这一过程。</span><span class="sxs-lookup"><span data-stu-id="a6192-103">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="a6192-104">不过，API 也可通过扩展来定义自己的操作。</span><span class="sxs-lookup"><span data-stu-id="a6192-104">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="a6192-105">可以通过两种方法来扩展 API：使用 `Sql()` 方法，或者通过定义自定义 `MigrationOperation` 对象。</span><span class="sxs-lookup"><span data-stu-id="a6192-105">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="a6192-106">为了说明这一点，让我们看一下如何实现使用每种方法创建数据库用户的操作。</span><span class="sxs-lookup"><span data-stu-id="a6192-106">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="a6192-107">在我们的迁移中，我们希望能够编写以下代码：</span><span class="sxs-lookup"><span data-stu-id="a6192-107">In our migrations, we want to enable writing the following code:</span></span>

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="a6192-108">使用 MigrationBuilder （）</span><span class="sxs-lookup"><span data-stu-id="a6192-108">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="a6192-109">实现自定义操作的最简单方法是定义调用 `MigrationBuilder.Sql()`的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="a6192-109">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="a6192-110">下面是生成相应 Transact-sql 的示例。</span><span class="sxs-lookup"><span data-stu-id="a6192-110">Here is an example that generates the appropriate Transact-SQL.</span></span>

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

<span data-ttu-id="a6192-111">如果迁移需要支持多个数据库提供程序，则可以使用 "`MigrationBuilder.ActiveProvider`" 属性。</span><span class="sxs-lookup"><span data-stu-id="a6192-111">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="a6192-112">下面是同时支持 Microsoft SQL Server 和 PostgreSQL 的示例。</span><span class="sxs-lookup"><span data-stu-id="a6192-112">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

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

<span data-ttu-id="a6192-113">此方法仅在知道将应用自定义操作的每个提供程序时才有效。</span><span class="sxs-lookup"><span data-stu-id="a6192-113">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="a6192-114">使用 MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="a6192-114">Using a MigrationOperation</span></span>

<span data-ttu-id="a6192-115">若要将自定义操作与 SQL 分离，可以定义自己的 `MigrationOperation` 来表示它。</span><span class="sxs-lookup"><span data-stu-id="a6192-115">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="a6192-116">然后，将操作传递给提供程序，以便它可以确定要生成的相应 SQL。</span><span class="sxs-lookup"><span data-stu-id="a6192-116">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

<span data-ttu-id="a6192-117">利用此方法，扩展方法只需将其中一个操作添加到 `MigrationBuilder.Operations`。</span><span class="sxs-lookup"><span data-stu-id="a6192-117">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

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

<span data-ttu-id="a6192-118">此方法要求每个提供程序都知道如何在 `IMigrationsSqlGenerator` 服务中为此操作生成 SQL。</span><span class="sxs-lookup"><span data-stu-id="a6192-118">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="a6192-119">下面是一个示例，用于重写 SQL Server 的生成器来处理新操作。</span><span class="sxs-lookup"><span data-stu-id="a6192-119">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

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

<span data-ttu-id="a6192-120">将默认迁移 sql 生成器服务替换为已更新的。</span><span class="sxs-lookup"><span data-stu-id="a6192-120">Replace the default migrations sql generator service with the updated one.</span></span>

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

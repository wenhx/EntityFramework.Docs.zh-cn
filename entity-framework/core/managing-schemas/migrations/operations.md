---
title: 自定义迁移操作-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: bd2bfdc24977a47eaf7a6756a88b758b563d818a
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72812045"
---
# <a name="custom-migrations-operations"></a>自定义迁移操作

借助 MigrationBuilder API，你可以在迁移过程中执行多种不同的操作，但远远超出了这一过程。 不过，API 也可通过扩展来定义自己的操作。 可以通过两种方法来扩展 API：使用 `Sql()` 方法，或者通过定义自定义 `MigrationOperation` 对象。

为了说明这一点，让我们看一下如何实现使用每种方法创建数据库用户的操作。 在我们的迁移中，我们希望能够编写以下代码：

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>使用 MigrationBuilder （）

实现自定义操作的最简单方法是定义调用 `MigrationBuilder.Sql()`的扩展方法。 下面是生成相应 Transact-sql 的示例。

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

如果迁移需要支持多个数据库提供程序，则可以使用 "`MigrationBuilder.ActiveProvider`" 属性。 下面是同时支持 Microsoft SQL Server 和 PostgreSQL 的示例。

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

此方法仅在知道将应用自定义操作的每个提供程序时才有效。

## <a name="using-a-migrationoperation"></a>使用 MigrationOperation

若要将自定义操作与 SQL 分离，可以定义自己的 `MigrationOperation` 来表示它。 然后，将操作传递给提供程序，以便它可以确定要生成的相应 SQL。

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

利用此方法，扩展方法只需将其中一个操作添加到 `MigrationBuilder.Operations`。

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

此方法要求每个提供程序都知道如何在 `IMigrationsSqlGenerator` 服务中为此操作生成 SQL。 下面是一个示例，用于重写 SQL Server 的生成器来处理新操作。

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

将默认迁移 sql 生成器服务替换为已更新的。

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```

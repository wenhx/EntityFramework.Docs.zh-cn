---
title: EF Core 5.0 中的新增功能
description: EF Core 5.0 中的新功能概述
author: ajcvickers
ms.date: 07/20/2020
uid: core/what-is-new/ef-core-5.0/whatsnew
ms.openlocfilehash: b4551a3c593694b104a750d500d81eb170a83dc0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618596"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="e6a73-103">EF Core 5.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="e6a73-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="e6a73-104">EF Core 5.0 目前正在开发中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-104">EF Core 5.0 is currently in development.</span></span> <span data-ttu-id="e6a73-105">此页面将包含每个预览版中引入的令人关注的更改的简要介绍。</span><span class="sxs-lookup"><span data-stu-id="e6a73-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="e6a73-106">此页面不会复制 [EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan) 的计划。</span><span class="sxs-lookup"><span data-stu-id="e6a73-106">This page does not duplicate the [plan for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan).</span></span> <span data-ttu-id="e6a73-107">计划中介绍了 EF Core 5.0 的整体主题，其中包括我们在交付最终版本之前打算包含的所有内容。</span><span class="sxs-lookup"><span data-stu-id="e6a73-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="e6a73-108">发布时，我们会将此处的链接添加到官方文档。</span><span class="sxs-lookup"><span data-stu-id="e6a73-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-8"></a><span data-ttu-id="e6a73-109">预览版 8</span><span class="sxs-lookup"><span data-stu-id="e6a73-109">Preview 8</span></span>

### <a name="table-per-type-tpt-mapping"></a><span data-ttu-id="e6a73-110">每个类型一张表 (TPT) 映射</span><span class="sxs-lookup"><span data-stu-id="e6a73-110">Table-per-type (TPT) mapping</span></span>

<span data-ttu-id="e6a73-111">默认情况下，EF Core 会将 .NET 类型的继承层次结构映射到单个数据库表。</span><span class="sxs-lookup"><span data-stu-id="e6a73-111">By default, EF Core maps an inheritance hierarchy of .NET types to a single database table.</span></span> <span data-ttu-id="e6a73-112">这称为每个层次结构一张表 (TPH) 映射。</span><span class="sxs-lookup"><span data-stu-id="e6a73-112">This is known as table-per-hierarchy (TPH) mapping.</span></span> <span data-ttu-id="e6a73-113">EF Core 5.0 还允许将继承层次结构中的每个 .NET 类型映射到另一个数据库表，这称为每个类型一张表 (TPT) 映射。</span><span class="sxs-lookup"><span data-stu-id="e6a73-113">EF Core 5.0 also allows mapping each .NET type in an inheritance hierarchy to a different database table; known as table-per-type (TPT) mapping.</span></span>

<span data-ttu-id="e6a73-114">例如，请考虑具有映射的层次结构的此模型：</span><span class="sxs-lookup"><span data-stu-id="e6a73-114">For example, consider this model with a mapped hierarchy:</span></span>

```c#
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

public class Pet : Animal
{
    public string Name { get; set; }
}

public class Cat : Pet
{
    public string EducationLevel { get; set; }
}

public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="e6a73-115">默认情况下，EF Core 会将此模型映射到单个表：</span><span class="sxs-lookup"><span data-stu-id="e6a73-115">By default, EF Core will map this to a single table:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    [Discriminator] nvarchar(max) NOT NULL,
    [Name] nvarchar(max) NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);
```

<span data-ttu-id="e6a73-116">但是，将每个实体类型映射到不同的表，会得到每个类型一张表的结果：</span><span class="sxs-lookup"><span data-stu-id="e6a73-116">However, mapping each entity type to a different table will instead result in one table per type:</span></span>

```sql
CREATE TABLE [Animals] (
    [Id] int NOT NULL IDENTITY,
    [Species] nvarchar(max) NULL,
    CONSTRAINT [PK_Animals] PRIMARY KEY ([Id])
);

CREATE TABLE [Pets] (
    [Id] int NOT NULL,
    [Name] nvarchar(max) NULL,
    CONSTRAINT [PK_Pets] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Pets_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Cats] (
    [Id] int NOT NULL,
    [EdcuationLevel] nvarchar(max) NULL,
    CONSTRAINT [PK_Cats] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Cats_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Cats_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);

CREATE TABLE [Dogs] (
    [Id] int NOT NULL,
    [FavoriteToy] nvarchar(max) NULL,
    CONSTRAINT [PK_Dogs] PRIMARY KEY ([Id]),
    CONSTRAINT [FK_Dogs_Animals_Id] FOREIGN KEY ([Id]) REFERENCES [Animals] ([Id]) ON DELETE NO ACTION,
    CONSTRAINT [FK_Dogs_Pets_Id] FOREIGN KEY ([Id]) REFERENCES [Pets] ([Id]) ON DELETE NO ACTION
);
```

<span data-ttu-id="e6a73-117">请注意，上述外键约束的创建是在对预览版 8 的代码创建分支后添加的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-117">Note that creation of the foreign key constraints shown above were added after branching the code for preview 8.</span></span>

<span data-ttu-id="e6a73-118">可使用映射特性将实体类型映射到不同的表：</span><span class="sxs-lookup"><span data-stu-id="e6a73-118">Entity types can be mapped to different tables using mapping attributes:</span></span>

```c#
[Table("Animals")]
public class Animal
{
    public int Id { get; set; }
    public string Species { get; set; }
}

[Table("Pets")]
public class Pet : Animal
{
    public string Name { get; set; }
}

[Table("Cats")]
public class Cat : Pet
{
    public string EdcuationLevel { get; set; }
}

[Table("Dogs")]
public class Dog : Pet
{
    public string FavoriteToy { get; set; }
}
```

<span data-ttu-id="e6a73-119">或使用 `ModelBuilder` 配置：</span><span class="sxs-lookup"><span data-stu-id="e6a73-119">Or using `ModelBuilder` configuration:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Animal>().ToTable("Animals");
    modelBuilder.Entity<Pet>().ToTable("Pets");
    modelBuilder.Entity<Cat>().ToTable("Cats");
    modelBuilder.Entity<Dog>().ToTable("Dogs");
}
```

<span data-ttu-id="e6a73-120">记录信息可通过问题 [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-120">Documentation is tracked by issue [#1979](https://github.com/dotnet/EntityFramework.Docs/issues/1979).</span></span>

### <a name="migrations-rebuild-sqlite-tables"></a><span data-ttu-id="e6a73-121">迁移：重新生成 SQLite 表</span><span class="sxs-lookup"><span data-stu-id="e6a73-121">Migrations: Rebuild SQLite tables</span></span>

<span data-ttu-id="e6a73-122">与其他数据库相比，SQLite 的架构操作功能相对有限。</span><span class="sxs-lookup"><span data-stu-id="e6a73-122">Compared to other database, SQLite is relatively limited in its schema manipulation capabilities.</span></span> <span data-ttu-id="e6a73-123">例如，从现有表中删除列需要删除并重新创建整个表。</span><span class="sxs-lookup"><span data-stu-id="e6a73-123">For example, dropping a column from an existing table requires that the entire table be dropped and re-created.</span></span> <span data-ttu-id="e6a73-124">EF Core 5.0 迁移现在支持自动重新生成表，以便实现所需的架构更改。</span><span class="sxs-lookup"><span data-stu-id="e6a73-124">EF Core 5.0 Migrations now supports automatic rebuilding the table for schema changes that require it.</span></span>

<span data-ttu-id="e6a73-125">例如，假设为 `Unicorn` 实体类型创建了 `Unicorns` 表：</span><span class="sxs-lookup"><span data-stu-id="e6a73-125">For example, imagine we have a `Unicorns` table created for a `Unicorn` entity type:</span></span>

```c#
public class Unicorn
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

```sql
CREATE TABLE "Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL,
    "Age" INTEGER NOT NULL
);
```

<span data-ttu-id="e6a73-126">然后我们了解到，存储独角兽的年龄被认为非常不礼貌，因此让我们删除该属性，添加新的迁移，并更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e6a73-126">We then learn that storing the age of a unicorn is considered very rude, so let's remove that property, add a new migration, and update the database.</span></span> <span data-ttu-id="e6a73-127">使用 EF Core 3.1 时，此更新将失败，因为无法删除该列。</span><span class="sxs-lookup"><span data-stu-id="e6a73-127">This update will fail when using EF Core 3.1 because the column cannot be dropped.</span></span> <span data-ttu-id="e6a73-128">在 EF Core 5.0 中，迁移将改为重新生成表：</span><span class="sxs-lookup"><span data-stu-id="e6a73-128">In EF Core 5.0, Migrations will instead rebuild the table:</span></span>

```sql
CREATE TABLE "ef_temp_Unicorns" (
    "Id" INTEGER NOT NULL CONSTRAINT "PK_Unicorns" PRIMARY KEY AUTOINCREMENT,
    "Name" TEXT NULL
);

INSERT INTO "ef_temp_Unicorns" ("Id", "Name")
SELECT "Id", "Name"
FROM Unicorns;

PRAGMA foreign_keys = 0;

DROP TABLE "Unicorns";

ALTER TABLE "ef_temp_Unicorns" RENAME TO "Unicorns";

PRAGMA foreign_keys = 1;
```

<span data-ttu-id="e6a73-129">请注意：</span><span class="sxs-lookup"><span data-stu-id="e6a73-129">Notice that:</span></span>
* <span data-ttu-id="e6a73-130">创建一个临时表，其中包含新表所需的架构</span><span class="sxs-lookup"><span data-stu-id="e6a73-130">A temporary table is created with the desired schema for the new table</span></span>
* <span data-ttu-id="e6a73-131">将数据从当前表复制到临时表中</span><span class="sxs-lookup"><span data-stu-id="e6a73-131">Data is copied from the current table into the temporary table</span></span>
* <span data-ttu-id="e6a73-132">关闭外键强制执行</span><span class="sxs-lookup"><span data-stu-id="e6a73-132">Foreign key enforcement is switched off</span></span>
* <span data-ttu-id="e6a73-133">删除当前表</span><span class="sxs-lookup"><span data-stu-id="e6a73-133">The current table is dropped</span></span>
* <span data-ttu-id="e6a73-134">将临时表重命名为新表</span><span class="sxs-lookup"><span data-stu-id="e6a73-134">The temporary table is renamed to be the new table</span></span>

<span data-ttu-id="e6a73-135">记录信息可通过问题 [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-135">Documentation is tracked by issue [#2583](https://github.com/dotnet/EntityFramework.Docs/issues/2583).</span></span>

### <a name="table-valued-functions"></a><span data-ttu-id="e6a73-136">表值函数</span><span class="sxs-lookup"><span data-stu-id="e6a73-136">Table-valued functions</span></span>

<span data-ttu-id="e6a73-137">此功能是 [@pmiddleton](https://github.com/pmiddleton) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-137">This feature was contributed from the community by [@pmiddleton](https://github.com/pmiddleton).</span></span> <span data-ttu-id="e6a73-138">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="e6a73-138">Many thanks for the contribution!</span></span>

<span data-ttu-id="e6a73-139">EF Core 5.0 为 .NET 方法到表值函数 (TVF) 的映射提供一流支持。</span><span class="sxs-lookup"><span data-stu-id="e6a73-139">EF Core 5.0 includes first-class support for mapping .NET methods to table-valued functions (TVFs).</span></span> <span data-ttu-id="e6a73-140">然后可在 LINQ 查询中使用这些函数，在此类查询中，函数结果上的其他组合也将转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="e6a73-140">These functions can then be used in LINQ queries where additional composition on the results of the function will also be translated to SQL.</span></span>

<span data-ttu-id="e6a73-141">例如，请考虑在 SQL Server 数据库中定义的以下 TVF：</span><span class="sxs-lookup"><span data-stu-id="e6a73-141">For example, consider this TVF defined in a SQL Server database:</span></span>

```sql
CREATE FUNCTION GetReports(@employeeId int)
RETURNS @reports TABLE
(
    Name nvarchar(50) NOT NULL,
    IsDeveloper bit NOT NULL
)
AS
BEGIN
    WITH cteEmployees AS
    (
        SELECT Id, Name, ManagerId, IsDeveloper
        FROM Employees
        WHERE Id = @employeeId
        UNION ALL
        SELECT e.Id, e.Name, e.ManagerId, e.IsDeveloper
        FROM Employees e
        INNER JOIN cteEmployees cteEmp ON cteEmp.Id = e.ManagerId
    )
    INSERT INTO @reports
    SELECT Name, IsDeveloper
    FROM cteEmployees
    WHERE Id != @employeeId

    RETURN
END
```

<span data-ttu-id="e6a73-142">EF Core 模型需要两种实体类型才能使用此 TVF：</span><span class="sxs-lookup"><span data-stu-id="e6a73-142">The EF Core model requires two entity types to use this TVF:</span></span>
* <span data-ttu-id="e6a73-143">以正常方式映射到 Employees 表的 `Employee` 类型</span><span class="sxs-lookup"><span data-stu-id="e6a73-143">An `Employee` type that maps to the Employees table in the normal way</span></span>
* <span data-ttu-id="e6a73-144">与 TVF 返回的形状相匹配的 `Report` 类型</span><span class="sxs-lookup"><span data-stu-id="e6a73-144">A `Report` type that matches the shape returned by the TVF</span></span>

```c#
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }

    public int? ManagerId { get; set; }
    public virtual Employee Manager { get; set; }
}
```

```c#
public class Report
{
    public string Name { get; set; }
    public bool IsDeveloper { get; set; }
}
```

<span data-ttu-id="e6a73-145">这些类型必须包含在 EF Core 模型中：</span><span class="sxs-lookup"><span data-stu-id="e6a73-145">These types must be included in the EF Core model:</span></span>

```c#
modelBuilder.Entity<Employee>();
modelBuilder.Entity(typeof(Report)).HasNoKey();
```

<span data-ttu-id="e6a73-146">请注意，`Report` 没有主键，因此必须这样配置。</span><span class="sxs-lookup"><span data-stu-id="e6a73-146">Notice that `Report` has no primary key and so must be configured as such.</span></span>

<span data-ttu-id="e6a73-147">最后，必须将 .NET 方法映射到数据库中的 TVF。</span><span class="sxs-lookup"><span data-stu-id="e6a73-147">Finally, a .NET method must be mapped to the TVF in the database.</span></span> <span data-ttu-id="e6a73-148">可以使用新的 `FromExpression` 方法在 DbContext 上定义此方法：</span><span class="sxs-lookup"><span data-stu-id="e6a73-148">This method can be defined on the DbContext using the new `FromExpression` method:</span></span>

```c#
public IQueryable<Report> GetReports(int managerId)
    => FromExpression(() => GetReports(managerId));
```

<span data-ttu-id="e6a73-149">此方法使用与上面定义的 TVF 匹配的参数和返回类型。</span><span class="sxs-lookup"><span data-stu-id="e6a73-149">This method uses a parameter and return type that match the TVF defined above.</span></span> <span data-ttu-id="e6a73-150">然后在 OnModelCreating 中将该方法添加到 EF Core 模型：</span><span class="sxs-lookup"><span data-stu-id="e6a73-150">The method is then added to the EF Core model in OnModelCreating:</span></span>

```c#
modelBuilder.HasDbFunction(() => GetReports(default));
```

<span data-ttu-id="e6a73-151">（在此处使用 lambda 可轻松将 `MethodInfo` 传递到 EF Core。</span><span class="sxs-lookup"><span data-stu-id="e6a73-151">(Using a lambda here is an easy way to pass the `MethodInfo` to EF Core.</span></span> <span data-ttu-id="e6a73-152">将忽略传递给该方法的参数。）</span><span class="sxs-lookup"><span data-stu-id="e6a73-152">The arguments passed to the method are ignored.)</span></span>

<span data-ttu-id="e6a73-153">现在可以编写查询，以调用 `GetReports` 并对结果进行组合。</span><span class="sxs-lookup"><span data-stu-id="e6a73-153">We can now write queries that call `GetReports` and compose over the results.</span></span> <span data-ttu-id="e6a73-154">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-154">For example:</span></span>

```c#
from e in context.Employees
from rc in context.GetReports(e.Id)
where rc.IsDeveloper == true
select new
{
  ManagerName = e.Name,
  EmployeeName = rc.Name,
})
```

<span data-ttu-id="e6a73-155">在 SQL Server 上，这将转换为：</span><span class="sxs-lookup"><span data-stu-id="e6a73-155">On SQL Server, this translates to:</span></span>

```sql
SELECT [e].[Name] AS [ManagerName], [g].[Name] AS [EmployeeName]
FROM [Employees] AS [e]
CROSS APPLY [dbo].[GetReports]([e].[Id]) AS [g]
WHERE [g].[IsDeveloper] = CAST(1 AS bit)
```

<span data-ttu-id="e6a73-156">请注意，SQL 以 `Employees` 表为根，调用 `GetReports`，然后在函数的结果上添加一个额外的 WHERE 子句。</span><span class="sxs-lookup"><span data-stu-id="e6a73-156">Notice that the SQL is rooted in the `Employees` table, calls `GetReports`, and then adds an additional WHERE clause on the results of the function.</span></span>

### <a name="flexible-queryupdate-mapping"></a><span data-ttu-id="e6a73-157">灵活查询/更新映射</span><span class="sxs-lookup"><span data-stu-id="e6a73-157">Flexible query/update mapping</span></span>

<span data-ttu-id="e6a73-158">EF Core 5.0 允许将同一实体类型映射到不同的数据库对象。</span><span class="sxs-lookup"><span data-stu-id="e6a73-158">EF Core 5.0 allows mapping the same entity type to different database objects.</span></span> <span data-ttu-id="e6a73-159">这些对象可以是表、视图或函数。</span><span class="sxs-lookup"><span data-stu-id="e6a73-159">These objects may be tables, views, or functions.</span></span>

<span data-ttu-id="e6a73-160">例如，可将一个实体类型同时映射到数据库视图和数据库表：</span><span class="sxs-lookup"><span data-stu-id="e6a73-160">For example, an entity type can be mapped to both a database view and a database table:</span></span>

```c#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Blog>()
        .ToTable("Blogs")
        .ToView("BlogsView");
}
```

<span data-ttu-id="e6a73-161">默认情况下，EF Core 随后将从视图进行查询，将更新发送到表。</span><span class="sxs-lookup"><span data-stu-id="e6a73-161">By default, EF Core will then query from the view and send updates to the table.</span></span> <span data-ttu-id="e6a73-162">例如，执行以下代码：</span><span class="sxs-lookup"><span data-stu-id="e6a73-162">For example, executing the following code:</span></span>

```c#
var blog = context.Set<Blog>().Single(e => e.Name == "One Unicorn");

blog.Name = "1unicorn2";

context.SaveChanges();
```

<span data-ttu-id="e6a73-163">生成针对视图的查询，然后对表进行更新：</span><span class="sxs-lookup"><span data-stu-id="e6a73-163">Results in a query against the view, and then an update to the table:</span></span>

```sql
SELECT TOP(2) [b].[Id], [b].[Name], [b].[Url]
FROM [BlogsView] AS [b]
WHERE [b].[Name] = N'One Unicorn'

SET NOCOUNT ON;
UPDATE [Blogs] SET [Name] = @p0
WHERE [Id] = @p1;
SELECT @@ROWCOUNT;
```

### <a name="context-wide-split-query-configuration"></a><span data-ttu-id="e6a73-164">上下文范围的拆分查询配置</span><span class="sxs-lookup"><span data-stu-id="e6a73-164">Context-wide split-query configuration</span></span>

<span data-ttu-id="e6a73-165">现在可以将拆分查询（见下文）配置为 DbContext 执行的任何查询的默认值。</span><span class="sxs-lookup"><span data-stu-id="e6a73-165">Split queries (see below) can now be configured as the default for any query executed by the DbContext.</span></span> <span data-ttu-id="e6a73-166">此配置仅适用于关系提供程序，因此必须将其指定为 `UseProvider` 配置的一部分。</span><span class="sxs-lookup"><span data-stu-id="e6a73-166">This configuration is only available for relational providers, and so must be specified as part of the `UseProvider` configuration.</span></span> <span data-ttu-id="e6a73-167">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-167">For example:</span></span>

```c#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(
            Your.SqlServerConnectionString,
            b => b.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));
```

<span data-ttu-id="e6a73-168">记录信息可通过问题 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-168">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="physicaladdress-mapping"></a><span data-ttu-id="e6a73-169">PhysicalAddress 映射</span><span class="sxs-lookup"><span data-stu-id="e6a73-169">PhysicalAddress mapping</span></span>

<span data-ttu-id="e6a73-170">此功能是 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-170">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="e6a73-171">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="e6a73-171">Many thanks for the contribution!</span></span>

<span data-ttu-id="e6a73-172">标准 .NET [PhysicalAddress 类](/dotnet/api/system.net.networkinformation.physicaladdress)现自动映射到尚不具备原生支持的数据库的字符串列中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-172">The standard .NET [PhysicalAddress class](/dotnet/api/system.net.networkinformation.physicaladdress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="e6a73-173">有关详细信息，请参阅下面的 `IPAddress` 示例。</span><span class="sxs-lookup"><span data-stu-id="e6a73-173">For more information, see the examples for `IPAddress` below.</span></span>

## <a name="preview-7"></a><span data-ttu-id="e6a73-174">预览版 7</span><span class="sxs-lookup"><span data-stu-id="e6a73-174">Preview 7</span></span>

### <a name="dbcontextfactory"></a><span data-ttu-id="e6a73-175">DbContextFactory</span><span class="sxs-lookup"><span data-stu-id="e6a73-175">DbContextFactory</span></span>

<span data-ttu-id="e6a73-176">EF Core 5.0 引入了 `AddDbContextFactory` 和 `AddPooledDbContextFactory`，可用于在应用程序的依赖关系注入 (D.I.) 容器中注册工厂来创建 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="e6a73-176">EF Core 5.0 introduces `AddDbContextFactory` and `AddPooledDbContextFactory` to register a factory for creating DbContext instances in the application's dependency injection (D.I.) container.</span></span> <span data-ttu-id="e6a73-177">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-177">For example:</span></span>

```csharp
services.AddDbContextFactory<SomeDbContext>(b =>
    b.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=Test"));
```

<span data-ttu-id="e6a73-178">应用程序服务（例如 ASP.NET Core 控制器）之后可依赖于服务构造函数中的 `IDbContextFactory<TContext>`。</span><span class="sxs-lookup"><span data-stu-id="e6a73-178">Application services such as ASP.NET Core controllers can then depend on `IDbContextFactory<TContext>` in the service constructor.</span></span> <span data-ttu-id="e6a73-179">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-179">For example:</span></span>

```csharp
public class MyController
{
    private readonly IDbContextFactory<SomeDbContext> _contextFactory;

    public MyController(IDbContextFactory<SomeDbContext> contextFactory)
    {
        _contextFactory = contextFactory;
    }
}
```

<span data-ttu-id="e6a73-180">之后可根据需要创建和使用 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="e6a73-180">DbContext instances can then be created and used as needed.</span></span> <span data-ttu-id="e6a73-181">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-181">For example:</span></span>

```csharp
public void DoSomeThing()
{
    using (var context = _contextFactory.CreateDbContext())
    {
        // ...
    }
}
```

<span data-ttu-id="e6a73-182">请注意，以这种方式创建的 DbContext 实例并非由应用程序的服务提供程序进行管理，因此必须由应用程序释放。</span><span class="sxs-lookup"><span data-stu-id="e6a73-182">Note that the DbContext instances created in this way are _not_ managed by the application's service provider and therefore must be disposed by the application.</span></span> <span data-ttu-id="e6a73-183">此类分离对于 Blazor 应用程序非常有用（此情况下建议使用 `IDbContextFactory`），但在其他方案中也很有用。</span><span class="sxs-lookup"><span data-stu-id="e6a73-183">This decoupling is very useful for Blazor applications, where using `IDbContextFactory` is recommended, but may also be useful in other scenarios.</span></span>

<span data-ttu-id="e6a73-184">你可通过调用 `AddPooledDbContextFactory` 来共用 DbContext 实例。</span><span class="sxs-lookup"><span data-stu-id="e6a73-184">DbContext instances can be pooled by calling `AddPooledDbContextFactory`.</span></span> <span data-ttu-id="e6a73-185">此类共用的原理与 `AddDbContextPool` 相同，且限制也相同。</span><span class="sxs-lookup"><span data-stu-id="e6a73-185">This pooling works the same way as for `AddDbContextPool`, and also has the same limitations.</span></span>

<span data-ttu-id="e6a73-186">记录信息可通过问题 [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-186">Documentation is tracked by issue [#2523](https://github.com/dotnet/EntityFramework.Docs/issues/2523).</span></span>

### <a name="reset-dbcontext-state"></a><span data-ttu-id="e6a73-187">重置 DbContext 状态</span><span class="sxs-lookup"><span data-stu-id="e6a73-187">Reset DbContext state</span></span>

<span data-ttu-id="e6a73-188">EF Core 5.0 引入了 `ChangeTracker.Clear()`，它可清除所有被跟踪实体的 DbContext。</span><span class="sxs-lookup"><span data-stu-id="e6a73-188">EF Core 5.0 introduces `ChangeTracker.Clear()` which clears the DbContext of all tracked entities.</span></span> <span data-ttu-id="e6a73-189">当使用为每个工作单元创建生存期较短的新上下文实例的最佳做法时，通常不需要这样做。</span><span class="sxs-lookup"><span data-stu-id="e6a73-189">This should usually not be needed when using the best practice of creating a new, short-lived context instance for each unit-of-work.</span></span> <span data-ttu-id="e6a73-190">但如果需要重置 DbContext 实例的状态，则使用新的 `Clear()` 方法比大量分离所有实体的性能和可靠性更强。</span><span class="sxs-lookup"><span data-stu-id="e6a73-190">However, if there is a need to reset the state of a DbContext instance, then using the new `Clear()` method is more performant and robust than mass-detaching all entities.</span></span>

<span data-ttu-id="e6a73-191">记录信息可通过问题 [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-191">Documentation is tracked by issue [#2524](https://github.com/dotnet/EntityFramework.Docs/issues/2524).</span></span>

### <a name="new-pattern-for-store-generated-defaults"></a><span data-ttu-id="e6a73-192">存储生成的默认值的新模式</span><span class="sxs-lookup"><span data-stu-id="e6a73-192">New pattern for store-generated defaults</span></span>

<span data-ttu-id="e6a73-193">EF Core 允许为可能还有默认值约束的列设置显式值。</span><span class="sxs-lookup"><span data-stu-id="e6a73-193">EF Core allows an explicit value to be set for a column that may also have default value constraint.</span></span> <span data-ttu-id="e6a73-194">EF Core 使用 type 属性类型的 CLR 默认值作为此项的 sentinel；如果该值不是 CLR 默认值，则会将其插入，否则将使用数据库默认值。</span><span class="sxs-lookup"><span data-stu-id="e6a73-194">EF Core uses the CLR default of type property type as a sentinel for this; if the value is not the CLR default, then it is inserted, otherwise the database default is used.</span></span>

<span data-ttu-id="e6a73-195">这会使 CLR 默认值不是良好的 sentinel（最值得注意的是 `bool` 属性）的类型出现问题。</span><span class="sxs-lookup"><span data-stu-id="e6a73-195">This creates problems for types where the CLR default is not a good sentinel--most notably, `bool` properties.</span></span> <span data-ttu-id="e6a73-196">EF Core 5.0 现允许支持字段在此类情况下可为 null。</span><span class="sxs-lookup"><span data-stu-id="e6a73-196">EF Core 5.0 now allows the backing field to be nullable for cases like this.</span></span> <span data-ttu-id="e6a73-197">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-197">For example:</span></span>

```csharp
public class Blog
{
    private bool? _isValid;

    public bool IsValid
    {
        get => _isValid ?? false;
        set => _isValid = value;
    }
}
```

<span data-ttu-id="e6a73-198">请注意，支持字段可为 null，但公开属性不可为 null。</span><span class="sxs-lookup"><span data-stu-id="e6a73-198">Note that the backing field is nullable, but the publicly exposed property is not.</span></span> <span data-ttu-id="e6a73-199">这允许 sentinel 值为 `null`，而不影响实体类型的公开内容。</span><span class="sxs-lookup"><span data-stu-id="e6a73-199">This allows the sentinel value to be `null` without impacting the public surface of the entity type.</span></span> <span data-ttu-id="e6a73-200">在本例中，如果绝不设置 `IsValid`，则将使用数据库默认值，因为支持字段仍为 null。</span><span class="sxs-lookup"><span data-stu-id="e6a73-200">In this case, if the `IsValid` is never set, then the database default will be used since the backing field remains null.</span></span> <span data-ttu-id="e6a73-201">如果设置 `true` 或 `false`，则此值将显式保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-201">If either `true` or `false` are set, then this value is saved explicitly to the database.</span></span>

<span data-ttu-id="e6a73-202">记录信息可通过问题 [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-202">Documentation is tracked by issue [#2525](https://github.com/dotnet/EntityFramework.Docs/issues/2525).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="e6a73-203">Cosmos 分区键</span><span class="sxs-lookup"><span data-stu-id="e6a73-203">Cosmos partition keys</span></span>

<span data-ttu-id="e6a73-204">EF Core 允许将 Cosmos 分区键包含在 EF 模型中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-204">EF Core allows the Cosmos partition key is included in the EF model.</span></span> <span data-ttu-id="e6a73-205">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-205">For example:</span></span>

```csharp
modelBuilder.Entity<Customer>().HasPartitionKey(b => b.AlternateKey)
```

<span data-ttu-id="e6a73-206">从预览版 7 开始，分区键将包含在实体类型的 PK 中，用于改进某些查询的性能。</span><span class="sxs-lookup"><span data-stu-id="e6a73-206">Starting with preview 7, the partition key is included in the entity type's PK and is used to improved performance in some queries.</span></span>

<span data-ttu-id="e6a73-207">记录信息可通过问题 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-207">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="cosmos-configuration"></a><span data-ttu-id="e6a73-208">Cosmos 配置</span><span class="sxs-lookup"><span data-stu-id="e6a73-208">Cosmos configuration</span></span>

<span data-ttu-id="e6a73-209">EF Core 5.0 改进了 Cosmos 和 Cosmos 连接的配置。</span><span class="sxs-lookup"><span data-stu-id="e6a73-209">EF Core 5.0 improves configuration of Cosmos and Cosmos connections.</span></span>

<span data-ttu-id="e6a73-210">之前，EF Core 需要在连接到 Cosmos 数据库时显式指定终结点和密钥。</span><span class="sxs-lookup"><span data-stu-id="e6a73-210">Previously, EF Core required the end-point and key to be specified explicitly when connecting to a Cosmos database.</span></span> <span data-ttu-id="e6a73-211">而 EF Core 5.0 允许使用连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e6a73-211">EF Core 5.0 allows use of a connection string instead.</span></span> <span data-ttu-id="e6a73-212">此外，EF Core 5.0 还允许显式设置 WebProxy 实例。</span><span class="sxs-lookup"><span data-stu-id="e6a73-212">In addition, EF Core 5.0 allows the WebProxy instance to be explicitly set.</span></span> <span data-ttu-id="e6a73-213">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-213">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.WebProxy(myProxyInstance);
            });
```

<span data-ttu-id="e6a73-214">现在还可配置许多其他超时值、限制等。</span><span class="sxs-lookup"><span data-stu-id="e6a73-214">Many other timeout values, limits, etc. can now also be configured.</span></span> <span data-ttu-id="e6a73-215">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-215">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseCosmos("my-cosmos-connection-string", "MyDb",
            cosmosOptionsBuilder =>
            {
                cosmosOptionsBuilder.LimitToEndpoint();
                cosmosOptionsBuilder.RequestTimeout(requestTimeout);
                cosmosOptionsBuilder.OpenTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.IdleTcpConnectionTimeout(timeout);
                cosmosOptionsBuilder.GatewayModeMaxConnectionLimit(connectionLimit);
                cosmosOptionsBuilder.MaxTcpConnectionsPerEndpoint(connectionLimit);
                cosmosOptionsBuilder.MaxRequestsPerTcpConnection(requestLimit);
            });
```

<span data-ttu-id="e6a73-216">最后，默认的连接模式现为 `ConnectionMode.Gateway`，兼容性通常更好。</span><span class="sxs-lookup"><span data-stu-id="e6a73-216">Finally, the default connection mode is now `ConnectionMode.Gateway`, which is generally more compatible.</span></span>

<span data-ttu-id="e6a73-217">记录信息可通过问题 [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-217">Documentation is tracked by issue [#2471](https://github.com/dotnet/EntityFramework.Docs/issues/2471).</span></span>

### <a name="scaffold-dbcontext-now-singularizes"></a><span data-ttu-id="e6a73-218">Scaffold-DbContext 现为单数形式</span><span class="sxs-lookup"><span data-stu-id="e6a73-218">Scaffold-DbContext now singularizes</span></span>

<span data-ttu-id="e6a73-219">此前，当从现有数据库搭建 DbContext 时，EF Core 将创建与数据库中的表名称匹配的实体类型名称。</span><span class="sxs-lookup"><span data-stu-id="e6a73-219">Previously when scaffolding a DbContext from an existing database, EF Core will create entity type names that match the table names in the database.</span></span> <span data-ttu-id="e6a73-220">例如，表 `People` 和 `Addresses` 对应的实体类型名称为 `People` 和 `Addresses`。</span><span class="sxs-lookup"><span data-stu-id="e6a73-220">For example, tables `People` and `Addresses` resulted in entity types named `People` and `Addresses`.</span></span>

<span data-ttu-id="e6a73-221">在之前的版本中，此行为是通过注册复数化服务来配置的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-221">In previous releases, this behavior was configurable through registration of a pluralization service.</span></span> <span data-ttu-id="e6a73-222">现在，在 EF Core 5.0 中，[Humanizer](https://www.nuget.org/packages/Humanizer.Core/) 包用作默认复数化服务。</span><span class="sxs-lookup"><span data-stu-id="e6a73-222">Now in EF Core 5.0, the [Humanizer](https://www.nuget.org/packages/Humanizer.Core/) package is used as a default pluralization service.</span></span> <span data-ttu-id="e6a73-223">这意味着 `People` 和 `Addresses` 现将被反向工程处理为名叫 `Person` 和 `Address` 的实体类型。</span><span class="sxs-lookup"><span data-stu-id="e6a73-223">This means tables `People` and `Addresses` will now be reverse engineered to entity types named `Person` and `Address`.</span></span>

### <a name="savepoints"></a><span data-ttu-id="e6a73-224">保存点</span><span class="sxs-lookup"><span data-stu-id="e6a73-224">Savepoints</span></span>

<span data-ttu-id="e6a73-225">EF Core 现支持[保存点](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks)，它们可更好地控制执行多个操作的事务。</span><span class="sxs-lookup"><span data-stu-id="e6a73-225">EF Core now supports [savepoints](/sql/t-sql/language-elements/save-transaction-transact-sql#remarks) for greater control over transactions that execute multiple operations.</span></span>

<span data-ttu-id="e6a73-226">你可手动创建、发布和回滚保存点。</span><span class="sxs-lookup"><span data-stu-id="e6a73-226">Savepoints can be manually created, released, and rolled back.</span></span> <span data-ttu-id="e6a73-227">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-227">For example:</span></span>

```csharp
context.Database.CreateSavepoint("MySavePoint");
```

<span data-ttu-id="e6a73-228">此外，在执行 `SaveChanges` 失败时，EF Core 现在会回滚到上一个保存点。</span><span class="sxs-lookup"><span data-stu-id="e6a73-228">In addition, EF Core will now roll back to the last savepoint when executing `SaveChanges` fails.</span></span> <span data-ttu-id="e6a73-229">因此，可重试 SaveChanges，而不用重试整个事务。</span><span class="sxs-lookup"><span data-stu-id="e6a73-229">This allows SaveChanges to be re-tried without re-trying the entire transaction.</span></span>

<span data-ttu-id="e6a73-230">记录信息可通过问题 [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-230">Documentation is tracked by issue [#2429](https://github.com/dotnet/EntityFramework.Docs/issues/2429).</span></span>

## <a name="preview-6"></a><span data-ttu-id="e6a73-231">预览版 6</span><span class="sxs-lookup"><span data-stu-id="e6a73-231">Preview 6</span></span>

### <a name="split-queries-for-related-collections"></a><span data-ttu-id="e6a73-232">适合相关集合的拆分查询</span><span class="sxs-lookup"><span data-stu-id="e6a73-232">Split queries for related collections</span></span>

<span data-ttu-id="e6a73-233">从 EF Core 3.0 开始，EF Core 始终会为每个 LINQ 查询生成一个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="e6a73-233">Starting with EF Core 3.0, EF Core always generates a single SQL query for each LINQ query.</span></span> <span data-ttu-id="e6a73-234">这可确保在使用中的事务模式的约束内返回的数据保持一致。</span><span class="sxs-lookup"><span data-stu-id="e6a73-234">This ensures consistency of the data returned within the constraints of the transaction mode in use.</span></span> <span data-ttu-id="e6a73-235">但是，当查询使用 `Include` 或投影来返回多个相关集合时，速度可能会变得非常慢。</span><span class="sxs-lookup"><span data-stu-id="e6a73-235">However, this can become very slow when the query uses `Include` or a projection to bring back multiple related collections.</span></span>

<span data-ttu-id="e6a73-236">EF Core 5.0 现允许将包含相关集合的一个 LINQ 查询拆分成多个 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="e6a73-236">EF Core 5.0 now allows a single LINQ query including related collections to be split into multiple SQL queries.</span></span> <span data-ttu-id="e6a73-237">这可能明显提升性能，但如果在两次查询之间数据发生了变化，则可能导致返回的结果不一致。</span><span class="sxs-lookup"><span data-stu-id="e6a73-237">This can significantly improve performance, but can result in inconsistency in the results returned if the data changes between the two queries.</span></span> <span data-ttu-id="e6a73-238">你能使用可序列化的事务或快照事务来缓解这种情况并通过拆分查询实现一致性，但这可能会带来其他性能成本并导致行为差异。</span><span class="sxs-lookup"><span data-stu-id="e6a73-238">Serializable or snapshot transactions can be used to mitigate this and achieve consistency with split queries, but that may bring other performance costs and behavioral difference.</span></span>

#### <a name="split-queries-with-include"></a><span data-ttu-id="e6a73-239">用 Include 拆分查询</span><span class="sxs-lookup"><span data-stu-id="e6a73-239">Split queries with Include</span></span>

<span data-ttu-id="e6a73-240">例如，请考虑使用 `Include` 提取两个级别的相关集合的查询：</span><span class="sxs-lookup"><span data-stu-id="e6a73-240">For example, consider a query that pulls in two levels of related collections using `Include`:</span></span>

```CSharp
var artists = context.Artists
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="e6a73-241">默认情况下，EF Core 将使用 SQLite 提供程序生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="e6a73-241">By default, EF Core will generate the following SQL when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name", "t0"."Id", "t0"."ArtistId", "t0"."Title", "t0"."Id0", "t0"."AlbumId", "t0"."Name"
FROM "Artists" AS "a"
LEFT JOIN (
    SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "t"."Id" AS "Id0", "t"."AlbumId", "t"."Name"
    FROM "Album" AS "a0"
    LEFT JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
) AS "t0" ON "a"."Id" = "t0"."ArtistId"
ORDER BY "a"."Id", "t0"."Id", "t0"."Id0"
```

<span data-ttu-id="e6a73-242">可使用新的 `AsSplitQuery` API 来更改此行为。</span><span class="sxs-lookup"><span data-stu-id="e6a73-242">The new `AsSplitQuery` API can be used to change this behavior.</span></span> <span data-ttu-id="e6a73-243">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-243">For example:</span></span>

```CSharp
var artists = context.Artists
    .AsSplitQuery()
    .Include(e => e.Albums).ThenInclude(e => e.Tags)
    .ToList();
```

<span data-ttu-id="e6a73-244">AsSplitQuery 适用于所有关系数据库提供程序，且如同 AsNoTracking 一样，可在查询中的任何位置使用。</span><span class="sxs-lookup"><span data-stu-id="e6a73-244">AsSplitQuery is available for all relational database providers and can be used anywhere in the query, just like AsNoTracking.</span></span> <span data-ttu-id="e6a73-245">EF Core 现将生成以下 3 个 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="e6a73-245">EF Core will now generate the following three SQL queries:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id", "a0"."Id"

SELECT "t"."Id", "t"."AlbumId", "t"."Name", "a"."Id", "a0"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
INNER JOIN "Tag" AS "t" ON "a0"."Id" = "t"."AlbumId"
ORDER BY "a"."Id", "a0"."Id"
```

<span data-ttu-id="e6a73-246">支持对查询根目录执行各种操作。</span><span class="sxs-lookup"><span data-stu-id="e6a73-246">All operations on the query root are supported.</span></span> <span data-ttu-id="e6a73-247">其中包括 OrderBy/Skip/Take、联接操作、FirstOrDefault 和类似的单结果选择操作。</span><span class="sxs-lookup"><span data-stu-id="e6a73-247">This includes OrderBy/Skip/Take, Join operations, FirstOrDefault and similar single result selecting operations.</span></span>

<span data-ttu-id="e6a73-248">请注意，预览版 6 中不支持带 OrderBy/Skip/Take 的 Filtered Include，但它们可在日常版本中使用，且将包含在预览版 7 中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-248">Note that filtered Includes with OrderBy/Skip/Take are not supported in preview 6, but are available in the daily builds and will be included in preview 7.</span></span>

#### <a name="split-queries-with-collection-projections"></a><span data-ttu-id="e6a73-249">用集合投影来拆分查询</span><span class="sxs-lookup"><span data-stu-id="e6a73-249">Split queries with collection projections</span></span>

<span data-ttu-id="e6a73-250">在投影中加载集合时，也可使用 `AsSplitQuery`。</span><span class="sxs-lookup"><span data-stu-id="e6a73-250">`AsSplitQuery` can also be used when collections are loaded in projections.</span></span> <span data-ttu-id="e6a73-251">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-251">For example:</span></span>

```CSharp
context.Artists
    .AsSplitQuery()
    .Select(e => new
    {
        Artist = e,
        Albums = e.Albums,
    }).ToList();
```

<span data-ttu-id="e6a73-252">使用 SQLite 提供程序时，此 LINQ 查询会生成以下两个 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="e6a73-252">This LINQ query generates the following two SQL queries when using the SQLite provider:</span></span>

```sql
SELECT "a"."Id", "a"."Name"
FROM "Artists" AS "a"
ORDER BY "a"."Id"

SELECT "a0"."Id", "a0"."ArtistId", "a0"."Title", "a"."Id"
FROM "Artists" AS "a"
INNER JOIN "Album" AS "a0" ON "a"."Id" = "a0"."ArtistId"
ORDER BY "a"."Id"
```

<span data-ttu-id="e6a73-253">请注意，仅支持将集合具体化。</span><span class="sxs-lookup"><span data-stu-id="e6a73-253">Note that only materialization of the collection is supported.</span></span> <span data-ttu-id="e6a73-254">上例中 `e.Albums` 之后的任何组合都不会产生拆分查询。</span><span class="sxs-lookup"><span data-stu-id="e6a73-254">Any composition after `e.Albums` in above case won't result in a split query.</span></span> <span data-ttu-id="e6a73-255">这方面的改进由 [#21234](https://github.com/dotnet/efcore/issues/21234) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-255">Improvements in this area are tracked by [#21234](https://github.com/dotnet/efcore/issues/21234).</span></span>

### <a name="indexattribute"></a><span data-ttu-id="e6a73-256">IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="e6a73-256">IndexAttribute</span></span>

<span data-ttu-id="e6a73-257">这个新的 IndexAttribute 可置于实体类型上来指定单列的索引。</span><span class="sxs-lookup"><span data-stu-id="e6a73-257">The new IndexAttribute can be placed on an entity type to specify an index for a single column.</span></span> <span data-ttu-id="e6a73-258">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-258">For example:</span></span>

```CSharp
[Index(nameof(FullName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(128)]
    public string FullName { get; set; }
}
```

<span data-ttu-id="e6a73-259">对于 SQL Server，迁移随后将生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="e6a73-259">For SQL Server, Migrations will then generate the following SQL:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FullName]
    ON [Users] ([FullName])
    WHERE [FullName] IS NOT NULL;
```

<span data-ttu-id="e6a73-260">IndexAttribute 也可用于指定横跨多个列的索引。</span><span class="sxs-lookup"><span data-stu-id="e6a73-260">IndexAttribute can also be used to specify an index spanning multiple columns.</span></span> <span data-ttu-id="e6a73-261">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-261">For example:</span></span>

```CSharp
[Index(nameof(FirstName), nameof(LastName), IsUnique = true)]
public class User
{
    public int Id { get; set; }

    [MaxLength(64)]
    public string FirstName { get; set; }

    [MaxLength(64)]
    public string LastName { get; set; }
}
```

<span data-ttu-id="e6a73-262">对于 SQL Server，这会导致：</span><span class="sxs-lookup"><span data-stu-id="e6a73-262">For SQL Server, this results in:</span></span>

```sql
CREATE UNIQUE INDEX [IX_Users_FirstName_LastName]
    ON [Users] ([FirstName], [LastName])
    WHERE [FirstName] IS NOT NULL AND [LastName] IS NOT NULL;
```

<span data-ttu-id="e6a73-263">记录信息可通过问题 [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-263">Documentation is tracked by issue [#2407](https://github.com/dotnet/EntityFramework.Docs/issues/2407).</span></span>

### <a name="improved-query-translation-exceptions"></a><span data-ttu-id="e6a73-264">改进了查询转换异常时显示的消息</span><span class="sxs-lookup"><span data-stu-id="e6a73-264">Improved query translation exceptions</span></span>

<span data-ttu-id="e6a73-265">我们将继续改进在查询转换失败时生成的异常消息。</span><span class="sxs-lookup"><span data-stu-id="e6a73-265">We are continuing to improve the exception messages generated when query translation fails.</span></span> <span data-ttu-id="e6a73-266">例如，以下查询使用未映射的属性 `IsSigned`：</span><span class="sxs-lookup"><span data-stu-id="e6a73-266">For example, this query uses the unmapped property `IsSigned`:</span></span>

```CSharp
var artists = context.Artists.Where(e => e.IsSigned).ToList();
```

<span data-ttu-id="e6a73-267">EF Core 将引发以下异常，指出由于 `IsSigned` 未映射而导致转换失败：</span><span class="sxs-lookup"><span data-stu-id="e6a73-267">EF Core will throw the following exception indicating that translation failed because `IsSigned` is not mapped:</span></span>

> <span data-ttu-id="e6a73-268">未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="e6a73-268">Unhandled exception.</span></span> <span data-ttu-id="e6a73-269">System.InvalidOperationException：无法转换 LINQ 表达式 "DbSet<Artist>() .Where(a => a.IsSigned)"。</span><span class="sxs-lookup"><span data-stu-id="e6a73-269">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.IsSigned)' could not be translated.</span></span> <span data-ttu-id="e6a73-270">其他信息：实体类型 "Artist" 上成员 "IsSigned" 的转换失败。</span><span class="sxs-lookup"><span data-stu-id="e6a73-270">Additional information: Translation of member 'IsSigned' on entity type 'Artist' failed.</span></span> <span data-ttu-id="e6a73-271">可能未映射指定的成员。</span><span class="sxs-lookup"><span data-stu-id="e6a73-271">Possibly the specified member is not mapped.</span></span> <span data-ttu-id="e6a73-272">请用可转换的形式重新编写查询，或者插入对 AsEnumerable()、AsAsyncEnumerable()、ToList() 或 ToListAsync() 的调用来显式切换到客户端评估。</span><span class="sxs-lookup"><span data-stu-id="e6a73-272">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="e6a73-273">有关更多信息，请参见 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="e6a73-273">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

<span data-ttu-id="e6a73-274">同样地，在尝试用依赖区域性的语义来转换字符串比较时，现将生成信息更丰富的异常消息。</span><span class="sxs-lookup"><span data-stu-id="e6a73-274">Similarly, better exception messages are now generated when attempting to translate string comparisons with culture-dependent semantics.</span></span> <span data-ttu-id="e6a73-275">例如，以下查询尝试使用 `StringComparison.CurrentCulture`：</span><span class="sxs-lookup"><span data-stu-id="e6a73-275">For example, this query attempts to use `StringComparison.CurrentCulture`:</span></span>

```CSharp
var artists = context.Artists
    .Where(e => e.Name.Equals("The Unicorns", StringComparison.CurrentCulture))
    .ToList();
```

<span data-ttu-id="e6a73-276">EF Core 现将引发以下异常：</span><span class="sxs-lookup"><span data-stu-id="e6a73-276">EF Core will now throw the following exception:</span></span>

> <span data-ttu-id="e6a73-277">未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="e6a73-277">Unhandled exception.</span></span> <span data-ttu-id="e6a73-278">System.InvalidOperationException：无法转换 LINQ 表达式 "DbSet<Artist>() .Where(a => a.Name.Equals( value:"The Unicorns", comparisonType:CurrentCulture))"。</span><span class="sxs-lookup"><span data-stu-id="e6a73-278">System.InvalidOperationException: The LINQ expression 'DbSet<Artist>() .Where(a => a.Name.Equals( value: "The Unicorns", comparisonType: CurrentCulture))' could not be translated.</span></span> <span data-ttu-id="e6a73-279">其他信息：不支持采用 "StringComparison" 的 "string.Equals" 方法的转换。</span><span class="sxs-lookup"><span data-stu-id="e6a73-279">Additional information: Translation of 'string.Equals' method which takes 'StringComparison' argument is not supported.</span></span> <span data-ttu-id="e6a73-280">有关更多信息，请参见 https://go.microsoft.com/fwlink/?linkid=2129535 。</span><span class="sxs-lookup"><span data-stu-id="e6a73-280">See https://go.microsoft.com/fwlink/?linkid=2129535 for more information.</span></span> <span data-ttu-id="e6a73-281">请用可转换的形式重新编写查询，或者插入对 AsEnumerable()、AsAsyncEnumerable()、ToList() 或 ToListAsync() 的调用来显式切换到客户端评估。</span><span class="sxs-lookup"><span data-stu-id="e6a73-281">Either rewrite the query in a form that can be translated, or switch to client evaluation explicitly by inserting a call to either AsEnumerable(), AsAsyncEnumerable(), ToList(), or ToListAsync().</span></span> <span data-ttu-id="e6a73-282">有关更多信息，请参见 https://go.microsoft.com/fwlink/?linkid=2101038 。</span><span class="sxs-lookup"><span data-stu-id="e6a73-282">See https://go.microsoft.com/fwlink/?linkid=2101038 for more information.</span></span>

### <a name="specify-transaction-id"></a><span data-ttu-id="e6a73-283">指定事务 ID</span><span class="sxs-lookup"><span data-stu-id="e6a73-283">Specify transaction ID</span></span>

<span data-ttu-id="e6a73-284">此功能是 [@Marusyk](https://github.com/Marusyk) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-284">This feature was contributed from the community by [@Marusyk](https://github.com/Marusyk).</span></span> <span data-ttu-id="e6a73-285">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="e6a73-285">Many thanks for the contribution!</span></span>

<span data-ttu-id="e6a73-286">EF Core 公开一个事务 ID 来跨调用关联事务。</span><span class="sxs-lookup"><span data-stu-id="e6a73-286">EF Core exposes a transaction ID for correlation of transactions across calls.</span></span> <span data-ttu-id="e6a73-287">此 ID 通常是在启动事务时由 EF Core 设置的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-287">This ID typically set by EF Core when a transaction is started.</span></span> <span data-ttu-id="e6a73-288">如果转而由应用程序启动事务，则应用程序可使用此功能来显式设置事务 ID，使其在所用的每个位置都正确关联。</span><span class="sxs-lookup"><span data-stu-id="e6a73-288">If the application starts the transaction instead, then this feature allows the application to explicitly set the transaction ID so it is correlated correctly everywhere it is used.</span></span> <span data-ttu-id="e6a73-289">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-289">For example:</span></span>

```CSharp
using (context.Database.UseTransaction(myTransaction, myId))
{
   ...
}
```

### <a name="ipaddress-mapping"></a><span data-ttu-id="e6a73-290">IPAddress 映射</span><span class="sxs-lookup"><span data-stu-id="e6a73-290">IPAddress mapping</span></span>

<span data-ttu-id="e6a73-291">此功能是 [@ralmsdeveloper](https://github.com/ralmsdeveloper) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-291">This feature was contributed from the community by [@ralmsdeveloper](https://github.com/ralmsdeveloper).</span></span> <span data-ttu-id="e6a73-292">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="e6a73-292">Many thanks for the contribution!</span></span>

<span data-ttu-id="e6a73-293">标准 .NET [IPAddress 类](/dotnet/api/system.net.ipaddress) 现自动映射到尚不具备原生支持的数据库的字符串列中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-293">The standard .NET [IPAddress class](/dotnet/api/system.net.ipaddress) is now automatically mapped to a string column for databases that do not already have native support.</span></span> <span data-ttu-id="e6a73-294">例如，请考虑映射以下实体类型：</span><span class="sxs-lookup"><span data-stu-id="e6a73-294">For example, consider mapping this entity type:</span></span>

```CSharp
public class Host
{
    public int Id { get; set; }
    public IPAddress Address { get; set; }
}
```

<span data-ttu-id="e6a73-295">在 SQL Server 上，这将导致迁移创建以下表：</span><span class="sxs-lookup"><span data-stu-id="e6a73-295">On SQL Server, this will result in Migrations creating the following table:</span></span>

```sql
CREATE TABLE [Host] (
    [Id] int NOT NULL,
    [Address] nvarchar(45) NULL,
    CONSTRAINT [PK_Host] PRIMARY KEY ([Id]));
```

<span data-ttu-id="e6a73-296">之后，可按正常的方式添加实体：</span><span class="sxs-lookup"><span data-stu-id="e6a73-296">Entities can then be added in the normal way:</span></span>

```CSharp
context.AddRange(
    new Host { Address = IPAddress.Parse("127.0.0.1")},
    new Host { Address = IPAddress.Parse("0000:0000:0000:0000:0000:0000:0000:0001")});
```

<span data-ttu-id="e6a73-297">所生成的 SQL 将插入规范化的 IPv4 或 IPv6 地址：</span><span class="sxs-lookup"><span data-stu-id="e6a73-297">And the resulting SQL will insert the normalized IPv4 or IPv6 address:</span></span>

```sql
Executed DbCommand (14ms) [Parameters=[@p0='1', @p1='127.0.0.1' (Size = 45), @p2='2', @p3='::1' (Size = 45)], CommandType='Text', CommandTimeout='30']
      SET NOCOUNT ON;
      INSERT INTO [Host] ([Id], [Address])
      VALUES (@p0, @p1), (@p2, @p3);
```

### <a name="exclude-onconfiguring-when-scaffolding"></a><span data-ttu-id="e6a73-298">在创建基架时排除 OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="e6a73-298">Exclude OnConfiguring when scaffolding</span></span>

<span data-ttu-id="e6a73-299">在基于现有数据库创建 DbContext 的基架时，EF Core 会默认创建一个 OnConfiguring 重载，该重载有一个连接字符串，以便上下文可立即供用户使用。</span><span class="sxs-lookup"><span data-stu-id="e6a73-299">When a DbContext is scaffolded from an existing database, EF Core by default creates an OnConfiguring overload with a connection string so that the context is immediately usable.</span></span> <span data-ttu-id="e6a73-300">不过，如果你只有带 OnConfiguring 的分部类，或者你要用其他方式部署上下文，则此方法将不起作用。</span><span class="sxs-lookup"><span data-stu-id="e6a73-300">However, this is not useful if you already have a partial class with OnConfiguring, or if you are configuring the context some other way.</span></span>

<span data-ttu-id="e6a73-301">若要解决这种情况，现可指示基架命令不生成 OnConfiguring。</span><span class="sxs-lookup"><span data-stu-id="e6a73-301">To address this, the scaffolding commands can now be instructed to omit generation of OnConfiguring.</span></span> <span data-ttu-id="e6a73-302">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-302">For example:</span></span>

```
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer --no-onconfiguring
```

<span data-ttu-id="e6a73-303">或者在包管理器控制台中：</span><span class="sxs-lookup"><span data-stu-id="e6a73-303">Or in the Package Manager Console:</span></span>

```
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer -NoOnConfiguring
```

<span data-ttu-id="e6a73-304">请注意，建议使用[命名的连接字符串和安全存储（如用户机密）](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets)。</span><span class="sxs-lookup"><span data-stu-id="e6a73-304">Note that we recommend using [a named connection string and secure storage like User Secrets](xref:core/managing-schemas/scaffolding#configuration-and-user-secrets).</span></span>

### <a name="translations-for-firstordefault-on-strings"></a><span data-ttu-id="e6a73-305">转换字符串中的 FirstOrDefault</span><span class="sxs-lookup"><span data-stu-id="e6a73-305">Translations for FirstOrDefault on strings</span></span>

<span data-ttu-id="e6a73-306">此功能是 [@dvoreckyaa](https://github.com/dvoreckyaa) 在社区中贡献的。</span><span class="sxs-lookup"><span data-stu-id="e6a73-306">This feature was contributed from the community by [@dvoreckyaa](https://github.com/dvoreckyaa).</span></span> <span data-ttu-id="e6a73-307">非常感谢贡献此功能！</span><span class="sxs-lookup"><span data-stu-id="e6a73-307">Many thanks for the contribution!</span></span>

<span data-ttu-id="e6a73-308">现将转换字符串中字符的 FirstOrDefault 和类似运算符。</span><span class="sxs-lookup"><span data-stu-id="e6a73-308">FirstOrDefault and similar operators for characters in strings are now translated.</span></span> <span data-ttu-id="e6a73-309">例如，使用 SQL Server 时，以下 LINQ 查询：</span><span class="sxs-lookup"><span data-stu-id="e6a73-309">For example, this LINQ query:</span></span>

```CSharp
context.Customers.Where(c => c.ContactName.FirstOrDefault() == 'A').ToList();
```

<span data-ttu-id="e6a73-310">将转换为以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="e6a73-310">Will be translated to the following SQL when using SQL Server:</span></span>

```sql
SELECT [c].[Id], [c].[ContactName]
FROM [Customer] AS [c]
WHERE SUBSTRING([c].[ContactName], 1, 1) = N'A'
```

### <a name="simplify-case-blocks"></a><span data-ttu-id="e6a73-311">简化 CASE 块</span><span class="sxs-lookup"><span data-stu-id="e6a73-311">Simplify case blocks</span></span>

<span data-ttu-id="e6a73-312">EF Core 现使用 CASE 块生成效果更佳的查询。</span><span class="sxs-lookup"><span data-stu-id="e6a73-312">EF Core now generates better queries with CASE blocks.</span></span> <span data-ttu-id="e6a73-313">例如，以下 LINQ 查询：</span><span class="sxs-lookup"><span data-stu-id="e6a73-313">For example, this LINQ query:</span></span>

```CSharp
context.Weapons
    .OrderBy(w => w.Name.CompareTo("Marcus' Lancer") == 0)
    .ThenBy(w => w.Id)
```

<span data-ttu-id="e6a73-314">之前在 SQL Server 上正式转换为：</span><span class="sxs-lookup"><span data-stu-id="e6a73-314">Was on SQL Server formally translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN (CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END = 0) AND CASE
        WHEN [w].[Name] = N'Marcus'' Lancer' THEN 0
        WHEN [w].[Name] > N'Marcus'' Lancer' THEN 1
        WHEN [w].[Name] < N'Marcus'' Lancer' THEN -1
    END IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

<span data-ttu-id="e6a73-315">但它现在转换为：</span><span class="sxs-lookup"><span data-stu-id="e6a73-315">But is now translated to:</span></span>

```sql
SELECT [w].[Id], [w].[AmmunitionType], [w].[IsAutomatic], [w].[Name], [w].[OwnerFullName], [w].[SynergyWithId]
FROM [Weapons] AS [w]
ORDER BY CASE
    WHEN ([w].[Name] = N'Marcus'' Lancer') AND [w].[Name] IS NOT NULL THEN CAST(1 AS bit)
    ELSE CAST(0 AS bit)
END, [w].[Id]");
```

## <a name="preview-5"></a><span data-ttu-id="e6a73-316">预览版 5</span><span class="sxs-lookup"><span data-stu-id="e6a73-316">Preview 5</span></span>

### <a name="database-collations"></a><span data-ttu-id="e6a73-317">数据库排序规则</span><span class="sxs-lookup"><span data-stu-id="e6a73-317">Database collations</span></span>

<span data-ttu-id="e6a73-318">现可在 EF 模式中指定数据库的默认排序规则。</span><span class="sxs-lookup"><span data-stu-id="e6a73-318">The default collation for a database can now be specified in the EF model.</span></span> <span data-ttu-id="e6a73-319">这将传输到生成的迁移，在创建数据库时设置排序规则。</span><span class="sxs-lookup"><span data-stu-id="e6a73-319">This will flow through to generated migrations to set the collation when the database is created.</span></span> <span data-ttu-id="e6a73-320">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-320">For example:</span></span>

```CSharp
modelBuilder.UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="e6a73-321">然后，迁移会生成以下内容，在 SQL Server 上创建数据库：</span><span class="sxs-lookup"><span data-stu-id="e6a73-321">Migrations then generates the following to create the database on SQL Server:</span></span>

```sql
CREATE DATABASE [Test]
COLLATE German_PhoneBook_CI_AS;
```

<span data-ttu-id="e6a73-322">可指定用于特定数据库列的排序规则。</span><span class="sxs-lookup"><span data-stu-id="e6a73-322">The collation to use for specific database columns can also be specified.</span></span> <span data-ttu-id="e6a73-323">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-323">For example:</span></span>

```CSharp
 modelBuilder
     .Entity<User>()
     .Property(e => e.Name)
     .UseCollation("German_PhoneBook_CI_AS");
```

<span data-ttu-id="e6a73-324">对于不使用迁移的列，可在创建 DbContext 基架时从数据库对排序规则进行反向工程处理。</span><span class="sxs-lookup"><span data-stu-id="e6a73-324">For those not using migrations, collations are now reverse-engineered from the database when scaffolding a DbContext.</span></span>

<span data-ttu-id="e6a73-325">最后，可通过 `EF.Functions.Collate()` 使用不同的排序规则进行即席查询。</span><span class="sxs-lookup"><span data-stu-id="e6a73-325">Finally, the `EF.Functions.Collate()` allows for ad-hoc queries using different collations.</span></span> <span data-ttu-id="e6a73-326">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-326">For example:</span></span>

```CSharp
context.Users.Single(e => EF.Functions.Collate(e.Name, "French_CI_AS") == "Jean-Michel Jarre");
```

<span data-ttu-id="e6a73-327">这将为 SQL Server 生成以下查询：</span><span class="sxs-lookup"><span data-stu-id="e6a73-327">This will generate the following query for SQL Server:</span></span>

```sql
SELECT TOP(2) [u].[Id], [u].[Name]
FROM [Users] AS [u]
WHERE [u].[Name] COLLATE French_CI_AS = N'Jean-Michel Jarre'
```

<span data-ttu-id="e6a73-328">请注意，应谨慎使用即席排序规则，因为它们可能会对数据库性能造成不良影响。</span><span class="sxs-lookup"><span data-stu-id="e6a73-328">Note that ad-hoc collations should be used with care as they can negatively impact database performance.</span></span>

<span data-ttu-id="e6a73-329">文档可通过问题 [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-329">Documentation is tracked by issue [#2273](https://github.com/dotnet/EntityFramework.Docs/issues/2273).</span></span>

### <a name="flow-arguments-into-idesigntimedbcontextfactory"></a><span data-ttu-id="e6a73-330">将参数传输到 IDesignTimeDbContextFactory</span><span class="sxs-lookup"><span data-stu-id="e6a73-330">Flow arguments into IDesignTimeDbContextFactory</span></span>

<span data-ttu-id="e6a73-331">参数现从命令行传输到 [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1) 的 `CreateDbContext` 方法。</span><span class="sxs-lookup"><span data-stu-id="e6a73-331">Arguments are now flowed from the command line into the `CreateDbContext` method of [IDesignTimeDbContextFactory](/dotnet/api/microsoft.entityframeworkcore.design.idesigntimedbcontextfactory-1).</span></span> <span data-ttu-id="e6a73-332">例如，若要指明这是开发生成，可以在命令行上传递自定义参数（例如 `dev`）：</span><span class="sxs-lookup"><span data-stu-id="e6a73-332">For example, to indicate this is a dev build, a custom argument (e.g. `dev`) can be passed on the command line:</span></span>

```
dotnet ef migrations add two --verbose --dev
```

<span data-ttu-id="e6a73-333">然后，该参数将传输到工厂，它在这里可用于控制如何创建和初始化上下文。</span><span class="sxs-lookup"><span data-stu-id="e6a73-333">This argument will then flow into the factory, where it can be used to control how the context is created and initialized.</span></span> <span data-ttu-id="e6a73-334">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-334">For example:</span></span>

```CSharp
public class MyDbContextFactory : IDesignTimeDbContextFactory<SomeDbContext>
{
    public SomeDbContext CreateDbContext(string[] args)
        => new SomeDbContext(args.Contains("--dev"));
}
```

<span data-ttu-id="e6a73-335">文档可通过问题 [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-335">Documentation is tracked by issue [#2419](https://github.com/dotnet/EntityFramework.Docs/issues/2419).</span></span>

### <a name="no-tracking-queries-with-identity-resolution"></a><span data-ttu-id="e6a73-336">具有标识解析的非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="e6a73-336">No-tracking queries with identity resolution</span></span>

<span data-ttu-id="e6a73-337">现可将非跟踪查询配置来执行标识解析。</span><span class="sxs-lookup"><span data-stu-id="e6a73-337">No-tracking queries can now be configured to perform identity resolution.</span></span> <span data-ttu-id="e6a73-338">例如，以下查询将为每个 Post 创建一个新的 Blog 实例，即使每个 Blog 的主键相同也是如此。</span><span class="sxs-lookup"><span data-stu-id="e6a73-338">For example, the following query will create a new Blog instance for each Post, even if each Blog has the same primary key.</span></span>

```CSharp
context.Posts.AsNoTracking().Include(e => e.Blog).ToList();
```

<span data-ttu-id="e6a73-339">然而，可更改此查询来确保只创建一个 Blog 实例，代价通常是稍微拖慢一些速度和总是使用更多的内存：</span><span class="sxs-lookup"><span data-stu-id="e6a73-339">However, at the expense of usually being slightly slower and always using more memory, this query can be changed to ensure only a single Blog instance is created:</span></span>

```CSharp
context.Posts.AsNoTracking().PerformIdentityResolution().Include(e => e.Blog).ToList();
```

<span data-ttu-id="e6a73-340">请注意，这仅适用于非跟踪查询，因为所有跟踪查询已显示此行为。</span><span class="sxs-lookup"><span data-stu-id="e6a73-340">Note that this is only useful for no-tracking queries since all tracking queries already exhibit this behavior.</span></span> <span data-ttu-id="e6a73-341">此外，在 API 评审后，将更改 `PerformIdentityResolution` 语法。</span><span class="sxs-lookup"><span data-stu-id="e6a73-341">Also, following API review, the `PerformIdentityResolution` syntax will be changed.</span></span> <span data-ttu-id="e6a73-342">请查看 [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073)。</span><span class="sxs-lookup"><span data-stu-id="e6a73-342">See [#19877](https://github.com/dotnet/efcore/issues/19877#issuecomment-637371073).</span></span>

<span data-ttu-id="e6a73-343">文档可通过问题 [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-343">Documentation is tracked by issue [#1895](https://github.com/dotnet/EntityFramework.Docs/issues/1895).</span></span>

### <a name="stored-persisted-computed-columns"></a><span data-ttu-id="e6a73-344">存储的（持久化）计算列</span><span class="sxs-lookup"><span data-stu-id="e6a73-344">Stored (persisted) computed columns</span></span>

<span data-ttu-id="e6a73-345">大多数数据库都允许在计算后存储计算得到的列值。</span><span class="sxs-lookup"><span data-stu-id="e6a73-345">Most databases allow computed column values to be stored after computation.</span></span> <span data-ttu-id="e6a73-346">虽然这会占用磁盘空间，但仅在更新时对计算的列计算一次，而不是在每次检索它的值时都计算。</span><span class="sxs-lookup"><span data-stu-id="e6a73-346">While this takes up disk space, the computed column is calculated only once on update, instead of each time its value is retrieved.</span></span> <span data-ttu-id="e6a73-347">这样也可对某些数据库编制列的索引。</span><span class="sxs-lookup"><span data-stu-id="e6a73-347">This also allows the column to be indexed for some databases.</span></span>

<span data-ttu-id="e6a73-348">EF Core 5.0 允许将计算列配置为存储计算列。</span><span class="sxs-lookup"><span data-stu-id="e6a73-348">EF Core 5.0 allows computed columns to be configured as stored.</span></span> <span data-ttu-id="e6a73-349">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-349">For example:</span></span>

```CSharp
modelBuilder
    .Entity<User>()
    .Property(e => e.SomethingComputed)
    .HasComputedColumnSql("my sql", stored: true);
```

### <a name="sqlite-computed-columns"></a><span data-ttu-id="e6a73-350">SQLite 计算列</span><span class="sxs-lookup"><span data-stu-id="e6a73-350">SQLite computed columns</span></span>

<span data-ttu-id="e6a73-351">EF Core 现支持在 SQLite 数据库中使用计算列。</span><span class="sxs-lookup"><span data-stu-id="e6a73-351">EF Core now supports computed columns in SQLite databases.</span></span>

## <a name="preview-4"></a><span data-ttu-id="e6a73-352">预览版 4</span><span class="sxs-lookup"><span data-stu-id="e6a73-352">Preview 4</span></span>

### <a name="configure-database-precisionscale-in-model"></a><span data-ttu-id="e6a73-353">在模型中配置数据库精度/小数位数</span><span class="sxs-lookup"><span data-stu-id="e6a73-353">Configure database precision/scale in model</span></span>

<span data-ttu-id="e6a73-354">现在，可以使用模型生成器指定属性的精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="e6a73-354">Precision and scale for a property can now be specified using the model builder.</span></span> <span data-ttu-id="e6a73-355">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-355">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Blog>()
    .Property(b => b.Numeric)
    .HasPrecision(16, 4);
```

<span data-ttu-id="e6a73-356">还可以通过完整的数据库类型（如“decimal(16,4)”）设置精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="e6a73-356">Precision and scale can still be set via the full database type, such as "decimal(16,4)".</span></span>

<span data-ttu-id="e6a73-357">文档可通过问题 [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-357">Documentation is tracked by issue [#527](https://github.com/dotnet/EntityFramework.Docs/issues/527).</span></span>

### <a name="specify-sql-server-index-fill-factor"></a><span data-ttu-id="e6a73-358">指定 SQL Server 索引填充因子</span><span class="sxs-lookup"><span data-stu-id="e6a73-358">Specify SQL Server index fill factor</span></span>

<span data-ttu-id="e6a73-359">现在可以在 SQL Server 上创建索引时指定填充因子。</span><span class="sxs-lookup"><span data-stu-id="e6a73-359">The fill factor can now be specified when creating an index on SQL Server.</span></span> <span data-ttu-id="e6a73-360">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-360">For example:</span></span>

```CSharp
modelBuilder
    .Entity<Customer>()
    .HasIndex(e => e.Name)
    .HasFillFactor(90);
```

## <a name="preview-3"></a><span data-ttu-id="e6a73-361">预览版 3</span><span class="sxs-lookup"><span data-stu-id="e6a73-361">Preview 3</span></span>

### <a name="filtered-include"></a><span data-ttu-id="e6a73-362">经过筛选的包含</span><span class="sxs-lookup"><span data-stu-id="e6a73-362">Filtered Include</span></span>

<span data-ttu-id="e6a73-363">Include 方法现在支持筛选包含的实体。</span><span class="sxs-lookup"><span data-stu-id="e6a73-363">The Include method now supports filtering of the entities included.</span></span> <span data-ttu-id="e6a73-364">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-364">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.Where(p => p.Title.Contains("Cheese")))
    .ToList();
```

<span data-ttu-id="e6a73-365">此查询将一并返回包含每个关联文章的博客（仅当文章标题包含“Cheese”时）。</span><span class="sxs-lookup"><span data-stu-id="e6a73-365">This query will return blogs together with each associated post, but only when the post title contains "Cheese".</span></span>

<span data-ttu-id="e6a73-366">Skip 和 Take 也可用于减少包含的实体数量。</span><span class="sxs-lookup"><span data-stu-id="e6a73-366">Skip and Take can also be used to reduce the number of included entities.</span></span> <span data-ttu-id="e6a73-367">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-367">For example:</span></span>

```CSharp
var blogs = context.Blogs
    .Include(e => e.Posts.OrderByDescending(post => post.Title).Take(5)))
    .ToList();
```
<span data-ttu-id="e6a73-368">此查询将返回博客，每个博客最多包含 5 篇文章。</span><span class="sxs-lookup"><span data-stu-id="e6a73-368">This query will return blogs with at most five posts included on each blog.</span></span>

<span data-ttu-id="e6a73-369">有关完整详细信息，请参阅 [Include 文档](xref:core/querying/related-data#filtered-include)。</span><span class="sxs-lookup"><span data-stu-id="e6a73-369">See the [Include documentation](xref:core/querying/related-data#filtered-include) for full details.</span></span>

### <a name="new-modelbuilder-api-for-navigation-properties"></a><span data-ttu-id="e6a73-370">用于导航属性的新 ModelBuilder API</span><span class="sxs-lookup"><span data-stu-id="e6a73-370">New ModelBuilder API for navigation properties</span></span>

<span data-ttu-id="e6a73-371">导航属性主要在[定义关系](xref:core/modeling/relationships)时配置。</span><span class="sxs-lookup"><span data-stu-id="e6a73-371">Navigation properties are primarily configured when [defining relationships](xref:core/modeling/relationships).</span></span> <span data-ttu-id="e6a73-372">但是，在导航属性需要额外配置的情况下，可以使用新的 `Navigation` 方法。</span><span class="sxs-lookup"><span data-stu-id="e6a73-372">However, the new `Navigation` method can be used in the cases where navigation properties need additional configuration.</span></span> <span data-ttu-id="e6a73-373">例如，如需在根据约定找不到支持字段时设置导航的支持字段，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e6a73-373">For example, to set a backing field for the navigation when the field would not be found by convention:</span></span>

```CSharp
modelBuilder.Entity<Blog>().Navigation(e => e.Posts).HasField("_myposts");
```

<span data-ttu-id="e6a73-374">请注意：`Navigation` API 不会替换关系配置。</span><span class="sxs-lookup"><span data-stu-id="e6a73-374">Note that the `Navigation` API does not replace relationship configuration.</span></span> <span data-ttu-id="e6a73-375">但是，它允许在已发现或定义的关系中进行其他导航属性配置。</span><span class="sxs-lookup"><span data-stu-id="e6a73-375">Instead it allows additional configuration of navigation properties in already discovered or defined relationships.</span></span>

<span data-ttu-id="e6a73-376">请参阅[配置导航属性文档](xref:core/modeling/relationships#configuring-navigation-properties)。</span><span class="sxs-lookup"><span data-stu-id="e6a73-376">See the [Configuring Navigation Properties documentation](xref:core/modeling/relationships#configuring-navigation-properties).</span></span>

### <a name="new-command-line-parameters-for-namespaces-and-connection-strings"></a><span data-ttu-id="e6a73-377">用于命名空间和连接字符串的新命令行参数</span><span class="sxs-lookup"><span data-stu-id="e6a73-377">New command-line parameters for namespaces and connection strings</span></span>

<span data-ttu-id="e6a73-378">迁移和基架现在允许在命令行上指定命名空间。</span><span class="sxs-lookup"><span data-stu-id="e6a73-378">Migrations and scaffolding now allow namespaces to be specified on the command line.</span></span> <span data-ttu-id="e6a73-379">例如，如需对数据库进行反向工程，将上下文和模型类放在不同的命名空间中：</span><span class="sxs-lookup"><span data-stu-id="e6a73-379">For example, to reverse engineer a database putting the context and model classes in different namespaces:</span></span>

```dotnetcli
dotnet ef dbcontext scaffold "connection string" Microsoft.EntityFrameworkCore.SqlServer --context-namespace "My.Context" --namespace "My.Model"
```

<span data-ttu-id="e6a73-380">有关完整详细信息，请参阅[迁移](xref:core/managing-schemas/migrations/index#namespaces)和[反向工程](xref:core/managing-schemas/scaffolding#directories-and-namespaces)文档。</span><span class="sxs-lookup"><span data-stu-id="e6a73-380">See the [Migrations](xref:core/managing-schemas/migrations/index#namespaces) and [Reverse Engineering](xref:core/managing-schemas/scaffolding#directories-and-namespaces) documentation for full details.</span></span>

---
<span data-ttu-id="e6a73-381">此外，连接字符串现在可以传递到 `database-update` 命令：</span><span class="sxs-lookup"><span data-stu-id="e6a73-381">Also, a connection string can now be passed to the `database-update` command:</span></span>

```dotnetcli
dotnet ef database update --connection "connection string"
```

<span data-ttu-id="e6a73-382">有关完整的详细信息，请参阅[工具文档](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update)。</span><span class="sxs-lookup"><span data-stu-id="e6a73-382">See the [Tools documentation](xref:core/miscellaneous/cli/dotnet#dotnet-ef-database-update) for full details.</span></span>

<span data-ttu-id="e6a73-383">等效参数已添加到 VS 程序包管理器控制台中使用的 PowerShell 命令中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-383">Equivalent parameters have also been added to the PowerShell commands used in the VS Package Manager Console.</span></span>

### <a name="enabledetailederrors-has-returned"></a><span data-ttu-id="e6a73-384">EnableDetailedErrors 已返回</span><span class="sxs-lookup"><span data-stu-id="e6a73-384">EnableDetailedErrors has returned</span></span>

<span data-ttu-id="e6a73-385">出于性能原因，在从数据库读取值时，EF 不会执行额外的 null 检查。</span><span class="sxs-lookup"><span data-stu-id="e6a73-385">For performance reasons, EF doesn't do additional null-checks when reading values from the database.</span></span> <span data-ttu-id="e6a73-386">当遇到意外的 null 时，这可能会导致难以查找根本原因的异常。</span><span class="sxs-lookup"><span data-stu-id="e6a73-386">This can result in exceptions that are hard to root-cause when an unexpected null is encountered.</span></span>

<span data-ttu-id="e6a73-387">使用 `EnableDetailedErrors` 会将额外的 null 检查添加到查询中，这样一来，对于较小的性能开销，这些错误就更容易追溯到根本原因。</span><span class="sxs-lookup"><span data-stu-id="e6a73-387">Using `EnableDetailedErrors` will add extra null checking to queries such that, for a small performance overhead, these errors are easier to trace back to a root cause.</span></span>

<span data-ttu-id="e6a73-388">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-388">For example:</span></span>

```CSharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .EnableDetailedErrors()
        .EnableSensitiveDataLogging() // Often also useful with EnableDetailedErrors
        .UseSqlServer(Your.SqlServerConnectionString);
```

<span data-ttu-id="e6a73-389">文档可通过问题 [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-389">Documentation is tracked by issue [#955](https://github.com/dotnet/EntityFramework.Docs/issues/955).</span></span>

### <a name="cosmos-partition-keys"></a><span data-ttu-id="e6a73-390">Cosmos 分区键</span><span class="sxs-lookup"><span data-stu-id="e6a73-390">Cosmos partition keys</span></span>

<span data-ttu-id="e6a73-391">现在可以在查询中指定用于给定查询的分区键。</span><span class="sxs-lookup"><span data-stu-id="e6a73-391">The partition key to use for a given query can now be specified in the query.</span></span> <span data-ttu-id="e6a73-392">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-392">For example:</span></span>

```CSharp
await context.Set<Customer>()
             .WithPartitionKey(myPartitionKey)
             .FirstAsync();
```

<span data-ttu-id="e6a73-393">文档可通过问题 [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-393">Documentation is tracked by issue [#2199](https://github.com/dotnet/EntityFramework.Docs/issues/2199).</span></span>

### <a name="support-for-the-sql-server-datalength-function"></a><span data-ttu-id="e6a73-394">支持 SQL Server DATALENGTH 函数</span><span class="sxs-lookup"><span data-stu-id="e6a73-394">Support for the SQL Server DATALENGTH function</span></span>

<span data-ttu-id="e6a73-395">可以使用新的 `EF.Functions.DataLength` 方法访问它。</span><span class="sxs-lookup"><span data-stu-id="e6a73-395">This can be accessed using the new `EF.Functions.DataLength` method.</span></span> <span data-ttu-id="e6a73-396">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-396">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => 100 < EF.Functions.DataLength(c.OrderDate));
```

## <a name="preview-2"></a><span data-ttu-id="e6a73-397">预览版 2</span><span class="sxs-lookup"><span data-stu-id="e6a73-397">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="e6a73-398">使用 C# 特性指定属性支持字段</span><span class="sxs-lookup"><span data-stu-id="e6a73-398">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="e6a73-399">现在，可以使用 C# 特性指定属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="e6a73-399">A C# attribute can now be used to specify the backing field for a property.</span></span> <span data-ttu-id="e6a73-400">使用此特性，即使在不能自动找到支持字段时，EF Core 也能正常读写支持字段。</span><span class="sxs-lookup"><span data-stu-id="e6a73-400">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span> <span data-ttu-id="e6a73-401">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-401">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="e6a73-402">文档可通过问题 [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-402">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="e6a73-403">完成鉴别器映射</span><span class="sxs-lookup"><span data-stu-id="e6a73-403">Complete discriminator mapping</span></span>

<span data-ttu-id="e6a73-404">EF Core 使用鉴别器列进行 [继承层次结构的 TPH 映射](xref:core/modeling/inheritance)。</span><span class="sxs-lookup"><span data-stu-id="e6a73-404">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](xref:core/modeling/inheritance).</span></span> <span data-ttu-id="e6a73-405">只要 EF Core 知道该鉴别器的所有可能的值，就可能实现某些性能改进。</span><span class="sxs-lookup"><span data-stu-id="e6a73-405">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span> <span data-ttu-id="e6a73-406">EF Core 5.0 现在可实现这些改进。</span><span class="sxs-lookup"><span data-stu-id="e6a73-406">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="e6a73-407">例如，对于返回层次结构中所有类型的查询，旧版 EF Core 总是会生成以下 SQL：</span><span class="sxs-lookup"><span data-stu-id="e6a73-407">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="e6a73-408">配置完整的鉴别器映射后，EF Core 5.0 现在将生成以下内容：</span><span class="sxs-lookup"><span data-stu-id="e6a73-408">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="e6a73-409">从预览版 3 开始，这将成为默认行为。</span><span class="sxs-lookup"><span data-stu-id="e6a73-409">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="e6a73-410">Microsoft.Data.Sqlite 中的性能改进</span><span class="sxs-lookup"><span data-stu-id="e6a73-410">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="e6a73-411">我们对 SQLIte 进行了两项性能改进：</span><span class="sxs-lookup"><span data-stu-id="e6a73-411">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="e6a73-412">现在，利用 SqliteBlob 和流，可以更高效地使用 GetBytes、GetChars 和 GetTextReader 检索二进制和字符串数据。</span><span class="sxs-lookup"><span data-stu-id="e6a73-412">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="e6a73-413">SqliteConnection 的初始化现在很慢。</span><span class="sxs-lookup"><span data-stu-id="e6a73-413">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="e6a73-414">这些改进已实施到 ADO.NET Microsoft.Data.Sqlite 提供程序中，因此还可以在 EF Core 之外提升性能。</span><span class="sxs-lookup"><span data-stu-id="e6a73-414">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="e6a73-415">预览版 1</span><span class="sxs-lookup"><span data-stu-id="e6a73-415">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="e6a73-416">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="e6a73-416">Simple logging</span></span>

<span data-ttu-id="e6a73-417">此特性会添加类似于 EF6 中 `Database.Log` 的功能。</span><span class="sxs-lookup"><span data-stu-id="e6a73-417">This feature adds functionality similar to `Database.Log` in EF6.</span></span> <span data-ttu-id="e6a73-418">也就是说，它提供了一种从 EF Core 获取日志的简单方法，而不需要配置任何类型的外部日志记录框架。</span><span class="sxs-lookup"><span data-stu-id="e6a73-418">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="e6a73-419">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-419">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="e6a73-420">其他文档可通过问题 [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-420">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="e6a73-421">获取生成的 SQL 的简单方法</span><span class="sxs-lookup"><span data-stu-id="e6a73-421">Simple way to get generated SQL</span></span>

<span data-ttu-id="e6a73-422">EF Core 5.0 引入了 `ToQueryString` 扩展方法，该方法会返回执行 LINQ 查询时 EF Core 生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="e6a73-422">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="e6a73-423">初步文档包含在 [2020 年 1 月 9 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246)中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-423">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="e6a73-424">其他文档可通过问题 [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-424">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="e6a73-425">使用 C# 特性指示实体没有键</span><span class="sxs-lookup"><span data-stu-id="e6a73-425">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="e6a73-426">现在可以将实体类型配置为不使用新 `KeylessAttribute`的键。</span><span class="sxs-lookup"><span data-stu-id="e6a73-426">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span> <span data-ttu-id="e6a73-427">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-427">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="e6a73-428">文档可通过问题 [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-428">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="e6a73-429">可在初始化的 DbContext 上更改连接或连接字符串</span><span class="sxs-lookup"><span data-stu-id="e6a73-429">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="e6a73-430">现在可以更轻松地创建 DbContext 实例，而无需任何连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e6a73-430">It is now easier to create a DbContext instance without any connection or connection string.</span></span> <span data-ttu-id="e6a73-431">而且，现在可以在上下文实例上更改连接或连接字符串。</span><span class="sxs-lookup"><span data-stu-id="e6a73-431">Also, the connection or connection string can now be mutated on the context instance.</span></span> <span data-ttu-id="e6a73-432">此功能使得同一个上下文实例能够动态地连接到不同的数据库。</span><span class="sxs-lookup"><span data-stu-id="e6a73-432">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="e6a73-433">文档可通过问题 [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-433">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="e6a73-434">更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="e6a73-434">Change-tracking proxies</span></span>

<span data-ttu-id="e6a73-435">EF Core 现在可以生成自动实现 [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) 和 [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged) 的运行时代理。</span><span class="sxs-lookup"><span data-stu-id="e6a73-435">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](/dotnet/api/system.componentmodel.inotifypropertychanging) and [INotifyPropertyChanged](/dotnet/api/system.componentmodel.inotifypropertychanged).</span></span> <span data-ttu-id="e6a73-436">这些代理会将实体属性的值更改直接报告给 EF Core，从而无需扫描更改。</span><span class="sxs-lookup"><span data-stu-id="e6a73-436">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span> <span data-ttu-id="e6a73-437">不过，代理有其自身的一组限制，因此并不适合所有人使用。</span><span class="sxs-lookup"><span data-stu-id="e6a73-437">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="e6a73-438">文档可通过问题 [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-438">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="e6a73-439">增强的调试视图</span><span class="sxs-lookup"><span data-stu-id="e6a73-439">Enhanced debug views</span></span>

<span data-ttu-id="e6a73-440">调试视图是调试问题时查看 EF Core 内部情况的一种简单方法。</span><span class="sxs-lookup"><span data-stu-id="e6a73-440">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span> <span data-ttu-id="e6a73-441">在一段时间之前，我们就已经实现了模型的调试视图。</span><span class="sxs-lookup"><span data-stu-id="e6a73-441">A debug view for the Model was implemented some time ago.</span></span> <span data-ttu-id="e6a73-442">对于 EF Core 5.0，我们使模型视图更易于读取，并在状态管理器中为跟踪的实体添加了新的调试视图。</span><span class="sxs-lookup"><span data-stu-id="e6a73-442">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="e6a73-443">初步文档包含在 [2019 年 12 月 12 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206)中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-443">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="e6a73-444">其他文档可通过问题 [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-444">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="e6a73-445">改进了对数据库 null 语义的处理</span><span class="sxs-lookup"><span data-stu-id="e6a73-445">Improved handling of database null semantics</span></span>

<span data-ttu-id="e6a73-446">关系数据库通常将 NULL 视为未知值，因此它不等于任何其他 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="e6a73-446">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span> <span data-ttu-id="e6a73-447">而 C# 将 null 视为与其他任何 null 相比均相等的定义值。</span><span class="sxs-lookup"><span data-stu-id="e6a73-447">While C# treats null as a defined value, which compares equal to any other null.</span></span> <span data-ttu-id="e6a73-448">默认情况下，EF Core 会转换查询，使查询使用 C# null 语义。</span><span class="sxs-lookup"><span data-stu-id="e6a73-448">EF Core by default translates queries so that they use C# null semantics.</span></span> <span data-ttu-id="e6a73-449">EF Core 5.0 极大地提升了这些转换操作的效率。</span><span class="sxs-lookup"><span data-stu-id="e6a73-449">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="e6a73-450">文档可通过问题 [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-450">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="e6a73-451">索引器属性</span><span class="sxs-lookup"><span data-stu-id="e6a73-451">Indexer properties</span></span>

<span data-ttu-id="e6a73-452">EF Core 5.0 支持 C# 索引器属性的映射。</span><span class="sxs-lookup"><span data-stu-id="e6a73-452">EF Core 5.0 supports mapping of C# indexer properties.</span></span> <span data-ttu-id="e6a73-453">这写属性使得实体能够充当属性包，实体中的列被映射为包中的命名属性。</span><span class="sxs-lookup"><span data-stu-id="e6a73-453">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="e6a73-454">文档可通过问题 [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-454">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="e6a73-455">为枚举映射生成检查约束</span><span class="sxs-lookup"><span data-stu-id="e6a73-455">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="e6a73-456">EF Core 5.0 迁移现可为枚举属性映射生成检查约束。</span><span class="sxs-lookup"><span data-stu-id="e6a73-456">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span> <span data-ttu-id="e6a73-457">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-457">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="e6a73-458">文档可通过问题 [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-458">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="e6a73-459">IsRelational</span><span class="sxs-lookup"><span data-stu-id="e6a73-459">IsRelational</span></span>

<span data-ttu-id="e6a73-460">除了现有 `IsSqlServer`、`IsSqlite` 和 `IsInMemory` 外，还添加了新的 `IsRelational` 方法。</span><span class="sxs-lookup"><span data-stu-id="e6a73-460">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span> <span data-ttu-id="e6a73-461">此方法可用于测试 DbContext 是否使用任何关系数据库提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6a73-461">This method can be used to test if the DbContext is using any relational database provider.</span></span> <span data-ttu-id="e6a73-462">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-462">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="e6a73-463">文档可通过问题 [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-463">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="e6a73-464">使用 ETag 的 Cosmos 开放式并发</span><span class="sxs-lookup"><span data-stu-id="e6a73-464">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="e6a73-465">Azure Cosmos DB 数据库提供程序现在支持使用 ETag 的开放式并发。</span><span class="sxs-lookup"><span data-stu-id="e6a73-465">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span> <span data-ttu-id="e6a73-466">使用 OnModelCreating 中的模型生成器配置 ETag：</span><span class="sxs-lookup"><span data-stu-id="e6a73-466">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="e6a73-467">然后，SaveChanges 将在并发冲突上引发 `DbUpdateConcurrencyException`，[可以处理](xref:core/saving/concurrency)它来实现重试等。</span><span class="sxs-lookup"><span data-stu-id="e6a73-467">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](xref:core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="e6a73-468">文档可通过问题 [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-468">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="e6a73-469">查询转换以获取更多 DateTime 构造</span><span class="sxs-lookup"><span data-stu-id="e6a73-469">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="e6a73-470">现在，包含新 DataTime 构造的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="e6a73-470">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="e6a73-471">此外，现在映射了以下 SQL Server 函数：</span><span class="sxs-lookup"><span data-stu-id="e6a73-471">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="e6a73-472">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="e6a73-472">DateDiffWeek</span></span>
* <span data-ttu-id="e6a73-473">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="e6a73-473">DateFromParts</span></span>

<span data-ttu-id="e6a73-474">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-474">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="e6a73-475">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-475">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="e6a73-476">查询转换以获取更多字节数组构造</span><span class="sxs-lookup"><span data-stu-id="e6a73-476">Query translations for more byte array constructs</span></span>

<span data-ttu-id="e6a73-477">现在，使用 Contains、Length、SequenceEqual 等对 byte[] 属性进行的查询会转换成 SQL。</span><span class="sxs-lookup"><span data-stu-id="e6a73-477">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="e6a73-478">初步文档包含在 [2019 年 12 月 5 日发布的 EF 每周状态](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863)中。</span><span class="sxs-lookup"><span data-stu-id="e6a73-478">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="e6a73-479">更多文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-479">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="e6a73-480">反向查询转换</span><span class="sxs-lookup"><span data-stu-id="e6a73-480">Query translation for Reverse</span></span>

<span data-ttu-id="e6a73-481">现在，使用 `Reverse` 的查询会被转换。</span><span class="sxs-lookup"><span data-stu-id="e6a73-481">Queries using `Reverse` are now translated.</span></span> <span data-ttu-id="e6a73-482">例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-482">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="e6a73-483">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-483">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="e6a73-484">按位运算符的查询转换</span><span class="sxs-lookup"><span data-stu-id="e6a73-484">Query translation for bitwise operators</span></span>

<span data-ttu-id="e6a73-485">现在，使用按位运算符的查询会在更多的情况下被转换，例如：</span><span class="sxs-lookup"><span data-stu-id="e6a73-485">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="e6a73-486">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-486">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="e6a73-487">Cosmos 上的字符串的查询转换</span><span class="sxs-lookup"><span data-stu-id="e6a73-487">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="e6a73-488">现在，在使用 Azure Cosmos DB 提供程序的情况下，使用字符串方法 Contains、StartsWith 和 EndsWith 的查询将被转换。</span><span class="sxs-lookup"><span data-stu-id="e6a73-488">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="e6a73-489">文档可通过问题 [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) 进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="e6a73-489">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

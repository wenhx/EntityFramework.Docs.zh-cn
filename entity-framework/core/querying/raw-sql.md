---
title: 原生 SQL 查询 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: d8f52edfdf4bd7776ab8d81185c867cbfd7bcf44
ms.sourcegitcommit: 6c28926a1e35e392b198a8729fc13c1c1968a27b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813594"
---
# <a name="raw-sql-queries"></a><span data-ttu-id="0ed24-102">原生 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="0ed24-102">Raw SQL Queries</span></span>

<span data-ttu-id="0ed24-103">通过 Entity Framework Core 可以在使用关系数据库时下降到原始 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="0ed24-103">Entity Framework Core allows you to drop down to raw SQL queries when working with a relational database.</span></span> <span data-ttu-id="0ed24-104">如果无法使用 LINQ 表达要执行的查询，活着因使用 LINQ 查询而导致 SQL 查询效率低下，则这非常有用。</span><span class="sxs-lookup"><span data-stu-id="0ed24-104">This can be useful if the query you want to perform can't be expressed using LINQ, or if using a LINQ query is resulting in an inefficient SQL query.</span></span> <span data-ttu-id="0ed24-105">原始 SQL 查询可返回一般实体类型或者模型中的[无键实体类型](xref:core/modeling/keyless-entity-types)。</span><span class="sxs-lookup"><span data-stu-id="0ed24-105">Raw SQL queries can return regular entity types or [keyless entity types](xref:core/modeling/keyless-entity-types) that are part of your model.</span></span>

> [!TIP]  
> <span data-ttu-id="0ed24-106">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs)。</span><span class="sxs-lookup"><span data-stu-id="0ed24-106">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs) on GitHub.</span></span>

## <a name="basic-raw-sql-queries"></a><span data-ttu-id="0ed24-107">基本原生 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="0ed24-107">Basic raw SQL queries</span></span>

<span data-ttu-id="0ed24-108">可使用 `FromSqlRaw` 扩展方法基于原始 SQL 查询开始 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="0ed24-108">You can use the `FromSqlRaw` extension method to begin a LINQ query based on a raw SQL query.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("SELECT * FROM dbo.Blogs")
    .ToList();
```

<span data-ttu-id="0ed24-109">原生 SQL 查询可用于执行存储过程。</span><span class="sxs-lookup"><span data-stu-id="0ed24-109">Raw SQL queries can be used to execute a stored procedure.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a><span data-ttu-id="0ed24-110">传递参数</span><span class="sxs-lookup"><span data-stu-id="0ed24-110">Passing parameters</span></span>

> [!WARNING]
> <span data-ttu-id="0ed24-111">**始终对原始 SQL 查询使用参数化**</span><span class="sxs-lookup"><span data-stu-id="0ed24-111">**Always use parameterization for raw SQL queries**</span></span>
>
> <span data-ttu-id="0ed24-112">向原始 SQL 查询引入任何用户提供的值时，必须注意防范 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="0ed24-112">When introducing any user-provided values into a raw SQL query, care must be taken to avoid SQL injection attacks.</span></span> <span data-ttu-id="0ed24-113">除了验证确保此类值不包含无效字符，请始终使用会将值与 SQL 文本分开发送的参数化处理。</span><span class="sxs-lookup"><span data-stu-id="0ed24-113">In addition to validating that such values don't contain invalid characters, always use parameterization which sends the values separate from the SQL text.</span></span>
>
> <span data-ttu-id="0ed24-114">尤其是，如果连接和内插的字符串 (`$""`) 带有用户提供的未经验证的值，则切勿将其传递到 `FromSqlRaw` 或 `ExecuteSqlRaw`。</span><span class="sxs-lookup"><span data-stu-id="0ed24-114">In particular, never pass a concatenated or interpolated string (`$""`) with unvalidated user-provided values into `FromSqlRaw` or `ExecuteSqlRaw`.</span></span> <span data-ttu-id="0ed24-115">通过 `FromSqlInterpolated` 和 `ExecuteSqlInterpolated` 方法，可采用一种能抵御 SQL 注入攻击的方式使用字符串内插语法。</span><span class="sxs-lookup"><span data-stu-id="0ed24-115">The `FromSqlInterpolated` and `ExecuteSqlInterpolated` methods allow using string interpolation syntax in a way that protects against SQL injection attacks.</span></span>

<span data-ttu-id="0ed24-116">下面的示例通过在 SQL 查询字符串中包含形参占位符并提供额外的实参，将单个形参传递到存储过程。</span><span class="sxs-lookup"><span data-stu-id="0ed24-116">The following example passes a single parameter to a stored procedure by including a parameter placeholder in the SQL query string and providing an additional argument.</span></span> <span data-ttu-id="0ed24-117">虽然这可能看上去像 `String.Format` 语法，但提供的值包装在 `DbParameter` 中，且生成的形参名称插入到指定 `{0}` 占位符的位置。</span><span class="sxs-lookup"><span data-stu-id="0ed24-117">While this may look like `String.Format` syntax, the supplied value is wrapped in a `DbParameter` and the generated parameter name inserted where the `{0}` placeholder was specified.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

<span data-ttu-id="0ed24-118">可使用 `FromSqlInterpolated` 替代 `FromSqlRaw`，前者可实现字符串内插的安全使用。</span><span class="sxs-lookup"><span data-stu-id="0ed24-118">As an alternative to `FromSqlRaw`, you can use `FromSqlInterpolated` which allows the safe use of string interpolation.</span></span> <span data-ttu-id="0ed24-119">与上述示例一样，该值会转换为 `DbParameter`，因此不易受到 SQL 注入攻击：</span><span class="sxs-lookup"><span data-stu-id="0ed24-119">As with the previous example, the value is converted to a `DbParameter` and is therefore not vulnerable to SQL injection:</span></span>

> [!NOTE]
> <span data-ttu-id="0ed24-120">在版本 3.0 之前，`FromSqlRaw` 和 `FromSqlInterpolated` 是名为 `FromSql` 的两个重载。</span><span class="sxs-lookup"><span data-stu-id="0ed24-120">Prior to version 3.0, `FromSqlRaw` and `FromSqlInterpolated` were two overloads named `FromSql`.</span></span> <span data-ttu-id="0ed24-121">有关详细信息，请参阅[历史版本部分](#previous-versions)。</span><span class="sxs-lookup"><span data-stu-id="0ed24-121">See the [previous versions section](#previous-versions) for more details.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlInterpolated($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

<span data-ttu-id="0ed24-122">还可以构造 DbParameter 并将其作为参数值提供。</span><span class="sxs-lookup"><span data-stu-id="0ed24-122">You can also construct a DbParameter and supply it as a parameter value.</span></span> <span data-ttu-id="0ed24-123">由于使用了常规 SQL 参数占位符而不是字符串占位符，因此可安全地使用 `FromSqlRaw`：</span><span class="sxs-lookup"><span data-stu-id="0ed24-123">Since a regular SQL parameter placeholder is used, rather than a string placeholder, `FromSqlRaw` can be safely used:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

<span data-ttu-id="0ed24-124">这样，你就能在 SQL 查询字符串中使用已命名的参数，这在存储的流程具有可选参数时非常有用：</span><span class="sxs-lookup"><span data-stu-id="0ed24-124">This allows you to use named parameters in the SQL query string, which is useful when a stored procedure has optional parameters:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a><span data-ttu-id="0ed24-125">使用 LINQ 编写</span><span class="sxs-lookup"><span data-stu-id="0ed24-125">Composing with LINQ</span></span>

<span data-ttu-id="0ed24-126">如果发送到数据库中的 SQL 查询是可组合的，则可以在原始 SQL 查询后面紧跟着使用 LINQ 运算符。</span><span class="sxs-lookup"><span data-stu-id="0ed24-126">If the SQL query can be composed on in the database, then you can compose on top of the initial raw SQL query using LINQ operators.</span></span> <span data-ttu-id="0ed24-127">以 `SELECT` 关键字开始的 SQL 查询一般是可组合的。</span><span class="sxs-lookup"><span data-stu-id="0ed24-127">SQL queries that can be composed on begin with the `SELECT` keyword.</span></span>

<span data-ttu-id="0ed24-128">下面的示例使用原始 SQL 从一个表值函数 (Table-Valued Function，TVF) 中进行查询，然后结合使用 LINQ 在其上进行筛选和排序。</span><span class="sxs-lookup"><span data-stu-id="0ed24-128">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF) and then composes on it using LINQ to perform filtering and sorting.</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

<span data-ttu-id="0ed24-129">这将生成以下 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="0ed24-129">This will produce the following SQL query:</span></span>

``` sql
SELECT [b].[Id], [b].[Name], [b].[Rating]
        FROM (
            SELECT * FROM dbo.SearchBlogs(@p0)
        ) AS b
        WHERE b."Rating" > 3
        ORDER BY b."Rating" DESC
```

## <a name="change-tracking"></a><span data-ttu-id="0ed24-130">更改跟踪</span><span class="sxs-lookup"><span data-stu-id="0ed24-130">Change Tracking</span></span>

<span data-ttu-id="0ed24-131">使用 `FromSql` 方法的查询遵循与 EF Core 中所有其他 LINQ 查询完全相同的更改跟踪规则。</span><span class="sxs-lookup"><span data-stu-id="0ed24-131">Queries that use the `FromSql` methods follow the exact same change tracking rules as any other LINQ query in EF Core.</span></span> <span data-ttu-id="0ed24-132">例如，如果该查询投影实体类型，默认情况下会跟踪结果。</span><span class="sxs-lookup"><span data-stu-id="0ed24-132">For example, if the query projects entity types, the results will be tracked by default.</span></span>

<span data-ttu-id="0ed24-133">下面的示例使用原始 SQL 查询，该查询从表值函数 (TVF) 中进行选择，然后禁用通过对 `AsNoTracking` 的调用来更改跟踪：</span><span class="sxs-lookup"><span data-stu-id="0ed24-133">The following example uses a raw SQL query that selects from a Table-Valued Function (TVF), then disables change tracking with the call to `AsNoTracking`:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a><span data-ttu-id="0ed24-134">包含关联数据</span><span class="sxs-lookup"><span data-stu-id="0ed24-134">Including related data</span></span>

<span data-ttu-id="0ed24-135">`Include` 方法可用于添加相关数据，就像对其他 LINQ 查询那样：</span><span class="sxs-lookup"><span data-stu-id="0ed24-135">The `Include` method can be used to include related data, just like with any other LINQ query:</span></span>

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

<span data-ttu-id="0ed24-136">请注意，这要求原始 SQL 查询可组合；它明显不可用于存储过程调用。</span><span class="sxs-lookup"><span data-stu-id="0ed24-136">Note that this requires your raw SQL query to be composable; it will notably not work with stored procedure calls.</span></span> <span data-ttu-id="0ed24-137">请在 [限制](#limitations)下查看有关可组合性的说明。</span><span class="sxs-lookup"><span data-stu-id="0ed24-137">See notes on composability under [Limitations](#limitations)).</span></span>

## <a name="limitations"></a><span data-ttu-id="0ed24-138">限制</span><span class="sxs-lookup"><span data-stu-id="0ed24-138">Limitations</span></span>

<span data-ttu-id="0ed24-139">使用原生 SQL 查询时需注意以下几个限制：</span><span class="sxs-lookup"><span data-stu-id="0ed24-139">There are a few limitations to be aware of when using raw SQL queries:</span></span>

* <span data-ttu-id="0ed24-140">SQL 查询必须返回实体类型的所有属性的数据。</span><span class="sxs-lookup"><span data-stu-id="0ed24-140">The SQL query must return data for all properties of the entity type.</span></span>

* <span data-ttu-id="0ed24-141">结果集中的列名必须与属性映射到的列名称匹配。</span><span class="sxs-lookup"><span data-stu-id="0ed24-141">The column names in the result set must match the column names that properties are mapped to.</span></span> <span data-ttu-id="0ed24-142">请注意，这与 EF6 不同，EF6 中忽略了原始 SQL 查询时的属性/列映射关系，只需结果集列名与属性名相匹配即可。</span><span class="sxs-lookup"><span data-stu-id="0ed24-142">Note this is different from EF6 where property/column mapping was ignored for raw SQL queries and result set column names had to match the property names.</span></span>

* <span data-ttu-id="0ed24-143">SQL 查询不能包含关联数据。</span><span class="sxs-lookup"><span data-stu-id="0ed24-143">The SQL query cannot contain related data.</span></span> <span data-ttu-id="0ed24-144">但是，在许多情况下你可以在查询后面紧跟着使用 `Include` 方法以返回关联数据（请参阅[包含关联数据](#including-related-data)）。</span><span class="sxs-lookup"><span data-stu-id="0ed24-144">However, in many cases you can compose on top of the query using the `Include` operator to return related data (see [Including related data](#including-related-data)).</span></span>

* <span data-ttu-id="0ed24-145">传递到此方法的 `SELECT` 语句通常应该是可组合的：如果 EF Core 需要在服务器端计算更多查询运算符（例如，转换 `FromSql` 方法后跟的 LINQ 运算符），所提供的 SQL 会被视为子查询。</span><span class="sxs-lookup"><span data-stu-id="0ed24-145">`SELECT` statements passed to this method should generally be composable: If EF Core needs to evaluate additional query operators on the server (for example, to translate LINQ operators applied after `FromSql` methods), the supplied SQL will be treated as a subquery.</span></span> <span data-ttu-id="0ed24-146">这意味着传递的 SQL 不应包含子查询上无效的任何字符或选项，如：</span><span class="sxs-lookup"><span data-stu-id="0ed24-146">This means that the SQL passed should not contain any characters or options that are not valid on a subquery, such as:</span></span>
  * <span data-ttu-id="0ed24-147">结尾分号</span><span class="sxs-lookup"><span data-stu-id="0ed24-147">A trailing semicolon</span></span>
  * <span data-ttu-id="0ed24-148">在 SQL Server 上，结尾处的查询级提示（例如，`OPTION (HASH JOIN)`）</span><span class="sxs-lookup"><span data-stu-id="0ed24-148">On SQL Server, a trailing query-level hint (for example, `OPTION (HASH JOIN)`)</span></span>
  * <span data-ttu-id="0ed24-149">在 SQL Server 上，`SELECT` 子句中不带 `OFFSET 0` 或 `TOP 100 PERCENT` 的 `ORDER BY` 子句</span><span class="sxs-lookup"><span data-stu-id="0ed24-149">On SQL Server, an `ORDER BY` clause that is not accompanied of `OFFSET 0` OR `TOP 100 PERCENT` in the `SELECT` clause</span></span>

* <span data-ttu-id="0ed24-150">请注意，SQL Server 不允许对存储过程调用，因此任何尝试向此类调用应用其他查询运算符的操作都将导致无效的 SQL。</span><span class="sxs-lookup"><span data-stu-id="0ed24-150">Note that SQL Server does not allow composing over stored procedure calls, so any attempt to apply additional query operators to such a call will result in invalid SQL.</span></span> <span data-ttu-id="0ed24-151">可在 `AsEnumerable()` 后引入查询运算符供客户端评估。</span><span class="sxs-lookup"><span data-stu-id="0ed24-151">Query operators may be introduced after `AsEnumerable()` for client evaluation.</span></span>

## <a name="previous-versions"></a><span data-ttu-id="0ed24-152">以前的版本</span><span class="sxs-lookup"><span data-stu-id="0ed24-152">Previous versions</span></span>

<span data-ttu-id="0ed24-153">EF Core 版本 2.2 及更低版本具有两个名为 `FromSql` 的重载，它们的行为方式与较新的 `FromSqlRaw` 和 `FromSqlInterpolated` 的相同。</span><span class="sxs-lookup"><span data-stu-id="0ed24-153">EF Core version 2.2 and earlier had two overloads named `FromSql` which behaved in the same way as the newer `FromSqlRaw` and `FromSqlInterpolated`.</span></span> <span data-ttu-id="0ed24-154">这使得在意图调用内插字符串方法时很容易意外调用原始字符串方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="0ed24-154">This made it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span> <span data-ttu-id="0ed24-155">这会导致查询中的本该参数化的结果没有参数化。</span><span class="sxs-lookup"><span data-stu-id="0ed24-155">This could result in queries not being parameterized when they should have been.</span></span>

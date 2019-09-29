---
title: 原生 SQL 查询 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: ebec5775770c0f1e297eaaf35bf644c605a69afc
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197768"
---
# <a name="raw-sql-queries"></a>原生 SQL 查询

通过 Entity Framework Core 可以在使用关系数据库时下降到原始 SQL 查询。 如果无法使用 LINQ 表达要执行的查询，活着因使用 LINQ 查询而导致 SQL 查询效率低下，则这非常有用。 原始 SQL 查询可返回一般实体类型或者模型中的[无键实体类型](xref:core/modeling/keyless-entity-types)。

> [!TIP]  
> 可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying/Querying/RawSQL/Sample.cs)。

## <a name="basic-raw-sql-queries"></a>基本原生 SQL 查询

可使用 `FromSqlRaw` 扩展方法基于原始 SQL 查询开始 LINQ 查询。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("SELECT * FROM dbo.Blogs")
    .ToList();
```

原生 SQL 查询可用于执行存储过程。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>快速参考

> [!WARNING]
> **始终对原始 SQL 查询使用参数化**
>
> 向原始 SQL 查询引入任何用户提供的值时，必须注意防范 SQL 注入攻击。 除了验证确保此类值不包含无效字符，请始终使用会将值与 SQL 文本分开发送的参数化处理。
>
> 尤其是，如果连接和内插的字符串 (`$""`) 带有用户提供的未经验证的值，则切勿将其传递到 `FromSqlRaw` 或 `ExecuteSqlRaw`。 通过 `FromSqlInterpolated` 和 `ExecuteSqlInterpolated` 方法，可采用一种能抵御 SQL 注入攻击的方式使用字符串内插语法。

下面的示例通过在 SQL 查询字符串中包含形参占位符并提供额外的实参，将单个形参传递到存储过程。 虽然这可能看上去像 `String.Format` 语法，但提供的值包装在 `DbParameter` 中，且生成的形参名称插入到指定 `{0}` 占位符的位置。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

可使用 `FromSqlInterpolated` 替代 `FromSqlRaw`，前者可实现字符串内插的安全使用。 与上述示例一样，该值会转换为 `DbParameter`，因此不易受到 SQL 注入攻击：

> [!NOTE]
> 在版本 3.0 之前，`FromSqlRaw` 和 `FromSqlInterpolated` 是名为 `FromSql` 的两个重载。 有关详细信息，请参阅[历史版本部分](#previous-versions)。


<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSqlInterpolated($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

还可以构造 DbParameter 并将其作为参数值提供。 由于使用了常规 SQL 参数占位符而不是字符串占位符，因此可安全地使用 `FromSqlRaw`：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

这样，你就能在 SQL 查询字符串中使用已命名的参数，这在存储的流程具有可选参数时非常有用：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSqlRaw("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>使用 LINQ 编写

如果发送到数据库中的 SQL 查询是可组合的，则可以在原始 SQL 查询后面紧跟着使用 LINQ 运算符。 以 `SELECT` 关键字开始的 SQL 查询一般是可组合的。

下面的示例使用原始 SQL 从一个表值函数 (Table-Valued Function，TVF) 中进行查询，然后结合使用 LINQ 在其上进行筛选和排序。

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

这将生成以下 SQL 查询：

``` sql
SELECT [b].[Id], [b].[Name], [b].[Rating]
        FROM (
            SELECT * FROM dbo.SearchBlogs(@p0)
        ) AS b
        WHERE b."Rating" > 3
        ORDER BY b."Rating" DESC
```

## <a name="change-tracking"></a>更改跟踪

使用 `FromSql` 方法的查询遵循与 EF Core 中所有其他 LINQ 查询完全相同的更改跟踪规则。 例如，如果该查询投影实体类型，默认情况下会跟踪结果。

下面的示例使用原始 SQL 查询，该查询从表值函数 (TVF) 中进行选择，然后禁用通过对 `AsNoTracking` 的调用来更改跟踪：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a>包含关联数据

`Include` 方法可用于添加相关数据，就像对其他 LINQ 查询那样：

<!-- [!code-csharp[Main](samples/core/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSqlInterpolated($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

请注意，这要求原始 SQL 查询可组合；它明显不可用于存储过程调用。 请在 [限制](#limitations)下查看有关可组合性的说明。

## <a name="limitations"></a>限制

使用原生 SQL 查询时需注意以下几个限制：

* SQL 查询必须返回实体类型的所有属性的数据。

* 结果集中的列名必须与属性映射到的列名称匹配。 请注意，这与 EF6 不同，EF6 中忽略了原始 SQL 查询时的属性/列映射关系，只需结果集列名与属性名相匹配即可。

* SQL 查询不能包含关联数据。 但是，在许多情况下你可以在查询后面紧跟着使用 `Include` 方法以返回关联数据（请参阅[包含关联数据](#including-related-data)）。

* 传递到此方法的 `SELECT` 语句通常应该是可组合的：如果 EF Core 需要在服务器端计算更多查询运算符（例如，转换 `FromSql` 方法后跟的 LINQ 运算符），所提供的 SQL 会被视为子查询。 这意味着传递的 SQL 不应包含子查询上无效的任何字符或选项，如：
  * 结尾分号
  * 在 SQL Server 上，结尾处的查询级提示（例如，`OPTION (HASH JOIN)`）
  * 在 SQL Server 上，`SELECT` 子句中不带 `OFFSET 0` 或 `TOP 100 PERCENT` 的 `ORDER BY` 子句

* 请注意，SQL Server 不允许对存储过程调用，因此任何尝试向此类调用应用其他查询运算符的操作都将导致无效的 SQL。 可在 `AsEnumerable()` 后引入查询运算符供客户端评估。

# <a name="previous-versions"></a>早期版本

EF Core 版本 2.2 及更低版本具有两个名为 `FromSql` 的重载，它们的行为方式与较新的 `FromSqlRaw` 和 `FromSqlInterpolated` 的相同。 这使得在意图调用内插字符串方法时很容易意外调用原始字符串方法，反之亦然。 这会导致查询中的本该参数化的结果没有参数化。

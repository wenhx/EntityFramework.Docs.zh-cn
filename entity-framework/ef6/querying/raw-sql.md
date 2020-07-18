---
title: 原始 SQL 查询-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: d336066b982e682e81067bbdac5b3781524cf6be
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451211"
---
# <a name="raw-sql-queries-ef6"></a>原始 SQL 查询（EF6）

实体框架允许使用 LINQ 和实体类进行查询。 但是，有时您可能想要对数据库直接使用原始 SQL 来运行查询。 这包括调用存储过程，这对于当前不支持映射到存储过程的 Code First 模型非常有用。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

## <a name="writing-sql-queries-for-entities"></a>为实体编写 SQL 查询  

DbSet 上的 SqlQuery 方法允许编写原始 SQL 查询来返回实体实例。 返回的对象将由上下文跟踪，就像它们是通过 LINQ 查询返回的一样。 例如：  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

请注意，就像 LINQ 查询一样，查询在枚举结果之前不会执行，在上面的示例中，此操作是通过调用 System.linq.enumerable.tolist 来完成的。  

出于两个原因而编写原始 SQL 查询时应谨慎。 首先，应编写查询以确保它仅返回真正请求类型的实体。 例如，使用继承等功能时，可以轻松编写一个查询，该查询将创建错误的 CLR 类型的实体。  

其次，某些类型的原始 SQL 查询会暴露潜在的安全风险，尤其是围绕 SQL 注入式攻击。 确保在查询中使用参数，以防止此类攻击。  

### <a name="loading-entities-from-stored-procedures"></a>从存储过程加载实体  

可以使用 DbSet 从存储过程的结果中加载实体。 例如，下面的代码调用 dbo。数据库中的 GetBlogs 过程：  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

还可以使用以下语法将参数传递给存储过程：  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>为非实体类型编写 SQL 查询  

返回任何类型的实例的 SQL 查询（包括基元类型）都可以在数据库类上使用 SqlQuery 方法创建。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

即使对象是实体类型的实例，上下文也永远不会跟踪从数据库的 SqlQuery 返回的结果。  

## <a name="sending-raw-commands-to-the-database"></a>向数据库发送原始命令  

可以使用数据库上的 ExecuteSqlCommand 方法将非查询命令发送到数据库。 例如：  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

请注意，使用 ExecuteSqlCommand 对数据库中的数据所做的任何更改在从数据库中加载或重新加载实体之前，对上下文的更改是不透明的。  

### <a name="output-parameters"></a>输出参数  

如果使用了 output 参数，则在完全读取结果之前，它们的值将不可用。 这是因为 DbDataReader 的基础行为，有关详细信息，请参阅[使用 DataReader 检索数据](https://go.microsoft.com/fwlink/?LinkID=398589)。  

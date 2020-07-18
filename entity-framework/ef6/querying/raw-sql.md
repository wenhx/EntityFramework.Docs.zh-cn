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
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="0e3dd-102">原始 SQL 查询（EF6）</span><span class="sxs-lookup"><span data-stu-id="0e3dd-102">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="0e3dd-103">实体框架允许使用 LINQ 和实体类进行查询。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-103">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="0e3dd-104">但是，有时您可能想要对数据库直接使用原始 SQL 来运行查询。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-104">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="0e3dd-105">这包括调用存储过程，这对于当前不支持映射到存储过程的 Code First 模型非常有用。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-105">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="0e3dd-106">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="0e3dd-107">为实体编写 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="0e3dd-107">Writing SQL queries for entities</span></span>  

<span data-ttu-id="0e3dd-108">DbSet 上的 SqlQuery 方法允许编写原始 SQL 查询来返回实体实例。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-108">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="0e3dd-109">返回的对象将由上下文跟踪，就像它们是通过 LINQ 查询返回的一样。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-109">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="0e3dd-110">例如：</span><span class="sxs-lookup"><span data-stu-id="0e3dd-110">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="0e3dd-111">请注意，就像 LINQ 查询一样，查询在枚举结果之前不会执行，在上面的示例中，此操作是通过调用 System.linq.enumerable.tolist 来完成的。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-111">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="0e3dd-112">出于两个原因而编写原始 SQL 查询时应谨慎。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-112">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="0e3dd-113">首先，应编写查询以确保它仅返回真正请求类型的实体。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-113">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="0e3dd-114">例如，使用继承等功能时，可以轻松编写一个查询，该查询将创建错误的 CLR 类型的实体。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-114">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="0e3dd-115">其次，某些类型的原始 SQL 查询会暴露潜在的安全风险，尤其是围绕 SQL 注入式攻击。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-115">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="0e3dd-116">确保在查询中使用参数，以防止此类攻击。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-116">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="0e3dd-117">从存储过程加载实体</span><span class="sxs-lookup"><span data-stu-id="0e3dd-117">Loading entities from stored procedures</span></span>  

<span data-ttu-id="0e3dd-118">可以使用 DbSet 从存储过程的结果中加载实体。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-118">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="0e3dd-119">例如，下面的代码调用 dbo。数据库中的 GetBlogs 过程：</span><span class="sxs-lookup"><span data-stu-id="0e3dd-119">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="0e3dd-120">还可以使用以下语法将参数传递给存储过程：</span><span class="sxs-lookup"><span data-stu-id="0e3dd-120">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="0e3dd-121">为非实体类型编写 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="0e3dd-121">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="0e3dd-122">返回任何类型的实例的 SQL 查询（包括基元类型）都可以在数据库类上使用 SqlQuery 方法创建。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-122">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="0e3dd-123">例如：</span><span class="sxs-lookup"><span data-stu-id="0e3dd-123">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="0e3dd-124">即使对象是实体类型的实例，上下文也永远不会跟踪从数据库的 SqlQuery 返回的结果。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-124">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="0e3dd-125">向数据库发送原始命令</span><span class="sxs-lookup"><span data-stu-id="0e3dd-125">Sending raw commands to the database</span></span>  

<span data-ttu-id="0e3dd-126">可以使用数据库上的 ExecuteSqlCommand 方法将非查询命令发送到数据库。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-126">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="0e3dd-127">例如：</span><span class="sxs-lookup"><span data-stu-id="0e3dd-127">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="0e3dd-128">请注意，使用 ExecuteSqlCommand 对数据库中的数据所做的任何更改在从数据库中加载或重新加载实体之前，对上下文的更改是不透明的。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-128">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="0e3dd-129">输出参数</span><span class="sxs-lookup"><span data-stu-id="0e3dd-129">Output Parameters</span></span>  

<span data-ttu-id="0e3dd-130">如果使用了 output 参数，则在完全读取结果之前，它们的值将不可用。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-130">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="0e3dd-131">这是因为 DbDataReader 的基础行为，有关详细信息，请参阅[使用 DataReader 检索数据](https://go.microsoft.com/fwlink/?LinkID=398589)。</span><span class="sxs-lookup"><span data-stu-id="0e3dd-131">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  

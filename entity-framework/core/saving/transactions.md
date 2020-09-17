---
title: 事务 - EF Core
description: 在使用 Entity Framework Core 保存数据时管理事务的原子性
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/transactions
ms.openlocfilehash: d824db6a4d6e1fc0fc385f007ccfc2c6cbbcae79
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070830"
---
# <a name="using-transactions"></a><span data-ttu-id="85667-103">使用事务</span><span class="sxs-lookup"><span data-stu-id="85667-103">Using Transactions</span></span>

<span data-ttu-id="85667-104">事务允许以原子方式处理多个数据库操作。</span><span class="sxs-lookup"><span data-stu-id="85667-104">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="85667-105">如果已提交事务，则所有操作都会成功应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="85667-105">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="85667-106">如果已回滚事务，则所有操作都不会应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="85667-106">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="85667-107">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/)。</span><span class="sxs-lookup"><span data-stu-id="85667-107">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="85667-108">默认事务行为</span><span class="sxs-lookup"><span data-stu-id="85667-108">Default transaction behavior</span></span>

<span data-ttu-id="85667-109">默认情况下，如果数据库提供程序支持事务，则会在事务中应用对 `SaveChanges()` 的单一调用中的所有更改。</span><span class="sxs-lookup"><span data-stu-id="85667-109">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="85667-110">如果其中有任何更改失败，则会回滚事务且所有更改都不会应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="85667-110">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="85667-111">这意味着，`SaveChanges()` 可保证完全成功，或在出现错误时不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="85667-111">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="85667-112">对于大多数应用程序，此默认行为已足够。</span><span class="sxs-lookup"><span data-stu-id="85667-112">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="85667-113">如果应用程序要求被视为有必要，则应该仅手动控制事务。</span><span class="sxs-lookup"><span data-stu-id="85667-113">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="85667-114">控制事务</span><span class="sxs-lookup"><span data-stu-id="85667-114">Controlling transactions</span></span>

<span data-ttu-id="85667-115">可以使用 `DbContext.Database` API 开始、提交和回滚事务。</span><span class="sxs-lookup"><span data-stu-id="85667-115">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="85667-116">以下示例显示了两个 `SaveChanges()` 操作以及正在单个事务中执行的 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="85667-116">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="85667-117">并非所有数据库提供程序都支持事务。</span><span class="sxs-lookup"><span data-stu-id="85667-117">Not all database providers support transactions.</span></span> <span data-ttu-id="85667-118">调用事务 API 时，某些提供程序可能会引发异常或不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="85667-118">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="85667-119">跨上下文事务（仅限关系数据库）</span><span class="sxs-lookup"><span data-stu-id="85667-119">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="85667-120">还可以跨多个上下文实例共享一个事务。</span><span class="sxs-lookup"><span data-stu-id="85667-120">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="85667-121">此功能仅在使用关系数据库提供程序时才可用，因为该提供程序需要使用特定于关系数据库的 `DbTransaction` 和 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="85667-121">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="85667-122">若要共享事务，上下文必须共享 `DbConnection` 和 `DbTransaction`。</span><span class="sxs-lookup"><span data-stu-id="85667-122">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="85667-123">允许在外部提供连接</span><span class="sxs-lookup"><span data-stu-id="85667-123">Allow connection to be externally provided</span></span>

<span data-ttu-id="85667-124">共享 `DbConnection` 需要在构造上下文时向其中传入连接的功能。</span><span class="sxs-lookup"><span data-stu-id="85667-124">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="85667-125">允许在外部提供 `DbConnection` 的最简单方式是，停止使用 `DbContext.OnConfiguring` 方法来配置上下文并在外部创建 `DbContextOptions`，然后将其传递到上下文构造函数。</span><span class="sxs-lookup"><span data-stu-id="85667-125">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="85667-126">`DbContextOptionsBuilder` 是在 `DbContext.OnConfiguring` 中用于配置上下文的 API，现在即将在外部使用它来创建 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="85667-126">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="85667-127">替代方法是继续使用 `DbContext.OnConfiguring`，但接受已保存并随后在 `DbContext.OnConfiguring` 中使用的 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="85667-127">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a><span data-ttu-id="85667-128">共享连接和事务</span><span class="sxs-lookup"><span data-stu-id="85667-128">Share connection and transaction</span></span>

<span data-ttu-id="85667-129">现在可以创建共享同一连接的多个上下文实例。</span><span class="sxs-lookup"><span data-stu-id="85667-129">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="85667-130">然后使用 `DbContext.Database.UseTransaction(DbTransaction)` API 在同一事务中登记两个上下文。</span><span class="sxs-lookup"><span data-stu-id="85667-130">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="85667-131">使用外部 DbTransactions（仅限关系数据库）</span><span class="sxs-lookup"><span data-stu-id="85667-131">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="85667-132">如果使用多个数据访问技术来访问关系数据库，则可能希望在这些不同技术所执行的操作之间共享事务。</span><span class="sxs-lookup"><span data-stu-id="85667-132">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="85667-133">以下示例显示了如何在同一事务中执行 ADO.NET SqlClient 操作和 Entity Framework Core 操作。</span><span class="sxs-lookup"><span data-stu-id="85667-133">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="85667-134">使用 System.Transactions</span><span class="sxs-lookup"><span data-stu-id="85667-134">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="85667-135">此功能是 EF Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="85667-135">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="85667-136">如果需要跨较大作用域进行协调，则可以使用环境事务。</span><span class="sxs-lookup"><span data-stu-id="85667-136">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="85667-137">还可以在显式事务中登记。</span><span class="sxs-lookup"><span data-stu-id="85667-137">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="85667-138">System.Transactions 的限制</span><span class="sxs-lookup"><span data-stu-id="85667-138">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="85667-139">EF Core 依赖数据库提供程序以实现对 System.Transactions 的支持。</span><span class="sxs-lookup"><span data-stu-id="85667-139">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="85667-140">虽然支持在 .NET Framework 的 ADO.NET 提供程序之间十分常见，但最近才将 API 添加到 .NET Core，因此支持并未得到广泛应用。</span><span class="sxs-lookup"><span data-stu-id="85667-140">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="85667-141">如果提供程序未实现对 System.Transactions 的支持，则可能会完全忽略对这些 API 的调用。</span><span class="sxs-lookup"><span data-stu-id="85667-141">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="85667-142">SqlClient for .NET Core 从 2.1 及以上版本开始支持 System.Transactions。</span><span class="sxs-lookup"><span data-stu-id="85667-142">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="85667-143">如果你尝试使用此功能，SqlClient for .NET Core 2.0 会抛出异常。</span><span class="sxs-lookup"><span data-stu-id="85667-143">SqlClient for .NET Core 2.0 will throw an exception if you attempt to use the feature.</span></span>

   > [!IMPORTANT]  
   > <span data-ttu-id="85667-144">建议你测试在依赖提供程序以管理事务之前 API 与该提供程序的行为是否正确。</span><span class="sxs-lookup"><span data-stu-id="85667-144">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="85667-145">如果不正确，则建议你与数据库提供程序的维护人员联系。</span><span class="sxs-lookup"><span data-stu-id="85667-145">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="85667-146">自版本 2.1 起，.NET Core 中的 System.Transactions 实现不包括对分布式事务的支持，因此不能使用 `TransactionScope` 或 `CommittableTransaction` 来跨多个资源管理器协调事务。</span><span class="sxs-lookup"><span data-stu-id="85667-146">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>

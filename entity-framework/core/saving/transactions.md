---
title: 事务 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
uid: core/saving/transactions
ms.openlocfilehash: 390d89398ebfdf015804749e71ff0b61d3f278d3
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413617"
---
# <a name="using-transactions"></a><span data-ttu-id="8c28e-102">使用事务</span><span class="sxs-lookup"><span data-stu-id="8c28e-102">Using Transactions</span></span>

<span data-ttu-id="8c28e-103">事务允许以原子方式处理多个数据库操作。</span><span class="sxs-lookup"><span data-stu-id="8c28e-103">Transactions allow several database operations to be processed in an atomic manner.</span></span> <span data-ttu-id="8c28e-104">如果已提交事务，则所有操作都会成功应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="8c28e-104">If the transaction is committed, all of the operations are successfully applied to the database.</span></span> <span data-ttu-id="8c28e-105">如果已回滚事务，则所有操作都不会应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="8c28e-105">If the transaction is rolled back, none of the operations are applied to the database.</span></span>

> [!TIP]  
> <span data-ttu-id="8c28e-106">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/)。</span><span class="sxs-lookup"><span data-stu-id="8c28e-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Transactions/) on GitHub.</span></span>

## <a name="default-transaction-behavior"></a><span data-ttu-id="8c28e-107">默认事务行为</span><span class="sxs-lookup"><span data-stu-id="8c28e-107">Default transaction behavior</span></span>

<span data-ttu-id="8c28e-108">默认情况下，如果数据库提供程序支持事务，则会在单次调用 `SaveChanges()` 时将所有更改都将应用到事务中。</span><span class="sxs-lookup"><span data-stu-id="8c28e-108">By default, if the database provider supports transactions, all changes in a single call to `SaveChanges()` are applied in a transaction.</span></span> <span data-ttu-id="8c28e-109">如果其中有任何更改失败，则会回滚事务且所有更改都不会应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="8c28e-109">If any of the changes fail, then the transaction is rolled back and none of the changes are applied to the database.</span></span> <span data-ttu-id="8c28e-110">这意味着，`SaveChanges()` 可保证要么完全成功，要么在出现错误时不修改数据库。</span><span class="sxs-lookup"><span data-stu-id="8c28e-110">This means that `SaveChanges()` is guaranteed to either completely succeed, or leave the database unmodified if an error occurs.</span></span>

<span data-ttu-id="8c28e-111">对于大多数应用程序，此默认行为已足够。</span><span class="sxs-lookup"><span data-stu-id="8c28e-111">For most applications, this default behavior is sufficient.</span></span> <span data-ttu-id="8c28e-112">除非应用程序确有需求，否则不应手动控制事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-112">You should only manually control transactions if your application requirements deem it necessary.</span></span>

## <a name="controlling-transactions"></a><span data-ttu-id="8c28e-113">控制事务</span><span class="sxs-lookup"><span data-stu-id="8c28e-113">Controlling transactions</span></span>

<span data-ttu-id="8c28e-114">可以使用 `DbContext.Database` API 开始、提交和回滚事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-114">You can use the `DbContext.Database` API to begin, commit, and rollback transactions.</span></span> <span data-ttu-id="8c28e-115">以下示例显示了在单个事务中执行的两个 `SaveChanges()` 操作以及 一个LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="8c28e-115">The following example shows two `SaveChanges()` operations and a LINQ query being executed in a single transaction.</span></span>

<span data-ttu-id="8c28e-116">并非所有数据库提供程序都支持事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-116">Not all database providers support transactions.</span></span> <span data-ttu-id="8c28e-117">调用事务 API 时，某些提供程序可能会引发异常或不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="8c28e-117">Some providers may throw or no-op when transaction APIs are called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ControllingTransaction/Sample.cs?name=Transaction&highlight=3,17,18,19)]

## <a name="cross-context-transaction-relational-databases-only"></a><span data-ttu-id="8c28e-118">跨上下文事务（仅限关系数据库）</span><span class="sxs-lookup"><span data-stu-id="8c28e-118">Cross-context transaction (relational databases only)</span></span>

<span data-ttu-id="8c28e-119">您还可以跨多个上下文实例共享一个事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-119">You can also share a transaction across multiple context instances.</span></span> <span data-ttu-id="8c28e-120">此功能仅在使用关系数据库提供程序时才可用，因为它需要使用特定于关系数据库的 `DbTransaction` 和 `DbConnection`。</span><span class="sxs-lookup"><span data-stu-id="8c28e-120">This functionality is only available when using a relational database provider because it requires the use of `DbTransaction` and `DbConnection`, which are specific to relational databases.</span></span>

<span data-ttu-id="8c28e-121">若要共享事务，上下文必须共享 `DbConnection` 和 `DbTransaction`。</span><span class="sxs-lookup"><span data-stu-id="8c28e-121">To share a transaction, the contexts must share both a `DbConnection` and a `DbTransaction`.</span></span>

### <a name="allow-connection-to-be-externally-provided"></a><span data-ttu-id="8c28e-122">允许在外部提供连接</span><span class="sxs-lookup"><span data-stu-id="8c28e-122">Allow connection to be externally provided</span></span>

<span data-ttu-id="8c28e-123">共享 `DbConnection` 需要在构造上下文时向其中传入连接的功能。</span><span class="sxs-lookup"><span data-stu-id="8c28e-123">Sharing a `DbConnection` requires the ability to pass a connection into a context when constructing it.</span></span>

<span data-ttu-id="8c28e-124">允许在外部提供 `DbConnection` 的最简单方式是，停止使用 `DbContext.OnConfiguring` 方法来配置上下文并在外部创建 `DbContextOptions`，然后将其传递到上下文构造函数。</span><span class="sxs-lookup"><span data-stu-id="8c28e-124">The easiest way to allow `DbConnection` to be externally provided, is to stop using the `DbContext.OnConfiguring` method to configure the context and externally create `DbContextOptions` and pass them to the context constructor.</span></span>

> [!TIP]  
> <span data-ttu-id="8c28e-125">`DbContextOptionsBuilder` 是在 `DbContext.OnConfiguring` 中用于配置上下文的 API，现在即将在外部使用它来创建 `DbContextOptions`。</span><span class="sxs-lookup"><span data-stu-id="8c28e-125">`DbContextOptionsBuilder` is the API you used in `DbContext.OnConfiguring` to configure the context, you are now going to use it externally to create `DbContextOptions`.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

<span data-ttu-id="8c28e-126">替代方法是继续使用 `DbContext.OnConfiguring`，但接受已保存并随后在 `DbConnection` 中使用的 `DbContext.OnConfiguring`。</span><span class="sxs-lookup"><span data-stu-id="8c28e-126">An alternative is to keep using `DbContext.OnConfiguring`, but accept a `DbConnection` that is saved and then used in `DbContext.OnConfiguring`.</span></span>

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

### <a name="share-connection-and-transaction"></a><span data-ttu-id="8c28e-127">共享连接和事务</span><span class="sxs-lookup"><span data-stu-id="8c28e-127">Share connection and transaction</span></span>

<span data-ttu-id="8c28e-128">现在可以创建共享同一连接的多个上下文实例。</span><span class="sxs-lookup"><span data-stu-id="8c28e-128">You can now create multiple context instances that share the same connection.</span></span> <span data-ttu-id="8c28e-129">然后使用 `DbContext.Database.UseTransaction(DbTransaction)` API 在同一事务中登记两个上下文。</span><span class="sxs-lookup"><span data-stu-id="8c28e-129">Then use the `DbContext.Database.UseTransaction(DbTransaction)` API to enlist both contexts in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a><span data-ttu-id="8c28e-130">使用外部 DbTransactions（仅限关系数据库）</span><span class="sxs-lookup"><span data-stu-id="8c28e-130">Using external DbTransactions (relational databases only)</span></span>

<span data-ttu-id="8c28e-131">如果使用多个数据访问技术来访问关系数据库，则可能希望在这些不同技术所执行的操作之间共享事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-131">If you are using multiple data access technologies to access a relational database, you may want to share a transaction between operations performed by these different technologies.</span></span>

<span data-ttu-id="8c28e-132">以下示例显示了如何在同一事务中执行 ADO.NET SqlClient 操作和 Entity Framework Core 操作。</span><span class="sxs-lookup"><span data-stu-id="8c28e-132">The following example, shows how to perform an ADO.NET SqlClient operation and an Entity Framework Core operation in the same transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a><span data-ttu-id="8c28e-133">使用 System.Transactions</span><span class="sxs-lookup"><span data-stu-id="8c28e-133">Using System.Transactions</span></span>

> [!NOTE]  
> <span data-ttu-id="8c28e-134">此功能是 EF Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="8c28e-134">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="8c28e-135">如果需要跨较大作用域进行协调，则可以使用环境事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-135">It is possible to use ambient transactions if you need to coordinate across a larger scope.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,2,3,26,27,28)]

<span data-ttu-id="8c28e-136">还可以在显式事务中登记。</span><span class="sxs-lookup"><span data-stu-id="8c28e-136">It is also possible to enlist in an explicit transaction.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,15,28,29,30)]

### <a name="limitations-of-systemtransactions"></a><span data-ttu-id="8c28e-137">System.Transactions 的限制</span><span class="sxs-lookup"><span data-stu-id="8c28e-137">Limitations of System.Transactions</span></span>  

1. <span data-ttu-id="8c28e-138">EF Core 依赖数据库提供程序以实现对 System.Transactions 的支持。</span><span class="sxs-lookup"><span data-stu-id="8c28e-138">EF Core relies on database providers to implement support for System.Transactions.</span></span> <span data-ttu-id="8c28e-139">虽然支持在 .NET Framework 的 ADO.NET 提供程序之间十分常见，但最近才将 API 添加到 .NET Core，因此支持并未得到广泛应用。</span><span class="sxs-lookup"><span data-stu-id="8c28e-139">Although support is quite common among ADO.NET providers for .NET Framework, the API has only been recently added to .NET Core and hence support is not as widespread.</span></span> <span data-ttu-id="8c28e-140">如果提供程序未实现对 System.Transactions 的支持，则可能会完全忽略对这些 API 的调用。</span><span class="sxs-lookup"><span data-stu-id="8c28e-140">If a provider does not implement support for System.Transactions, it is possible that calls to these APIs will be completely ignored.</span></span> <span data-ttu-id="8c28e-141">SqlClient for .NET Core 从 2.1 及以上版本开始支持 System.Transactions。</span><span class="sxs-lookup"><span data-stu-id="8c28e-141">SqlClient for .NET Core does support it from 2.1 onwards.</span></span> <span data-ttu-id="8c28e-142">如果你尝试使用此功能，SqlClient for .NET Core 2.0 会抛出异常。</span><span class="sxs-lookup"><span data-stu-id="8c28e-142">SqlClient for .NET Core 2.0 will throw an exception if you attempt to use the feature.</span></span>

   > [!IMPORTANT]  
   > <span data-ttu-id="8c28e-143">建议你测试在依赖提供程序以管理事务之前 API 与该提供程序的行为是否正确。</span><span class="sxs-lookup"><span data-stu-id="8c28e-143">It is recommended that you test that the API behaves correctly with your provider before you rely on it for managing transactions.</span></span> <span data-ttu-id="8c28e-144">如果不正确，则建议你与数据库提供程序的维护人员联系。</span><span class="sxs-lookup"><span data-stu-id="8c28e-144">You are encouraged to contact the maintainer of the database provider if it does not.</span></span>

2. <span data-ttu-id="8c28e-145">自版本 2.1 起，.NET Core 中的 System.Transactions 实现不包括对分布式事务的支持，因此不能使用 `TransactionScope` 或 `CommittableTransaction` 来跨多个资源管理器协调事务。</span><span class="sxs-lookup"><span data-stu-id="8c28e-145">As of version 2.1, the System.Transactions implementation in .NET Core does not include support for distributed transactions, therefore you cannot use `TransactionScope` or `CommittableTransaction` to coordinate transactions across multiple resource managers.</span></span>

---
title: 处理并发冲突 - EF Core
description: 在使用 Entity Framework Core 并发更新相同数据时管理冲突
author: ajcvickers
ms.date: 03/03/2018
uid: core/saving/concurrency
ms.openlocfilehash: b596a99db431331bb12a28fc6ddc06f1c941b67c
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063018"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="46d50-103">处理并发冲突</span><span class="sxs-lookup"><span data-stu-id="46d50-103">Handling Concurrency Conflicts</span></span>

> [!NOTE]
> <span data-ttu-id="46d50-104">此页面记录了并发在 EF Core 中的工作原理以及如何处理应用程序中的并发冲突。</span><span class="sxs-lookup"><span data-stu-id="46d50-104">This page documents how concurrency works in EF Core and how to handle concurrency conflicts in your application.</span></span> <span data-ttu-id="46d50-105">有关如何配置模型中的并发令牌的详细信息，请参阅[并发令牌](xref:core/modeling/concurrency)。</span><span class="sxs-lookup"><span data-stu-id="46d50-105">See [Concurrency Tokens](xref:core/modeling/concurrency) for details on how to configure concurrency tokens in your model.</span></span>

> [!TIP]
> <span data-ttu-id="46d50-106">可在 GitHub 上查看此文章的[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/)。</span><span class="sxs-lookup"><span data-stu-id="46d50-106">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Concurrency/) on GitHub.</span></span>

<span data-ttu-id="46d50-107">_数据库并发_指的是多个进程或用户同时访问或更改数据库中的相同数据的情况。</span><span class="sxs-lookup"><span data-stu-id="46d50-107">_Database concurrency_ refers to situations in which multiple processes or users access or change the same data in a database at the same time.</span></span> <span data-ttu-id="46d50-108">_并发控制_指的是用于在发生并发更改时确保数据一致性的特定机制。</span><span class="sxs-lookup"><span data-stu-id="46d50-108">_Concurrency control_ refers to specific mechanisms used to ensure data consistency in presence of concurrent changes.</span></span>

<span data-ttu-id="46d50-109">EF Core 实现_乐观并发控制_，这意味着它将允许多个进程或用户独立进行更改而不产生同步或锁定的开销。</span><span class="sxs-lookup"><span data-stu-id="46d50-109">EF Core implements _optimistic concurrency control_, meaning that it will let multiple processes or users make changes independently without the overhead of synchronization or locking.</span></span> <span data-ttu-id="46d50-110">在理想情况下，这些更改将不会相互干扰，因此都能够成功。</span><span class="sxs-lookup"><span data-stu-id="46d50-110">In the ideal situation, these changes will not interfere with each other and therefore will be able to succeed.</span></span> <span data-ttu-id="46d50-111">在最坏的情况下，两个或更多进程将尝试进行冲突更改，其中只有一个进程应该成功。</span><span class="sxs-lookup"><span data-stu-id="46d50-111">In the worst case scenario, two or more processes will attempt to make conflicting changes, and only one of them should succeed.</span></span>

## <a name="how-concurrency-control-works-in-ef-core"></a><span data-ttu-id="46d50-112">并发控制在 EF Core 中的工作原理</span><span class="sxs-lookup"><span data-stu-id="46d50-112">How concurrency control works in EF Core</span></span>

<span data-ttu-id="46d50-113">配置为并发令牌的属性用于实现乐观并发控制：每当在 `SaveChanges` 期间执行更新或删除操作时，会将数据库上的并发令牌值与通过 EF Core 读取的原始值进行比较。</span><span class="sxs-lookup"><span data-stu-id="46d50-113">Properties configured as concurrency tokens are used to implement optimistic concurrency control: whenever an update or delete operation is performed during `SaveChanges`, the value of the concurrency token on the database is compared against the original value read by EF Core.</span></span>

- <span data-ttu-id="46d50-114">如果这些值匹配，则可以完成该操作。</span><span class="sxs-lookup"><span data-stu-id="46d50-114">If the values match, the operation can complete.</span></span>
- <span data-ttu-id="46d50-115">如果这些值不匹配，EF Core 会假设另一个用户已执行冲突操作，并中止当前事务。</span><span class="sxs-lookup"><span data-stu-id="46d50-115">If the values do not match, EF Core assumes that another user has performed a conflicting operation and aborts the current transaction.</span></span>

<span data-ttu-id="46d50-116">另一个用户已执行与当前操作冲突的操作的情况称为_并发冲突_。</span><span class="sxs-lookup"><span data-stu-id="46d50-116">The situation when another user has performed an operation that conflicts with the current operation is known as _concurrency conflict_.</span></span>

<span data-ttu-id="46d50-117">数据库提供程序负责实现并发令牌值的比较。</span><span class="sxs-lookup"><span data-stu-id="46d50-117">Database providers are responsible for implementing the comparison of concurrency token values.</span></span>

<span data-ttu-id="46d50-118">在关系数据库上，EF Core 包括对任何 `UPDATE` 或 `DELETE` 语句的 `WHERE` 子句中的并发令牌值的检查。</span><span class="sxs-lookup"><span data-stu-id="46d50-118">On relational databases EF Core includes a check for the value of the concurrency token in the `WHERE` clause of any `UPDATE` or `DELETE` statements.</span></span> <span data-ttu-id="46d50-119">执行这些语句后，EF Core 会读取受影响的行数。</span><span class="sxs-lookup"><span data-stu-id="46d50-119">After executing the statements, EF Core reads the number of rows that were affected.</span></span>

<span data-ttu-id="46d50-120">如果未影响任何行，将检测到并发冲突，并且 EF Core 会引发 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="46d50-120">If no rows are affected, a concurrency conflict is detected, and EF Core throws `DbUpdateConcurrencyException`.</span></span>

<span data-ttu-id="46d50-121">例如，我们可能希望将 `Person` 上的 `LastName` 配置为并发令牌。</span><span class="sxs-lookup"><span data-stu-id="46d50-121">For example, we may want to configure `LastName` on `Person` to be a concurrency token.</span></span> <span data-ttu-id="46d50-122">则针对用户的任何更新操作将包括 `WHERE` 子句中的并发检查：</span><span class="sxs-lookup"><span data-stu-id="46d50-122">Then any update operation on Person will include the concurrency check in the `WHERE` clause:</span></span>

```sql
UPDATE [Person] SET [FirstName] = @p1
WHERE [PersonId] = @p0 AND [LastName] = @p2;
```

## <a name="resolving-concurrency-conflicts"></a><span data-ttu-id="46d50-123">解决并发冲突</span><span class="sxs-lookup"><span data-stu-id="46d50-123">Resolving concurrency conflicts</span></span>

<span data-ttu-id="46d50-124">继续前面的示例，如果一个用户尝试保存对 `Person` 所做的某些更改，但另一个用户已更改 `LastName`，则将引发异常。</span><span class="sxs-lookup"><span data-stu-id="46d50-124">Continuing with the previous example, if one user tries to save some changes to a `Person`, but another user has already changed the `LastName`, then an exception will be thrown.</span></span>

<span data-ttu-id="46d50-125">此时，应用程序可能只需通知用户由于发生冲突更改而导致更新未成功，然后继续操作。</span><span class="sxs-lookup"><span data-stu-id="46d50-125">At this point, the application could simply inform the user that the update was not successful due to conflicting changes and move on.</span></span> <span data-ttu-id="46d50-126">但可能需要提示用户确保此记录仍表示同一实际用户并重试该操作。</span><span class="sxs-lookup"><span data-stu-id="46d50-126">But it may be desirable to prompt the user to ensure this record still represents the same actual person and to retry the operation.</span></span>

<span data-ttu-id="46d50-127">此过程是_解决并发冲突_的一个示例。</span><span class="sxs-lookup"><span data-stu-id="46d50-127">This process is an example of _resolving a concurrency conflict_.</span></span>

<span data-ttu-id="46d50-128">解决并发冲突涉及将当前 `DbContext` 中挂起的更改与数据库中的值进行合并。</span><span class="sxs-lookup"><span data-stu-id="46d50-128">Resolving a concurrency conflict involves merging the pending changes from the current `DbContext` with the values in the database.</span></span> <span data-ttu-id="46d50-129">要合并的值将根据应用程序的不同而有所不同，并且可能由用户输入指示。</span><span class="sxs-lookup"><span data-stu-id="46d50-129">What values get merged will vary based on the application and may be directed by user input.</span></span>

<span data-ttu-id="46d50-130">**有三组值可用于帮助解决并发冲突：**</span><span class="sxs-lookup"><span data-stu-id="46d50-130">**There are three sets of values available to help resolve a concurrency conflict:**</span></span>

- <span data-ttu-id="46d50-131">“当前值”\*\*\*\* 是应用程序尝试写入数据库的值。</span><span class="sxs-lookup"><span data-stu-id="46d50-131">**Current values** are the values that the application was attempting to write to the database.</span></span>
- <span data-ttu-id="46d50-132">“原始值”\*\*\*\* 是在进行任何编辑之前最初从数据库中检索的值。</span><span class="sxs-lookup"><span data-stu-id="46d50-132">**Original values** are the values that were originally retrieved from the database, before any edits were made.</span></span>
- <span data-ttu-id="46d50-133">“数据库值”\*\*\*\* 是当前存储在数据库中的值。</span><span class="sxs-lookup"><span data-stu-id="46d50-133">**Database values** are the values currently stored in the database.</span></span>

<span data-ttu-id="46d50-134">处理并发冲突的常规方法是：</span><span class="sxs-lookup"><span data-stu-id="46d50-134">The general approach to handle a concurrency conflicts is:</span></span>

1. <span data-ttu-id="46d50-135">在 `SaveChanges` 期间捕获 `DbUpdateConcurrencyException`。</span><span class="sxs-lookup"><span data-stu-id="46d50-135">Catch `DbUpdateConcurrencyException` during `SaveChanges`.</span></span>
2. <span data-ttu-id="46d50-136">使用 `DbUpdateConcurrencyException.Entries` 为受影响的实体准备一组新更改。</span><span class="sxs-lookup"><span data-stu-id="46d50-136">Use `DbUpdateConcurrencyException.Entries` to prepare a new set of changes for the affected entities.</span></span>
3. <span data-ttu-id="46d50-137">刷新并发令牌的原始值以反映数据库中的当前值。</span><span class="sxs-lookup"><span data-stu-id="46d50-137">Refresh the original values of the concurrency token to reflect the current values in the database.</span></span>
4. <span data-ttu-id="46d50-138">重试该过程，直到不发生任何冲突。</span><span class="sxs-lookup"><span data-stu-id="46d50-138">Retry the process until no conflicts occur.</span></span>

<span data-ttu-id="46d50-139">在下面的示例中，将 `Person.FirstName` 和 `Person.LastName` 设置为并发令牌。</span><span class="sxs-lookup"><span data-stu-id="46d50-139">In the following example, `Person.FirstName` and `Person.LastName` are set up as concurrency tokens.</span></span> <span data-ttu-id="46d50-140">在包括应用程序特定逻辑以选择要保存的值的位置处有一条 `// TODO:` 注释。</span><span class="sxs-lookup"><span data-stu-id="46d50-140">There is a `// TODO:` comment in the location where you include application specific logic to choose the value to be saved.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Concurrency/Sample.cs?name=ConcurrencyHandlingCode&highlight=33-34)]

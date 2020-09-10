---
title: 处理并发冲突-EF6
description: 处理实体框架6中的并发冲突
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
uid: ef6/saving/concurrency
ms.openlocfilehash: 1cec47ce346e8a6c86338747c01fba4d030e7388
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619871"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="54cb6-103">处理并发冲突 (EF6) </span><span class="sxs-lookup"><span data-stu-id="54cb6-103">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="54cb6-104">乐观并发性涉及到乐观地尝试将实体保存到数据库，希望数据在加载实体后未发生更改。</span><span class="sxs-lookup"><span data-stu-id="54cb6-104">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="54cb6-105">如果事实证明数据已更改，则会引发异常，并且在尝试再次保存之前必须解决冲突。</span><span class="sxs-lookup"><span data-stu-id="54cb6-105">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="54cb6-106">本主题介绍如何在实体框架中处理此类异常。</span><span class="sxs-lookup"><span data-stu-id="54cb6-106">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="54cb6-107">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="54cb6-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="54cb6-108">这篇文章并不适合完整讨论开放式并发。</span><span class="sxs-lookup"><span data-stu-id="54cb6-108">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="54cb6-109">以下各节介绍了并发解决方案的一些知识，并显示了常见任务的模式。</span><span class="sxs-lookup"><span data-stu-id="54cb6-109">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="54cb6-110">其中的许多模式使用 [属性值](xref:ef6/saving/change-tracking/property-values)中讨论的主题。</span><span class="sxs-lookup"><span data-stu-id="54cb6-110">Many of these patterns make use of the topics discussed in [Working with Property Values](xref:ef6/saving/change-tracking/property-values).</span></span>  

<span data-ttu-id="54cb6-111">当你使用独立关联 (（其中外键未映射到) 实体中的属性）时解决并发问题，比使用外键关联更难。</span><span class="sxs-lookup"><span data-stu-id="54cb6-111">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="54cb6-112">因此，如果你要在应用程序中执行并发解析，则建议你始终将外键映射到你的实体中。</span><span class="sxs-lookup"><span data-stu-id="54cb6-112">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="54cb6-113">以下所有示例假设你使用的是外键关联。</span><span class="sxs-lookup"><span data-stu-id="54cb6-113">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="54cb6-114">当尝试保存使用外键关联的实体时，如果检测到乐观并发异常，则 SaveChanges 将引发 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="54cb6-114">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="54cb6-115">通过重载 (数据库入选) 解决开放式并发异常</span><span class="sxs-lookup"><span data-stu-id="54cb6-115">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="54cb6-116">可以使用 Reload.sql 方法，用数据库中的值覆盖当前实体的值。</span><span class="sxs-lookup"><span data-stu-id="54cb6-116">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="54cb6-117">然后，通常以某种形式向用户返回该实体，并且这些实体必须重试更改，然后重新保存。</span><span class="sxs-lookup"><span data-stu-id="54cb6-117">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="54cb6-118">例如：</span><span class="sxs-lookup"><span data-stu-id="54cb6-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="54cb6-119">模拟并发异常的一种好方法是在 SaveChanges 调用上设置断点，然后使用其他工具（如 SQL Server Management Studio）修改要保存在数据库中的实体。</span><span class="sxs-lookup"><span data-stu-id="54cb6-119">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="54cb6-120">您还可以在 SaveChanges 之前插入一行，以便使用 SqlCommand 直接更新数据库。</span><span class="sxs-lookup"><span data-stu-id="54cb6-120">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="54cb6-121">例如：</span><span class="sxs-lookup"><span data-stu-id="54cb6-121">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="54cb6-122">DbUpdateConcurrencyException 上的条目方法返回未能更新的实体的 DbEntityEntry 实例。</span><span class="sxs-lookup"><span data-stu-id="54cb6-122">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="54cb6-123"> (此属性当前总是返回并发问题的单个值。</span><span class="sxs-lookup"><span data-stu-id="54cb6-123">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="54cb6-124">此方法可能会返回多个值以用于一般更新异常 ) 。在某些情况下，可能需要为可能需要从数据库重新加载的所有实体获取条目，并为每个实体调用 reload.sql。</span><span class="sxs-lookup"><span data-stu-id="54cb6-124">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="54cb6-125">将开放式并发异常解析为客户端入选</span><span class="sxs-lookup"><span data-stu-id="54cb6-125">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="54cb6-126">以上使用重载的示例有时被称为数据库入选或存储入选，因为该实体中的值由数据库中的值覆盖。</span><span class="sxs-lookup"><span data-stu-id="54cb6-126">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="54cb6-127">有时，您可能希望执行相反的操作，并用实体中当前的值覆盖数据库中的值。</span><span class="sxs-lookup"><span data-stu-id="54cb6-127">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="54cb6-128">这有时称为客户端入选，可以通过获取当前数据库值并将其设置为实体的原始值来完成。</span><span class="sxs-lookup"><span data-stu-id="54cb6-128">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="54cb6-129"> (参阅使用 [属性值](xref:ef6/saving/change-tracking/property-values) 获取有关当前值和原始值的信息。 ) 例如：</span><span class="sxs-lookup"><span data-stu-id="54cb6-129">(See [Working with Property Values](xref:ef6/saving/change-tracking/property-values) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="54cb6-130">开放式并发异常的自定义解决</span><span class="sxs-lookup"><span data-stu-id="54cb6-130">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="54cb6-131">有时，您可能想要将数据库中当前的值与实体中的当前值组合在一起。</span><span class="sxs-lookup"><span data-stu-id="54cb6-131">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="54cb6-132">这通常需要一些自定义逻辑或用户交互。</span><span class="sxs-lookup"><span data-stu-id="54cb6-132">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="54cb6-133">例如，您可能向用户提供窗体，其中包含当前值、数据库中的值和一组默认的已解析值。</span><span class="sxs-lookup"><span data-stu-id="54cb6-133">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="54cb6-134">然后，用户将根据需要编辑解析的值，并将这些已解决的值保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="54cb6-134">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="54cb6-135">为此，可以使用 DbPropertyValues 对象，该对象是从实体条目的 CurrentValues 和 GetDatabaseValues 返回的。</span><span class="sxs-lookup"><span data-stu-id="54cb6-135">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="54cb6-136">例如：</span><span class="sxs-lookup"><span data-stu-id="54cb6-136">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="54cb6-137">使用对象的开放式并发异常的自定义解决</span><span class="sxs-lookup"><span data-stu-id="54cb6-137">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="54cb6-138">上面的代码使用 DbPropertyValues 实例来传递当前、数据库和解析的值。</span><span class="sxs-lookup"><span data-stu-id="54cb6-138">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="54cb6-139">有时，使用实体类型的实例可能会更容易。</span><span class="sxs-lookup"><span data-stu-id="54cb6-139">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="54cb6-140">可以使用 DbPropertyValues 的 ToObject 和 SetValues 方法完成此操作。</span><span class="sxs-lookup"><span data-stu-id="54cb6-140">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="54cb6-141">例如：</span><span class="sxs-lookup"><span data-stu-id="54cb6-141">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  

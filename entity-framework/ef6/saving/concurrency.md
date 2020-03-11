---
title: 处理并发冲突-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 81ae186201fdfac331b1d4e7836b222545fe78b5
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416245"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="0167c-102">处理并发冲突</span><span class="sxs-lookup"><span data-stu-id="0167c-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="0167c-103">乐观并发性涉及到乐观地尝试将实体保存到数据库，希望数据在加载实体后未发生更改。</span><span class="sxs-lookup"><span data-stu-id="0167c-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="0167c-104">如果事实证明数据已更改，则会引发异常，并且在尝试再次保存之前必须解决冲突。</span><span class="sxs-lookup"><span data-stu-id="0167c-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="0167c-105">本主题介绍如何在实体框架中处理此类异常。</span><span class="sxs-lookup"><span data-stu-id="0167c-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="0167c-106">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="0167c-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="0167c-107">这篇文章并不适合完整讨论开放式并发。</span><span class="sxs-lookup"><span data-stu-id="0167c-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="0167c-108">以下各节介绍了并发解决方案的一些知识，并显示了常见任务的模式。</span><span class="sxs-lookup"><span data-stu-id="0167c-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="0167c-109">其中的许多模式使用[属性值](~/ef6/saving/change-tracking/property-values.md)中讨论的主题。</span><span class="sxs-lookup"><span data-stu-id="0167c-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="0167c-110">在使用独立关联（其中外键未映射到实体中的属性）时解决并发性问题比使用外键关联更难。</span><span class="sxs-lookup"><span data-stu-id="0167c-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="0167c-111">因此，如果你要在应用程序中执行并发解析，则建议你始终将外键映射到你的实体中。</span><span class="sxs-lookup"><span data-stu-id="0167c-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="0167c-112">以下所有示例假设你使用的是外键关联。</span><span class="sxs-lookup"><span data-stu-id="0167c-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="0167c-113">当尝试保存使用外键关联的实体时，如果检测到乐观并发异常，则 SaveChanges 将引发 DbUpdateConcurrencyException。</span><span class="sxs-lookup"><span data-stu-id="0167c-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="0167c-114">通过重载解决开放式并发异常（数据库入选）</span><span class="sxs-lookup"><span data-stu-id="0167c-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="0167c-115">可以使用 Reload.sql 方法，用数据库中的值覆盖当前实体的值。</span><span class="sxs-lookup"><span data-stu-id="0167c-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="0167c-116">然后，通常以某种形式向用户返回该实体，并且这些实体必须重试更改，然后重新保存。</span><span class="sxs-lookup"><span data-stu-id="0167c-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="0167c-117">例如：</span><span class="sxs-lookup"><span data-stu-id="0167c-117">For example:</span></span>  

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

<span data-ttu-id="0167c-118">模拟并发异常的一种好方法是在 SaveChanges 调用上设置断点，然后使用其他工具（如 SQL Management Studio）修改要保存在数据库中的实体。</span><span class="sxs-lookup"><span data-stu-id="0167c-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Management Studio.</span></span> <span data-ttu-id="0167c-119">您还可以在 SaveChanges 之前插入一行，以便使用 SqlCommand 直接更新数据库。</span><span class="sxs-lookup"><span data-stu-id="0167c-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="0167c-120">例如：</span><span class="sxs-lookup"><span data-stu-id="0167c-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="0167c-121">DbUpdateConcurrencyException 上的条目方法返回未能更新的实体的 DbEntityEntry 实例。</span><span class="sxs-lookup"><span data-stu-id="0167c-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="0167c-122">（此属性当前总是返回并发问题的单个值。</span><span class="sxs-lookup"><span data-stu-id="0167c-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="0167c-123">对于常规更新异常，它可能会返回多个值。）在某些情况下，另一种方法可能是从数据库中获取可能需要重新加载的所有实体的条目，并为每个实体调用 "重新加载"。</span><span class="sxs-lookup"><span data-stu-id="0167c-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="0167c-124">将开放式并发异常解析为客户端入选</span><span class="sxs-lookup"><span data-stu-id="0167c-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="0167c-125">以上使用重载的示例有时被称为数据库入选或存储入选，因为该实体中的值由数据库中的值覆盖。</span><span class="sxs-lookup"><span data-stu-id="0167c-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="0167c-126">有时，您可能希望执行相反的操作，并用实体中当前的值覆盖数据库中的值。</span><span class="sxs-lookup"><span data-stu-id="0167c-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="0167c-127">这有时称为客户端入选，可以通过获取当前数据库值并将其设置为实体的原始值来完成。</span><span class="sxs-lookup"><span data-stu-id="0167c-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="0167c-128">（有关当前值和原始值的信息，请参阅使用[属性值](~/ef6/saving/change-tracking/property-values.md)。）例如：</span><span class="sxs-lookup"><span data-stu-id="0167c-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="0167c-129">开放式并发异常的自定义解决</span><span class="sxs-lookup"><span data-stu-id="0167c-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="0167c-130">有时，您可能想要将数据库中当前的值与实体中的当前值组合在一起。</span><span class="sxs-lookup"><span data-stu-id="0167c-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="0167c-131">这通常需要一些自定义逻辑或用户交互。</span><span class="sxs-lookup"><span data-stu-id="0167c-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="0167c-132">例如，您可能向用户提供窗体，其中包含当前值、数据库中的值和一组默认的已解析值。</span><span class="sxs-lookup"><span data-stu-id="0167c-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="0167c-133">然后，用户将根据需要编辑解析的值，并将这些已解决的值保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="0167c-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="0167c-134">为此，可以使用 DbPropertyValues 对象，该对象是从实体条目的 CurrentValues 和 GetDatabaseValues 返回的。</span><span class="sxs-lookup"><span data-stu-id="0167c-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="0167c-135">例如：</span><span class="sxs-lookup"><span data-stu-id="0167c-135">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="0167c-136">使用对象的开放式并发异常的自定义解决</span><span class="sxs-lookup"><span data-stu-id="0167c-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="0167c-137">上面的代码使用 DbPropertyValues 实例来传递当前、数据库和解析的值。</span><span class="sxs-lookup"><span data-stu-id="0167c-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="0167c-138">有时，使用实体类型的实例可能会更容易。</span><span class="sxs-lookup"><span data-stu-id="0167c-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="0167c-139">可以使用 DbPropertyValues 的 ToObject 和 SetValues 方法完成此操作。</span><span class="sxs-lookup"><span data-stu-id="0167c-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="0167c-140">例如：</span><span class="sxs-lookup"><span data-stu-id="0167c-140">For example:</span></span>  

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

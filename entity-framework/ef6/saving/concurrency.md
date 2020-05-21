---
title: 处理并发冲突-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: a99f824fe256a10b84f539a5339a09624315efa4
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672707"
---
# <a name="handling-concurrency-conflicts"></a>处理并发冲突
乐观并发性涉及到乐观地尝试将实体保存到数据库，希望数据在加载实体后未发生更改。 如果事实证明数据已更改，则会引发异常，并且在尝试再次保存之前必须解决冲突。 本主题介绍如何在实体框架中处理此类异常。 本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。  

这篇文章并不适合完整讨论开放式并发。 以下各节介绍了并发解决方案的一些知识，并显示了常见任务的模式。  

其中的许多模式使用[属性值](~/ef6/saving/change-tracking/property-values.md)中讨论的主题。  

在使用独立关联（其中外键未映射到实体中的属性）时解决并发性问题比使用外键关联更难。 因此，如果你要在应用程序中执行并发解析，则建议你始终将外键映射到你的实体中。 以下所有示例假设你使用的是外键关联。  

当尝试保存使用外键关联的实体时，如果检测到乐观并发异常，则 SaveChanges 将引发 DbUpdateConcurrencyException。  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>通过重载解决开放式并发异常（数据库入选）  

可以使用 Reload.sql 方法，用数据库中的值覆盖当前实体的值。 然后，通常以某种形式向用户返回该实体，并且这些实体必须重试更改，然后重新保存。 例如：  

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

模拟并发异常的一种好方法是在 SaveChanges 调用上设置断点，然后使用其他工具（如 SQL Server Management Studio）修改要保存在数据库中的实体。 您还可以在 SaveChanges 之前插入一行，以便使用 SqlCommand 直接更新数据库。 例如：  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

DbUpdateConcurrencyException 上的条目方法返回未能更新的实体的 DbEntityEntry 实例。 （此属性当前总是返回并发问题的单个值。 对于常规更新异常，它可能会返回多个值。）在某些情况下，另一种方法可能是从数据库中获取可能需要重新加载的所有实体的条目，并为每个实体调用 "重新加载"。  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>将开放式并发异常解析为客户端入选  

以上使用重载的示例有时被称为数据库入选或存储入选，因为该实体中的值由数据库中的值覆盖。 有时，您可能希望执行相反的操作，并用实体中当前的值覆盖数据库中的值。 这有时称为客户端入选，可以通过获取当前数据库值并将其设置为实体的原始值来完成。 （有关当前值和原始值的信息，请参阅使用[属性值](~/ef6/saving/change-tracking/property-values.md)。）例如：  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>开放式并发异常的自定义解决  

有时，您可能想要将数据库中当前的值与实体中的当前值组合在一起。 这通常需要一些自定义逻辑或用户交互。 例如，您可能向用户提供窗体，其中包含当前值、数据库中的值和一组默认的已解析值。 然后，用户将根据需要编辑解析的值，并将这些已解决的值保存到数据库中。 为此，可以使用 DbPropertyValues 对象，该对象是从实体条目的 CurrentValues 和 GetDatabaseValues 返回的。 例如：  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>使用对象的开放式并发异常的自定义解决  

上面的代码使用 DbPropertyValues 实例来传递当前、数据库和解析的值。 有时，使用实体类型的实例可能会更容易。 可以使用 DbPropertyValues 的 ToObject 和 SetValues 方法完成此操作。 例如：  

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

---
title: 自定义迁移历史记录表的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
ms.openlocfilehash: eb19f367611a86f685557a6741a5f2f0bad6b718
ms.sourcegitcommit: e66745c9f91258b2cacf5ff263141be3cba4b09e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2019
ms.locfileid: "54058742"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="b1e43-102">自定义迁移历史记录表</span><span class="sxs-lookup"><span data-stu-id="b1e43-102">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="b1e43-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="b1e43-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="b1e43-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="b1e43-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="b1e43-105">本文假定你知道如何在基本方案中使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="b1e43-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="b1e43-106">如果不这样做，则你将需要读取[Code First 迁移](~/ef6/modeling/code-first/migrations/index.md)然后再继续。</span><span class="sxs-lookup"><span data-stu-id="b1e43-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="b1e43-107">迁移历史记录表是什么？</span><span class="sxs-lookup"><span data-stu-id="b1e43-107">What is Migrations History Table?</span></span>

<span data-ttu-id="b1e43-108">迁移历史记录表是用于存储有关迁移应用到数据库的详细信息的 Code First 迁移的表。</span><span class="sxs-lookup"><span data-stu-id="b1e43-108">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="b1e43-109">默认情况下在数据库中的表的名称是\_ \_MigrationHistory 和它将第一次迁移应用到数据库时创建。</span><span class="sxs-lookup"><span data-stu-id="b1e43-109">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="b1e43-110">在 Entity Framework 5 此表是系统表，如果应用程序使用 Microsoft Sql Server 数据库。</span><span class="sxs-lookup"><span data-stu-id="b1e43-110">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="b1e43-111">但是这已在 Entity Framework 6 和迁移历史记录表不再标记系统表。</span><span class="sxs-lookup"><span data-stu-id="b1e43-111">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="b1e43-112">为什么要自定义迁移历史记录表？</span><span class="sxs-lookup"><span data-stu-id="b1e43-112">Why customize Migrations History Table?</span></span>

<span data-ttu-id="b1e43-113">迁移历史记录表是应仅由 Code First 迁移一起，并手动更改可能会中断迁移。</span><span class="sxs-lookup"><span data-stu-id="b1e43-113">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="b1e43-114">但是，有时默认配置不适合和表需要进行自定义，例如：</span><span class="sxs-lookup"><span data-stu-id="b1e43-114">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="b1e43-115">您需要更改名称和/或列以启用 3 方面<sup>rd</sup>参与方迁移提供程序</span><span class="sxs-lookup"><span data-stu-id="b1e43-115">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="b1e43-116">你想要更改的表的名称</span><span class="sxs-lookup"><span data-stu-id="b1e43-116">You want to change the name of the table</span></span>
-   <span data-ttu-id="b1e43-117">您需要使用的非默认架构\_ \_MigrationHistory 表</span><span class="sxs-lookup"><span data-stu-id="b1e43-117">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="b1e43-118">若要将上下文的给定版本的其他数据，因此你需向表中添加一个额外的列</span><span class="sxs-lookup"><span data-stu-id="b1e43-118">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="b1e43-119">预防措施的单词</span><span class="sxs-lookup"><span data-stu-id="b1e43-119">Words of precaution</span></span>

<span data-ttu-id="b1e43-120">更改迁移历史记录表是功能强大，但您需要注意不过度编写。</span><span class="sxs-lookup"><span data-stu-id="b1e43-120">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="b1e43-121">EF 运行时当前不会检查自定义的迁移历史记录表是否与运行时兼容。</span><span class="sxs-lookup"><span data-stu-id="b1e43-121">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="b1e43-122">如果不是你的应用程序可能会导致运行时或不可预知的行为。</span><span class="sxs-lookup"><span data-stu-id="b1e43-122">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="b1e43-123">这一点尤为重要，如果使用每个数据库的多个上下文中这种情况下多个上下文可以使用相同的迁移历史记录表以存储有关迁移信息。</span><span class="sxs-lookup"><span data-stu-id="b1e43-123">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="b1e43-124">如何自定义迁移历史记录表？</span><span class="sxs-lookup"><span data-stu-id="b1e43-124">How to customize Migrations History Table?</span></span>

<span data-ttu-id="b1e43-125">在开始之前需要知道，仅在应用第一次迁移之前，您可以自定义迁移历史记录表。</span><span class="sxs-lookup"><span data-stu-id="b1e43-125">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="b1e43-126">现在，对代码。</span><span class="sxs-lookup"><span data-stu-id="b1e43-126">Now, to the code.</span></span>

<span data-ttu-id="b1e43-127">首先，需要创建一个 System.Data.Entity.Migrations.History.HistoryContext 类派生的类。</span><span class="sxs-lookup"><span data-stu-id="b1e43-127">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="b1e43-128">HistoryContext 类派生自 DbContext 类中，因此配置迁移历史记录表是非常类似于使用 fluent API 配置 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="b1e43-128">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="b1e43-129">只需重写 OnModelCreating 方法并使用 fluent API 来配置表。</span><span class="sxs-lookup"><span data-stu-id="b1e43-129">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="b1e43-130">配置 EF 模型时通常不需要调用基。重写 OnModelCreating 方法由于 DbContext.OnModelCreating() 具有空白正文从 onmodelcreating （)。</span><span class="sxs-lookup"><span data-stu-id="b1e43-130">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="b1e43-131">配置迁移历史记录表时，这不是这种情况。</span><span class="sxs-lookup"><span data-stu-id="b1e43-131">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="b1e43-132">在此情况下第一个需要在 onmodelcreating （） 重写中执行操作是实际调用基。Onmodelcreating （)。</span><span class="sxs-lookup"><span data-stu-id="b1e43-132">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="b1e43-133">这会将迁移历史记录表配置的默认方式，其中，然后调整中重写方法中。</span><span class="sxs-lookup"><span data-stu-id="b1e43-133">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="b1e43-134">让我们假设你想要重命名的迁移历史记录表，并将其放到名为"admin"的自定义架构。</span><span class="sxs-lookup"><span data-stu-id="b1e43-134">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="b1e43-135">除了数据库管理员希望您 MigrationId 列重命名为迁移\_id。</span><span class="sxs-lookup"><span data-stu-id="b1e43-135">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="b1e43-136"> 可以通过创建从 HistoryContext 派生的以下类实现此目的：</span><span class="sxs-lookup"><span data-stu-id="b1e43-136"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

<span data-ttu-id="b1e43-137">自定义 HistoryContext 准备就绪后，您需要通过注册它通过使 EF 意识到这一点[基于代码的配置](https://msdn.com/data/jj680699):</span><span class="sxs-lookup"><span data-stu-id="b1e43-137">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

<span data-ttu-id="b1e43-138">这就是这么多了。</span><span class="sxs-lookup"><span data-stu-id="b1e43-138">That’s pretty much it.</span></span> <span data-ttu-id="b1e43-139">现在可以转到包管理器控制台中，Enable-migrations，添加迁移和最后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="b1e43-139">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="b1e43-140">这会导致将添加到迁移历史记录表配置根据 HistoryContext 派生类中指定的详细信息的数据库。</span><span class="sxs-lookup"><span data-stu-id="b1e43-140">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![数据库](~/ef6/media/database.png)

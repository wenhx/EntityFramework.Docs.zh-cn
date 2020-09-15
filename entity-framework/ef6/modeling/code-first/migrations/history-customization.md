---
title: 自定义迁移历史记录表-EF6
description: 自定义实体框架6中的迁移历史记录表
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/history-customization
ms.openlocfilehash: b71ac7635d6804af2db9bf00acf7e8f4b2a11033
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073456"
---
# <a name="customizing-the-migrations-history-table"></a><span data-ttu-id="9657a-103">自定义迁移历史记录表</span><span class="sxs-lookup"><span data-stu-id="9657a-103">Customizing the migrations history table</span></span>
> [!NOTE]
> <span data-ttu-id="9657a-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="9657a-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9657a-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="9657a-105">If you are using an earlier version, some or all of the information does not apply.</span></span>

> [!NOTE]
> <span data-ttu-id="9657a-106">本文假设你知道如何在基本方案中使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="9657a-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="9657a-107">如果没有，则需要先阅读 [Code First 迁移](xref:ef6/modeling/code-first/migrations/index) ，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="9657a-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="what-is-migrations-history-table"></a><span data-ttu-id="9657a-108">什么是迁移历史记录表？</span><span class="sxs-lookup"><span data-stu-id="9657a-108">What is Migrations History Table?</span></span>

<span data-ttu-id="9657a-109">迁移历史记录表是 Code First 迁移用来存储应用于数据库的迁移的详细信息的表。</span><span class="sxs-lookup"><span data-stu-id="9657a-109">Migrations history table is a table used by Code First Migrations to store details about migrations applied to the database.</span></span> <span data-ttu-id="9657a-110">默认情况下，数据库中的表的名称为 \_ \_ MigrationHistory，并且在应用第一次迁移到数据库时创建该名称。</span><span class="sxs-lookup"><span data-stu-id="9657a-110">By default the name of the table in the database is \_\_MigrationHistory and it is created when applying the first migration to the database.</span></span> <span data-ttu-id="9657a-111">在实体框架5中，如果应用程序使用 Microsoft Sql Server 数据库，则此表为系统表。</span><span class="sxs-lookup"><span data-stu-id="9657a-111">In Entity Framework 5 this table was a system table if the application used Microsoft Sql Server database.</span></span> <span data-ttu-id="9657a-112">这在实体框架6中已更改，并且迁移历史记录表不再标记为系统表。</span><span class="sxs-lookup"><span data-stu-id="9657a-112">This has changed in Entity Framework 6 however and the migrations history table is no longer marked a system table.</span></span>

## <a name="why-customize-migrations-history-table"></a><span data-ttu-id="9657a-113">为什么自定义迁移历史记录表？</span><span class="sxs-lookup"><span data-stu-id="9657a-113">Why customize Migrations History Table?</span></span>

<span data-ttu-id="9657a-114">迁移历史记录表应仅由 Code First 迁移使用，并可以手动更改，从而中断迁移。</span><span class="sxs-lookup"><span data-stu-id="9657a-114">Migrations history table is supposed to be used solely by Code First Migrations and changing it manually can break migrations.</span></span> <span data-ttu-id="9657a-115">但有时，默认配置不合适，需要对表进行自定义，例如：</span><span class="sxs-lookup"><span data-stu-id="9657a-115">However sometimes the default configuration is not suitable and the table needs to be customized, for instance:</span></span>

-   <span data-ttu-id="9657a-116">需要更改列的名称和/或 facet，以启用3个<sup>rd</sup> 方迁移提供程序</span><span class="sxs-lookup"><span data-stu-id="9657a-116">You need to change names and/or facets of the columns to enable a 3<sup>rd</sup> party Migrations provider</span></span>
-   <span data-ttu-id="9657a-117">要更改表的名称</span><span class="sxs-lookup"><span data-stu-id="9657a-117">You want to change the name of the table</span></span>
-   <span data-ttu-id="9657a-118">需要对 \_ MigrationHistory 表使用非默认架构 \_</span><span class="sxs-lookup"><span data-stu-id="9657a-118">You need to use a non-default schema for the \_\_MigrationHistory table</span></span>
-   <span data-ttu-id="9657a-119">需要为特定版本的上下文存储其他数据，因此需要向表中添加其他列</span><span class="sxs-lookup"><span data-stu-id="9657a-119">You need to store additional data for a given version of the context and therefore you need to add an additional column to the table</span></span>

## <a name="words-of-precaution"></a><span data-ttu-id="9657a-120">预防词</span><span class="sxs-lookup"><span data-stu-id="9657a-120">Words of precaution</span></span>

<span data-ttu-id="9657a-121">更改迁移历史记录表的功能非常强大，但需小心不不要过度编写。</span><span class="sxs-lookup"><span data-stu-id="9657a-121">Changing the migration history table is powerful but you need to be careful to not overdo it.</span></span> <span data-ttu-id="9657a-122">EF 运行时当前不检查自定义迁移历史记录表是否与运行时兼容。</span><span class="sxs-lookup"><span data-stu-id="9657a-122">EF runtime currently does not check whether the customized migrations history table is compatible with the runtime.</span></span> <span data-ttu-id="9657a-123">如果不是，你的应用程序可能会在运行时中断或以不可预知的方式运行。</span><span class="sxs-lookup"><span data-stu-id="9657a-123">If it is not your application may break at runtime or behave in unpredictable ways.</span></span> <span data-ttu-id="9657a-124">如果对每个数据库使用多个上下文，这一点更重要，在这种情况下，多个上下文可以使用相同的迁移历史记录表来存储有关迁移的信息。</span><span class="sxs-lookup"><span data-stu-id="9657a-124">This is even more important if you use multiple contexts per database in which case multiple contexts can use the same migration history table to store information about migrations.</span></span>

## <a name="how-to-customize-migrations-history-table"></a><span data-ttu-id="9657a-125">如何自定义迁移历史记录表？</span><span class="sxs-lookup"><span data-stu-id="9657a-125">How to customize Migrations History Table?</span></span>

<span data-ttu-id="9657a-126">在开始之前，你需要知道只能在应用首次迁移之前自定义迁移历史记录表。</span><span class="sxs-lookup"><span data-stu-id="9657a-126">Before you start you need to know that you can customize the migrations history table only before you apply the first migration.</span></span> <span data-ttu-id="9657a-127">现在，到代码。</span><span class="sxs-lookup"><span data-stu-id="9657a-127">Now, to the code.</span></span>

<span data-ttu-id="9657a-128">首先，你将需要创建一个派生自 HistoryContext 类的类，。</span><span class="sxs-lookup"><span data-stu-id="9657a-128">First, you will need to create a class derived from System.Data.Entity.Migrations.History.HistoryContext class.</span></span> <span data-ttu-id="9657a-129">HistoryContext 类派生自 DbContext 类，因此配置迁移历史记录表非常类似于配置 EF 模型与 Fluent API。</span><span class="sxs-lookup"><span data-stu-id="9657a-129">The HistoryContext class is derived from the DbContext class so configuring the migrations history table is very similar to configuring EF models with fluent API.</span></span> <span data-ttu-id="9657a-130">只需重写 OnModelCreating 方法并使用 Fluent API 来配置该表。</span><span class="sxs-lookup"><span data-stu-id="9657a-130">You just need to override the OnModelCreating method and use fluent API to configure the table.</span></span>

>[!NOTE]
> <span data-ttu-id="9657a-131">通常，在配置 EF 模型时，无需调用 base。OnModelCreating 从重写的 OnModelCreating 方法 ( # A1，因为 OnModelCreating ( # A3 包含空正文。</span><span class="sxs-lookup"><span data-stu-id="9657a-131">Typically when you configure EF models you don’t need to call base.OnModelCreating() from the overridden OnModelCreating method since the DbContext.OnModelCreating() has empty body.</span></span> <span data-ttu-id="9657a-132">配置迁移历史记录表时不会出现这种情况。</span><span class="sxs-lookup"><span data-stu-id="9657a-132">This is not the case when configuring the migrations history table.</span></span> <span data-ttu-id="9657a-133">在这种情况下，需要在 OnModelCreating 中执行的第一件事 ( # A1 替代是实际调用 base。OnModelCreating ( # A3。</span><span class="sxs-lookup"><span data-stu-id="9657a-133">In this case the first thing to do in your OnModelCreating() override is to actually call base.OnModelCreating().</span></span> <span data-ttu-id="9657a-134">这会将迁移历史记录表配置为默认方法，然后在重写方法中进行调整。</span><span class="sxs-lookup"><span data-stu-id="9657a-134">This will configure the migrations history table in the default way which you then tweak in the overriding method.</span></span>

<span data-ttu-id="9657a-135">假设你想要重命名迁移历史记录表，并将其放入名为 "admin" 的自定义架构。</span><span class="sxs-lookup"><span data-stu-id="9657a-135">Let’s say you want to rename the migrations history table and put it to a custom schema called “admin”.</span></span> <span data-ttu-id="9657a-136">此外，DBA 需要将 MigrationId 列重命名为 "迁移 ID" \_ 。</span><span class="sxs-lookup"><span data-stu-id="9657a-136">In addition your DBA would like you to rename the MigrationId column to Migration\_ID.</span></span> <span data-ttu-id="9657a-137">可以通过创建以下派生自 HistoryContext 的类来实现此目的：</span><span class="sxs-lookup"><span data-stu-id="9657a-137"> You could achieve this by creating the following class derived from HistoryContext:</span></span>

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

<span data-ttu-id="9657a-138">自定义 HistoryContext 准备就绪后，你需要通过 [基于代码的配置](https://msdn.com/data/jj680699)注册 EF 来了解它：</span><span class="sxs-lookup"><span data-stu-id="9657a-138">Once your custom HistoryContext is ready you need to make EF aware of it by registering it via [code-based configuration](https://msdn.com/data/jj680699):</span></span>

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

<span data-ttu-id="9657a-139">就这么多了。</span><span class="sxs-lookup"><span data-stu-id="9657a-139">That’s pretty much it.</span></span> <span data-ttu-id="9657a-140">现在，你可以开始使用包管理器控制台，启用-迁移，添加-迁移，最后更新-数据库。</span><span class="sxs-lookup"><span data-stu-id="9657a-140">Now you can go to the Package Manager Console, Enable-Migrations, Add-Migration and finally Update-Database.</span></span> <span data-ttu-id="9657a-141">这会导致将迁移历史记录表添加到数据库，并根据你在 HistoryContext 派生类中指定的详细信息进行配置。</span><span class="sxs-lookup"><span data-stu-id="9657a-141">This should result in adding to the database a migrations history table configured according to the details you specified in your HistoryContext derived class.</span></span>

![迁移历史记录表](~/ef6/media/database.png)

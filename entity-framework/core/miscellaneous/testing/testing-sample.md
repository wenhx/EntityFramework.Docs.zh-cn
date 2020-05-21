---
title: ''
description: ''
author: ''
ms.date: ''
uid: ''
no-loc:
- Item
- Tag
- Items
- Tags
ms.openlocfilehash: ae073fc0b3a99fb9de07a3e0a42c638fe0838a5a
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672814"
---
# <a name="ef-core-testing-sample"></a><span data-ttu-id="23f06-101">EF Core 测试示例</span><span class="sxs-lookup"><span data-stu-id="23f06-101">EF Core testing sample</span></span>

> [!TIP]
> <span data-ttu-id="23f06-102">可在 GitHub 上找到此文档中的代码作为可[运行示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)。</span><span class="sxs-lookup"><span data-stu-id="23f06-102">The code in this document can be found on GitHub as a [runnable sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/).</span></span>
> <span data-ttu-id="23f06-103">请注意，其中某些测试**应该会失败**。</span><span class="sxs-lookup"><span data-stu-id="23f06-103">Note that some of these tests **are expected to fail**.</span></span> <span data-ttu-id="23f06-104">下面说明了这种情况的原因。</span><span class="sxs-lookup"><span data-stu-id="23f06-104">The reasons for this are explained below.</span></span> 

<span data-ttu-id="23f06-105">此文档介绍了用于测试使用 EF Core 的代码的示例。</span><span class="sxs-lookup"><span data-stu-id="23f06-105">This doc walks through a sample for testing code that uses EF Core.</span></span>

## <a name="the-application"></a><span data-ttu-id="23f06-106">应用程序</span><span class="sxs-lookup"><span data-stu-id="23f06-106">The application</span></span>

<span data-ttu-id="23f06-107">该[示例](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/)包含两个项目：</span><span class="sxs-lookup"><span data-stu-id="23f06-107">The [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Miscellaneous/Testing/ItemsWebApi/) contains two projects:</span></span>
- <span data-ttu-id="23f06-108">ItemsWebApi：通过单个控制器[ASP.NET Core 支持](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)的非常简单的 Web API</span><span class="sxs-lookup"><span data-stu-id="23f06-108">ItemsWebApi: A very simple [Web API backed by ASP.NET Core](/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio) with a single controller</span></span>
- <span data-ttu-id="23f06-109">测试：用于测试控制器的[XUnit](https://xunit.net/)测试项目</span><span class="sxs-lookup"><span data-stu-id="23f06-109">Tests: An [XUnit](https://xunit.net/) test project to test the controller</span></span>

### <a name="the-model-and-business-rules"></a><span data-ttu-id="23f06-110">模型和业务规则</span><span class="sxs-lookup"><span data-stu-id="23f06-110">The model and business rules</span></span>

<span data-ttu-id="23f06-111">此 API 支持以下两种实体类型： Items 和 Tags 。</span><span class="sxs-lookup"><span data-stu-id="23f06-111">The model backing this API has two entity types: Items and Tags.</span></span>

* Items<span data-ttu-id="23f06-112">具有区分大小写的名称和的集合 Tags 。</span><span class="sxs-lookup"><span data-stu-id="23f06-112"> have a case-sensitive name and a collection of Tags.</span></span>
* <span data-ttu-id="23f06-113">每个 Tag 都有一个标签和一个计数，表示它应用到的次数 Item 。</span><span class="sxs-lookup"><span data-stu-id="23f06-113">Each Tag has a label and a count representing the number of times it has been applied to the Item.</span></span>
* <span data-ttu-id="23f06-114">每个 Item 仅应有一个 Tag 具有给定标签的。</span><span class="sxs-lookup"><span data-stu-id="23f06-114">Each Item should only have one Tag with a given label.</span></span>
  * <span data-ttu-id="23f06-115">如果多次用相同标签标记项，则会增加带有该标签的现有标记的计数，而不会增加正在创建的新标记。</span><span class="sxs-lookup"><span data-stu-id="23f06-115">If an item is tagged with the same label more than once, then the count on the existing tag with that label is incremented instead of a new tag being created.</span></span> 
* <span data-ttu-id="23f06-116">删除将 Item 删除所有关联的 Tags 。</span><span class="sxs-lookup"><span data-stu-id="23f06-116">Deleting an Item should delete all associated Tags.</span></span>

#### <a name="the-item-entity-type"></a><span data-ttu-id="23f06-117">Item实体类型</span><span class="sxs-lookup"><span data-stu-id="23f06-117">The Item entity type</span></span>

<span data-ttu-id="23f06-118">`Item`实体类型：</span><span class="sxs-lookup"><span data-stu-id="23f06-118">The `Item` entity type:</span></span>

[!code-csharp[ItemEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Item.cs?name=ItemEntityType)]

<span data-ttu-id="23f06-119">及其在中的配置 `DbContext.OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="23f06-119">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureItem)]

<span data-ttu-id="23f06-120">请注意，实体类型限制了它可用于反映域模型和业务规则的方式。</span><span class="sxs-lookup"><span data-stu-id="23f06-120">Notice that entity type constrains the way it can be used to reflect the domain model and business rules.</span></span> <span data-ttu-id="23f06-121">具体而言：</span><span class="sxs-lookup"><span data-stu-id="23f06-121">In particular:</span></span>
- <span data-ttu-id="23f06-122">主键直接映射到 `_id` 字段，而不公开公开</span><span class="sxs-lookup"><span data-stu-id="23f06-122">The primary key is mapped directly to the `_id` field and not exposed publicly</span></span>
  - <span data-ttu-id="23f06-123">EF 检测并使用接受主键值和名称的私有构造函数。</span><span class="sxs-lookup"><span data-stu-id="23f06-123">EF detects and uses the private constructor accepting the primary key value and name.</span></span>
- <span data-ttu-id="23f06-124">此 `Name` 属性是只读的，仅在构造函数中设置。</span><span class="sxs-lookup"><span data-stu-id="23f06-124">The `Name` property is read-only and set only in the constructor.</span></span> 
- Tags<span data-ttu-id="23f06-125">公开为 `IReadOnlyList<Tag>` 以防止任意修改。</span><span class="sxs-lookup"><span data-stu-id="23f06-125"> are exposed as a `IReadOnlyList<Tag>` to prevent arbitrary modification.</span></span>
  - <span data-ttu-id="23f06-126">EF `Tags` 通过匹配属性的名称将属性与 `_tags` 支持字段相关联。</span><span class="sxs-lookup"><span data-stu-id="23f06-126">EF associates the `Tags` property with the `_tags` backing field by matching their names.</span></span> 
  - <span data-ttu-id="23f06-127">`AddTag`方法采用标签标签，并实现上面所述的业务规则。</span><span class="sxs-lookup"><span data-stu-id="23f06-127">The `AddTag` method takes a tag label and implements the business rule described above.</span></span>
    <span data-ttu-id="23f06-128">也就是说，只为新标签添加了标记。</span><span class="sxs-lookup"><span data-stu-id="23f06-128">That is, a tag is only added for new labels.</span></span>
    <span data-ttu-id="23f06-129">否则，现有标签上的计数将增加。</span><span class="sxs-lookup"><span data-stu-id="23f06-129">Otherwise the count on an existing label is incremented.</span></span>
- <span data-ttu-id="23f06-130">为 `Tags` 多对一关系配置导航属性</span><span class="sxs-lookup"><span data-stu-id="23f06-130">The `Tags` navigation property is configured for a many-to-one relationship</span></span>
  - <span data-ttu-id="23f06-131">不需要从到的导航属性 Tag Item ，因此不包含。</span><span class="sxs-lookup"><span data-stu-id="23f06-131">There is no need for a navigation property from Tag to Item, so it is not included.</span></span>
  - <span data-ttu-id="23f06-132">此外，不 Tag 会定义外键属性。</span><span class="sxs-lookup"><span data-stu-id="23f06-132">Also, Tag does not define a foreign key property.</span></span>
    <span data-ttu-id="23f06-133">EF 将创建和管理卷影状态中的属性。</span><span class="sxs-lookup"><span data-stu-id="23f06-133">Instead, EF will create and manage a property in shadow-state.</span></span>

#### <a name="the-tag-entity-type"></a><span data-ttu-id="23f06-134">Tag实体类型</span><span class="sxs-lookup"><span data-stu-id="23f06-134">The Tag entity type</span></span>

<span data-ttu-id="23f06-135">`Tag`实体类型：</span><span class="sxs-lookup"><span data-stu-id="23f06-135">The `Tag` entity type:</span></span>

[!code-csharp[TagEntityType](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Tag.cs?name=TagEntityType)]

<span data-ttu-id="23f06-136">及其在中的配置 `DbContext.OnModelCreating` ：</span><span class="sxs-lookup"><span data-stu-id="23f06-136">And its configuration in `DbContext.OnModelCreating`:</span></span>

[!code-csharp[ConfigureTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/ItemsContext.cs?name=ConfigureTag)]

<span data-ttu-id="23f06-137">与类似 Item ， Tag 隐藏其主键并使属性成为 `Label` 只读的。</span><span class="sxs-lookup"><span data-stu-id="23f06-137">Similarly to Item, Tag hides its primary key and makes the `Label` property read-only.</span></span>

### <a name="the-itemscontroller"></a><span data-ttu-id="23f06-138">ItemsController</span><span class="sxs-lookup"><span data-stu-id="23f06-138">The ItemsController</span></span>

<span data-ttu-id="23f06-139">Web API 控制器非常基本。</span><span class="sxs-lookup"><span data-stu-id="23f06-139">The Web API controller is pretty basic.</span></span>
<span data-ttu-id="23f06-140">它 `DbContext` 通过构造函数注入从依赖关系注入容器中获取：</span><span class="sxs-lookup"><span data-stu-id="23f06-140">It gets a `DbContext` from the dependency injection container through constructor injection:</span></span>

[!code-csharp[Constructor](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Constructor)]

<span data-ttu-id="23f06-141">它具有获取所有 Items 或 Item 具有给定名称的方法：</span><span class="sxs-lookup"><span data-stu-id="23f06-141">It has methods to get all Items or an Item with a given name:</span></span>

[!code-csharp[Get](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=Get)]

<span data-ttu-id="23f06-142">它有一个用于添加新的方法 Item ：</span><span class="sxs-lookup"><span data-stu-id="23f06-142">It has a method to add a new Item:</span></span>

[!code-csharp[PostItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostItem)]

<span data-ttu-id="23f06-143">用标签标记的方法 Item ：</span><span class="sxs-lookup"><span data-stu-id="23f06-143">A method to tag an Item with a label:</span></span>

[!code-csharp[PostTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=PostTag)]

<span data-ttu-id="23f06-144">以及用于删除 Item 和全部关联的方法 Tags ：</span><span class="sxs-lookup"><span data-stu-id="23f06-144">And a method to delete an Item and all associated Tags:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/ItemsWebApi/Controllers/ItemsController.cs?name=DeleteItem)]

<span data-ttu-id="23f06-145">为了减少混乱，已经删除了大多数验证和错误处理。</span><span class="sxs-lookup"><span data-stu-id="23f06-145">Most validation and error handling have been removed to reduce clutter.</span></span>

## <a name="the-tests"></a><span data-ttu-id="23f06-146">测试</span><span class="sxs-lookup"><span data-stu-id="23f06-146">The Tests</span></span>

<span data-ttu-id="23f06-147">这些测试被组织成通过多数据库提供程序配置运行：</span><span class="sxs-lookup"><span data-stu-id="23f06-147">The tests are organized to run with multiple database provider configurations:</span></span>
* <span data-ttu-id="23f06-148">SQL Server 提供程序，它是应用程序使用的提供程序</span><span class="sxs-lookup"><span data-stu-id="23f06-148">The SQL Server provider, which is the provider used by the application</span></span>
* <span data-ttu-id="23f06-149">SQLite 提供程序</span><span class="sxs-lookup"><span data-stu-id="23f06-149">The SQLite provider</span></span>
* <span data-ttu-id="23f06-150">使用内存中 SQLite 数据库的 SQLite 提供程序</span><span class="sxs-lookup"><span data-stu-id="23f06-150">The SQLite provider using in-memory SQLite databases</span></span>
* <span data-ttu-id="23f06-151">EF 内存中数据库提供程序</span><span class="sxs-lookup"><span data-stu-id="23f06-151">The EF in-memory database provider</span></span>

<span data-ttu-id="23f06-152">为实现此目的，方法是将所有测试放在一个基类中，然后从此继承以测试每个提供程序。</span><span class="sxs-lookup"><span data-stu-id="23f06-152">This is achieved by putting all the tests in a base class, then inheriting from this to test with each provider.</span></span>

> [!TIP]
> <span data-ttu-id="23f06-153">如果使用的不是 LocalDB，则需要更改 SQL Server 连接字符串。</span><span class="sxs-lookup"><span data-stu-id="23f06-153">You will need to change the SQL Server connection string if you're not using LocalDB.</span></span>

> [!TIP]
> <span data-ttu-id="23f06-154">有关使用 SQLite 进行内存中测试的指南，请参阅[使用 sqlite 进行测试](xref:core/miscellaneous/testing/sqlite)。</span><span class="sxs-lookup"><span data-stu-id="23f06-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for guidance on using SQLite for in-memory testing.</span></span> 

<span data-ttu-id="23f06-155">以下两个测试应该会失败：</span><span class="sxs-lookup"><span data-stu-id="23f06-155">The following two tests are expected to fail:</span></span>
* <span data-ttu-id="23f06-156">`Can_remove_item_and_all_associated_tags`与 EF 内存中数据库提供程序一起运行时</span><span class="sxs-lookup"><span data-stu-id="23f06-156">`Can_remove_item_and_all_associated_tags` when running with the EF in-memory database provider</span></span>
* <span data-ttu-id="23f06-157">`Can_add_item_differing_only_by_case`与 SQL Server 提供程序一起运行时</span><span class="sxs-lookup"><span data-stu-id="23f06-157">`Can_add_item_differing_only_by_case` when running with the SQL Server provider</span></span>

<span data-ttu-id="23f06-158">下面更详细地介绍了这种情况。</span><span class="sxs-lookup"><span data-stu-id="23f06-158">This is covered in more detail below.</span></span>

### <a name="setting-up-and-seeding-the-database"></a><span data-ttu-id="23f06-159">设置和播种数据库</span><span class="sxs-lookup"><span data-stu-id="23f06-159">Setting up and seeding the database</span></span>

<span data-ttu-id="23f06-160">与大多数测试框架一样，XUnit 将为每个测试运行创建一个新的测试类实例。</span><span class="sxs-lookup"><span data-stu-id="23f06-160">XUnit, like most testing frameworks, will create a new test class instance for each test run.</span></span>
<span data-ttu-id="23f06-161">此外，XUnit 不会并行运行给定测试类中的测试。</span><span class="sxs-lookup"><span data-stu-id="23f06-161">Also, XUnit will not run tests within a given test class in parallel.</span></span> <span data-ttu-id="23f06-162">这意味着我们可以在测试构造函数中设置和配置数据库，并且它将处于每个测试的已知状态。</span><span class="sxs-lookup"><span data-stu-id="23f06-162">This means that we can setup and configure the database in the test constructor and it will be in a well-known state for each test.</span></span>

> [!TIP]
> <span data-ttu-id="23f06-163">此示例将重新创建每个测试的数据库。</span><span class="sxs-lookup"><span data-stu-id="23f06-163">This sample recreates the database for each test.</span></span>
> <span data-ttu-id="23f06-164">这适用于 SQLite 和 EF 内存中数据库测试，但会涉及到与其他数据库系统（包括 SQL Server）的巨大开销。</span><span class="sxs-lookup"><span data-stu-id="23f06-164">This works well for SQLite and EF in-memory database testing but can involve significant overhead with other database systems, including SQL Server.</span></span>
> <span data-ttu-id="23f06-165">[跨测试共享数据库](xref:core/miscellaneous/testing/sharing-databases)中介绍了降低此开销的方法。</span><span class="sxs-lookup"><span data-stu-id="23f06-165">Approaches for reducing this overhead are covered in [Sharing databases across tests](xref:core/miscellaneous/testing/sharing-databases).</span></span>

<span data-ttu-id="23f06-166">运行每个测试时：</span><span class="sxs-lookup"><span data-stu-id="23f06-166">When each test is run:</span></span>
* <span data-ttu-id="23f06-167">为使用中的提供程序配置 DbContextOptions，并将其传递到基类构造函数</span><span class="sxs-lookup"><span data-stu-id="23f06-167">DbContextOptions are configured for the provider in use and passed to the base class constructor</span></span>
  * <span data-ttu-id="23f06-168">这些选项存储在属性中，并在整个测试中用于创建 DbContext 实例</span><span class="sxs-lookup"><span data-stu-id="23f06-168">These options are stored in a property and used throughout the tests for creating DbContext instances</span></span>
* <span data-ttu-id="23f06-169">调用种子方法来创建和播种数据库</span><span class="sxs-lookup"><span data-stu-id="23f06-169">A Seed method is called to create and seed the database</span></span>
  * <span data-ttu-id="23f06-170">Seed 方法通过删除并重新创建数据库来确保数据库干净</span><span class="sxs-lookup"><span data-stu-id="23f06-170">The Seed method ensures the database is clean by deleting it and then re-creating it</span></span>
  * <span data-ttu-id="23f06-171">创建了一些已知的测试实体，并将其保存到数据库中</span><span class="sxs-lookup"><span data-stu-id="23f06-171">Some well-known test entities are created and saved to the database</span></span>

[!code-csharp[Seeding](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=Seeding)]

<span data-ttu-id="23f06-172">然后，每个具体的测试类都继承自此。</span><span class="sxs-lookup"><span data-stu-id="23f06-172">Each concrete test class then inherits from this.</span></span>
<span data-ttu-id="23f06-173">例如：</span><span class="sxs-lookup"><span data-stu-id="23f06-173">For example:</span></span>

[!code-csharp[SqliteItemsControllerTest](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteItemsControllerTest.cs?name=SqliteItemsControllerTest)]

### <a name="test-structure"></a><span data-ttu-id="23f06-174">测试结构</span><span class="sxs-lookup"><span data-stu-id="23f06-174">Test structure</span></span>

<span data-ttu-id="23f06-175">即使应用程序使用依赖关系注入，测试也不是这样。</span><span class="sxs-lookup"><span data-stu-id="23f06-175">Even though the application uses dependency injection, the tests do not.</span></span>
<span data-ttu-id="23f06-176">在此处使用依赖项注入会很好，但它需要的附加代码却没有什么价值。</span><span class="sxs-lookup"><span data-stu-id="23f06-176">It would be fine to use dependency injection here, but the additional code it requires has little value.</span></span>
<span data-ttu-id="23f06-177">而是使用创建 DbContext， `new` 然后直接作为依赖关系传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="23f06-177">Instead, a DbContext is created using `new` and then directly passed as the dependency to the controller.</span></span>

<span data-ttu-id="23f06-178">然后，每个测试在控制器上执行受测方法，并断言结果与预期结果相同。</span><span class="sxs-lookup"><span data-stu-id="23f06-178">Each test then executes the method under test on the controller and asserts the results are as expected.</span></span>
<span data-ttu-id="23f06-179">例如：</span><span class="sxs-lookup"><span data-stu-id="23f06-179">For example:</span></span>

[!code-csharp[CanGetItems](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanGetItems)]

<span data-ttu-id="23f06-180">请注意，不同的 DbContext 实例用于对数据库进行种子设定并运行测试。</span><span class="sxs-lookup"><span data-stu-id="23f06-180">Notice that different DbContext instances are used to seed the database and run the tests.</span></span> <span data-ttu-id="23f06-181">这可确保在进行种子设定时测试不使用（或正在转移）上下文所跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="23f06-181">This ensures that the test is not using (or tripping over) entities tracked by the context when seeding.</span></span>
<span data-ttu-id="23f06-182">它还更适合于 web 应用和服务中发生的情况。</span><span class="sxs-lookup"><span data-stu-id="23f06-182">It also better matches what happens in web apps and services.</span></span>

<span data-ttu-id="23f06-183">用于改变数据库的测试在测试中创建第二个 DbContext 实例，原因类似。</span><span class="sxs-lookup"><span data-stu-id="23f06-183">Tests that mutate the database create a second DbContext instance in the test for similar reasons.</span></span>
<span data-ttu-id="23f06-184">也就是说，创建新的、干净的上下文，然后从数据库中读取该数据库，以确保将更改保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="23f06-184">That is, creating a new, clean, context and then reading into it from the database to ensure that the changes were saved to the database.</span></span> <span data-ttu-id="23f06-185">例如：</span><span class="sxs-lookup"><span data-stu-id="23f06-185">For example:</span></span>

[!code-csharp[CanAddItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItem)]

<span data-ttu-id="23f06-186">另外两个相关测试涵盖了有关添加标记的业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="23f06-186">Two slightly more involved tests cover the business logic around adding tags.</span></span>

[!code-csharp[CanAddTag](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddTag)]

[!code-csharp[CanUpTagCount](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanUpTagCount)]

## <a name="issues-using-different-database-providers"></a><span data-ttu-id="23f06-187">使用不同数据库提供程序时出现的问题</span><span class="sxs-lookup"><span data-stu-id="23f06-187">Issues using different database providers</span></span>

<span data-ttu-id="23f06-188">使用与生产应用程序中使用的不同的数据库系统进行测试可能导致出现问题。</span><span class="sxs-lookup"><span data-stu-id="23f06-188">Testing with a different database system than is used in the production application can lead to problems.</span></span>
<span data-ttu-id="23f06-189">它们在[测试使用 EF Core 的代码](xref:core/miscellaneous/testing/index)的概念级别中进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="23f06-189">These are covered at the conceptual level in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  
<span data-ttu-id="23f06-190">以下部分介绍了本示例中的测试演示的两个问题示例。</span><span class="sxs-lookup"><span data-stu-id="23f06-190">The sections below cover two examples of such issues demonstrated by the tests in this sample.</span></span>

### <a name="test-passes-when-the-application-is-broken"></a><span data-ttu-id="23f06-191">应用程序中断时的测试通过</span><span class="sxs-lookup"><span data-stu-id="23f06-191">Test passes when the application is broken</span></span>

<span data-ttu-id="23f06-192">应用程序的要求之一是 " Items 具有区分大小写的名称和集合 Tags "。</span><span class="sxs-lookup"><span data-stu-id="23f06-192">One of the requirements for our application is that "Items have a case-sensitive name and a collection of Tags."</span></span>
<span data-ttu-id="23f06-193">这非常简单，可以测试：</span><span class="sxs-lookup"><span data-stu-id="23f06-193">This is pretty simple to test:</span></span>

[!code-csharp[CanAddItemCaseInsensitive](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=CanAddItemCaseInsensitive)]

<span data-ttu-id="23f06-194">对 EF 内存中数据库运行此测试表明一切正常。</span><span class="sxs-lookup"><span data-stu-id="23f06-194">Running this test against the EF in-memory database indicates that everything is fine.</span></span>
<span data-ttu-id="23f06-195">使用 SQLite 时，所有内容仍能正常工作。</span><span class="sxs-lookup"><span data-stu-id="23f06-195">Everything still looks fine when using SQLite.</span></span>
<span data-ttu-id="23f06-196">但运行时测试会失败 SQL Server！</span><span class="sxs-lookup"><span data-stu-id="23f06-196">But the test fails when run against SQL Server!</span></span>

```
System.InvalidOperationException : Sequence contains more than one element
   at System.Linq.ThrowHelper.ThrowMoreThanOneElementException()
   at System.Linq.Enumerable.Single[TSource](IEnumerable`1 source)
   at Microsoft.EntityFrameworkCore.Query.Internal.QueryCompiler.Execute[TResult](Expression query)
   at Microsoft.EntityFrameworkCore.Query.Internal.EntityQueryProvider.Execute[TResult](Expression expression)
   at System.Linq.Queryable.Single[TSource](IQueryable`1 source, Expression`1 predicate)
   at Tests.ItemsControllerTest.Can_add_item_differing_only_by_case()
```

<span data-ttu-id="23f06-197">这是因为在默认情况下，EF 内存中数据库数据库和 SQLite 数据库都是区分大小写的。</span><span class="sxs-lookup"><span data-stu-id="23f06-197">This is because both the EF in-memory database and the SQLite database are case-sensitive by default.</span></span>
<span data-ttu-id="23f06-198">另一方面，SQL Server 不区分大小写！</span><span class="sxs-lookup"><span data-stu-id="23f06-198">SQL Server, on the other hand, is case-insensitive!</span></span> 

<span data-ttu-id="23f06-199">按照设计，EF Core 不会更改这些行为，因为强制进行区分大小写可能会对性能产生重大影响。</span><span class="sxs-lookup"><span data-stu-id="23f06-199">EF Core, by design, does not change these behaviors because forcing a change in case-sensitivity can have a big performance impact.</span></span>

<span data-ttu-id="23f06-200">知道这是一个问题，我们可以修复应用程序并在测试中进行补偿。</span><span class="sxs-lookup"><span data-stu-id="23f06-200">Once we know this is a problem we can fix the application and compensate in tests.</span></span>
<span data-ttu-id="23f06-201">但是，此处的要点是，如果仅通过 EF 内存中数据库或 SQLite 提供程序进行测试，则可能会丢失此 bug。</span><span class="sxs-lookup"><span data-stu-id="23f06-201">However, the point here is that this bug could be missed if only testing with the EF in-memory database or SQLite providers.</span></span>

### <a name="test-fails-when-the-application-is-correct"></a><span data-ttu-id="23f06-202">当应用程序正确时测试失败</span><span class="sxs-lookup"><span data-stu-id="23f06-202">Test fails when the application is correct</span></span> 

<span data-ttu-id="23f06-203">我们的应用程序的另一个要求是 "删除" Item 应删除所有关联的 Tags 。 "</span><span class="sxs-lookup"><span data-stu-id="23f06-203">Another of the requirements for our application is that "deleting an Item should delete all associated Tags."</span></span>
<span data-ttu-id="23f06-204">同样，易于测试：</span><span class="sxs-lookup"><span data-stu-id="23f06-204">Again, easy to test:</span></span>

[!code-csharp[DeleteItem](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/ItemsControllerTest.cs?name=DeleteItem)]

<span data-ttu-id="23f06-205">此测试通过 SQL Server 和 SQLite，但对于 EF 内存中数据库失败！</span><span class="sxs-lookup"><span data-stu-id="23f06-205">This test passes on SQL Server and SQLite, but fails with the EF in-memory database!</span></span>

```
Assert.False() Failure
Expected: False
Actual:   True
   at Tests.ItemsControllerTest.Can_remove_item_and_all_associated_tags()
```

<span data-ttu-id="23f06-206">在这种情况下，应用程序将正常工作，因为 SQL Server 支持[级联删除](xref:core/saving/cascade-delete)。</span><span class="sxs-lookup"><span data-stu-id="23f06-206">In this case, the application is working correctly because SQL Server supports [cascade deletes](xref:core/saving/cascade-delete).</span></span> <span data-ttu-id="23f06-207">SQLite 还支持级联删除，这与大多数关系数据库一样，因此，可以在 SQLite 上进行测试。</span><span class="sxs-lookup"><span data-stu-id="23f06-207">SQLite also supports cascade deletes, as do most relational databases, so testing this on SQLite works.</span></span>
<span data-ttu-id="23f06-208">另一方面，EF 内存中数据库不[支持级联删除](https://github.com/dotnet/efcore/issues/3924)。</span><span class="sxs-lookup"><span data-stu-id="23f06-208">On the other hand, the EF in-memory database [does not support cascade deletes](https://github.com/dotnet/efcore/issues/3924).</span></span>
<span data-ttu-id="23f06-209">这意味着应用程序的此部分不能通过 EF 内存中数据库提供程序进行测试。</span><span class="sxs-lookup"><span data-stu-id="23f06-209">This means that this part of the application cannot be tested with the EF in-memory database provider.</span></span>

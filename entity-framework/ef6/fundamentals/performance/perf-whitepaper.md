---
title: EF4、 EF5 和 EF6 的性能注意事项
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 4c1f03533cf6df49555c3ef8d09d5949b9a3335c
ms.sourcegitcommit: 33b2e84dae96040f60a613186a24ff3c7b00b6db
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56459206"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="fa216-102">有关 EF 4、 5 和 6 的性能注意事项</span><span class="sxs-lookup"><span data-stu-id="fa216-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="fa216-103">由 David Obando、 Eric Dettinger 等</span><span class="sxs-lookup"><span data-stu-id="fa216-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="fa216-104">发布日期：2012 年 4 月</span><span class="sxs-lookup"><span data-stu-id="fa216-104">Published: April 2012</span></span>

<span data-ttu-id="fa216-105">上次更新时间：2014 年 5 月</span><span class="sxs-lookup"><span data-stu-id="fa216-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="fa216-106">1.介绍</span><span class="sxs-lookup"><span data-stu-id="fa216-106">1. Introduction</span></span>

<span data-ttu-id="fa216-107">对象关系映射框架都提供面向对象的应用程序中的数据访问的抽象的简便方法。</span><span class="sxs-lookup"><span data-stu-id="fa216-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="fa216-108">对于.NET 应用程序，Microsoft 建议的 O/RM 是实体框架。</span><span class="sxs-lookup"><span data-stu-id="fa216-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="fa216-109">不过，使用任何抽象层性能可能会是问题。</span><span class="sxs-lookup"><span data-stu-id="fa216-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="fa216-110">此白皮书旨在开发应用程序使用 Entity Framework，开发人员以了解可能会影响性能，实体框架内部算法并提供提示以进行调查时显示的性能注意事项和提高性能，使用实体框架的应用程序中。</span><span class="sxs-lookup"><span data-stu-id="fa216-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="fa216-111">有多个对性能的良好主题已在 web 上，并且我们也尝试了指向这些资源，在可能的情况。</span><span class="sxs-lookup"><span data-stu-id="fa216-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="fa216-112">性能是一个棘手的主题。</span><span class="sxs-lookup"><span data-stu-id="fa216-112">Performance is a tricky topic.</span></span> <span data-ttu-id="fa216-113">此白皮书旨在作为资源以帮助使性能相关的使用实体框架的应用程序的决策。</span><span class="sxs-lookup"><span data-stu-id="fa216-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="fa216-114">我们包含了一些测试指标来演示性能，但这些指标不应为绝对指示器，您将看到在应用程序中的性能。</span><span class="sxs-lookup"><span data-stu-id="fa216-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="fa216-115">实用的角度而言，本文档假定 Entity Framework 4 运行在.NET 4.0 和 Entity Framework 5 和 6.NET 4.5 下运行。</span><span class="sxs-lookup"><span data-stu-id="fa216-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="fa216-116">Entity Framework 5 的性能改进的许多驻留在.NET 4.5 附带的核心组件。</span><span class="sxs-lookup"><span data-stu-id="fa216-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="fa216-117">实体框架 6 是带外版本，并不依赖于随.NET 实体框架组件。</span><span class="sxs-lookup"><span data-stu-id="fa216-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="fa216-118">实体框架 6，适用于.NET 4.0 和.NET 4.5 中，并可提供向那些尚未升级从.NET 4.0，但希望将其应用程序中的最新实体框架位大性能优势。</span><span class="sxs-lookup"><span data-stu-id="fa216-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="fa216-119">当本文档提到 Entity Framework 6 时，它是指在撰写本文时可用的最新版本： 版本 6.1.0。</span><span class="sxs-lookup"><span data-stu-id="fa216-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="fa216-120">2.冷 vs。热查询执行</span><span class="sxs-lookup"><span data-stu-id="fa216-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="fa216-121">第一次针对给定的模型，进行任何查询的实体框架做了大量工作在后台加载和验证模型。</span><span class="sxs-lookup"><span data-stu-id="fa216-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="fa216-122">我们经常引用此第一个查询为"冷"查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-122">We frequently refer to this first query as a "cold" query.</span></span><span data-ttu-id="fa216-123">  针对已加载模型的进一步查询名为"热"的查询，并且快得多。</span><span class="sxs-lookup"><span data-stu-id="fa216-123">  Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="fa216-124">让时间的花费时使用实体框架中，执行查询的高级视图，请参阅操作 Entity Framework 6 中的改进。</span><span class="sxs-lookup"><span data-stu-id="fa216-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="fa216-125">**第一次查询执行 – 冷查询**</span><span class="sxs-lookup"><span data-stu-id="fa216-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="fa216-126">代码用户写入</span><span class="sxs-lookup"><span data-stu-id="fa216-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="fa216-127">操作</span><span class="sxs-lookup"><span data-stu-id="fa216-127">Action</span></span>                    | <span data-ttu-id="fa216-128">EF4 性能影响</span><span class="sxs-lookup"><span data-stu-id="fa216-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="fa216-129">EF5 性能影响</span><span class="sxs-lookup"><span data-stu-id="fa216-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="fa216-130">EF6 对性能的影响</span><span class="sxs-lookup"><span data-stu-id="fa216-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="fa216-131">上下文创建</span><span class="sxs-lookup"><span data-stu-id="fa216-131">Context creation</span></span>          | <span data-ttu-id="fa216-132">中等</span><span class="sxs-lookup"><span data-stu-id="fa216-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="fa216-133">中等</span><span class="sxs-lookup"><span data-stu-id="fa216-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="fa216-134">低</span><span class="sxs-lookup"><span data-stu-id="fa216-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="fa216-135">查询表达式创建</span><span class="sxs-lookup"><span data-stu-id="fa216-135">Query expression creation</span></span> | <span data-ttu-id="fa216-136">低</span><span class="sxs-lookup"><span data-stu-id="fa216-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="fa216-137">低</span><span class="sxs-lookup"><span data-stu-id="fa216-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="fa216-138">低</span><span class="sxs-lookup"><span data-stu-id="fa216-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="fa216-139">LINQ 查询执行</span><span class="sxs-lookup"><span data-stu-id="fa216-139">LINQ query execution</span></span>      | <span data-ttu-id="fa216-140">元数据加载：高但缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="fa216-141">-查看生成：但缓存可能是非常高</span><span class="sxs-lookup"><span data-stu-id="fa216-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="fa216-142">参数评估：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="fa216-143">-查询转换：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="fa216-144">-具体化器生成：但缓存中</span><span class="sxs-lookup"><span data-stu-id="fa216-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="fa216-145">执行数据库查询：可能会很高</span><span class="sxs-lookup"><span data-stu-id="fa216-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="fa216-146">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="fa216-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="fa216-147">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="fa216-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="fa216-148">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="fa216-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="fa216-149">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="fa216-150">-标识查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="fa216-151">元数据加载：高但缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="fa216-152">-查看生成：但缓存可能是非常高</span><span class="sxs-lookup"><span data-stu-id="fa216-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="fa216-153">参数评估：低</span><span class="sxs-lookup"><span data-stu-id="fa216-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="fa216-154">-查询转换：但缓存中</span><span class="sxs-lookup"><span data-stu-id="fa216-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="fa216-155">-具体化器生成：但缓存中</span><span class="sxs-lookup"><span data-stu-id="fa216-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="fa216-156">执行数据库查询：可能会很高 （更好的查询在某些情况下）</span><span class="sxs-lookup"><span data-stu-id="fa216-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="fa216-157">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="fa216-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="fa216-158">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="fa216-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="fa216-159">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="fa216-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="fa216-160">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="fa216-161">-标识查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="fa216-162">元数据加载：高但缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="fa216-163">-查看生成：但缓存中</span><span class="sxs-lookup"><span data-stu-id="fa216-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="fa216-164">参数评估：低</span><span class="sxs-lookup"><span data-stu-id="fa216-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="fa216-165">-查询转换：但缓存中</span><span class="sxs-lookup"><span data-stu-id="fa216-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="fa216-166">-具体化器生成：但缓存中</span><span class="sxs-lookup"><span data-stu-id="fa216-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="fa216-167">执行数据库查询：可能会很高 （更好的查询在某些情况下）</span><span class="sxs-lookup"><span data-stu-id="fa216-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="fa216-168">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="fa216-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="fa216-169">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="fa216-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="fa216-170">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="fa216-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="fa216-171">对象具体化：中等 （比 EF5 快）</span><span class="sxs-lookup"><span data-stu-id="fa216-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="fa216-172">-标识查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="fa216-173">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="fa216-173">Connection.Close</span></span>          | <span data-ttu-id="fa216-174">低</span><span class="sxs-lookup"><span data-stu-id="fa216-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="fa216-175">低</span><span class="sxs-lookup"><span data-stu-id="fa216-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="fa216-176">低</span><span class="sxs-lookup"><span data-stu-id="fa216-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="fa216-177">**第二个查询执行 – 暖查询**</span><span class="sxs-lookup"><span data-stu-id="fa216-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="fa216-178">代码用户写入</span><span class="sxs-lookup"><span data-stu-id="fa216-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="fa216-179">操作</span><span class="sxs-lookup"><span data-stu-id="fa216-179">Action</span></span>                    | <span data-ttu-id="fa216-180">EF4 性能影响</span><span class="sxs-lookup"><span data-stu-id="fa216-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="fa216-181">EF5 性能影响</span><span class="sxs-lookup"><span data-stu-id="fa216-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="fa216-182">EF6 对性能的影响</span><span class="sxs-lookup"><span data-stu-id="fa216-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="fa216-183">上下文创建</span><span class="sxs-lookup"><span data-stu-id="fa216-183">Context creation</span></span>          | <span data-ttu-id="fa216-184">中等</span><span class="sxs-lookup"><span data-stu-id="fa216-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="fa216-185">中等</span><span class="sxs-lookup"><span data-stu-id="fa216-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="fa216-186">低</span><span class="sxs-lookup"><span data-stu-id="fa216-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="fa216-187">查询表达式创建</span><span class="sxs-lookup"><span data-stu-id="fa216-187">Query expression creation</span></span> | <span data-ttu-id="fa216-188">低</span><span class="sxs-lookup"><span data-stu-id="fa216-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="fa216-189">低</span><span class="sxs-lookup"><span data-stu-id="fa216-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="fa216-190">低</span><span class="sxs-lookup"><span data-stu-id="fa216-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="fa216-191">LINQ 查询执行</span><span class="sxs-lookup"><span data-stu-id="fa216-191">LINQ query execution</span></span>      | <span data-ttu-id="fa216-192">元数据~~加载~~查找：~~高，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-193">-查看~~生成~~查找：~~可能会非常高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-194">参数评估：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="fa216-195">-查询~~翻译~~查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="fa216-196">-具体化~~生成~~查找：~~中型，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-197">执行数据库查询：可能会很高</span><span class="sxs-lookup"><span data-stu-id="fa216-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="fa216-198">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="fa216-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="fa216-199">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="fa216-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="fa216-200">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="fa216-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="fa216-201">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="fa216-202">-标识查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="fa216-203">元数据~~加载~~查找：~~高，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-204">-查看~~生成~~查找：~~可能会非常高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-205">参数评估：低</span><span class="sxs-lookup"><span data-stu-id="fa216-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="fa216-206">-查询~~翻译~~查找：~~中型，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-207">-具体化~~生成~~查找：~~中型，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-208">执行数据库查询：可能会很高 （更好的查询在某些情况下）</span><span class="sxs-lookup"><span data-stu-id="fa216-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="fa216-209">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="fa216-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="fa216-210">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="fa216-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="fa216-211">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="fa216-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="fa216-212">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="fa216-213">-标识查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="fa216-214">元数据~~加载~~查找：~~高，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-215">-查看~~生成~~查找：~~中型，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-216">参数评估：低</span><span class="sxs-lookup"><span data-stu-id="fa216-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="fa216-217">-查询~~翻译~~查找：~~中型，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-218">-具体化~~生成~~查找：~~中型，但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="fa216-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="fa216-219">执行数据库查询：可能会很高 （更好的查询在某些情况下）</span><span class="sxs-lookup"><span data-stu-id="fa216-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="fa216-220">+ Connection.Open</span><span class="sxs-lookup"><span data-stu-id="fa216-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="fa216-221">+ Command.ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="fa216-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="fa216-222">+ DataReader.Read</span><span class="sxs-lookup"><span data-stu-id="fa216-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="fa216-223">对象具体化：中等 （比 EF5 快）</span><span class="sxs-lookup"><span data-stu-id="fa216-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="fa216-224">-标识查找：中等</span><span class="sxs-lookup"><span data-stu-id="fa216-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="fa216-225">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="fa216-225">Connection.Close</span></span>          | <span data-ttu-id="fa216-226">低</span><span class="sxs-lookup"><span data-stu-id="fa216-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="fa216-227">低</span><span class="sxs-lookup"><span data-stu-id="fa216-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="fa216-228">低</span><span class="sxs-lookup"><span data-stu-id="fa216-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="fa216-229">有几种方法，以减少冷和热查询的性能开销，我们将查看这些下一节中。</span><span class="sxs-lookup"><span data-stu-id="fa216-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="fa216-230">具体而言，我们将介绍减少模型加载冷查询中通过使用预生成的视图，这将有助于缓解性能问题的视图生成过程中发生的成本。</span><span class="sxs-lookup"><span data-stu-id="fa216-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="fa216-231">对于感兴趣的查询，我们将介绍查询计划缓存、 没有跟踪查询和不同的查询执行选项。</span><span class="sxs-lookup"><span data-stu-id="fa216-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="fa216-232">2.1 视图生成是什么？</span><span class="sxs-lookup"><span data-stu-id="fa216-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="fa216-233">若要了解哪些视图生成是，我们必须先了解什么是"映射视图"。</span><span class="sxs-lookup"><span data-stu-id="fa216-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="fa216-234">映射视图是可执行表示形式中每个实体集和关联的映射指定的转换。</span><span class="sxs-lookup"><span data-stu-id="fa216-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="fa216-235">在内部，这些映射视图将采用 CQTs （规范查询目录树） 的形状。</span><span class="sxs-lookup"><span data-stu-id="fa216-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="fa216-236">有两种类型的映射视图：</span><span class="sxs-lookup"><span data-stu-id="fa216-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="fa216-237">查询视图： 这些表示从数据库架构转到概念模型所需的转换。</span><span class="sxs-lookup"><span data-stu-id="fa216-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="fa216-238">更新的视图： 这些表示从概念模型转到数据库架构所需的转换。</span><span class="sxs-lookup"><span data-stu-id="fa216-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="fa216-239">请记住，概念模型可能不同于数据库架构以各种方式。</span><span class="sxs-lookup"><span data-stu-id="fa216-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="fa216-240">例如，可能使用一个单个表来存储两个不同的实体类型的数据。</span><span class="sxs-lookup"><span data-stu-id="fa216-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="fa216-241">继承和重要的映射的映射视图复杂程度方面扮演角色。</span><span class="sxs-lookup"><span data-stu-id="fa216-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="fa216-242">计算与指定的映射基于这些视图的过程是我们所说的视图生成。</span><span class="sxs-lookup"><span data-stu-id="fa216-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="fa216-243">视图生成可以或者发生动态加载模型时，或在生成时，通过使用"预生成的视图";后一种进行序列化到 C 的 Entity SQL 语句的形式\#或 VB 文件。</span><span class="sxs-lookup"><span data-stu-id="fa216-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="fa216-244">视图生成时，它们也进行了验证。</span><span class="sxs-lookup"><span data-stu-id="fa216-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="fa216-245">从性能角度看，大多数的成本视图生成的是实际的验证视图这可确保实体之间的连接有意义，并且具有正确的基数为所有受支持的操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="fa216-246">针对实体集的查询执行时，查询结合相应的查询视图中，此组合的结果是通过和运行计划编译器创建的查询的后备存储可以理解的表示形式。</span><span class="sxs-lookup"><span data-stu-id="fa216-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="fa216-247">对于 SQL Server，此编译的最终结果将 T-SQL SELECT 语句。</span><span class="sxs-lookup"><span data-stu-id="fa216-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="fa216-248">首次执行实体集的更新，更新视图是通过将其转换为目标数据库的 DML 语句类似的过程运行。</span><span class="sxs-lookup"><span data-stu-id="fa216-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="fa216-249">2.2 因素会影响视图生成性能</span><span class="sxs-lookup"><span data-stu-id="fa216-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="fa216-250">视图生成步骤的性能不仅取决于您的模型的大小，但还上如何相互关联的模型是。</span><span class="sxs-lookup"><span data-stu-id="fa216-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="fa216-251">如果通过继承链或关联连接两个实体，即是连接。</span><span class="sxs-lookup"><span data-stu-id="fa216-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="fa216-252">同样如果两个表连接通过外键，则说明连接。</span><span class="sxs-lookup"><span data-stu-id="fa216-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="fa216-253">已连接的实体和架构中的表数增加时，视图生成的成本增加。</span><span class="sxs-lookup"><span data-stu-id="fa216-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="fa216-254">虽然我们使用一些优化此进行改进，我们使用来生成和验证视图的算法是在最坏的情况下，指数。</span><span class="sxs-lookup"><span data-stu-id="fa216-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="fa216-255">似乎对性能产生负面影响的最大因素是：</span><span class="sxs-lookup"><span data-stu-id="fa216-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="fa216-256">模型引用的实体数和这些实体之间的关联量大小。</span><span class="sxs-lookup"><span data-stu-id="fa216-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="fa216-257">模型的复杂性，特别是涉及大量的类型的继承。</span><span class="sxs-lookup"><span data-stu-id="fa216-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="fa216-258">使用独立关联，而不外键关联。</span><span class="sxs-lookup"><span data-stu-id="fa216-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="fa216-259">小型的简单模型的成本可能很小，可不会受到使用预生成的视图。</span><span class="sxs-lookup"><span data-stu-id="fa216-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="fa216-260">模型大小和复杂性增加，有几种方式可以降低的成本视图生成和验证。</span><span class="sxs-lookup"><span data-stu-id="fa216-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="fa216-261">2.3 使用 Pre-Generated 视图以减少模型加载时间</span><span class="sxs-lookup"><span data-stu-id="fa216-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

<span data-ttu-id="fa216-262">有关如何使用 Entity Framework 6 的预生成的视图的详细信息，请访问[Pre-Generated 映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)</span><span class="sxs-lookup"><span data-stu-id="fa216-262">For detailed information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md)</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a><span data-ttu-id="fa216-263">2.3.1 预生成视图使用 Entity Framework Power Tools 社区版</span><span class="sxs-lookup"><span data-stu-id="fa216-263">2.3.1 Pre-Generated views using the Entity Framework Power Tools Community Edition</span></span>

<span data-ttu-id="fa216-264">可以使用[Entity Framework 6 Power 工具 Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)通过右键单击模型类文件并使用实体框架菜单选择"生成视图"中生成的 EDMX 和 Code First 模型视图。</span><span class="sxs-lookup"><span data-stu-id="fa216-264">You can use the [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="fa216-265">Entity Framework Power Tools Community 版本仅适用于 DbContext 派生上下文。</span><span class="sxs-lookup"><span data-stu-id="fa216-265">The Entity Framework Power Tools Community Edition work only on DbContext-derived contexts.</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="fa216-266">2.3.2 如何 EDMGen 所创建的模型使用预生成的视图</span><span class="sxs-lookup"><span data-stu-id="fa216-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="fa216-267">EDMGen 是一个实用工具，附带了.NET 和工作原理与 Entity Framework 4 和 5，但不能与 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="fa216-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="fa216-268">EDMGen 可以从命令行生成的模型文件、 对象层和视图。</span><span class="sxs-lookup"><span data-stu-id="fa216-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="fa216-269">一个输出将为你的选择，VB 或 C 语言中的视图文件\#。</span><span class="sxs-lookup"><span data-stu-id="fa216-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="fa216-270">这是包含每个实体集的实体 SQL 代码段的代码文件。</span><span class="sxs-lookup"><span data-stu-id="fa216-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="fa216-271">若要启用预生成的视图，只应在项目中包含该文件。</span><span class="sxs-lookup"><span data-stu-id="fa216-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="fa216-272">如果您手动对模型的架构文件进行编辑，将需要重新生成视图文件。</span><span class="sxs-lookup"><span data-stu-id="fa216-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="fa216-273">您可以执行此操作通过运行使用 EDMGen **/mode:ViewGeneration**标志。</span><span class="sxs-lookup"><span data-stu-id="fa216-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="fa216-274">2.3.3 如何 Pre-Generated 视图使用 EDMX 文件</span><span class="sxs-lookup"><span data-stu-id="fa216-274">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="fa216-275">此外可以使用 EDMGen 生成的 EDMX 文件视图-上面引用的 MSDN 主题介绍如何添加预生成事件，若要这样做的但这是复杂，有某些情况下，根本不可能。</span><span class="sxs-lookup"><span data-stu-id="fa216-275">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="fa216-276">它是使用 T4 模板在 edmx 文件中您的模型时生成视图通常更为方便。</span><span class="sxs-lookup"><span data-stu-id="fa216-276">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="fa216-277">ADO.NET 团队博客中发布了一文章，介绍如何为视图生成使用 T4 模板 ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。</span><span class="sxs-lookup"><span data-stu-id="fa216-277">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>).</span></span> <span data-ttu-id="fa216-278">此文章包括可以下载并添加到项目的模板。</span><span class="sxs-lookup"><span data-stu-id="fa216-278">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="fa216-279">模板已编写实体框架的第一个版本，因此它们不保证适用于实体框架的最新版本。</span><span class="sxs-lookup"><span data-stu-id="fa216-279">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="fa216-280">但是，您可以下载一组较新的视图生成模板的 Entity Framework 4 和 5from Visual Studio 库：</span><span class="sxs-lookup"><span data-stu-id="fa216-280">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="fa216-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="fa216-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="fa216-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="fa216-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="fa216-283">如果您使用的 Entity Framework 6，可以获取视图生成 T4 模板从 Visual Studio 库\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>。</span><span class="sxs-lookup"><span data-stu-id="fa216-283">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="fa216-284">2.4 降低的成本视图生成</span><span class="sxs-lookup"><span data-stu-id="fa216-284">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="fa216-285">使用预生成的视图移到设计时的视图生成从加载的模型 （运行时间） 的成本。</span><span class="sxs-lookup"><span data-stu-id="fa216-285">Using pre-generated views moves the cost of view generation from model loading (run time) to design time.</span></span> <span data-ttu-id="fa216-286">尽管这改进了在运行时的启动性能，你将在开发时仍然遇到视图生成的痛苦。</span><span class="sxs-lookup"><span data-stu-id="fa216-286">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="fa216-287">有几个其他技巧可帮助降低成本的同时在编译时和运行的时的视图生成。</span><span class="sxs-lookup"><span data-stu-id="fa216-287">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="fa216-288">2.4.1 使用外键关联以减少视图生成成本</span><span class="sxs-lookup"><span data-stu-id="fa216-288">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="fa216-289">我们已看到许多情况下，极大地切换到外键关联从独立关联模型中的关联改进了视图生成中所用的时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-289">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="fa216-290">为了演示这一改进，我们使用 EDMGen 生成 Navision 模型的两个版本。</span><span class="sxs-lookup"><span data-stu-id="fa216-290">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="fa216-291">*注意： seeappendix Cfor Navision 模型的说明。*</span><span class="sxs-lookup"><span data-stu-id="fa216-291">*Note: seeappendix Cfor a description of the Navision model.*</span></span> <span data-ttu-id="fa216-292">对于此练习，因其极大量的实体和它们之间的关系而有趣的是 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="fa216-292">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="fa216-293">与外键关联生成的此非常大的模型的一个版本，其他生成使用独立关联。</span><span class="sxs-lookup"><span data-stu-id="fa216-293">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="fa216-294">我们然后超时所用的时间来生成每个模型的视图。</span><span class="sxs-lookup"><span data-stu-id="fa216-294">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="fa216-295">实体 Framework5 测试用于从类 EntityViewGenerator GenerateViews() 方法生成视图，而 Entity Framework 6 测试使用了从类 StorageMappingItemCollection GenerateViews() 方法。</span><span class="sxs-lookup"><span data-stu-id="fa216-295">Entity Framework5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="fa216-296">这由于代码重新构建 Entity Framework 6 基本代码中发生的。</span><span class="sxs-lookup"><span data-stu-id="fa216-296">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="fa216-297">使用 Entity Framework 5，具有外键的模型的视图生成需要在实验室计算机 65 分钟。</span><span class="sxs-lookup"><span data-stu-id="fa216-297">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="fa216-298">未知的时间长度则可能需要花费生成使用独立关联的模型的视图。</span><span class="sxs-lookup"><span data-stu-id="fa216-298">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="fa216-299">我们保留超过一个月前我们的实验室安装每月更新中重新启动计算机正在运行的测试。</span><span class="sxs-lookup"><span data-stu-id="fa216-299">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="fa216-300">使用 Entity Framework 6，具有外键的模型的视图生成需要在同一台实验室计算机 28 秒。</span><span class="sxs-lookup"><span data-stu-id="fa216-300">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="fa216-301">使用独立关联的模型的视图生成需要 58 秒。</span><span class="sxs-lookup"><span data-stu-id="fa216-301">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="fa216-302">对其视图生成代码完成到 Entity Framework 6 的改进意味着多个项目不需要预生成的视图来获取更快的启动时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-302">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="fa216-303">它是重要的预生成 Entity Framework 4 和 5 中的视图，可完成使用 EDMGen 或 Entity Framework Power Tools 的备注。</span><span class="sxs-lookup"><span data-stu-id="fa216-303">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="fa216-304">通过 Entity Framework Power Tools 或如中所述以编程方式可以在 Entity Framework 6 图完成生成[Pre-Generated 映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)。</span><span class="sxs-lookup"><span data-stu-id="fa216-304">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="fa216-305">2.4.1.1 如何使用外键而不是独立关联</span><span class="sxs-lookup"><span data-stu-id="fa216-305">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="fa216-306">默认情况下，在 Visual Studio 中使用 EDMGen 或在实体设计器，获取 Fk，它只需单个复选框或命令行标志的 Fk 和 IAs 之间进行切换。</span><span class="sxs-lookup"><span data-stu-id="fa216-306">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="fa216-307">如果您有一个大型的 Code First 模型，使用独立关联必须视图生成相同的作用。</span><span class="sxs-lookup"><span data-stu-id="fa216-307">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="fa216-308">虽然一些开发人员会认为它污染它们的对象模型，通过包括你依赖的对象的类上的外键属性，可以避免这种影响。</span><span class="sxs-lookup"><span data-stu-id="fa216-308">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="fa216-309">您可以找到这一主题的详细信息\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>。</span><span class="sxs-lookup"><span data-stu-id="fa216-309">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="fa216-310">使用时</span><span class="sxs-lookup"><span data-stu-id="fa216-310">When using</span></span>      | <span data-ttu-id="fa216-311">操作步骤</span><span class="sxs-lookup"><span data-stu-id="fa216-311">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="fa216-312">实体设计器</span><span class="sxs-lookup"><span data-stu-id="fa216-312">Entity Designer</span></span> | <span data-ttu-id="fa216-313">添加后两个实体之间的关联，请确保具有引用约束。</span><span class="sxs-lookup"><span data-stu-id="fa216-313">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="fa216-314">引用约束告诉实体框架使用而不是独立关联的外键。</span><span class="sxs-lookup"><span data-stu-id="fa216-314">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="fa216-315">有关更多详细信息，请访问\<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-315">For additional details visit \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>.</span></span> |
| <span data-ttu-id="fa216-316">EDMGen</span><span class="sxs-lookup"><span data-stu-id="fa216-316">EDMGen</span></span>          | <span data-ttu-id="fa216-317">当使用 EDMGen 从数据库生成你的文件，将考虑外键，并将其添加到这种情况下的模型中。</span><span class="sxs-lookup"><span data-stu-id="fa216-317">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="fa216-318">有关由 EDMGen 公开的不同选项的详细信息，请访问[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-318">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="fa216-319">Code First</span><span class="sxs-lookup"><span data-stu-id="fa216-319">Code First</span></span>      | <span data-ttu-id="fa216-320">请参阅的"关系约定"部分[Code First 约定](~/ef6/modeling/code-first/conventions/built-in.md)主题，了解如何使用 Code First 时包括依赖对象上的外键属性的信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-320">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="fa216-321">2.4.2 将您的模型移动到单独的程序集</span><span class="sxs-lookup"><span data-stu-id="fa216-321">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="fa216-322">时直接在应用程序的项目中包含您的模型并生成通过预生成事件或 T4 模板的视图，视图生成和验证会发生时重新生成项目时，即使该模型未更改。</span><span class="sxs-lookup"><span data-stu-id="fa216-322">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="fa216-323">如果将模型移动到单独的程序集并从应用程序的项目中引用它，可以无需重新生成包含模型的项目对应用程序进行其他更改。</span><span class="sxs-lookup"><span data-stu-id="fa216-323">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="fa216-324">*注意：*  移动您的模型来分隔时程序集，请务必将该模型的连接字符串复制到客户端项目的应用程序配置文件。</span><span class="sxs-lookup"><span data-stu-id="fa216-324">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="fa216-325">2.4.3 禁用了基于 edmx 的模型的验证</span><span class="sxs-lookup"><span data-stu-id="fa216-325">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="fa216-326">EDMX 模型是在编译时验证，即使模型保持不变。</span><span class="sxs-lookup"><span data-stu-id="fa216-326">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="fa216-327">如果已验证了您的模型，可以禁止在编译时验证显示在属性窗口中的"验证"生成属性设置为 false。</span><span class="sxs-lookup"><span data-stu-id="fa216-327">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="fa216-328">当您更改映射或模型时，可以重新暂时启用验证，以验证所做的更改。</span><span class="sxs-lookup"><span data-stu-id="fa216-328">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="fa216-329">请注意，性能改进了实体框架设计器的 Entity Framework 6 的"验证"生成成本是远低于在以前版本的设计器。</span><span class="sxs-lookup"><span data-stu-id="fa216-329">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="fa216-330">在实体框架中的 3 个缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-330">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="fa216-331">实体框架具有以下形式的缓存内置：</span><span class="sxs-lookup"><span data-stu-id="fa216-331">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="fa216-332">对象缓存-内置于一个 ObjectContext 实例 ObjectStateManager 跟踪已使用该实例在检索对象的内存中。</span><span class="sxs-lookup"><span data-stu-id="fa216-332">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="fa216-333">这也称为是第一级缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-333">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="fa216-334">查询计划缓存的不止一次执行查询时重复使用生成的存储命令。</span><span class="sxs-lookup"><span data-stu-id="fa216-334">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="fa216-335">元数据缓存-在同一个模型的不同连接之间共享模型的元数据。</span><span class="sxs-lookup"><span data-stu-id="fa216-335">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="fa216-336">除了 EF 提供了默认情况下，一种特殊的 ADO.NET 数据提供程序名为换行提供程序还可用于扩展实体框架使用从数据库中检索的结果缓存的缓存也称为第二级缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-336">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="fa216-337">3.1 对象缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-337">3.1 Object Caching</span></span>

<span data-ttu-id="fa216-338">默认情况下时查询的结果中返回实体之前只是 EF 将具体化，ObjectContext 会检查是否具有相同键的实体具有已被加载到其 ObjectStateManager。</span><span class="sxs-lookup"><span data-stu-id="fa216-338">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="fa216-339">如果已存在具有相同键的实体 EF 将其包括在查询的结果。</span><span class="sxs-lookup"><span data-stu-id="fa216-339">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="fa216-340">尽管 EF 仍将发出对数据库查询，此行为可以跳过大多数具体化实体多次的成本。</span><span class="sxs-lookup"><span data-stu-id="fa216-340">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="fa216-341">3.1.1 从使用查找 DbContext 对象缓存中获取实体</span><span class="sxs-lookup"><span data-stu-id="fa216-341">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="fa216-342">与常规查询，不同 DbSet (第一次 EF 4.1 中包含的 Api) 中的查找方法将搜索在内存中执行之前甚至发出对数据库查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-342">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="fa216-343">请务必注意，两个不同的 ObjectContext 实例将具有两个不同的 ObjectStateManager 实例，这意味着它们具有单独的对象缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-343">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="fa216-344">查找使用的主键值来尝试查找由上下文跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-344">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="fa216-345">如果该实体不在上下文中然后执行并针对数据库评估查询和如果上下文中或在数据库中找不到该实体，则返回 null。</span><span class="sxs-lookup"><span data-stu-id="fa216-345">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="fa216-346">请注意，查找也会返回已添加到上下文，但尚未保存到数据库的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-346">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="fa216-347">没有需要时使用查找执行性能考虑事项。</span><span class="sxs-lookup"><span data-stu-id="fa216-347">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="fa216-348">默认情况下此方法的调用以便能够检测到仍在等待提交到数据库的更改都将触发对象缓存的验证。</span><span class="sxs-lookup"><span data-stu-id="fa216-348">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="fa216-349">此过程可能会非常昂贵，如果有大量的对象缓存中或要添加到对象缓存中，一个大型的对象图中的对象，但它也可禁用。</span><span class="sxs-lookup"><span data-stu-id="fa216-349">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="fa216-350">在某些情况下，您可能会觉察到通过调用方法时禁用自动检测的到差异一个数量级的更改。</span><span class="sxs-lookup"><span data-stu-id="fa216-350">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="fa216-351">尚未第二个数量级，所以我们认为该对象实际上是时与在缓存中对象具有要检索从数据库时。</span><span class="sxs-lookup"><span data-stu-id="fa216-351">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="fa216-352">下面是与所使用的以毫秒为单位的负载为 5000 实体表示我们 microbenchmarks 一些测量结果的示例图表：</span><span class="sxs-lookup"><span data-stu-id="fa216-352">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="fa216-353">![.NET 4.5 对数刻度](~/ef6/media/net45logscale.png ".NET 4.5 的对数刻度")</span><span class="sxs-lookup"><span data-stu-id="fa216-353">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="fa216-354">禁用自动检测更改的查找示例：</span><span class="sxs-lookup"><span data-stu-id="fa216-354">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="fa216-355">您拥有使用 Find 方法时要考虑的是：</span><span class="sxs-lookup"><span data-stu-id="fa216-355">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="fa216-356">如果对象不在缓存中查找的优点起作用，但语法仍比通过键查询更简单。</span><span class="sxs-lookup"><span data-stu-id="fa216-356">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="fa216-357">如果自动检测更改已启用的 Find 方法成本可能会增加了一个数量级，或更多具体取决于您的模型和对象缓存中的实体的量的复杂性。</span><span class="sxs-lookup"><span data-stu-id="fa216-357">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="fa216-358">此外，请记住，仅查找返回要查找的实体，它不会自动加载其关联的实体如果它们尚不在对象缓存中。</span><span class="sxs-lookup"><span data-stu-id="fa216-358">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="fa216-359">如果需要检索关联的实体，可以通过预先加载与键使用查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-359">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="fa216-360">有关详细信息请参阅**8.1 延迟加载 vs。预先加载**。</span><span class="sxs-lookup"><span data-stu-id="fa216-360">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="fa216-361">3.1.2 性能问题时对象缓存中有多个实体</span><span class="sxs-lookup"><span data-stu-id="fa216-361">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="fa216-362">对象缓存有助于提高实体框架的整体响应能力。</span><span class="sxs-lookup"><span data-stu-id="fa216-362">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="fa216-363">但是，当对象缓存了极大量的实体加载它可能会影响某些操作，如添加、 删除、 查找、 入口、 SaveChanges 和的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-363">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="fa216-364">具体而言，将极大对象缓存产生负面影响触发调用 DetectChanges 的操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-364">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="fa216-365">DetectChanges 对象状态管理器和直接由对象关系图的大小决定其性能将与同步对象图。</span><span class="sxs-lookup"><span data-stu-id="fa216-365">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="fa216-366">有关 DetectChanges 的详细信息，请参阅[跟踪 POCO 实体中的更改](https://msdn.microsoft.com/library/dd456848.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-366">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="fa216-367">在使用 Entity Framework 6，开发人员就能够直接在 DbSet，而不是对集合进行迭代和调用添加一次每个实例上调用 AddRange 和 RemoveRange。</span><span class="sxs-lookup"><span data-stu-id="fa216-367">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="fa216-368">使用 range 方法的优点是 DetectChanges 的成本仅为整个实体而不是每个添加的实体每一次的集的一次付费。</span><span class="sxs-lookup"><span data-stu-id="fa216-368">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="fa216-369">3.2 查询计划缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-369">3.2 Query Plan Caching</span></span>

<span data-ttu-id="fa216-370">第一次执行查询时，它将经历内部计划编译器来将概念的查询转换为存储命令 (例如，T-SQL 针对 SQL Server 运行时执行)。</span><span class="sxs-lookup"><span data-stu-id="fa216-370">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span><span data-ttu-id="fa216-371">  如果启用了查询计划缓存，查询是在下次执行存储命令检索直接从查询计划缓存的执行，从而绕过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="fa216-371">  If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="fa216-372">查询计划缓存在同一 AppDomain 中的 ObjectContext 实例之间共享。</span><span class="sxs-lookup"><span data-stu-id="fa216-372">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="fa216-373">无需保存到一个 ObjectContext 实例，以便从查询计划缓存中受益。</span><span class="sxs-lookup"><span data-stu-id="fa216-373">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="fa216-374">3.2.1 有关查询计划缓存的一些注意事项</span><span class="sxs-lookup"><span data-stu-id="fa216-374">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="fa216-375">查询计划缓存将共享所有查询类型：实体 SQL、 LINQ to Entities 和 CompiledQuery 对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-375">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="fa216-376">默认情况下，查询计划缓存可用于 Entity SQL 查询是否执行通过 EntityCommand 或 ObjectQuery。</span><span class="sxs-lookup"><span data-stu-id="fa216-376">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="fa216-377">它还默认情况下启用 linq to Entities 查询在实体框架在.NET 4.5 和 Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="fa216-377">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="fa216-378">可以通过 （在 EntityCommand 或 ObjectQuery） EnablePlanCaching 属性设置为 false 禁用查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-378">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="fa216-379">例如：</span><span class="sxs-lookup"><span data-stu-id="fa216-379">For example:</span></span>
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   <span data-ttu-id="fa216-380">对于参数化查询，更改参数的值将仍命中缓存的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-380">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="fa216-381">但是，更改参数的方面 （例如，大小、 精度或规模） 都将遇到缓存中的不同项。</span><span class="sxs-lookup"><span data-stu-id="fa216-381">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="fa216-382">使用实体 SQL，查询字符串时键的一部分。</span><span class="sxs-lookup"><span data-stu-id="fa216-382">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="fa216-383">在所有更改查询将导致不同的缓存条目，即使查询在功能上等效。</span><span class="sxs-lookup"><span data-stu-id="fa216-383">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="fa216-384">这包括更改大小写或空格。</span><span class="sxs-lookup"><span data-stu-id="fa216-384">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="fa216-385">当使用 LINQ，查询的处理来生成键的一部分。</span><span class="sxs-lookup"><span data-stu-id="fa216-385">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="fa216-386">LINQ 表达式更改因此将生成不同的密钥。</span><span class="sxs-lookup"><span data-stu-id="fa216-386">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="fa216-387">可以应用其他技术限制;有关更多详细信息，请参阅 Autocompiled 查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-387">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322-cache-eviction-algorithm"></a><span data-ttu-id="fa216-388">3.2.2 缓存逐出算法</span><span class="sxs-lookup"><span data-stu-id="fa216-388">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="fa216-389">了解内部算法可将帮助你找出时到启用或禁用查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-389">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="fa216-390">清理算法如下所示：</span><span class="sxs-lookup"><span data-stu-id="fa216-390">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="fa216-391">后缓存中包含了固定数量的条目 (800)，我们开始计时器定期 （一次每分钟） 扫描缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-391">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="fa216-392">缓存扫描期间条目已从 LFRU （最频繁-最近使用过的） 上的缓存的基础。</span><span class="sxs-lookup"><span data-stu-id="fa216-392">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="fa216-393">确定哪些项弹出时，此算法将考虑命中的次数和年龄。</span><span class="sxs-lookup"><span data-stu-id="fa216-393">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="fa216-394">在每个缓存扫描结束时，缓存中同样包含 800 条目。</span><span class="sxs-lookup"><span data-stu-id="fa216-394">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="fa216-395">确定要逐出的项时，将同等对待所有缓存条目。</span><span class="sxs-lookup"><span data-stu-id="fa216-395">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="fa216-396">这意味着 CompiledQuery 存储命令具有相同的机会逐出作为 Entity SQL 查询的存储命令。</span><span class="sxs-lookup"><span data-stu-id="fa216-396">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="fa216-397">请注意，缓存逐出计时器触发事件时在缓存中，有 800 实体但缓存仅扫频 60 秒后此计时器已启动。</span><span class="sxs-lookup"><span data-stu-id="fa216-397">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="fa216-398">这意味着，最多 60 秒缓存可能会增长得非常大。</span><span class="sxs-lookup"><span data-stu-id="fa216-398">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="fa216-399">3.2.3 测试演示查询计划缓存性能指标</span><span class="sxs-lookup"><span data-stu-id="fa216-399">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="fa216-400">若要演示的查询计划缓存在应用程序的性能效果，我们执行测试，我们执行多个针对 Navision 模型的 Entity SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-400">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="fa216-401">请参阅的附录 Navision 模型和执行的查询的类型的说明。</span><span class="sxs-lookup"><span data-stu-id="fa216-401">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="fa216-402">在此测试中，我们首先循环访问查询的列表并执行每个一次将其添加到缓存中，（如果缓存已启用）。</span><span class="sxs-lookup"><span data-stu-id="fa216-402">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="fa216-403">此步骤是 untimed。</span><span class="sxs-lookup"><span data-stu-id="fa216-403">This step is untimed.</span></span> <span data-ttu-id="fa216-404">接下来，我们睡眠状态超过 60 秒，以允许缓存扫描结合使用以执行; 的主线程最后，我们循环访问列表第 2 个时间来执行缓存的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-404">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="fa216-405">此外，他 SQL Server 计划高速缓存刷新之前每个集的查询执行，以便我们获得准确的时间反映由查询计划缓存的优势。</span><span class="sxs-lookup"><span data-stu-id="fa216-405">Additionally, he SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-test-results"></a><span data-ttu-id="fa216-406">3.2.3.1 测试结果</span><span class="sxs-lookup"><span data-stu-id="fa216-406">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="fa216-407">测试</span><span class="sxs-lookup"><span data-stu-id="fa216-407">Test</span></span>                                                                   | <span data-ttu-id="fa216-408">EF5 没有缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-408">EF5 no cache</span></span> | <span data-ttu-id="fa216-409">EF5 缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-409">EF5 cached</span></span> | <span data-ttu-id="fa216-410">EF6 不缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-410">EF6 no cache</span></span> | <span data-ttu-id="fa216-411">EF6 缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-411">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="fa216-412">枚举所有 18723 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-412">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="fa216-413">124</span><span class="sxs-lookup"><span data-stu-id="fa216-413">124</span></span>          | <span data-ttu-id="fa216-414">125.4</span><span class="sxs-lookup"><span data-stu-id="fa216-414">125.4</span></span>      | <span data-ttu-id="fa216-415">124.3</span><span class="sxs-lookup"><span data-stu-id="fa216-415">124.3</span></span>        | <span data-ttu-id="fa216-416">125.3</span><span class="sxs-lookup"><span data-stu-id="fa216-416">125.3</span></span>      |
| <span data-ttu-id="fa216-417">避免扫描 （只是第一次 800 查询，无论复杂性如何）</span><span class="sxs-lookup"><span data-stu-id="fa216-417">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="fa216-418">41.7</span><span class="sxs-lookup"><span data-stu-id="fa216-418">41.7</span></span>         | <span data-ttu-id="fa216-419">5.5</span><span class="sxs-lookup"><span data-stu-id="fa216-419">5.5</span></span>        | <span data-ttu-id="fa216-420">40.5</span><span class="sxs-lookup"><span data-stu-id="fa216-420">40.5</span></span>         | <span data-ttu-id="fa216-421">5.4</span><span class="sxs-lookup"><span data-stu-id="fa216-421">5.4</span></span>        |
| <span data-ttu-id="fa216-422">只需 AggregatingSubtotals 查询 （178 总计-可避免扫描）</span><span class="sxs-lookup"><span data-stu-id="fa216-422">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="fa216-423">39.5</span><span class="sxs-lookup"><span data-stu-id="fa216-423">39.5</span></span>         | <span data-ttu-id="fa216-424">4.5</span><span class="sxs-lookup"><span data-stu-id="fa216-424">4.5</span></span>        | <span data-ttu-id="fa216-425">38.1</span><span class="sxs-lookup"><span data-stu-id="fa216-425">38.1</span></span>         | <span data-ttu-id="fa216-426">4.6</span><span class="sxs-lookup"><span data-stu-id="fa216-426">4.6</span></span>        |

<span data-ttu-id="fa216-427">*所有时间以秒为单位。*</span><span class="sxs-lookup"><span data-stu-id="fa216-427">*All times in seconds.*</span></span>

<span data-ttu-id="fa216-428">道德的时执行还有的不同查询 （例如，动态创建的查询），缓存不起作用并生成刷新的缓存可以保留受益最充分地利用计划缓存中实际使用的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-428">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="fa216-429">AggregatingSubtotals 查询是最复杂的与我们测试的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-429">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="fa216-430">按预期运行，更复杂的查询是，你将看到从查询计划缓存的益处越多。</span><span class="sxs-lookup"><span data-stu-id="fa216-430">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="fa216-431">由于 CompiledQuery 实际上是具有其计划缓存的 LINQ 查询，而不是等效的 Entity SQL 查询 CompiledQuery 比较应具有类似的结果。</span><span class="sxs-lookup"><span data-stu-id="fa216-431">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="fa216-432">事实上，如果应用具有大量动态 Entity SQL 查询，填充查询缓存将实际上也会导致 CompiledQueries"反编译"时从缓存中刷新。</span><span class="sxs-lookup"><span data-stu-id="fa216-432">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="fa216-433">在此方案中，可能通过禁用动态查询，以确定优先级 CompiledQueries 的缓存提高性能。</span><span class="sxs-lookup"><span data-stu-id="fa216-433">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="fa216-434">更棒的是，当然，会重写应用程序以使用参数化的查询，而不是动态查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-434">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="fa216-435">3.3 使用 CompiledQuery 提高使用 LINQ 查询的性能</span><span class="sxs-lookup"><span data-stu-id="fa216-435">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="fa216-436">我们测试表明，使用 CompiledQuery 可以通过自带的 7%权益 autocompiled LINQ 查询;这意味着，您将 7%更少的时间从 Entity Framework 堆栈; 执行代码它并不意味着你的应用程序将在 7%更快。</span><span class="sxs-lookup"><span data-stu-id="fa216-436">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="fa216-437">通常情况下，编写和 EF 5.0 中的 CompiledQuery 对象进行维护的成本可能不值得时带来的益处相的麻烦。</span><span class="sxs-lookup"><span data-stu-id="fa216-437">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="fa216-438">您的实际效果可能会有所不同，因此执行此选项，如果你的项目需要额外的推送。</span><span class="sxs-lookup"><span data-stu-id="fa216-438">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="fa216-439">请注意，CompiledQueries 仅与 ObjectContext 派生模型兼容，并与 DbContext 派生模型不兼容。</span><span class="sxs-lookup"><span data-stu-id="fa216-439">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="fa216-440">有关创建和调用 CompiledQuery 的详细信息，请参阅[编译的查询 (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-440">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="fa216-441">有两个您需要执行使用 CompiledQuery，即要求使用静态实例和这些问题所拥有的可组合性时的注意事项。</span><span class="sxs-lookup"><span data-stu-id="fa216-441">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="fa216-442">以下是这些两个注意事项的详细说明。</span><span class="sxs-lookup"><span data-stu-id="fa216-442">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-use-static-compiledquery-instances"></a><span data-ttu-id="fa216-443">3.3.1 使用静态 CompiledQuery 实例</span><span class="sxs-lookup"><span data-stu-id="fa216-443">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="fa216-444">编译 LINQ 查询是一个耗时的过程，因为我们不希望每次我们需要从数据库提取数据时执行此操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-444">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="fa216-445">CompiledQuery 实例可以进行一次编译和运行多次，但必须要小心操作和采购以重复使用相同的 CompiledQuery 实例而不是反复地编译每次。</span><span class="sxs-lookup"><span data-stu-id="fa216-445">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="fa216-446">使用静态成员来存储 CompiledQuery 实例将成为必需的;否则不会看到任何权益。</span><span class="sxs-lookup"><span data-stu-id="fa216-446">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="fa216-447">例如，假设页面包含用于处理显示所选类别产品的以下方法正文：</span><span class="sxs-lookup"><span data-stu-id="fa216-447">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

<span data-ttu-id="fa216-448">在这种情况下，将动态创建新的 CompiledQuery 实例，每次调用的方法。</span><span class="sxs-lookup"><span data-stu-id="fa216-448">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="fa216-449">而不是通过从查询计划缓存中检索存储命令来查看性能优势，CompiledQuery 会通过计划编译器每次创建一个新实例。</span><span class="sxs-lookup"><span data-stu-id="fa216-449">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="fa216-450">事实上，您将为污染查询计划缓存的新的 CompiledQuery 项，每次调用的方法。</span><span class="sxs-lookup"><span data-stu-id="fa216-450">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="fa216-451">相反，你想要创建的已编译的查询，一个静态实例，以便每次调用该方法时所调用同一个已编译的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-451">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="fa216-452">一种方法这是通过将 CompiledQuery 实例添加为对象上下文的成员。</span><span class="sxs-lookup"><span data-stu-id="fa216-452">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span><span data-ttu-id="fa216-453">  然后可以操作小更简洁的帮助器方法通过访问 CompiledQuery:</span><span class="sxs-lookup"><span data-stu-id="fa216-453">  You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

<span data-ttu-id="fa216-454">此帮助器方法会调用，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fa216-454">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a><span data-ttu-id="fa216-455">3.3.2 撰写 CompiledQuery 转移</span><span class="sxs-lookup"><span data-stu-id="fa216-455">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="fa216-456">用于任何 LINQ 查询组合的功能是非常有用;若要执行此操作，您只需调用方法后 IQueryable 如*skip （)* 或*count （)*。</span><span class="sxs-lookup"><span data-stu-id="fa216-456">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="fa216-457">但是，所以本质上来说返回一个新的 IQueryable 对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-457">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="fa216-458">虽然没有执行任何操作，使您从技术上讲不再通过 CompiledQuery 撰写，但这样做将导致生成新的 IQueryable 对象，需要再次传递通过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="fa216-458">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="fa216-459">某些组件将使用组合 IQueryable 对象，若要启用高级的功能。</span><span class="sxs-lookup"><span data-stu-id="fa216-459">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="fa216-460">例如，ASP。NET 的 GridView 可以进行数据绑定到 IQueryable 对象通过 SelectMethod 属性。</span><span class="sxs-lookup"><span data-stu-id="fa216-460">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="fa216-461">GridView 然后将组合此 IQueryable 对象，以允许排序和分页通过数据模型。</span><span class="sxs-lookup"><span data-stu-id="fa216-461">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="fa216-462">正如您所看到的 GridView 使用 CompiledQuery 将不会命中已编译的查询，但会生成一个新的 autocompiled 查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-462">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="fa216-463">当向查询添加渐进式筛选器时，其中可能会遇到此类的一个位置。</span><span class="sxs-lookup"><span data-stu-id="fa216-463">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="fa216-464">例如，假设你有客户页的可选筛选器 （例如，国家/地区和 OrdersCount） 的多个下拉列表。</span><span class="sxs-lookup"><span data-stu-id="fa216-464">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="fa216-465">你可以组合这些筛选器的 CompiledQuery IQueryable 结果，但这样做将导致每次执行该计划编译器通过将新查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-465">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 <span data-ttu-id="fa216-466">若要避免此重新编译，可以重写 CompiledQuery 要考虑的可能的筛选器：</span><span class="sxs-lookup"><span data-stu-id="fa216-466">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="fa216-467">这会在 UI 中，如调用：</span><span class="sxs-lookup"><span data-stu-id="fa216-467">Which would be invoked in the UI like:</span></span>

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 <span data-ttu-id="fa216-468">此处的影响是生成的存储命令始终具有带 null 检查，筛选器，但这些应是要优化的数据库服务器相当简单：</span><span class="sxs-lookup"><span data-stu-id="fa216-468">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="fa216-469">3.4 元数据缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-469">3.4 Metadata caching</span></span>

<span data-ttu-id="fa216-470">实体框架还支持元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-470">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="fa216-471">这实质上跨同一个模型的不同连接缓存的类型信息和类型到数据库的映射信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-471">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="fa216-472">元数据缓存是每个 AppDomain 唯一的。</span><span class="sxs-lookup"><span data-stu-id="fa216-472">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="fa216-473">3.4.1 元数据缓存算法</span><span class="sxs-lookup"><span data-stu-id="fa216-473">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="fa216-474">每个 EntityConnection 存储在 ItemCollection 模型的元数据信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-474">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="fa216-475">请注意，有不同的 ItemCollection 对象模型的不同部分。</span><span class="sxs-lookup"><span data-stu-id="fa216-475">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="fa216-476">例如，StoreItemCollections 包含数据库模型中; 有关的信息ObjectItemCollection 包含数据模型中; 有关的信息EdmItemCollection 包含有关概念模型的信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-476">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="fa216-477">如果两个连接使用相同的连接字符串，它们将共享同一个 ItemCollection 实例。</span><span class="sxs-lookup"><span data-stu-id="fa216-477">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="fa216-478">在功能上等效，但文本上不同的连接字符串可能会导致不同的元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-478">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="fa216-479">我们不要对进行词汇切分的连接字符串，因此，只需更改标记的顺序应导致共享元数据。</span><span class="sxs-lookup"><span data-stu-id="fa216-479">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="fa216-480">但是，在功能上看起来相同的两个连接字符串可能无法计算为相同后词汇切分。</span><span class="sxs-lookup"><span data-stu-id="fa216-480">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="fa216-481">ItemCollection 定期检查的使用。</span><span class="sxs-lookup"><span data-stu-id="fa216-481">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="fa216-482">如果确定，未被最近访问工作区，它会将标记为在下一步的缓存扫描的清理。</span><span class="sxs-lookup"><span data-stu-id="fa216-482">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="fa216-483">只创建一个 EntityConnection 将导致要创建 （尽管直到打开连接时，将不会初始化在其中的项集合） 的元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-483">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="fa216-484">此工作区将保留内存中，直到缓存算法确定它不是"使用中"。</span><span class="sxs-lookup"><span data-stu-id="fa216-484">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="fa216-485">客户顾问团队编写了描述保存到 ItemCollection 的引用以使用大型模型时避免"弃用"的博客文章： \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-485">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="fa216-486">3.4.2 元数据缓存和缓存查询计划之间的关系</span><span class="sxs-lookup"><span data-stu-id="fa216-486">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="fa216-487">查询计划缓存实例位于 MetadataWorkspace ItemCollection 的存储类型。</span><span class="sxs-lookup"><span data-stu-id="fa216-487">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="fa216-488">这意味着缓存的存储命令将用于针对任何上下文中使用给定的 MetadataWorkspace 实例化的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-488">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="fa216-489">这还意味着，如果您有两个连接字符串稍有不同之后将拆分为标记, 不匹配，将具有不同的查询计划缓存实例。</span><span class="sxs-lookup"><span data-stu-id="fa216-489">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="fa216-490">3.5 结果缓存</span><span class="sxs-lookup"><span data-stu-id="fa216-490">3.5 Results caching</span></span>

<span data-ttu-id="fa216-491">与结果缓存 （也称为"第二层缓存"），在本地缓存中保留查询的结果。</span><span class="sxs-lookup"><span data-stu-id="fa216-491">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="fa216-492">发出查询时，您首先查看结果是否之前针对存储查询本地可用。</span><span class="sxs-lookup"><span data-stu-id="fa216-492">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="fa216-493">虽然实体框架不直接支持缓存的结果，就可以使用包装提供程序添加第二层缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-493">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="fa216-494">第二层缓存使用的示例包装提供程序是 Alachisoft 的[实体框架第二个级别缓存的基础 NCache](http://www.alachisoft.com/ncache/entity-framework.html)。</span><span class="sxs-lookup"><span data-stu-id="fa216-494">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](http://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="fa216-495">此实现的第二级缓存是一种采用 LINQ 表达式计算后的位置 （和 funcletized） 注入的功能和计算或从第一级缓存中检索查询执行计划。</span><span class="sxs-lookup"><span data-stu-id="fa216-495">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="fa216-496">第二级缓存将然后仅将结果存储在原始数据库以便具体化管道仍之后执行。</span><span class="sxs-lookup"><span data-stu-id="fa216-496">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="fa216-497">3.5.1 使用包装提供程序缓存的结果的其他参考</span><span class="sxs-lookup"><span data-stu-id="fa216-497">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="fa216-498">Julie Lerman 撰写了包括如何更新示例包装提供程序以使用 Windows Server AppFabric caching"第二级缓存在实体框架和 Windows Azure"MSDN 文章： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="fa216-498">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="fa216-499">如果您正在使用 Entity Framework 5，团队博客中发布了一文章，介绍如何使用 Entity Framework 5 的缓存提供程序运行的工作： \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-499">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>.</span></span> <span data-ttu-id="fa216-500">它还包括帮助自动执行添加到你的项目第二级缓存的 T4 模板。</span><span class="sxs-lookup"><span data-stu-id="fa216-500">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="fa216-501">4 个 Autocompiled 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-501">4 Autocompiled Queries</span></span>

<span data-ttu-id="fa216-502">在使用实体框架对数据库发出查询时，它必须都将通过一系列的步骤之前实际上具体化结果;此类的一个步骤是将查询编译。</span><span class="sxs-lookup"><span data-stu-id="fa216-502">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="fa216-503">实体 SQL 查询是已知可具有良好的性能，因为它们会自动缓存，因此第二个或第三个时间执行同一查询，它可以跳过计划编译器，改为使用缓存的计划。</span><span class="sxs-lookup"><span data-stu-id="fa216-503">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="fa216-504">实体框架 5 引入了自动缓存 LINQ to Entities 查询以及。</span><span class="sxs-lookup"><span data-stu-id="fa216-504">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="fa216-505">在过去版本的 Entity Framework 创建 CompiledQuery 加快应用的性能是常见的做法，因为这将使 LINQ to Entities 查询可缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-505">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="fa216-506">由于缓存而不使用的 compiledquery 都现在自动完成，我们调用此功能"autocompiled 查询"。</span><span class="sxs-lookup"><span data-stu-id="fa216-506">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="fa216-507">有关查询计划缓存和其机制的详细信息，请参阅查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-507">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="fa216-508">实体框架检测时查询所需重新编译，并且这样做，即使它已编译之前调用该查询时。</span><span class="sxs-lookup"><span data-stu-id="fa216-508">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="fa216-509">导致要重新编译的查询的常见情况是：</span><span class="sxs-lookup"><span data-stu-id="fa216-509">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="fa216-510">更改 mergeoption，然后在查询相关联。</span><span class="sxs-lookup"><span data-stu-id="fa216-510">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="fa216-511">将不使用缓存的查询、 改为将再次运行计划编译器和新创建的计划缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-511">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="fa216-512">更改 ContextOptions.UseCSharpNullComparisonBehavior 的值。</span><span class="sxs-lookup"><span data-stu-id="fa216-512">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="fa216-513">获取更改 mergeoption，然后与相同的效果。</span><span class="sxs-lookup"><span data-stu-id="fa216-513">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="fa216-514">其他条件可以防止您的查询使用缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-514">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="fa216-515">常见示例包括：</span><span class="sxs-lookup"><span data-stu-id="fa216-515">Common examples are:</span></span>

-   <span data-ttu-id="fa216-516">使用 IEnumerable&lt;T&gt;。包含&lt;&gt;（T 值）。</span><span class="sxs-lookup"><span data-stu-id="fa216-516">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="fa216-517">使用生成常量具有查询的函数。</span><span class="sxs-lookup"><span data-stu-id="fa216-517">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="fa216-518">使用非映射对象的属性。</span><span class="sxs-lookup"><span data-stu-id="fa216-518">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="fa216-519">将查询链接到另一个需要在下次重新编译的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-519">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="fa216-520">4.1 使用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值）</span><span class="sxs-lookup"><span data-stu-id="fa216-520">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="fa216-521">实体框架不会缓存查询调用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值） 对内存中集合，因为集合的值被视为易失性。</span><span class="sxs-lookup"><span data-stu-id="fa216-521">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="fa216-522">下面的示例查询将不缓存，因此始终将由计划编译器处理：</span><span class="sxs-lookup"><span data-stu-id="fa216-522">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="fa216-523">请注意，它包含对 IEnumerable 的大小执行速度快或如何慢确定您的查询进行编译。</span><span class="sxs-lookup"><span data-stu-id="fa216-523">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="fa216-524">使用在上面的示例所示的大型集合时，性能可能会显著降低。</span><span class="sxs-lookup"><span data-stu-id="fa216-524">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="fa216-525">实体框架 6 包含优化方式 IEnumerable&lt;T&gt;。包含&lt;T&gt;执行查询时，工作原理 （T 值）。</span><span class="sxs-lookup"><span data-stu-id="fa216-525">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="fa216-526">生成的 SQL 代码是生成要快得多且更具可读性，并在大多数情况下它也会执行更快地在服务器中。</span><span class="sxs-lookup"><span data-stu-id="fa216-526">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="fa216-527">4.2 使用生成常量具有查询的函数</span><span class="sxs-lookup"><span data-stu-id="fa216-527">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="fa216-528">Skip （）、 take （）、 contains （） 和 DefautIfEmpty() LINQ 运算符不会生成包含参数的 SQL 查询，但改为将作为常量传递给它们的值。</span><span class="sxs-lookup"><span data-stu-id="fa216-528">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="fa216-529">正因为如此，会是相同的最终污染查询的查询计划缓存中，EF 堆栈上和在数据库服务器上，并执行操作不获取重复利用除非在后续查询执行过程中使用相同的常量。</span><span class="sxs-lookup"><span data-stu-id="fa216-529">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="fa216-530">例如：</span><span class="sxs-lookup"><span data-stu-id="fa216-530">For example:</span></span>

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="fa216-531">在此示例中，每次执行此查询时使用的查询 id 的值将编译到新的计划。</span><span class="sxs-lookup"><span data-stu-id="fa216-531">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="fa216-532">中的 Skip 和 Take 执行分页时使用特定注意。</span><span class="sxs-lookup"><span data-stu-id="fa216-532">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="fa216-533">在 EF6 中这些方法具有有效地使缓存的查询计划可重复使用，因为 EF 可以捕获变量传递给这些方法，并将它们转换成 SQLparameters lambda 重载。</span><span class="sxs-lookup"><span data-stu-id="fa216-533">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="fa216-534">这也有助于使缓存更整洁，因为每个查询使用 Skip 和 Take 的不同常量否则会收到其自己的查询计划缓存条目。</span><span class="sxs-lookup"><span data-stu-id="fa216-534">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="fa216-535">请考虑下面的代码，这并非最佳，但仅用来将此类查询：</span><span class="sxs-lookup"><span data-stu-id="fa216-535">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="fa216-536">这一相同代码的速度更快版本将涉及使用 lambda 调用跳过：</span><span class="sxs-lookup"><span data-stu-id="fa216-536">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i \< count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="fa216-537">第二个代码片段可能运行更快达 11%，因为每次运行查询时，这可以节省 CPU 时间，可避免污染查询缓存使用相同的查询计划。</span><span class="sxs-lookup"><span data-stu-id="fa216-537">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="fa216-538">此外，由于要跳过该参数是在闭包中的代码可能也类似以下形式现在：</span><span class="sxs-lookup"><span data-stu-id="fa216-538">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="fa216-539">4.3 使用非映射对象的属性</span><span class="sxs-lookup"><span data-stu-id="fa216-539">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="fa216-540">当查询使用非映射对象类型的属性，因为将不获取缓存参数则执行查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-540">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="fa216-541">例如：</span><span class="sxs-lookup"><span data-stu-id="fa216-541">For example:</span></span>

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

<span data-ttu-id="fa216-542">在此示例中，假定类 NonMappedType 不是实体模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="fa216-542">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="fa216-543">此查询可以轻易改为使用非映射类型并改为使用本地变量作为查询的参数：</span><span class="sxs-lookup"><span data-stu-id="fa216-543">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

<span data-ttu-id="fa216-544">在这种情况下，查询将能够获取缓存，并将从查询计划缓存中受益。</span><span class="sxs-lookup"><span data-stu-id="fa216-544">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="fa216-545">4.4 链接到要求重新编译的查询</span><span class="sxs-lookup"><span data-stu-id="fa216-545">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="fa216-546">与上述示例相同，如果您依赖于需要进行重新编译的查询的第二个查询整个第二个查询将还重新编译。</span><span class="sxs-lookup"><span data-stu-id="fa216-546">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="fa216-547">下面是示例演示了此方案中：</span><span class="sxs-lookup"><span data-stu-id="fa216-547">Here’s an example to illustrate this scenario:</span></span>

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

<span data-ttu-id="fa216-548">该示例为泛型，但它演示了如何将链接到 firstQuery 导致 secondQuery 无法获取缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-548">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="fa216-549">如果 firstQuery 不需要重新编译的查询，然后 secondQuery 将已缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-549">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="fa216-550">5 NoTracking 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-550">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="fa216-551">5.1 禁用更改跟踪来减少管理开销状态</span><span class="sxs-lookup"><span data-stu-id="fa216-551">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="fa216-552">如果您在只读的情况下，并且想要避免加载到 ObjectStateManager 对象的开销，您可以发出"不跟踪"的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-552">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span><span data-ttu-id="fa216-553">  可以在查询级别禁用更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="fa216-553">  Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="fa216-554">但请注意，通过禁用更改跟踪您会有效地关闭对象缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-554">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="fa216-555">在查询实体时，我们不能通过从 ObjectStateManager 中拉取之前具体化查询结果中跳过具体化。</span><span class="sxs-lookup"><span data-stu-id="fa216-555">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="fa216-556">如果重复查询上相同的上下文相同的实体，您实际上可能会看到从启用更改跟踪中受益的性能。</span><span class="sxs-lookup"><span data-stu-id="fa216-556">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="fa216-557">在查询时使用 ObjectContext，ObjectQuery 和 ObjectSet 实例将记住 MergeOption 设置，并对其组成的查询将继承父查询有效 MergeOption 后。</span><span class="sxs-lookup"><span data-stu-id="fa216-557">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="fa216-558">在使用 DbContext，可以通过对 DbSet 调用 AsNoTracking() 修饰符禁用跟踪。</span><span class="sxs-lookup"><span data-stu-id="fa216-558">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="fa216-559">5.1.1 禁用更改跟踪的查询使用 DbContext 时</span><span class="sxs-lookup"><span data-stu-id="fa216-559">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="fa216-560">可以通过链接到查询中的 AsNoTracking() 方法的调用来切换到 NoTracking 的查询模式。</span><span class="sxs-lookup"><span data-stu-id="fa216-560">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="fa216-561">与不同的 ObjectQuery，DbContext API 中的 DbSet 和 DbQuery 类不为 mergeoption，然后包含可变属性。</span><span class="sxs-lookup"><span data-stu-id="fa216-561">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="fa216-562">5.1.2 禁用更改跟踪在查询级别使用 ObjectContext</span><span class="sxs-lookup"><span data-stu-id="fa216-562">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="fa216-563">5.1.3 禁用更改跟踪的整个实体集使用 ObjectContext</span><span class="sxs-lookup"><span data-stu-id="fa216-563">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="fa216-564">5.2 测试演示 NoTracking 查询的性能优势的度量值</span><span class="sxs-lookup"><span data-stu-id="fa216-564">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="fa216-565">在此测试我们将介绍但代价是通过比较跟踪对 Navision 模型 NoTracking 查询填充 ObjectStateManager。</span><span class="sxs-lookup"><span data-stu-id="fa216-565">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="fa216-566">请参阅的附录 Navision 模型和执行的查询的类型的说明。</span><span class="sxs-lookup"><span data-stu-id="fa216-566">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="fa216-567">在此测试中，我们循环访问查询的列表，并执行每个一次。</span><span class="sxs-lookup"><span data-stu-id="fa216-567">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="fa216-568">我们已使用默认合并选项的"仅附加"运行测试、 一次使用 NoTracking 查询和一次的两种变体。</span><span class="sxs-lookup"><span data-stu-id="fa216-568">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="fa216-569">我们运行 3 次的每个变体，并需要在运行的平均值。</span><span class="sxs-lookup"><span data-stu-id="fa216-569">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="fa216-570">测试之间我们清除 SQL Server 上的查询缓存，并将 tempdb 收缩通过运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="fa216-570">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="fa216-571">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="fa216-571">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="fa216-572">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="fa216-572">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="fa216-573">DBCC SHRINKDATABASE (tempdb，0)</span><span class="sxs-lookup"><span data-stu-id="fa216-573">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="fa216-574">测试结果，中间值的 3 个运行：</span><span class="sxs-lookup"><span data-stu-id="fa216-574">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="fa216-575">不跟踪-工作集</span><span class="sxs-lookup"><span data-stu-id="fa216-575">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="fa216-576">无跟踪-时间</span><span class="sxs-lookup"><span data-stu-id="fa216-576">NO TRACKING – TIME</span></span> | <span data-ttu-id="fa216-577">仅 – 追加工作集</span><span class="sxs-lookup"><span data-stu-id="fa216-577">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="fa216-578">追加专用 – 时间</span><span class="sxs-lookup"><span data-stu-id="fa216-578">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="fa216-579">**实体框架 5**</span><span class="sxs-lookup"><span data-stu-id="fa216-579">**Entity Framework 5**</span></span> | <span data-ttu-id="fa216-580">460361728</span><span class="sxs-lookup"><span data-stu-id="fa216-580">460361728</span></span>                 | <span data-ttu-id="fa216-581">1163536 ms</span><span class="sxs-lookup"><span data-stu-id="fa216-581">1163536 ms</span></span>         | <span data-ttu-id="fa216-582">596545536</span><span class="sxs-lookup"><span data-stu-id="fa216-582">596545536</span></span>                 | <span data-ttu-id="fa216-583">1273042 ms</span><span class="sxs-lookup"><span data-stu-id="fa216-583">1273042 ms</span></span>         |
| <span data-ttu-id="fa216-584">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="fa216-584">**Entity Framework 6**</span></span> | <span data-ttu-id="fa216-585">647127040</span><span class="sxs-lookup"><span data-stu-id="fa216-585">647127040</span></span>                 | <span data-ttu-id="fa216-586">190228 ms</span><span class="sxs-lookup"><span data-stu-id="fa216-586">190228 ms</span></span>          | <span data-ttu-id="fa216-587">832798720</span><span class="sxs-lookup"><span data-stu-id="fa216-587">832798720</span></span>                 | <span data-ttu-id="fa216-588">195521 ms</span><span class="sxs-lookup"><span data-stu-id="fa216-588">195521 ms</span></span>          |

<span data-ttu-id="fa216-589">实体框架 5 将具有较小的内存需求量运行结束时比 Entity Framework 6。</span><span class="sxs-lookup"><span data-stu-id="fa216-589">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="fa216-590">使用 Entity Framework 6 的其他内存是额外的内存结构和启用新功能和更好的性能的代码的结果。</span><span class="sxs-lookup"><span data-stu-id="fa216-590">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="fa216-591">使用 ObjectStateManager 时，也是有占用的内存中有明显差异。</span><span class="sxs-lookup"><span data-stu-id="fa216-591">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="fa216-592">当跟踪我们从数据库具体化的所有实体，实体框架 5 30%的增加其内存占用。</span><span class="sxs-lookup"><span data-stu-id="fa216-592">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="fa216-593">执行此操作时，实体框架 6 通过 28%增加其内存占用。</span><span class="sxs-lookup"><span data-stu-id="fa216-593">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="fa216-594">根据时间，Entity Framework 6 优于 Entity Framework 5，在此测试中由大边距。</span><span class="sxs-lookup"><span data-stu-id="fa216-594">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="fa216-595">实体框架 6 完成大约 16%的时间由 Entity Framework 5 中的测试。</span><span class="sxs-lookup"><span data-stu-id="fa216-595">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="fa216-596">此外，Entity Framework 5 需要 9%的时间才能完成时正在使用 ObjectStateManager。</span><span class="sxs-lookup"><span data-stu-id="fa216-596">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="fa216-597">在比较中，Entity Framework 6 使用多的时间来使用 ObjectStateManager 时的 3%。</span><span class="sxs-lookup"><span data-stu-id="fa216-597">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="fa216-598">6 个查询执行选项</span><span class="sxs-lookup"><span data-stu-id="fa216-598">6 Query Execution Options</span></span>

<span data-ttu-id="fa216-599">实体框架提供几种不同方式查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-599">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="fa216-600">我们将看一看以下选项，比较各自的优缺点，并检查其性能特征：</span><span class="sxs-lookup"><span data-stu-id="fa216-600">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="fa216-601">LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="fa216-601">LINQ to Entities.</span></span>
-   <span data-ttu-id="fa216-602">不是跟踪，LINQ 到实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-602">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="fa216-603">ObjectQuery 上的 SQL 的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-603">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="fa216-604">实体 EntityCommand 上的 SQL。</span><span class="sxs-lookup"><span data-stu-id="fa216-604">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="fa216-605">ExecuteStoreQuery。</span><span class="sxs-lookup"><span data-stu-id="fa216-605">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="fa216-606">SqlQuery。</span><span class="sxs-lookup"><span data-stu-id="fa216-606">SqlQuery.</span></span>
-   <span data-ttu-id="fa216-607">CompiledQuery。</span><span class="sxs-lookup"><span data-stu-id="fa216-607">CompiledQuery.</span></span>

### <a name="61-linq-to-entities-queries"></a><span data-ttu-id="fa216-608">6.1 LINQ to Entities 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-608">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="fa216-609">**Pros**</span><span class="sxs-lookup"><span data-stu-id="fa216-609">**Pros**</span></span>

-   <span data-ttu-id="fa216-610">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-610">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="fa216-611">完全实例化的对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-611">Fully materialized objects.</span></span>
-   <span data-ttu-id="fa216-612">最简单的方法编写使用内置于编程语言的语法。</span><span class="sxs-lookup"><span data-stu-id="fa216-612">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="fa216-613">良好的性能。</span><span class="sxs-lookup"><span data-stu-id="fa216-613">Good performance.</span></span>

<span data-ttu-id="fa216-614">**缺点**</span><span class="sxs-lookup"><span data-stu-id="fa216-614">**Cons**</span></span>

-   <span data-ttu-id="fa216-615">某些技术限制，如：</span><span class="sxs-lookup"><span data-stu-id="fa216-615">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="fa216-616">OUTER JOIN 查询使用 DefaultIfEmpty 模式会导致更复杂查询的速度比在实体 SQL 的简单 OUTER JOIN 语句。</span><span class="sxs-lookup"><span data-stu-id="fa216-616">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="fa216-617">仍不能使用 LIKE 与常规模式匹配。</span><span class="sxs-lookup"><span data-stu-id="fa216-617">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-no-tracking-linq-to-entities-queries"></a><span data-ttu-id="fa216-618">6.2 无跟踪 LINQ to Entities 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-618">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="fa216-619">当上下文派生 ObjectContext:</span><span class="sxs-lookup"><span data-stu-id="fa216-619">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="fa216-620">当上下文派生 DbContext:</span><span class="sxs-lookup"><span data-stu-id="fa216-620">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="fa216-621">**Pros**</span><span class="sxs-lookup"><span data-stu-id="fa216-621">**Pros**</span></span>

-   <span data-ttu-id="fa216-622">通过定期的 LINQ 查询的改进了的性能。</span><span class="sxs-lookup"><span data-stu-id="fa216-622">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="fa216-623">完全实例化的对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-623">Fully materialized objects.</span></span>
-   <span data-ttu-id="fa216-624">最简单的方法编写使用内置于编程语言的语法。</span><span class="sxs-lookup"><span data-stu-id="fa216-624">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="fa216-625">**缺点**</span><span class="sxs-lookup"><span data-stu-id="fa216-625">**Cons**</span></span>

-   <span data-ttu-id="fa216-626">不适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-626">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="fa216-627">某些技术限制，如：</span><span class="sxs-lookup"><span data-stu-id="fa216-627">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="fa216-628">OUTER JOIN 查询使用 DefaultIfEmpty 模式会导致更复杂查询的速度比在实体 SQL 的简单 OUTER JOIN 语句。</span><span class="sxs-lookup"><span data-stu-id="fa216-628">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="fa216-629">仍不能使用 LIKE 与常规模式匹配。</span><span class="sxs-lookup"><span data-stu-id="fa216-629">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="fa216-630">请注意，即使未指定 NoTracking 不会跟踪项目标量属性的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-630">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="fa216-631">例如：</span><span class="sxs-lookup"><span data-stu-id="fa216-631">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="fa216-632">此特定查询未显式指定正在 NoTracking，但由于它不具体化不跟踪具有已知的对象状态管理器然后实例化的结果的类型。</span><span class="sxs-lookup"><span data-stu-id="fa216-632">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-entity-sql-over-an-objectquery"></a><span data-ttu-id="fa216-633">6.3 实体 ObjectQuery 上的 SQL</span><span class="sxs-lookup"><span data-stu-id="fa216-633">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="fa216-634">**Pros**</span><span class="sxs-lookup"><span data-stu-id="fa216-634">**Pros**</span></span>

-   <span data-ttu-id="fa216-635">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-635">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="fa216-636">完全实例化的对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-636">Fully materialized objects.</span></span>
-   <span data-ttu-id="fa216-637">支持查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="fa216-637">Supports query plan caching.</span></span>

<span data-ttu-id="fa216-638">**缺点**</span><span class="sxs-lookup"><span data-stu-id="fa216-638">**Cons**</span></span>

-   <span data-ttu-id="fa216-639">涉及到文本的查询字符串，它们是更容易出现用户错误比内置于语言的查询构造。</span><span class="sxs-lookup"><span data-stu-id="fa216-639">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-entity-sql-over-an-entity-command"></a><span data-ttu-id="fa216-640">6.4 实体的实体命令上的 SQL</span><span class="sxs-lookup"><span data-stu-id="fa216-640">6.4       Entity SQL over an Entity Command</span></span>

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

<span data-ttu-id="fa216-641">**Pros**</span><span class="sxs-lookup"><span data-stu-id="fa216-641">**Pros**</span></span>

-   <span data-ttu-id="fa216-642">支持查询计划缓存在.NET 4.0 （计划缓存的.NET 4.5 中的所有其他查询类型支持）。</span><span class="sxs-lookup"><span data-stu-id="fa216-642">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="fa216-643">**缺点**</span><span class="sxs-lookup"><span data-stu-id="fa216-643">**Cons**</span></span>

-   <span data-ttu-id="fa216-644">涉及到文本的查询字符串，它们是更容易出现用户错误比内置于语言的查询构造。</span><span class="sxs-lookup"><span data-stu-id="fa216-644">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="fa216-645">不适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-645">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="fa216-646">结果自动未具体化，并且必须从数据读取器读取。</span><span class="sxs-lookup"><span data-stu-id="fa216-646">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-sqlquery-and-executestorequery"></a><span data-ttu-id="fa216-647">6.5 SqlQuery 和 ExecuteStoreQuery</span><span class="sxs-lookup"><span data-stu-id="fa216-647">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="fa216-648">在数据库上的 SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="fa216-648">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="fa216-649">DbSet 的 SqlQuery:</span><span class="sxs-lookup"><span data-stu-id="fa216-649">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="fa216-650">ExecyteStoreQuery:</span><span class="sxs-lookup"><span data-stu-id="fa216-650">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="fa216-651">**Pros**</span><span class="sxs-lookup"><span data-stu-id="fa216-651">**Pros**</span></span>

-   <span data-ttu-id="fa216-652">通常最快的性能由于计划编译器，则跳过。</span><span class="sxs-lookup"><span data-stu-id="fa216-652">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="fa216-653">完全实例化的对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-653">Fully materialized objects.</span></span>
-   <span data-ttu-id="fa216-654">适用于 CUD 操作中使用的 DbSet。</span><span class="sxs-lookup"><span data-stu-id="fa216-654">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="fa216-655">**缺点**</span><span class="sxs-lookup"><span data-stu-id="fa216-655">**Cons**</span></span>

-   <span data-ttu-id="fa216-656">查询是文本且容易出错。</span><span class="sxs-lookup"><span data-stu-id="fa216-656">Query is textual and error prone.</span></span>
-   <span data-ttu-id="fa216-657">通过使用存储区语义而不概念语义情况下，查询绑定到特定的后端。</span><span class="sxs-lookup"><span data-stu-id="fa216-657">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="fa216-658">当存在继承时，精心设计的查询所需的请求的类型的映射条件。</span><span class="sxs-lookup"><span data-stu-id="fa216-658">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-compiledquery"></a><span data-ttu-id="fa216-659">6.6 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="fa216-659">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="fa216-660">**Pros**</span><span class="sxs-lookup"><span data-stu-id="fa216-660">**Pros**</span></span>

-   <span data-ttu-id="fa216-661">通过定期的 LINQ 查询提供最多 7%的性能改善。</span><span class="sxs-lookup"><span data-stu-id="fa216-661">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="fa216-662">完全实例化的对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-662">Fully materialized objects.</span></span>
-   <span data-ttu-id="fa216-663">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-663">Suitable for CUD operations.</span></span>

<span data-ttu-id="fa216-664">**缺点**</span><span class="sxs-lookup"><span data-stu-id="fa216-664">**Cons**</span></span>

-   <span data-ttu-id="fa216-665">增加复杂性和编程的开销。</span><span class="sxs-lookup"><span data-stu-id="fa216-665">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="fa216-666">在撰写基于已编译查询时，性能改进会丢失。</span><span class="sxs-lookup"><span data-stu-id="fa216-666">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="fa216-667">某些 LINQ 查询不能被编写为 CompiledQuery-例如，匿名类型的投影。</span><span class="sxs-lookup"><span data-stu-id="fa216-667">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-performance-comparison-of-different-query-options"></a><span data-ttu-id="fa216-668">6.7 性能比较的不同的查询选项</span><span class="sxs-lookup"><span data-stu-id="fa216-668">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="fa216-669">已不定时上下文创建的简单 microbenchmarks 已放置到测试。</span><span class="sxs-lookup"><span data-stu-id="fa216-669">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="fa216-670">查询的非缓存在受控环境中的实体集 5000 倍，我们测量。</span><span class="sxs-lookup"><span data-stu-id="fa216-670">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="fa216-671">这些数字是要执行但出现警告： 它们不反映实际数字生成由应用程序，而它们是非常准确多少不同的查询选项进行比较时没有性能差异的度量值同类对象，不包括创建新的上下文的成本。</span><span class="sxs-lookup"><span data-stu-id="fa216-671">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="fa216-672">EF</span><span class="sxs-lookup"><span data-stu-id="fa216-672">EF</span></span>  | <span data-ttu-id="fa216-673">测试</span><span class="sxs-lookup"><span data-stu-id="fa216-673">Test</span></span>                                 | <span data-ttu-id="fa216-674">时间 （毫秒）</span><span class="sxs-lookup"><span data-stu-id="fa216-674">Time (ms)</span></span> | <span data-ttu-id="fa216-675">内存</span><span class="sxs-lookup"><span data-stu-id="fa216-675">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="fa216-676">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-676">EF5</span></span> | <span data-ttu-id="fa216-677">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="fa216-677">ObjectContext ESQL</span></span>                   | <span data-ttu-id="fa216-678">2414</span><span class="sxs-lookup"><span data-stu-id="fa216-678">2414</span></span>      | <span data-ttu-id="fa216-679">38801408</span><span class="sxs-lookup"><span data-stu-id="fa216-679">38801408</span></span> |
| <span data-ttu-id="fa216-680">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-680">EF5</span></span> | <span data-ttu-id="fa216-681">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-681">ObjectContext Linq Query</span></span>             | <span data-ttu-id="fa216-682">2692</span><span class="sxs-lookup"><span data-stu-id="fa216-682">2692</span></span>      | <span data-ttu-id="fa216-683">38277120</span><span class="sxs-lookup"><span data-stu-id="fa216-683">38277120</span></span> |
| <span data-ttu-id="fa216-684">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-684">EF5</span></span> | <span data-ttu-id="fa216-685">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-685">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="fa216-686">2818</span><span class="sxs-lookup"><span data-stu-id="fa216-686">2818</span></span>      | <span data-ttu-id="fa216-687">41840640</span><span class="sxs-lookup"><span data-stu-id="fa216-687">41840640</span></span> |
| <span data-ttu-id="fa216-688">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-688">EF5</span></span> | <span data-ttu-id="fa216-689">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-689">DbContext Linq Query</span></span>                 | <span data-ttu-id="fa216-690">2930</span><span class="sxs-lookup"><span data-stu-id="fa216-690">2930</span></span>      | <span data-ttu-id="fa216-691">41771008</span><span class="sxs-lookup"><span data-stu-id="fa216-691">41771008</span></span> |
| <span data-ttu-id="fa216-692">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-692">EF5</span></span> | <span data-ttu-id="fa216-693">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-693">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="fa216-694">3013</span><span class="sxs-lookup"><span data-stu-id="fa216-694">3013</span></span>      | <span data-ttu-id="fa216-695">38412288</span><span class="sxs-lookup"><span data-stu-id="fa216-695">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="fa216-696">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-696">EF6</span></span> | <span data-ttu-id="fa216-697">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="fa216-697">ObjectContext ESQL</span></span>                   | <span data-ttu-id="fa216-698">2059</span><span class="sxs-lookup"><span data-stu-id="fa216-698">2059</span></span>      | <span data-ttu-id="fa216-699">46039040</span><span class="sxs-lookup"><span data-stu-id="fa216-699">46039040</span></span> |
| <span data-ttu-id="fa216-700">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-700">EF6</span></span> | <span data-ttu-id="fa216-701">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-701">ObjectContext Linq Query</span></span>             | <span data-ttu-id="fa216-702">3074</span><span class="sxs-lookup"><span data-stu-id="fa216-702">3074</span></span>      | <span data-ttu-id="fa216-703">45248512</span><span class="sxs-lookup"><span data-stu-id="fa216-703">45248512</span></span> |
| <span data-ttu-id="fa216-704">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-704">EF6</span></span> | <span data-ttu-id="fa216-705">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-705">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="fa216-706">3125</span><span class="sxs-lookup"><span data-stu-id="fa216-706">3125</span></span>      | <span data-ttu-id="fa216-707">47575040</span><span class="sxs-lookup"><span data-stu-id="fa216-707">47575040</span></span> |
| <span data-ttu-id="fa216-708">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-708">EF6</span></span> | <span data-ttu-id="fa216-709">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-709">DbContext Linq Query</span></span>                 | <span data-ttu-id="fa216-710">3420</span><span class="sxs-lookup"><span data-stu-id="fa216-710">3420</span></span>      | <span data-ttu-id="fa216-711">47652864</span><span class="sxs-lookup"><span data-stu-id="fa216-711">47652864</span></span> |
| <span data-ttu-id="fa216-712">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-712">EF6</span></span> | <span data-ttu-id="fa216-713">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-713">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="fa216-714">3593</span><span class="sxs-lookup"><span data-stu-id="fa216-714">3593</span></span>      | <span data-ttu-id="fa216-715">45260800</span><span class="sxs-lookup"><span data-stu-id="fa216-715">45260800</span></span> |

![EF5 微基准，5000 热迭代](~/ef6/media/ef5micro5000warm.png)

![EF6 微基准，5000 热迭代](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="fa216-718">Microbenchmarks 都对代码中的微小变化非常敏感。</span><span class="sxs-lookup"><span data-stu-id="fa216-718">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="fa216-719">在这种情况下，Entity Framework 5 的成本和 Entity Framework 6 之间的差异是由于的加法[拦截](~/ef6/fundamentals/logging-and-interception.md)并[事务改进](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="fa216-719">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="fa216-720">这些 microbenchmarks 数字，但是，是放大的构想嵌入的实体框架的作用非常小片段。</span><span class="sxs-lookup"><span data-stu-id="fa216-720">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="fa216-721">从 Entity Framework 5 升级到 Entity Framework 6 时，实际方案的热查询看不到性能回归。</span><span class="sxs-lookup"><span data-stu-id="fa216-721">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="fa216-722">若要比较不同的查询选项的实际性能，我们创建了 5 个单独的测试变体，其中我们使用不同的查询选项来选择所有产品类别名称是"饮料"。</span><span class="sxs-lookup"><span data-stu-id="fa216-722">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="fa216-723">每次迭代包括创建上下文的成本和将返回所有实体具体化的成本。</span><span class="sxs-lookup"><span data-stu-id="fa216-723">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="fa216-724">10 次迭代执行已超时的 1000年次迭代的总和之前运行 untimed。</span><span class="sxs-lookup"><span data-stu-id="fa216-724">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="fa216-725">显示的结果是从每个测试的 5 个运行的中间值运行。</span><span class="sxs-lookup"><span data-stu-id="fa216-725">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="fa216-726">有关详细信息，请参阅附录 B，其中包括测试的代码。</span><span class="sxs-lookup"><span data-stu-id="fa216-726">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="fa216-727">EF</span><span class="sxs-lookup"><span data-stu-id="fa216-727">EF</span></span>  | <span data-ttu-id="fa216-728">测试</span><span class="sxs-lookup"><span data-stu-id="fa216-728">Test</span></span>                                        | <span data-ttu-id="fa216-729">时间 （毫秒）</span><span class="sxs-lookup"><span data-stu-id="fa216-729">Time (ms)</span></span> | <span data-ttu-id="fa216-730">内存</span><span class="sxs-lookup"><span data-stu-id="fa216-730">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="fa216-731">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-731">EF5</span></span> | <span data-ttu-id="fa216-732">ObjectContext 实体命令</span><span class="sxs-lookup"><span data-stu-id="fa216-732">ObjectContext Entity Command</span></span>                | <span data-ttu-id="fa216-733">621</span><span class="sxs-lookup"><span data-stu-id="fa216-733">621</span></span>       | <span data-ttu-id="fa216-734">39350272</span><span class="sxs-lookup"><span data-stu-id="fa216-734">39350272</span></span> |
| <span data-ttu-id="fa216-735">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-735">EF5</span></span> | <span data-ttu-id="fa216-736">DbContext 上数据库的 Sql 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-736">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="fa216-737">825</span><span class="sxs-lookup"><span data-stu-id="fa216-737">825</span></span>       | <span data-ttu-id="fa216-738">37519360</span><span class="sxs-lookup"><span data-stu-id="fa216-738">37519360</span></span> |
| <span data-ttu-id="fa216-739">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-739">EF5</span></span> | <span data-ttu-id="fa216-740">ObjectContext 存储查询</span><span class="sxs-lookup"><span data-stu-id="fa216-740">ObjectContext Store Query</span></span>                   | <span data-ttu-id="fa216-741">878</span><span class="sxs-lookup"><span data-stu-id="fa216-741">878</span></span>       | <span data-ttu-id="fa216-742">39460864</span><span class="sxs-lookup"><span data-stu-id="fa216-742">39460864</span></span> |
| <span data-ttu-id="fa216-743">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-743">EF5</span></span> | <span data-ttu-id="fa216-744">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-744">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="fa216-745">969</span><span class="sxs-lookup"><span data-stu-id="fa216-745">969</span></span>       | <span data-ttu-id="fa216-746">38293504</span><span class="sxs-lookup"><span data-stu-id="fa216-746">38293504</span></span> |
| <span data-ttu-id="fa216-747">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-747">EF5</span></span> | <span data-ttu-id="fa216-748">使用对象查询的 ObjectContext 实体 Sql</span><span class="sxs-lookup"><span data-stu-id="fa216-748">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="fa216-749">1089</span><span class="sxs-lookup"><span data-stu-id="fa216-749">1089</span></span>      | <span data-ttu-id="fa216-750">38981632</span><span class="sxs-lookup"><span data-stu-id="fa216-750">38981632</span></span> |
| <span data-ttu-id="fa216-751">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-751">EF5</span></span> | <span data-ttu-id="fa216-752">ObjectContext 已编译的查询</span><span class="sxs-lookup"><span data-stu-id="fa216-752">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="fa216-753">1099</span><span class="sxs-lookup"><span data-stu-id="fa216-753">1099</span></span>      | <span data-ttu-id="fa216-754">38682624</span><span class="sxs-lookup"><span data-stu-id="fa216-754">38682624</span></span> |
| <span data-ttu-id="fa216-755">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-755">EF5</span></span> | <span data-ttu-id="fa216-756">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-756">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="fa216-757">1152</span><span class="sxs-lookup"><span data-stu-id="fa216-757">1152</span></span>      | <span data-ttu-id="fa216-758">38178816</span><span class="sxs-lookup"><span data-stu-id="fa216-758">38178816</span></span> |
| <span data-ttu-id="fa216-759">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-759">EF5</span></span> | <span data-ttu-id="fa216-760">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-760">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="fa216-761">1208</span><span class="sxs-lookup"><span data-stu-id="fa216-761">1208</span></span>      | <span data-ttu-id="fa216-762">41803776</span><span class="sxs-lookup"><span data-stu-id="fa216-762">41803776</span></span> |
| <span data-ttu-id="fa216-763">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-763">EF5</span></span> | <span data-ttu-id="fa216-764">DbSet 的 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-764">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="fa216-765">1414</span><span class="sxs-lookup"><span data-stu-id="fa216-765">1414</span></span>      | <span data-ttu-id="fa216-766">37982208</span><span class="sxs-lookup"><span data-stu-id="fa216-766">37982208</span></span> |
| <span data-ttu-id="fa216-767">EF5</span><span class="sxs-lookup"><span data-stu-id="fa216-767">EF5</span></span> | <span data-ttu-id="fa216-768">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-768">DbContext Linq Query</span></span>                        | <span data-ttu-id="fa216-769">1574</span><span class="sxs-lookup"><span data-stu-id="fa216-769">1574</span></span>      | <span data-ttu-id="fa216-770">41738240</span><span class="sxs-lookup"><span data-stu-id="fa216-770">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="fa216-771">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-771">EF6</span></span> | <span data-ttu-id="fa216-772">ObjectContext 实体命令</span><span class="sxs-lookup"><span data-stu-id="fa216-772">ObjectContext Entity Command</span></span>                | <span data-ttu-id="fa216-773">480</span><span class="sxs-lookup"><span data-stu-id="fa216-773">480</span></span>       | <span data-ttu-id="fa216-774">47247360</span><span class="sxs-lookup"><span data-stu-id="fa216-774">47247360</span></span> |
| <span data-ttu-id="fa216-775">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-775">EF6</span></span> | <span data-ttu-id="fa216-776">ObjectContext 存储查询</span><span class="sxs-lookup"><span data-stu-id="fa216-776">ObjectContext Store Query</span></span>                   | <span data-ttu-id="fa216-777">493</span><span class="sxs-lookup"><span data-stu-id="fa216-777">493</span></span>       | <span data-ttu-id="fa216-778">46739456</span><span class="sxs-lookup"><span data-stu-id="fa216-778">46739456</span></span> |
| <span data-ttu-id="fa216-779">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-779">EF6</span></span> | <span data-ttu-id="fa216-780">DbContext 上数据库的 Sql 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-780">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="fa216-781">614</span><span class="sxs-lookup"><span data-stu-id="fa216-781">614</span></span>       | <span data-ttu-id="fa216-782">41607168</span><span class="sxs-lookup"><span data-stu-id="fa216-782">41607168</span></span> |
| <span data-ttu-id="fa216-783">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-783">EF6</span></span> | <span data-ttu-id="fa216-784">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-784">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="fa216-785">684</span><span class="sxs-lookup"><span data-stu-id="fa216-785">684</span></span>       | <span data-ttu-id="fa216-786">46333952</span><span class="sxs-lookup"><span data-stu-id="fa216-786">46333952</span></span> |
| <span data-ttu-id="fa216-787">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-787">EF6</span></span> | <span data-ttu-id="fa216-788">使用对象查询的 ObjectContext 实体 Sql</span><span class="sxs-lookup"><span data-stu-id="fa216-788">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="fa216-789">767</span><span class="sxs-lookup"><span data-stu-id="fa216-789">767</span></span>       | <span data-ttu-id="fa216-790">48865280</span><span class="sxs-lookup"><span data-stu-id="fa216-790">48865280</span></span> |
| <span data-ttu-id="fa216-791">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-791">EF6</span></span> | <span data-ttu-id="fa216-792">ObjectContext 已编译的查询</span><span class="sxs-lookup"><span data-stu-id="fa216-792">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="fa216-793">788</span><span class="sxs-lookup"><span data-stu-id="fa216-793">788</span></span>       | <span data-ttu-id="fa216-794">48467968</span><span class="sxs-lookup"><span data-stu-id="fa216-794">48467968</span></span> |
| <span data-ttu-id="fa216-795">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-795">EF6</span></span> | <span data-ttu-id="fa216-796">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="fa216-796">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="fa216-797">878</span><span class="sxs-lookup"><span data-stu-id="fa216-797">878</span></span>       | <span data-ttu-id="fa216-798">47554560</span><span class="sxs-lookup"><span data-stu-id="fa216-798">47554560</span></span> |
| <span data-ttu-id="fa216-799">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-799">EF6</span></span> | <span data-ttu-id="fa216-800">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-800">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="fa216-801">953</span><span class="sxs-lookup"><span data-stu-id="fa216-801">953</span></span>       | <span data-ttu-id="fa216-802">47632384</span><span class="sxs-lookup"><span data-stu-id="fa216-802">47632384</span></span> |
| <span data-ttu-id="fa216-803">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-803">EF6</span></span> | <span data-ttu-id="fa216-804">DbSet 的 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-804">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="fa216-805">1023</span><span class="sxs-lookup"><span data-stu-id="fa216-805">1023</span></span>      | <span data-ttu-id="fa216-806">41992192</span><span class="sxs-lookup"><span data-stu-id="fa216-806">41992192</span></span> |
| <span data-ttu-id="fa216-807">EF6</span><span class="sxs-lookup"><span data-stu-id="fa216-807">EF6</span></span> | <span data-ttu-id="fa216-808">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-808">DbContext Linq Query</span></span>                        | <span data-ttu-id="fa216-809">1290</span><span class="sxs-lookup"><span data-stu-id="fa216-809">1290</span></span>      | <span data-ttu-id="fa216-810">47529984</span><span class="sxs-lookup"><span data-stu-id="fa216-810">47529984</span></span> |


![EF5 热查询 1000年次迭代](~/ef6/media/ef5warmquery1000.png)

![EF6 热查询 1000年次迭代](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="fa216-813">出于完整性的考虑，我们将包括 EntityCommand 执行 Entity SQL 查询的一种变体。</span><span class="sxs-lookup"><span data-stu-id="fa216-813">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="fa216-814">但是，对于这种查询未具体化结果，因为比较并不一定是苹果苹果。</span><span class="sxs-lookup"><span data-stu-id="fa216-814">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="fa216-815">测试包括具体化尝试使较公平地进行比较的近似值。</span><span class="sxs-lookup"><span data-stu-id="fa216-815">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="fa216-816">在端到端此种情况下，Entity Framework 6 优于 Entity Framework 5 上的堆栈，包括得更亮 DbContext 初始化和更快的 MetadataCollection 多个部分由性能改进引起&lt;T&gt;查找。</span><span class="sxs-lookup"><span data-stu-id="fa216-816">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="fa216-817">7 设计时间性能注意事项</span><span class="sxs-lookup"><span data-stu-id="fa216-817">7 Design time performance considerations</span></span>

### <a name="71-inheritance-strategies"></a><span data-ttu-id="fa216-818">7.1 继承策略</span><span class="sxs-lookup"><span data-stu-id="fa216-818">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="fa216-819">使用实体框架时的另一个性能注意事项是你使用的继承策略。</span><span class="sxs-lookup"><span data-stu-id="fa216-819">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="fa216-820">实体框架支持 3 种基本类型的继承和它们二者的组合：</span><span class="sxs-lookup"><span data-stu-id="fa216-820">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="fa216-821">每个层次结构 (TPH) – 其中每个继承设置映射到表中以指示要表示的行中的层次结构中的特定类型的鉴别器列的表。</span><span class="sxs-lookup"><span data-stu-id="fa216-821">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="fa216-822">表每个类型 (TPT) – 其中每个类型具有其自己的表中该数据库。子表只定义父表不包含的列。</span><span class="sxs-lookup"><span data-stu-id="fa216-822">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="fa216-823">表每个类 (TPC) – 其中每个类型具有自己的完整表中该数据库。子表定义所有字段，包括那些在父类型中定义。</span><span class="sxs-lookup"><span data-stu-id="fa216-823">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="fa216-824">如果您的模型使用 TPT 继承，生成的查询将比使用其他继承策略，这可能导致存储区上的执行时间较长上生成更复杂。</span><span class="sxs-lookup"><span data-stu-id="fa216-824">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span><span data-ttu-id="fa216-825">  它通常需要花费更长，以通过 TPT 模型生成查询并具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-825">  It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="fa216-826">请参阅"性能注意事项时在实体框架中使用 （每种类型的表） TPT 继承"MSDN 博客文章： \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-826">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>.</span></span>

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="fa216-827">7.1.1 Model First 或 Code First 应用程序中避免 TPT</span><span class="sxs-lookup"><span data-stu-id="fa216-827">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="fa216-828">通过具有 TPT 架构的现有数据库创建模型，你没有在很多选项。</span><span class="sxs-lookup"><span data-stu-id="fa216-828">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="fa216-829">但在创建时使用 Model First 或 Code First 的应用程序，应避免性能问题的 TPT 继承。</span><span class="sxs-lookup"><span data-stu-id="fa216-829">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="fa216-830">当在实体设计器向导中使用模型优先时，会获得 TPT 任何继承模型中。</span><span class="sxs-lookup"><span data-stu-id="fa216-830">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="fa216-831">如果你想要切换到使用模型优先的 TPH 继承策略，可以使用"Entity Designer Database Generation Power Pack"可从 Visual Studio 库 ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)。</span><span class="sxs-lookup"><span data-stu-id="fa216-831">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="fa216-832">在使用 Code First 配置继承模型的映射，EF 将使用 TPH 默认情况下，因此继承层次结构中的所有实体将都映射到同一个表。</span><span class="sxs-lookup"><span data-stu-id="fa216-832">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="fa216-833">请参阅 MSDN 杂志 》 中的"代码第一个中实体 Framework4.1"项目的"映射的 Fluent API"一节 ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) 的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-833">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="fa216-834">7.2 升级从 EF4 以提高模型生成时间</span><span class="sxs-lookup"><span data-stu-id="fa216-834">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="fa216-835">安装 Visual Studio 2010 SP1 时，用于生成存储层 (SSDL) 的算法的特定于 SQL Server 的改进是模型的在 Entity Framework 5 和 6，并作为对 Entity Framework 4 的更新可用。</span><span class="sxs-lookup"><span data-stu-id="fa216-835">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="fa216-836">以下测试结果时生成非常大的模型，在这种情况下 Navision 模型演示改进。</span><span class="sxs-lookup"><span data-stu-id="fa216-836">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="fa216-837">更多详细信息，请参阅附录 C。</span><span class="sxs-lookup"><span data-stu-id="fa216-837">See Appendix C for more details about it.</span></span>

<span data-ttu-id="fa216-838">该模型包含 1005年实体集和 4227 关联集。</span><span class="sxs-lookup"><span data-stu-id="fa216-838">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="fa216-839">配置</span><span class="sxs-lookup"><span data-stu-id="fa216-839">Configuration</span></span>                              | <span data-ttu-id="fa216-840">使用时间的细目</span><span class="sxs-lookup"><span data-stu-id="fa216-840">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="fa216-841">Visual Studio 2010 中，实体框架 4</span><span class="sxs-lookup"><span data-stu-id="fa216-841">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="fa216-842">SSDL 生成：2 小时 27 分钟</span><span class="sxs-lookup"><span data-stu-id="fa216-842">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="fa216-843">生成的映射：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-843">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-844">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-844">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-845">ObjectLayer 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-845">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-846">视图生成：2 小时 14 分钟</span><span class="sxs-lookup"><span data-stu-id="fa216-846">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="fa216-847">Visual Studio 2010 SP1 中，实体框架 4</span><span class="sxs-lookup"><span data-stu-id="fa216-847">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="fa216-848">SSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-848">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-849">生成的映射：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-850">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-851">ObjectLayer 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-852">视图生成：1 小时 53 分钟</span><span class="sxs-lookup"><span data-stu-id="fa216-852">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="fa216-853">Visual Studio 2013 中，实体框架 5</span><span class="sxs-lookup"><span data-stu-id="fa216-853">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="fa216-854">SSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-855">生成的映射：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-856">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-857">ObjectLayer 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-858">视图生成：65 分钟</span><span class="sxs-lookup"><span data-stu-id="fa216-858">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="fa216-859">Visual Studio 2013 中，实体框架 6</span><span class="sxs-lookup"><span data-stu-id="fa216-859">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="fa216-860">SSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-861">生成的映射：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-862">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-863">ObjectLayer 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="fa216-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="fa216-864">视图生成：28 秒。</span><span class="sxs-lookup"><span data-stu-id="fa216-864">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="fa216-865">值得注意是，生成 SSDL 时, 负载几乎完全所用的 SQL 服务器上，等待客户端开发计算机时，结果才会返回从服务器空闲。</span><span class="sxs-lookup"><span data-stu-id="fa216-865">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="fa216-866">Dba 应特别感谢此项改进。</span><span class="sxs-lookup"><span data-stu-id="fa216-866">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="fa216-867">它也是值得注意成本模型生成的实质上是整个现在采用视图生成中的位置。</span><span class="sxs-lookup"><span data-stu-id="fa216-867">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="fa216-868">7.3 首先拆分与数据库的大型模型和模型优先</span><span class="sxs-lookup"><span data-stu-id="fa216-868">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="fa216-869">随着模型大小的增加，设计器图面变得混乱且难以使用。</span><span class="sxs-lookup"><span data-stu-id="fa216-869">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="fa216-870">我们通常认为具有 300 多个实体太大而无法有效地使用设计器的模型。</span><span class="sxs-lookup"><span data-stu-id="fa216-870">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="fa216-871">下面的博客文章介绍了有关拆分大型模型的多个选项： \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-871">The following blog post describes several options for splitting large models: \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>.</span></span>

<span data-ttu-id="fa216-872">开机自检编写实体框架的第一个版本，但步骤仍然适用。</span><span class="sxs-lookup"><span data-stu-id="fa216-872">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="fa216-873">7.4 实体数据源控件的性能注意事项</span><span class="sxs-lookup"><span data-stu-id="fa216-873">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="fa216-874">我们已经看到在多线程的性能和压力测试的情况下，使用 EntityDataSource 控件的 web 应用程序的性能会降低显著。</span><span class="sxs-lookup"><span data-stu-id="fa216-874">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="fa216-875">根本原因是 EntityDataSource Web 应用程序，以发现要用作实体的类型引用的程序集上反复调用 MetadataWorkspace.LoadFromAssembly。</span><span class="sxs-lookup"><span data-stu-id="fa216-875">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="fa216-876">解决方案是设置的 EntityDataSource ContextTypeName 为派生的 ObjectContext 类的类型名称。</span><span class="sxs-lookup"><span data-stu-id="fa216-876">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="fa216-877">这将关闭扫描的实体类型的所有引用程序集的机制。</span><span class="sxs-lookup"><span data-stu-id="fa216-877">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="fa216-878">设置 ContextTypeName 字段还可以防止其中 EntityDataSource.NET 4.0 中的时，会引发出现 ReflectionTypeLoadException 它不能从通过反射程序集加载的类型的功能问题。</span><span class="sxs-lookup"><span data-stu-id="fa216-878">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="fa216-879">在.NET 4.5 中已修复此问题。</span><span class="sxs-lookup"><span data-stu-id="fa216-879">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="fa216-880">7.5 POCO 实体和更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="fa216-880">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="fa216-881">实体框架，可自定义数据类与数据模型一起使用而无需对数据类本身进行任何修改。</span><span class="sxs-lookup"><span data-stu-id="fa216-881">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="fa216-882">这意味着可以将“纯旧式”CLR 对象 (POCO)（例如，现有的域对象）与数据模型一起使用。</span><span class="sxs-lookup"><span data-stu-id="fa216-882">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="fa216-883">这些 POCO 数据类 （也称为持久性未知对象），其映射到实体数据模型中定义的支持的大多数相同的查询、 插入、 更新，和删除实体类型由 Entity Data Model 工具生成的行为。</span><span class="sxs-lookup"><span data-stu-id="fa216-883">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="fa216-884">实体框架还可以创建派生自 POCO 类型，在你想要启用功能，例如延迟加载和自动更改跟踪 POCO 实体时使用的代理类。</span><span class="sxs-lookup"><span data-stu-id="fa216-884">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="fa216-885">POCO 类必须满足某些要求允许实体框架才能使用代理，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-885">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="fa216-886">机会跟踪代理的任何实体的属性使实体框架知道你的实体的实际状态的时间已发生更改时，其值每次将通知对象状态管理器。</span><span class="sxs-lookup"><span data-stu-id="fa216-886">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="fa216-887">这是通过将通知事件添加到您的属性的 setter 方法的正文，并让对象状态管理器处理此类事件。</span><span class="sxs-lookup"><span data-stu-id="fa216-887">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="fa216-888">请注意，创建代理实体将通常要比创建非代理 POCO 实体由于添加了一组事件 Entity Framework 创建的昂贵得多。</span><span class="sxs-lookup"><span data-stu-id="fa216-888">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="fa216-889">如果 POCO 实体不具有更改跟踪代理，通过比较针对以前保存的状态的副本实体的内容来发现更改。</span><span class="sxs-lookup"><span data-stu-id="fa216-889">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="fa216-890">在您的上下文中包含多个实体或实体具有极大量的属性，即使它们没有任何更改上次比较以来此深入比较将成为一个漫长的过程。</span><span class="sxs-lookup"><span data-stu-id="fa216-890">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="fa216-891">总之： 需支付费用的性能下降时创建的更改跟踪代理，但是更改跟踪可帮助您加快完成此更改检测过程，当你的实体具有许多属性或您的模型中有多个实体时。</span><span class="sxs-lookup"><span data-stu-id="fa216-891">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="fa216-892">对于较少的属性，其中的实体不会增长过多的实体，具有更改跟踪代理不可能的很大的收益。</span><span class="sxs-lookup"><span data-stu-id="fa216-892">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="fa216-893">8 加载相关实体</span><span class="sxs-lookup"><span data-stu-id="fa216-893">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="fa216-894">8.1 延迟加载 vs。预先加载</span><span class="sxs-lookup"><span data-stu-id="fa216-894">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="fa216-895">实体框架提供了几种方式将加载到目标实体相关的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-895">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="fa216-896">例如，当查询产品时，有多种相关的订单将被加载到对象状态管理器。</span><span class="sxs-lookup"><span data-stu-id="fa216-896">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="fa216-897">从性能角度看，最大问题来加载相关的实体时，请考虑将能是否使用延迟加载或预先加载。</span><span class="sxs-lookup"><span data-stu-id="fa216-897">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="fa216-898">在使用预先加载时，你的目标实体集以及加载相关的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-898">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="fa216-899">在查询中使用一个 Include 语句以指示哪些相关的实体，你想要导入。</span><span class="sxs-lookup"><span data-stu-id="fa216-899">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="fa216-900">在使用延迟加载，您的初始查询只会使目标实体集中。</span><span class="sxs-lookup"><span data-stu-id="fa216-900">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="fa216-901">但是，只要访问导航属性，对要加载相关的实体的存储区发出另一个查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-901">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="fa216-902">加载实体后，任何进一步查询的实体将加载它直接从对象状态管理器中，无论您使用延迟加载或预先加载。</span><span class="sxs-lookup"><span data-stu-id="fa216-902">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="fa216-903">8.2 如何延迟加载和预先加载之间进行选择</span><span class="sxs-lookup"><span data-stu-id="fa216-903">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="fa216-904">重要的一点是了解延迟加载和预先加载之间的差异，以便您可以为应用程序做出正确选择。</span><span class="sxs-lookup"><span data-stu-id="fa216-904">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="fa216-905">这将帮助你评估对与单个请求，可能包含大型有效负载的数据库的多个请求之间的权衡。</span><span class="sxs-lookup"><span data-stu-id="fa216-905">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="fa216-906">它可能需要在其他部分中使用你的应用程序的某些部分中预先加载和延迟加载。</span><span class="sxs-lookup"><span data-stu-id="fa216-906">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="fa216-907">作为在后台发生的情况的示例，假设您想要查询的客户居住在英国和其订单计数。</span><span class="sxs-lookup"><span data-stu-id="fa216-907">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="fa216-908">**使用预先加载**</span><span class="sxs-lookup"><span data-stu-id="fa216-908">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="fa216-909">**使用延迟加载**</span><span class="sxs-lookup"><span data-stu-id="fa216-909">**Using Lazy Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="fa216-910">在使用预先加载时，将发布单个查询返回所有客户的所有订单。</span><span class="sxs-lookup"><span data-stu-id="fa216-910">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="fa216-911">存储命令如下所示：</span><span class="sxs-lookup"><span data-stu-id="fa216-911">The store command looks like:</span></span>

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

<span data-ttu-id="fa216-912">在使用延迟加载，您将最初发出以下查询：</span><span class="sxs-lookup"><span data-stu-id="fa216-912">When using lazy loading, you'll issue the following query initially:</span></span>

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

<span data-ttu-id="fa216-913">和访问客户的订单导航属性每次对应用商店发出另一个查询如下所示：</span><span class="sxs-lookup"><span data-stu-id="fa216-913">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

<span data-ttu-id="fa216-914">有关详细信息，请参阅[加载相关对象](https://msdn.microsoft.com/library/bb896272.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-914">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="fa216-915">8.2.1 与预先加载备忘单延迟加载</span><span class="sxs-lookup"><span data-stu-id="fa216-915">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="fa216-916">就没有通用于选择预先加载与延迟加载。</span><span class="sxs-lookup"><span data-stu-id="fa216-916">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="fa216-917">第一次尝试了解这两种策略，这样您可以为也明智的决策; 之间的差异此外，请考虑到任何以下情况下是否适合你的代码：</span><span class="sxs-lookup"><span data-stu-id="fa216-917">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="fa216-918">方案</span><span class="sxs-lookup"><span data-stu-id="fa216-918">Scenario</span></span>                                                                    | <span data-ttu-id="fa216-919">我们建议</span><span class="sxs-lookup"><span data-stu-id="fa216-919">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="fa216-920">若要从提取实体访问多个导航属性需要吗？</span><span class="sxs-lookup"><span data-stu-id="fa216-920">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="fa216-921">**不**-这两个选项可能会执行操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-921">**No** - Both options will probably do.</span></span> <span data-ttu-id="fa216-922">但是，如果将您的查询的有效负载不是太大，则可能会遇到性能优势，使用预先加载，因为它将需要更少的网络往返行程具体化对象。</span><span class="sxs-lookup"><span data-stu-id="fa216-922">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="fa216-923">**是**-如果需要从实体来访问多个导航属性，将执行操作，通过使用多个语句在查询中包括与预先加载。</span><span class="sxs-lookup"><span data-stu-id="fa216-923">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="fa216-924">更多实体包含，越大你的查询将返回的有效负载。</span><span class="sxs-lookup"><span data-stu-id="fa216-924">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="fa216-925">一旦在查询中包括三个或多个实体，请考虑切换为延迟加载。</span><span class="sxs-lookup"><span data-stu-id="fa216-925">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="fa216-926">您是否知道将完全在运行时间需要哪些数据？</span><span class="sxs-lookup"><span data-stu-id="fa216-926">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="fa216-927">**不**的延迟加载会更好。</span><span class="sxs-lookup"><span data-stu-id="fa216-927">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="fa216-928">否则，可能最终会查询数据，则不需。</span><span class="sxs-lookup"><span data-stu-id="fa216-928">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="fa216-929">**是**-预先加载可能是最好的方法; 它将帮助更快地加载整个集。</span><span class="sxs-lookup"><span data-stu-id="fa216-929">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="fa216-930">如果您的查询需要提取非常大量的数据，并且这变得太慢，然后尝试加载改为 Lazy。</span><span class="sxs-lookup"><span data-stu-id="fa216-930">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="fa216-931">姑且不论您的数据库执行你的代码？</span><span class="sxs-lookup"><span data-stu-id="fa216-931">Is your code executing far from your database?</span></span> <span data-ttu-id="fa216-932">（增加了的网络延迟）</span><span class="sxs-lookup"><span data-stu-id="fa216-932">(increased network latency)</span></span>  | <span data-ttu-id="fa216-933">**不**-时的网络延迟不是问题，使用延迟加载可以简化你的代码。</span><span class="sxs-lookup"><span data-stu-id="fa216-933">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="fa216-934">请记住你的应用程序拓扑可能会更改，因此不需要数据库邻近理所当然的。</span><span class="sxs-lookup"><span data-stu-id="fa216-934">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="fa216-935">**是**-网络问题时，仅可以决定更好地适合您的方案。</span><span class="sxs-lookup"><span data-stu-id="fa216-935">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="fa216-936">通常将预先加载是更好，因为它需要往返次数较少。</span><span class="sxs-lookup"><span data-stu-id="fa216-936">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a><span data-ttu-id="fa216-937">8.2.2 使用多个包括性能问题</span><span class="sxs-lookup"><span data-stu-id="fa216-937">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="fa216-938">如果我们听到涉及服务器响应时间问题的性能问题，问题的原因通常是具有多个包含语句的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-938">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="fa216-939">虽然在查询中包括相关的实体很强大，务必了解在后台发生的事情。</span><span class="sxs-lookup"><span data-stu-id="fa216-939">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="fa216-940">它在其中可通过我们内部计划编译器生成的存储命令需要具有多个包含语句的查询相对较长时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-940">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="fa216-941">大多数此时间花费在尝试优化生成的查询上。</span><span class="sxs-lookup"><span data-stu-id="fa216-941">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="fa216-942">将生成的存储命令将包含 Outer Join 或 Union 的每个包含，具体取决于您的映射。</span><span class="sxs-lookup"><span data-stu-id="fa216-942">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="fa216-943">此类查询将从您的数据库中单个有效负载，将 acerbate 带宽问题，尤其是当存在很多 （例如，在使用多个级别的 Include 遍历时的有效负载中的冗余引入大已连接的关系图中的一个多方向关联）。</span><span class="sxs-lookup"><span data-stu-id="fa216-943">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="fa216-944">你可以检查您的查询，来访问基础 TSQL 查询使用 ToTraceString 和 SQL Server Management Studio，若要查看的有效负载大小中执行存储命令，从而返回过大负载的情况。</span><span class="sxs-lookup"><span data-stu-id="fa216-944">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="fa216-945">在这种情况下，您可以尝试减少直接在查询中的 Include 语句数引入所需的数据。</span><span class="sxs-lookup"><span data-stu-id="fa216-945">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="fa216-946">或者，您可能能够将查询分解为较小序列的子查询，例如：</span><span class="sxs-lookup"><span data-stu-id="fa216-946">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="fa216-947">**之前的重大查询：**</span><span class="sxs-lookup"><span data-stu-id="fa216-947">**Before breaking the query:**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

<span data-ttu-id="fa216-948">**之后的重大查询：**</span><span class="sxs-lookup"><span data-stu-id="fa216-948">**After breaking the query:**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

<span data-ttu-id="fa216-949">这将仅适用于跟踪的查询，因为我们将使用的上下文具有以自动执行标识解析和关联链接地址信息的功能。</span><span class="sxs-lookup"><span data-stu-id="fa216-949">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="fa216-950">与延迟加载后，其不利的一面将更多的较小的负载的查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-950">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="fa216-951">您还可以使用投影的个别属性的显式从每个实体中，选择所需的数据，但您将不会加载实体在这种情况下，和不支持更新。</span><span class="sxs-lookup"><span data-stu-id="fa216-951">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="fa216-952">8.2.3 解决方法，以获取延迟加载的属性</span><span class="sxs-lookup"><span data-stu-id="fa216-952">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="fa216-953">实体框架当前不支持延迟加载的标量或复杂属性。</span><span class="sxs-lookup"><span data-stu-id="fa216-953">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="fa216-954">但是，在必须包含一个大型对象，如 BLOB 的表的情况下，您可以使用表拆分将大型属性分成单独的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-954">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="fa216-955">例如，假设有一个包括 varbinary photo 列的产品表。</span><span class="sxs-lookup"><span data-stu-id="fa216-955">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="fa216-956">如果不经常需要访问此属性在查询中的，可以使用表拆分，以使在仅部分通常需要的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-956">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="fa216-957">在明确需要时，将仅加载表示产品照片的实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-957">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="fa216-958">演示如何启用表拆分的良好资源是方便 Gil Fink 的 《 表拆分中 Entity Framework 》 博客文章： \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-958">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="fa216-959">9 其他注意事项</span><span class="sxs-lookup"><span data-stu-id="fa216-959">9 Other considerations</span></span>

### <a name="91-server-garbage-collection"></a><span data-ttu-id="fa216-960">9.1 服务器垃圾回收</span><span class="sxs-lookup"><span data-stu-id="fa216-960">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="fa216-961">某些用户可能会遇到限制的并行度，垃圾回收器未正确配置时收到意外的资源争用。</span><span class="sxs-lookup"><span data-stu-id="fa216-961">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="fa216-962">每次在多线程方案中，使用 EF 或任何应用程序中的类似于服务器端的系统，请确保启用服务器垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="fa216-962">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="fa216-963">通过在应用程序配置文件中的简单设置完成此操作：</span><span class="sxs-lookup"><span data-stu-id="fa216-963">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="fa216-964">这应减少线程争用并提高吞吐量最多 30 %cpu 饱和方案中。</span><span class="sxs-lookup"><span data-stu-id="fa216-964">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="fa216-965">概括地说，您始终应测试您的应用程序的行为方式使用经典的垃圾回收 （，更好地适用于 UI 和客户端端方案） 以及服务器垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="fa216-965">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92-autodetectchanges"></a><span data-ttu-id="fa216-966">9.2 AutoDetectChanges</span><span class="sxs-lookup"><span data-stu-id="fa216-966">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="fa216-967">前面曾提到，实体框架可能会显示性能问题时对象缓存中有多个实体。</span><span class="sxs-lookup"><span data-stu-id="fa216-967">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="fa216-968">某些操作，如添加、 删除、 查找、 条目和 SaveChanges，触发调用 DetectChanges，这可能会占用大量 CPU 基于对象缓存已成为多大。</span><span class="sxs-lookup"><span data-stu-id="fa216-968">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="fa216-969">这样做的原因是对象缓存和对象状态管理器尝试将保持为同步尽可能执行到上下文，以便生成的数据保证可正确了很多情况下每个操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-969">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="fa216-970">它通常是将实体框架自动脏值检测启用你的应用程序的整个生存期内保留一个好办法。</span><span class="sxs-lookup"><span data-stu-id="fa216-970">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="fa216-971">如果你的方案受 CPU 使用率过高造成负面影响，并且在配置文件指示罪魁祸首是调用 DetectChanges，请考虑暂时关闭 AutoDetectChanges，在你的代码的敏感部分中：</span><span class="sxs-lookup"><span data-stu-id="fa216-971">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

<span data-ttu-id="fa216-972">在关闭 AutoDetectChanges 之前, 是最好的了解，这可能会导致实体框架可以在其无法跟踪的更改的实体上所发生的某些信息。</span><span class="sxs-lookup"><span data-stu-id="fa216-972">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="fa216-973">如果处理不正确，这可能导致你的应用程序上的数据不一致。</span><span class="sxs-lookup"><span data-stu-id="fa216-973">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="fa216-974">关闭 AutoDetectChanges 的详细信息，请阅读\<\ http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。</span><span class="sxs-lookup"><span data-stu-id="fa216-974">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93-context-per-request"></a><span data-ttu-id="fa216-975">9.3 每个请求上下文</span><span class="sxs-lookup"><span data-stu-id="fa216-975">9.3      Context per request</span></span>

<span data-ttu-id="fa216-976">实体框架的上下文是为了用作生存期较短实例以便提供最佳性能体验。</span><span class="sxs-lookup"><span data-stu-id="fa216-976">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="fa216-977">上下文都将是短生存期和丢弃的项，并且这种情况下已实现非常轻型的软件和 reutilize 只要有可能的元数据。</span><span class="sxs-lookup"><span data-stu-id="fa216-977">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="fa216-978">在 web 方案中是一定要记住这一点并不具有对多个单个请求的持续时间的上下文。</span><span class="sxs-lookup"><span data-stu-id="fa216-978">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="fa216-979">同样，在非 web 方案中，上下文应放弃根据您对不同级别的缓存在实体框架的理解。</span><span class="sxs-lookup"><span data-stu-id="fa216-979">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="fa216-980">通常情况下，一个应避免必须在整个生命周期内的应用程序，以及每个线程的上下文和静态上下文的上下文实例。</span><span class="sxs-lookup"><span data-stu-id="fa216-980">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94-database-null-semantics"></a><span data-ttu-id="fa216-981">9.4 数据库 null 语义</span><span class="sxs-lookup"><span data-stu-id="fa216-981">9.4      Database null semantics</span></span>

<span data-ttu-id="fa216-982">默认情况下的实体框架将生成具有 C 的 SQL 代码\#null 比较语义。</span><span class="sxs-lookup"><span data-stu-id="fa216-982">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="fa216-983">请考虑下面的示例查询：</span><span class="sxs-lookup"><span data-stu-id="fa216-983">Consider the following example query:</span></span>

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

<span data-ttu-id="fa216-984">在此示例中，我们比较数对可以为 null 的实体，例如供应商和单价属性可以为 null 的变量。</span><span class="sxs-lookup"><span data-stu-id="fa216-984">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="fa216-985">如果参数值的列值，与相同或在参数和列的值均为 null，将要求生成此查询的 SQL。</span><span class="sxs-lookup"><span data-stu-id="fa216-985">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="fa216-986">这将隐藏数据库服务器处理 null 值的方式，并将提供一致的 C\#跨不同的数据库供应商，则为 null 的体验。</span><span class="sxs-lookup"><span data-stu-id="fa216-986">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="fa216-987">但是，生成的代码有点令人费解，可能无法执行时，也比较量在 where 语句的查询数增长到很多。</span><span class="sxs-lookup"><span data-stu-id="fa216-987">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="fa216-988">若要处理这种情况的一种方法是使用数据库 null 语义。</span><span class="sxs-lookup"><span data-stu-id="fa216-988">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="fa216-989">请注意，这可能会行为的行为不同到 C\# null 语义，因为现在实体框架将生成更简单的 SQL 数据库引擎处理 null 值的方式公开。</span><span class="sxs-lookup"><span data-stu-id="fa216-989">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="fa216-990">数据库 null 语义可以是激活每个上下文只需单个配置一行针对上下文配置：</span><span class="sxs-lookup"><span data-stu-id="fa216-990">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="fa216-991">小型到中等大小的查询将不会显示可察觉的性能改进时使用数据库 null 语义，但不同之处将变得更明显上具有大量的潜在 null 比较查询。</span><span class="sxs-lookup"><span data-stu-id="fa216-991">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="fa216-992">在上面的示例查询中，性能差异是在受控环境中运行 microbenchmark 小于 2%。</span><span class="sxs-lookup"><span data-stu-id="fa216-992">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95-async"></a><span data-ttu-id="fa216-993">9.5 异步</span><span class="sxs-lookup"><span data-stu-id="fa216-993">9.5      Async</span></span>

<span data-ttu-id="fa216-994">异步操作时运行在.NET 4.5 或更高版本的 entity Framework 6 引入了支持。</span><span class="sxs-lookup"><span data-stu-id="fa216-994">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="fa216-995">大多数情况下，具有 IO 的应用程序相关的争用将受益于最多使用异步查询和保存操作。</span><span class="sxs-lookup"><span data-stu-id="fa216-995">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="fa216-996">如果你的应用程序不会受到 IO 争用，使用 async 将在最佳情况下，以同步方式运行和返回结果，在相同的时间量为同步调用，或在最坏情况下，只需推迟到一个异步任务的执行并添加额外 tim完成你的方案的 e。</span><span class="sxs-lookup"><span data-stu-id="fa216-996">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="fa216-997">有关如何异步编程工作，可帮助您决定是否异步会提高应用程序的性能所访问的信息[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-997">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="fa216-998">有关使用实体框架的异步操作的详细信息，请参阅[异步查询和保存](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="fa216-998">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96-ngen"></a><span data-ttu-id="fa216-999">9.6      NGEN</span><span class="sxs-lookup"><span data-stu-id="fa216-999">9.6      NGEN</span></span>

<span data-ttu-id="fa216-1000">Entity Framework 6 不能在默认安装.NET framework 中。</span><span class="sxs-lookup"><span data-stu-id="fa216-1000">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="fa216-1001">在这种情况下，实体框架程序集不是默认情况下，这意味着所有实体框架代码都受到相同的 JIT'ing 成本与任何其他 MSIL 程序集的 NGEN 'd。</span><span class="sxs-lookup"><span data-stu-id="fa216-1001">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="fa216-1002">这可能会降低 F5 体验，同时开发和生产环境中的应用程序的冷启动。</span><span class="sxs-lookup"><span data-stu-id="fa216-1002">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="fa216-1003">为了降低 JIT'ing 的 CPU 和内存成本最好是的 NGEN 映像作为相应的实体框架。</span><span class="sxs-lookup"><span data-stu-id="fa216-1003">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="fa216-1004">有关如何改进使用 NGEN Entity Framework 6 的启动性能的详细信息，请参阅[使用 NGen 提高启动性能](~/ef6/fundamentals/performance/ngen.md)。</span><span class="sxs-lookup"><span data-stu-id="fa216-1004">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97-code-first-versus-edmx"></a><span data-ttu-id="fa216-1005">9.7 代码优先与 EDMX</span><span class="sxs-lookup"><span data-stu-id="fa216-1005">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="fa216-1006">有关面向对象编程和通过概念模型 （对象）、 存储架构 （数据库） 和之间的映射的内存中表示的关系数据库之间的阻抗不匹配问题的实体框架原因两个。</span><span class="sxs-lookup"><span data-stu-id="fa216-1006">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="fa216-1007">此元数据的实体数据模型或 EDM 为调用短。</span><span class="sxs-lookup"><span data-stu-id="fa216-1007">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="fa216-1008">此 EDM 中，从实体框架将派生自视图往返数据到数据库的内存中对象和备份。</span><span class="sxs-lookup"><span data-stu-id="fa216-1008">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="fa216-1009">当 Entity Framework 用于 EDMX 文件的正式指定概念模型、 存储架构和映射，则模型加载阶段仅具有来验证 EDM 是正确 （例如，请确保没有映射是缺失），然后生成的视图，然后验证视图，并已准备好使用此元数据。</span><span class="sxs-lookup"><span data-stu-id="fa216-1009">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="fa216-1010">仅执行然后可以查询或将新的数据保存到数据存储区。</span><span class="sxs-lookup"><span data-stu-id="fa216-1010">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="fa216-1011">代码第一种方法就其本质而言，是一个复杂的实体数据模型生成器。</span><span class="sxs-lookup"><span data-stu-id="fa216-1011">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="fa216-1012">Entity Framework 有以生成所提供的代码; 从 EDM它会分析模型、 应用约定和配置通过 Fluent API 模型中涉及的类。</span><span class="sxs-lookup"><span data-stu-id="fa216-1012">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="fa216-1013">生成 EDM 后，实体框架实质上是行为的行为相同方式与将具有 EDMX 文件已存在于项目的方式。</span><span class="sxs-lookup"><span data-stu-id="fa216-1013">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="fa216-1014">因此，从 Code First 生成模型将添加额外的复杂性，转换为实体框架相比具有 EDMX 时速度较慢的启动时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-1014">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="fa216-1015">成本是模型的完全依赖于的大小和复杂性正在生成。</span><span class="sxs-lookup"><span data-stu-id="fa216-1015">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="fa216-1016">在选择使用 Code First 和 EDMX，务必知道引入的 Code First 的灵活性，增加的第一次生成模型的成本。</span><span class="sxs-lookup"><span data-stu-id="fa216-1016">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="fa216-1017">如果你的应用程序可承受的此第一次加载成本则通常 Code First 将转的首选的方法。</span><span class="sxs-lookup"><span data-stu-id="fa216-1017">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="fa216-1018">10 调查性能</span><span class="sxs-lookup"><span data-stu-id="fa216-1018">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="fa216-1019">10.1 使用 Visual Studio Profiler</span><span class="sxs-lookup"><span data-stu-id="fa216-1019">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="fa216-1020">如果在使用实体框架的性能问题，可以使用类似于内置到 Visual Studio 探查器以查看你的应用程序上花费其时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-1020">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="fa216-1021">这是我们用于生成饼图"探讨 ADO.NET 实体框架的第 1 部分的性能"博客文章中的工具 ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) ，可显示实体框架何处消耗在冷和热查询过程及其时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-1021">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="fa216-1022">由数据和建模客户顾问团队编写的 《 使用 Visual Studio 2010 Profiler 分析 Entity Framework 》 博客文章显示了如何在使用探查器来调查性能问题的一个实际示例。</span><span class="sxs-lookup"><span data-stu-id="fa216-1022">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span><span data-ttu-id="fa216-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span><span class="sxs-lookup"><span data-stu-id="fa216-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span></span> <span data-ttu-id="fa216-1024">此文章针对 windows 应用程序的编写。</span><span class="sxs-lookup"><span data-stu-id="fa216-1024">This post was written for a windows application.</span></span> <span data-ttu-id="fa216-1025">如果你需要分析 web 应用程序的 Windows 性能记录器 (WPR) 和 Windows Performance Analyzer (WPA) 工具可能效果更佳从 Visual Studio 的工作。</span><span class="sxs-lookup"><span data-stu-id="fa216-1025">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="fa216-1026">WPR 和 WPA 是 Windows 性能工具包附带 Windows 评估和部署工具包的一部分 ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982))。</span><span class="sxs-lookup"><span data-stu-id="fa216-1026">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="fa216-1027">10.2 应用程序/数据库分析</span><span class="sxs-lookup"><span data-stu-id="fa216-1027">10.2 Application/Database profiling</span></span>

<span data-ttu-id="fa216-1028">内置到 Visual Studio 探查器等工具告诉你的应用程序上花费时间。</span><span class="sxs-lookup"><span data-stu-id="fa216-1028">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span><span data-ttu-id="fa216-1029">  另一种类型的探查器是可用的执行动态分析运行的应用程序，在生产或根据需要，预生产中，并查找常见缺陷和反模式的数据库访问权限。</span><span class="sxs-lookup"><span data-stu-id="fa216-1029">  Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="fa216-1030">两个地区销售的探查器是实体框架 Profiler ( \<http://efprof.com>) ORMProfiler 和 ( \<http://ormprofiler.com>)。</span><span class="sxs-lookup"><span data-stu-id="fa216-1030">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="fa216-1031">如果应用程序是使用 Code First 的 MVC 应用程序，可以使用 StackExchange 的 MiniProfiler。</span><span class="sxs-lookup"><span data-stu-id="fa216-1031">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="fa216-1032">Scott Hanselman 在他的博客中介绍了此工具： \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>。</span><span class="sxs-lookup"><span data-stu-id="fa216-1032">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="fa216-1033">有关详细信息分析应用程序的数据库活动，请参阅标题为 Julie Lerman 的 MSDN 杂志 》 文章[分析实体框架中的数据库活动](https://msdn.microsoft.com/magazine/gg490349.aspx)。</span><span class="sxs-lookup"><span data-stu-id="fa216-1033">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="fa216-1034">10.3 数据库记录器</span><span class="sxs-lookup"><span data-stu-id="fa216-1034">10.3 Database logger</span></span>

<span data-ttu-id="fa216-1035">如果您使用的 Entity Framework 6 也可考虑使用内置日志记录功能。</span><span class="sxs-lookup"><span data-stu-id="fa216-1035">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="fa216-1036">通过简单的单行配置其活动记录，可指示上下文的数据库属性：</span><span class="sxs-lookup"><span data-stu-id="fa216-1036">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="fa216-1037">在此示例中的数据库活动将记录到控制台中，但日志属性可以配置为调用任何操作&lt;字符串&gt;委托。</span><span class="sxs-lookup"><span data-stu-id="fa216-1037">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="fa216-1038">如果你想要启用数据库日志记录，而无需重新编译，并且您使用的 Entity Framework 6.1 或更高版本，您可以这样做通过你的应用程序的 web.config 或 app.config 文件中添加侦听器。</span><span class="sxs-lookup"><span data-stu-id="fa216-1038">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="fa216-1039">有关如何添加日志记录，无需重新编译转到详细信息\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>。</span><span class="sxs-lookup"><span data-stu-id="fa216-1039">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="fa216-1040">11 附录</span><span class="sxs-lookup"><span data-stu-id="fa216-1040">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="fa216-1041">11.1 A.测试环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1041">11.1 A. Test Environment</span></span>

<span data-ttu-id="fa216-1042">此环境使用具有客户端应用程序从一台计算机上数据库的计算机 2 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="fa216-1042">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="fa216-1043">机位于同一机架中，因此网络延迟是相对较低，但比单计算机环境更真实。</span><span class="sxs-lookup"><span data-stu-id="fa216-1043">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-app-server"></a><span data-ttu-id="fa216-1044">11.1.1 应用服务器</span><span class="sxs-lookup"><span data-stu-id="fa216-1044">11.1.1       App Server</span></span>

##### <a name="11111-software-environment"></a><span data-ttu-id="fa216-1045">11.1.1.1 软件环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1045">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="fa216-1046">实体框架 4 软件环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1046">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="fa216-1047">OS 名称：Windows Server 2008 R2 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="fa216-1047">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="fa216-1048">Visual Studio 2010 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="fa216-1048">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="fa216-1049">（仅适用于某些比较） 的 visual Studio 2010 SP1。</span><span class="sxs-lookup"><span data-stu-id="fa216-1049">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="fa216-1050">Entity Framework 5 和 6 软件环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1050">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="fa216-1051">OS 名称：Windows 8.1 Enterprise</span><span class="sxs-lookup"><span data-stu-id="fa216-1051">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="fa216-1052">Visual Studio 2013 – Ultimate。</span><span class="sxs-lookup"><span data-stu-id="fa216-1052">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112-hardware-environment"></a><span data-ttu-id="fa216-1053">11.1.1.2 硬件环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1053">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="fa216-1054">双处理器：    @ 2.27 GHz intel （) 至强® CPU L5520 W3530、 2261 Mhz8 g h z、 4 个核心、 84 逻辑处理器。</span><span class="sxs-lookup"><span data-stu-id="fa216-1054">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="fa216-1055">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="fa216-1055">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="fa216-1056">136 GB SCSI250GB SATA 7200 rpm 3 GB/s 驱动器将拆分为 4 个分区。</span><span class="sxs-lookup"><span data-stu-id="fa216-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-db-server"></a><span data-ttu-id="fa216-1057">11.1.2 DB 服务器</span><span class="sxs-lookup"><span data-stu-id="fa216-1057">11.1.2       DB server</span></span>

##### <a name="11121-software-environment"></a><span data-ttu-id="fa216-1058">11.1.2.1 软件环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1058">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="fa216-1059">OS 名称：Windows Server 2008 R28.1 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="fa216-1059">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="fa216-1060">SQL Server 2008 R22012.</span><span class="sxs-lookup"><span data-stu-id="fa216-1060">SQL Server 2008 R22012.</span></span>

##### <a name="11122-hardware-environment"></a><span data-ttu-id="fa216-1061">11.1.2.2 硬件环境</span><span class="sxs-lookup"><span data-stu-id="fa216-1061">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="fa216-1062">单处理器：2.27 GHz intel （) 至强® CPU L5520、 2261 MhzES-1620 0 @ 3.60 g h z、 4 个核心、 8 逻辑处理器。</span><span class="sxs-lookup"><span data-stu-id="fa216-1062">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="fa216-1063">824 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="fa216-1063">824 GB RamRAM.</span></span>
-   <span data-ttu-id="fa216-1064">465 GB ATA500GB SATA 7200 rpm 6 GB/s 驱动器将拆分为 4 个分区。</span><span class="sxs-lookup"><span data-stu-id="fa216-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112-b-query-performance-comparison-tests"></a><span data-ttu-id="fa216-1065">11.2 B.查询性能比较测试</span><span class="sxs-lookup"><span data-stu-id="fa216-1065">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="fa216-1066">Northwind 模型用于执行这些测试。</span><span class="sxs-lookup"><span data-stu-id="fa216-1066">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="fa216-1067">它是使用实体框架设计器从数据库生成的。</span><span class="sxs-lookup"><span data-stu-id="fa216-1067">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="fa216-1068">然后，使用以下代码来比较查询执行选项的性能：</span><span class="sxs-lookup"><span data-stu-id="fa216-1068">Then, the following code was used to compare the performance of the query execution options:</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a><span data-ttu-id="fa216-1069">11.3 C.Navision 模型</span><span class="sxs-lookup"><span data-stu-id="fa216-1069">11.3 C. Navision Model</span></span>

<span data-ttu-id="fa216-1070">Navision 数据库是用于演示 Microsoft Dynamics – 导航的大型数据库</span><span class="sxs-lookup"><span data-stu-id="fa216-1070">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="fa216-1071">生成概念模型包含 1005年实体集和 4227 关联集。</span><span class="sxs-lookup"><span data-stu-id="fa216-1071">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="fa216-1072">在测试中使用的模型是"平面"– 没有继承已添加到它。</span><span class="sxs-lookup"><span data-stu-id="fa216-1072">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="fa216-1073">11.3.1 查询用于 Navision 测试</span><span class="sxs-lookup"><span data-stu-id="fa216-1073">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="fa216-1074">使用 Navision 模型使用的查询列表中包含 Entity SQL 查询的 3 个的类别：</span><span class="sxs-lookup"><span data-stu-id="fa216-1074">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="fa216-1075">11.3.1.1 查找</span><span class="sxs-lookup"><span data-stu-id="fa216-1075">11.3.1.1 Lookup</span></span>

<span data-ttu-id="fa216-1076">简单的查找查询不包含聚合</span><span class="sxs-lookup"><span data-stu-id="fa216-1076">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="fa216-1077">计数:16232</span><span class="sxs-lookup"><span data-stu-id="fa216-1077">Count: 16232</span></span>
-   <span data-ttu-id="fa216-1078">示例:</span><span class="sxs-lookup"><span data-stu-id="fa216-1078">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a><span data-ttu-id="fa216-1079">11.3.1.2 SingleAggregating</span><span class="sxs-lookup"><span data-stu-id="fa216-1079">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="fa216-1080">具有多个聚合，但没有小计 （单个查询） 的正常 BI 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-1080">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="fa216-1081">计数:2313</span><span class="sxs-lookup"><span data-stu-id="fa216-1081">Count: 2313</span></span>
-   <span data-ttu-id="fa216-1082">示例:</span><span class="sxs-lookup"><span data-stu-id="fa216-1082">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="fa216-1083">其中 MDF\_SessionLogin\_时间\_max （） 作为模型中定义：</span><span class="sxs-lookup"><span data-stu-id="fa216-1083">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a><span data-ttu-id="fa216-1084">11.3.1.3 AggregatingSubtotals</span><span class="sxs-lookup"><span data-stu-id="fa216-1084">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="fa216-1085">具有聚合和小计 （通过所有的并集） 的 BI 查询</span><span class="sxs-lookup"><span data-stu-id="fa216-1085">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="fa216-1086">计数:178</span><span class="sxs-lookup"><span data-stu-id="fa216-1086">Count: 178</span></span>
-   <span data-ttu-id="fa216-1087">示例:</span><span class="sxs-lookup"><span data-stu-id="fa216-1087">Example:</span></span>

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```

---
title: EF4、EF5 和 EF6 的性能注意事项 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434334"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="f31ac-102">EF 4、5 和 6 的性能注意事项</span><span class="sxs-lookup"><span data-stu-id="f31ac-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="f31ac-103">由大卫·奥班多，埃里克·丁格和其他人</span><span class="sxs-lookup"><span data-stu-id="f31ac-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="f31ac-104">发布时间：2012 年 4 月</span><span class="sxs-lookup"><span data-stu-id="f31ac-104">Published: April 2012</span></span>

<span data-ttu-id="f31ac-105">上次更新时间：2014 年 5 月</span><span class="sxs-lookup"><span data-stu-id="f31ac-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="f31ac-106">1. 介绍</span><span class="sxs-lookup"><span data-stu-id="f31ac-106">1. Introduction</span></span>

<span data-ttu-id="f31ac-107">对象关系映射框架是一种为面向对象的应用程序中的数据访问提供抽象的便捷方法。</span><span class="sxs-lookup"><span data-stu-id="f31ac-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="f31ac-108">对于 .NET 应用程序，Microsoft 推荐的 O/RM 是实体框架。</span><span class="sxs-lookup"><span data-stu-id="f31ac-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="f31ac-109">然而，对于任何抽象，性能都可能成为一个问题。</span><span class="sxs-lookup"><span data-stu-id="f31ac-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="f31ac-110">编写本白皮书是为了显示使用实体框架开发应用程序时的性能注意事项，让开发人员了解可能影响性能的实体框架内部算法，并提供调查和改进使用实体框架的应用程序的性能的提示。</span><span class="sxs-lookup"><span data-stu-id="f31ac-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="f31ac-111">Web 上已有许多有关性能的好主题，我们还尝试尽可能指向这些资源。</span><span class="sxs-lookup"><span data-stu-id="f31ac-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="f31ac-112">性能是一个棘手的话题。</span><span class="sxs-lookup"><span data-stu-id="f31ac-112">Performance is a tricky topic.</span></span> <span data-ttu-id="f31ac-113">本白皮书旨在作为资源，帮助您为使用实体框架的应用程序做出与性能相关的决策。</span><span class="sxs-lookup"><span data-stu-id="f31ac-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="f31ac-114">我们包括一些测试指标来演示性能，但这些指标并非旨在作为您在应用程序中看到的性能的绝对指标。</span><span class="sxs-lookup"><span data-stu-id="f31ac-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="f31ac-115">出于实际目的，本文档假定实体框架 4 在 .NET 4.0 下运行，实体框架 5 和 6 在 .NET 4.5 下运行。</span><span class="sxs-lookup"><span data-stu-id="f31ac-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="f31ac-116">实体框架 5 的许多性能改进都位于提供 .NET 4.5 的核心组件中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="f31ac-117">实体框架 6 是带外版本，不依赖于随 .NET 一起发货的实体框架组件。</span><span class="sxs-lookup"><span data-stu-id="f31ac-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="f31ac-118">实体框架 6 在 .NET 4.0 和 .NET 4.5 上都工作，可以为尚未从 .NET 4.0 升级但希望在其应用程序中使用最新实体框架位的用户提供巨大的性能优势。</span><span class="sxs-lookup"><span data-stu-id="f31ac-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="f31ac-119">当本文档提到实体框架 6 时，它指的是本文编写时可用的最新版本：版本 6.1.0。</span><span class="sxs-lookup"><span data-stu-id="f31ac-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="f31ac-120">2. 冷与暖查询执行</span><span class="sxs-lookup"><span data-stu-id="f31ac-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="f31ac-121">首次对给定模型进行任何查询时，实体框架会在幕后执行大量工作来加载和验证模型。</span><span class="sxs-lookup"><span data-stu-id="f31ac-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="f31ac-122">我们经常将第一个查询称为"冷"查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-122">We frequently refer to this first query as a "cold" query.</span></span><span data-ttu-id="f31ac-123">针对已加载模型的进一步查询称为"暖"查询，并且速度更快。</span><span class="sxs-lookup"><span data-stu-id="f31ac-123">  Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="f31ac-124">让我们从高级视图来了解使用实体框架执行查询时所花费的时间，并了解实体框架 6 中情况正在改善。</span><span class="sxs-lookup"><span data-stu-id="f31ac-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="f31ac-125">**第一次查询执行 = 冷查询**</span><span class="sxs-lookup"><span data-stu-id="f31ac-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="f31ac-126">代码用户写入</span><span class="sxs-lookup"><span data-stu-id="f31ac-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="f31ac-127">操作</span><span class="sxs-lookup"><span data-stu-id="f31ac-127">Action</span></span>                    | <span data-ttu-id="f31ac-128">EF4 性能影响</span><span class="sxs-lookup"><span data-stu-id="f31ac-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="f31ac-129">EF5 性能影响</span><span class="sxs-lookup"><span data-stu-id="f31ac-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="f31ac-130">EF6 性能影响</span><span class="sxs-lookup"><span data-stu-id="f31ac-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="f31ac-131">上下文创建</span><span class="sxs-lookup"><span data-stu-id="f31ac-131">Context creation</span></span>          | <span data-ttu-id="f31ac-132">中型</span><span class="sxs-lookup"><span data-stu-id="f31ac-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="f31ac-133">中型</span><span class="sxs-lookup"><span data-stu-id="f31ac-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="f31ac-134">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="f31ac-135">查询表达式创建</span><span class="sxs-lookup"><span data-stu-id="f31ac-135">Query expression creation</span></span> | <span data-ttu-id="f31ac-136">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="f31ac-137">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="f31ac-138">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="f31ac-139">LINQ 查询执行</span><span class="sxs-lookup"><span data-stu-id="f31ac-139">LINQ query execution</span></span>      | <span data-ttu-id="f31ac-140">- 元数据加载：高但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="f31ac-141">- 视图生成：可能非常高但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="f31ac-142">- 参数评估：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="f31ac-143">- 查询翻译：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="f31ac-144">- 具体生成：中等但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="f31ac-145">- 数据库查询执行：可能很高</span><span class="sxs-lookup"><span data-stu-id="f31ac-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="f31ac-146">• 连接。打开</span><span class="sxs-lookup"><span data-stu-id="f31ac-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="f31ac-147">• 命令.执行读取器</span><span class="sxs-lookup"><span data-stu-id="f31ac-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="f31ac-148">• 数据阅读器。阅读</span><span class="sxs-lookup"><span data-stu-id="f31ac-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="f31ac-149">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="f31ac-150">- 身份查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="f31ac-151">- 元数据加载：高但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="f31ac-152">- 视图生成：可能非常高但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="f31ac-153">- 参数评估：低</span><span class="sxs-lookup"><span data-stu-id="f31ac-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="f31ac-154">- 查询翻译：中等但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="f31ac-155">- 具体生成：中等但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="f31ac-156">- 数据库查询执行：可能很高（在某些情况下查询更好）</span><span class="sxs-lookup"><span data-stu-id="f31ac-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="f31ac-157">• 连接。打开</span><span class="sxs-lookup"><span data-stu-id="f31ac-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="f31ac-158">• 命令.执行读取器</span><span class="sxs-lookup"><span data-stu-id="f31ac-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="f31ac-159">• 数据阅读器。阅读</span><span class="sxs-lookup"><span data-stu-id="f31ac-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="f31ac-160">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="f31ac-161">- 身份查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="f31ac-162">- 元数据加载：高但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="f31ac-163">- 视图生成：中等但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="f31ac-164">- 参数评估：低</span><span class="sxs-lookup"><span data-stu-id="f31ac-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="f31ac-165">- 查询翻译：中等但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="f31ac-166">- 具体生成：中等但缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="f31ac-167">- 数据库查询执行：可能很高（在某些情况下查询更好）</span><span class="sxs-lookup"><span data-stu-id="f31ac-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="f31ac-168">• 连接。打开</span><span class="sxs-lookup"><span data-stu-id="f31ac-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="f31ac-169">• 命令.执行读取器</span><span class="sxs-lookup"><span data-stu-id="f31ac-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="f31ac-170">• 数据阅读器。阅读</span><span class="sxs-lookup"><span data-stu-id="f31ac-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="f31ac-171">对象具体化：中等（快于 EF5）</span><span class="sxs-lookup"><span data-stu-id="f31ac-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="f31ac-172">- 身份查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="f31ac-173">连接.关闭</span><span class="sxs-lookup"><span data-stu-id="f31ac-173">Connection.Close</span></span>          | <span data-ttu-id="f31ac-174">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="f31ac-175">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="f31ac-176">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="f31ac-177">**第二次查询执行 = 暖查询**</span><span class="sxs-lookup"><span data-stu-id="f31ac-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="f31ac-178">代码用户写入</span><span class="sxs-lookup"><span data-stu-id="f31ac-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="f31ac-179">操作</span><span class="sxs-lookup"><span data-stu-id="f31ac-179">Action</span></span>                    | <span data-ttu-id="f31ac-180">EF4 性能影响</span><span class="sxs-lookup"><span data-stu-id="f31ac-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="f31ac-181">EF5 性能影响</span><span class="sxs-lookup"><span data-stu-id="f31ac-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="f31ac-182">EF6 性能影响</span><span class="sxs-lookup"><span data-stu-id="f31ac-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="f31ac-183">上下文创建</span><span class="sxs-lookup"><span data-stu-id="f31ac-183">Context creation</span></span>          | <span data-ttu-id="f31ac-184">中型</span><span class="sxs-lookup"><span data-stu-id="f31ac-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="f31ac-185">中型</span><span class="sxs-lookup"><span data-stu-id="f31ac-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="f31ac-186">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="f31ac-187">查询表达式创建</span><span class="sxs-lookup"><span data-stu-id="f31ac-187">Query expression creation</span></span> | <span data-ttu-id="f31ac-188">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="f31ac-189">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="f31ac-190">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="f31ac-191">LINQ 查询执行</span><span class="sxs-lookup"><span data-stu-id="f31ac-191">LINQ query execution</span></span>      | <span data-ttu-id="f31ac-192">- 元数据~~加载~~查找：~~高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-193">- 查看~~生成~~查找：~~可能非常高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-194">- 参数评估：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="f31ac-195">- 查询~~翻译~~查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="f31ac-196">- 具体~~生成查找~~：~~中等但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-197">- 数据库查询执行：可能很高</span><span class="sxs-lookup"><span data-stu-id="f31ac-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="f31ac-198">• 连接。打开</span><span class="sxs-lookup"><span data-stu-id="f31ac-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="f31ac-199">• 命令.执行读取器</span><span class="sxs-lookup"><span data-stu-id="f31ac-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="f31ac-200">• 数据阅读器。阅读</span><span class="sxs-lookup"><span data-stu-id="f31ac-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="f31ac-201">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="f31ac-202">- 身份查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="f31ac-203">- 元数据~~加载~~查找：~~高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-204">- 查看~~生成~~查找：~~可能非常高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-205">- 参数评估：低</span><span class="sxs-lookup"><span data-stu-id="f31ac-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="f31ac-206">- 查询~~翻译~~查找：~~中但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-207">- 具体~~生成查找~~：~~中等但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-208">- 数据库查询执行：可能很高（在某些情况下查询更好）</span><span class="sxs-lookup"><span data-stu-id="f31ac-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="f31ac-209">• 连接。打开</span><span class="sxs-lookup"><span data-stu-id="f31ac-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="f31ac-210">• 命令.执行读取器</span><span class="sxs-lookup"><span data-stu-id="f31ac-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="f31ac-211">• 数据阅读器。阅读</span><span class="sxs-lookup"><span data-stu-id="f31ac-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="f31ac-212">对象具体化：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="f31ac-213">- 身份查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="f31ac-214">- 元数据~~加载~~查找：~~高但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-215">- 查看~~生成~~查找：~~中等但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-216">- 参数评估：低</span><span class="sxs-lookup"><span data-stu-id="f31ac-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="f31ac-217">- 查询~~翻译~~查找：~~中但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-218">- 具体~~生成查找~~：~~中等但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="f31ac-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="f31ac-219">- 数据库查询执行：可能很高（在某些情况下查询更好）</span><span class="sxs-lookup"><span data-stu-id="f31ac-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="f31ac-220">• 连接。打开</span><span class="sxs-lookup"><span data-stu-id="f31ac-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="f31ac-221">• 命令.执行读取器</span><span class="sxs-lookup"><span data-stu-id="f31ac-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="f31ac-222">• 数据阅读器。阅读</span><span class="sxs-lookup"><span data-stu-id="f31ac-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="f31ac-223">对象具体化：中等（快于 EF5）</span><span class="sxs-lookup"><span data-stu-id="f31ac-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="f31ac-224">- 身份查找：中等</span><span class="sxs-lookup"><span data-stu-id="f31ac-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="f31ac-225">连接.关闭</span><span class="sxs-lookup"><span data-stu-id="f31ac-225">Connection.Close</span></span>          | <span data-ttu-id="f31ac-226">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="f31ac-227">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="f31ac-228">低</span><span class="sxs-lookup"><span data-stu-id="f31ac-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="f31ac-229">有几种方法可以降低冷查询和暖查询的性能成本，我们将在下一节中介绍这些查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="f31ac-230">具体来说，我们将考虑通过使用预生成的视图来降低冷查询中模型加载的成本，这将有助于缓解在生成视图期间遇到的性能难题。</span><span class="sxs-lookup"><span data-stu-id="f31ac-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="f31ac-231">对于暖查询，我们将介绍查询计划缓存、无跟踪查询和不同的查询执行选项。</span><span class="sxs-lookup"><span data-stu-id="f31ac-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="f31ac-232">2.1 什么是视图生成？</span><span class="sxs-lookup"><span data-stu-id="f31ac-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="f31ac-233">为了了解什么是视图生成，我们必须首先了解什么是"映射视图"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="f31ac-234">映射视图是映射中为每个实体集和关联指定的转换的可执行表示形式。</span><span class="sxs-lookup"><span data-stu-id="f31ac-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="f31ac-235">在内部，这些映射视图采用 CQT（规范查询树）的形状。</span><span class="sxs-lookup"><span data-stu-id="f31ac-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="f31ac-236">有两种类型的映射视图：</span><span class="sxs-lookup"><span data-stu-id="f31ac-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="f31ac-237">查询视图：这些视图表示从数据库架构到概念模型所需的转换。</span><span class="sxs-lookup"><span data-stu-id="f31ac-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="f31ac-238">更新视图：这些视图表示从概念模型到数据库架构所需的转换。</span><span class="sxs-lookup"><span data-stu-id="f31ac-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="f31ac-239">请记住，概念模型可能以各种方式与数据库架构不同。</span><span class="sxs-lookup"><span data-stu-id="f31ac-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="f31ac-240">例如，一个表可用于存储两种不同实体类型的数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="f31ac-241">继承和非常规映射在映射视图的复杂性中起着一定的作用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="f31ac-242">基于映射规范计算这些视图的过程就是我们所说的视图生成。</span><span class="sxs-lookup"><span data-stu-id="f31ac-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="f31ac-243">视图生成可以在加载模型时动态进行，也可以在生成时使用"预生成的视图"进行动态生成;后者以实体 SQL 语句的形式序列化到 C\#或 VB 文件。</span><span class="sxs-lookup"><span data-stu-id="f31ac-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="f31ac-244">生成视图时，也会验证视图。</span><span class="sxs-lookup"><span data-stu-id="f31ac-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="f31ac-245">从性能角度来看，视图生成成本的绝大多数实际上是对视图的验证，这可确保实体之间的连接有意义，并且对所有支持的操作具有正确的基数。</span><span class="sxs-lookup"><span data-stu-id="f31ac-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="f31ac-246">执行对实体集的查询时，查询将与相应的查询视图组合，并且此合成的结果通过计划编译器运行，以创建备份存储可以理解的查询的表示形式。</span><span class="sxs-lookup"><span data-stu-id="f31ac-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="f31ac-247">对于 SQL 服务器，此编译的最终结果将是 T-SQL SELECT 语句。</span><span class="sxs-lookup"><span data-stu-id="f31ac-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="f31ac-248">首次对实体集执行更新时，更新视图将通过类似的过程运行，以将其转换为目标数据库的 DML 语句。</span><span class="sxs-lookup"><span data-stu-id="f31ac-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="f31ac-249">2.2 影响视图生成性能的因素</span><span class="sxs-lookup"><span data-stu-id="f31ac-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="f31ac-250">视图生成步骤的性能不仅取决于模型的大小，还取决于模型的互连程度。</span><span class="sxs-lookup"><span data-stu-id="f31ac-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="f31ac-251">如果两个实体通过继承链或关联连接，则它们表示已连接。</span><span class="sxs-lookup"><span data-stu-id="f31ac-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="f31ac-252">同样，如果两个表通过外键连接，则它们被连接。</span><span class="sxs-lookup"><span data-stu-id="f31ac-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="f31ac-253">随着架构中连接的实体和表数量的增加，视图生成成本也会增加。</span><span class="sxs-lookup"><span data-stu-id="f31ac-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="f31ac-254">在最坏的情况下，我们用于生成和验证视图的算法呈指数级，尽管我们使用一些优化来改进这一点。</span><span class="sxs-lookup"><span data-stu-id="f31ac-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="f31ac-255">似乎对性能产生负面影响的最大因素是：</span><span class="sxs-lookup"><span data-stu-id="f31ac-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="f31ac-256">模型大小，指实体的数量和这些实体之间的关联量。</span><span class="sxs-lookup"><span data-stu-id="f31ac-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="f31ac-257">模型复杂性，特别是涉及大量类型的继承。</span><span class="sxs-lookup"><span data-stu-id="f31ac-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="f31ac-258">使用独立关联，而不是外国密钥关联。</span><span class="sxs-lookup"><span data-stu-id="f31ac-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="f31ac-259">对于小型、简单的模型，成本可能足够小，无需使用预生成的视图。</span><span class="sxs-lookup"><span data-stu-id="f31ac-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="f31ac-260">随着模型大小和复杂性的增加，有多种选项可用于降低视图生成和验证的成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="f31ac-261">2.3 使用预生成的视图减少模型加载时间</span><span class="sxs-lookup"><span data-stu-id="f31ac-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

<span data-ttu-id="f31ac-262">有关如何在实体框架 6 上使用预生成的视图的详细信息，请访问[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)</span><span class="sxs-lookup"><span data-stu-id="f31ac-262">For detailed information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md)</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a><span data-ttu-id="f31ac-263">2.3.1 使用实体框架电动工具社区版的预生成视图</span><span class="sxs-lookup"><span data-stu-id="f31ac-263">2.3.1 Pre-Generated views using the Entity Framework Power Tools Community Edition</span></span>

<span data-ttu-id="f31ac-264">您可以使用[实体框架 6 电动工具社区版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)生成 EDMX 和代码优先模型的视图，通过右键单击模型类文件并使用实体框架菜单选择"生成视图"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-264">You can use the [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="f31ac-265">实体框架电动工具社区版仅适用于 DbContext 派生的上下文。</span><span class="sxs-lookup"><span data-stu-id="f31ac-265">The Entity Framework Power Tools Community Edition work only on DbContext-derived contexts.</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="f31ac-266">2.3.2 如何使用预生成的视图与 EDMGen 创建的模型</span><span class="sxs-lookup"><span data-stu-id="f31ac-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="f31ac-267">EDMGen 是一个实用程序，它与 .NET 一起提供，并与实体框架 4 和 5 一起工作，但与实体框架 6 无关。</span><span class="sxs-lookup"><span data-stu-id="f31ac-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="f31ac-268">EDMGen 允许您从命令行生成模型文件、对象层和视图。</span><span class="sxs-lookup"><span data-stu-id="f31ac-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="f31ac-269">其中一个输出是您选择的语言的"视图"文件，VB 或 C\#。</span><span class="sxs-lookup"><span data-stu-id="f31ac-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="f31ac-270">这是一个代码文件，其中包含每个实体集的实体 SQL 代码段。</span><span class="sxs-lookup"><span data-stu-id="f31ac-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="f31ac-271">要启用预生成的视图，只需将文件包含在项目中即可。</span><span class="sxs-lookup"><span data-stu-id="f31ac-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="f31ac-272">如果手动编辑模型的架构文件，则需要重新生成视图文件。</span><span class="sxs-lookup"><span data-stu-id="f31ac-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="f31ac-273">可以通过使用 **/mode：ViewGeneration**标志运行 EDMGen 来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="f31ac-274">2.3.3 如何使用预生成的视图与 EDMX 文件</span><span class="sxs-lookup"><span data-stu-id="f31ac-274">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="f31ac-275">您还可以使用 EDMGen 为 EDMX 文件生成视图 - 前面引用的 MSDN 主题介绍如何添加预构建事件来执行此操作 - 但这样做很复杂，在某些情况下，这是不可能的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-275">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="f31ac-276">当模型位于 edmx 文件中时，通常使用 T4 模板生成视图会更容易。</span><span class="sxs-lookup"><span data-stu-id="f31ac-276">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="f31ac-277">ADO.NET团队博客有一篇文章，描述如何使用 T4 模板生成视图 （。 \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)</span><span class="sxs-lookup"><span data-stu-id="f31ac-277">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>).</span></span> <span data-ttu-id="f31ac-278">此帖子包含一个模板，可以下载并添加到您的项目中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-278">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="f31ac-279">模板是为实体框架的第一个版本编写的，因此不能保证它们能够与最新版本的实体框架配合使用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-279">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="f31ac-280">但是，可以从可视化工作室库下载实体框架 4 和 5 的最新视图生成模板集：</span><span class="sxs-lookup"><span data-stu-id="f31ac-280">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="f31ac-281">VB.NET：\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="f31ac-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="f31ac-282">C\#：\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="f31ac-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="f31ac-283">如果您使用的是实体框架 6，则可以从 中的\<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>可视化工作室库获取视图生成 T4 模板。</span><span class="sxs-lookup"><span data-stu-id="f31ac-283">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="f31ac-284">2.4 降低视图生成成本</span><span class="sxs-lookup"><span data-stu-id="f31ac-284">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="f31ac-285">使用预生成的视图可将视图生成成本从模型加载（运行时间）移动到设计时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-285">Using pre-generated views moves the cost of view generation from model loading (run time) to design time.</span></span> <span data-ttu-id="f31ac-286">虽然这提高了运行时的启动性能，但在开发时，您仍将经历视图生成的痛苦。</span><span class="sxs-lookup"><span data-stu-id="f31ac-286">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="f31ac-287">还有其他几种技巧可以帮助降低在编译时间和运行时生成视图的成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-287">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="f31ac-288">2.4.1 使用外键关联降低视图生成成本</span><span class="sxs-lookup"><span data-stu-id="f31ac-288">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="f31ac-289">我们看到许多案例，将模型中的关联从独立协会切换到外国密钥关联，从而大大缩短了在视图生成中花费的时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-289">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="f31ac-290">为了证明这一改进，我们使用 EDMGen 生成了两个版本的 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="f31ac-290">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="f31ac-291">*注意：有关 Navision 模型的说明，请参阅附录 C。*</span><span class="sxs-lookup"><span data-stu-id="f31ac-291">*Note: see appendix C for a description of the Navision model.*</span></span> <span data-ttu-id="f31ac-292">Navision 模型对本练习非常感兴趣，因为它拥有大量实体和它们之间的关系。</span><span class="sxs-lookup"><span data-stu-id="f31ac-292">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="f31ac-293">此非常大的模型的一个版本是使用外键关联生成的，另一个版本是使用独立关联生成的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-293">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="f31ac-294">然后，我们安排为每个模型生成视图需要多长时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-294">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="f31ac-295">实体框架 5 测试使用类 EntityViewGenerator 的 GenerateViews（） 方法生成视图，而实体框架 6 测试使用类存储映射项目集合中的 GenerateViews（） 方法。</span><span class="sxs-lookup"><span data-stu-id="f31ac-295">Entity Framework 5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="f31ac-296">这是因为实体框架 6 代码库中的代码重组。</span><span class="sxs-lookup"><span data-stu-id="f31ac-296">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="f31ac-297">使用实体框架 5，使用外键生成模型的视图在实验室机器中花了 65 分钟。</span><span class="sxs-lookup"><span data-stu-id="f31ac-297">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="f31ac-298">不知道为使用独立关联的模型生成视图需要多长时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-298">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="f31ac-299">在实验室中重新启动计算机以安装每月更新之前，我们让测试运行了一个多月。</span><span class="sxs-lookup"><span data-stu-id="f31ac-299">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="f31ac-300">使用实体框架 6，使用外键生成模型的视图生成需要 28 秒在同一实验室机器中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-300">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="f31ac-301">使用独立关联的模型的视图生成需要 58 秒。</span><span class="sxs-lookup"><span data-stu-id="f31ac-301">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="f31ac-302">实体框架 6 对其视图生成代码所做的改进意味着许多项目不需要预生成的视图来获取更快的启动时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-302">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="f31ac-303">请务必指出，实体框架 4 和 5 中的预生成视图可以使用 EDMGen 或实体框架电动工具完成。</span><span class="sxs-lookup"><span data-stu-id="f31ac-303">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="f31ac-304">对于实体框架 6 视图生成，可以通过实体框架电源工具完成，也可以以编程方式完成，如[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="f31ac-304">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="f31ac-305">2.4.1.1 如何使用外键而不是独立关联</span><span class="sxs-lookup"><span data-stu-id="f31ac-305">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="f31ac-306">在 Visual Studio 中使用 EDMGen 或实体设计器时，默认情况下，您将获得 PK，并且只需单个复选框或命令行标志即可在 SDK 和 I 之间切换。</span><span class="sxs-lookup"><span data-stu-id="f31ac-306">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="f31ac-307">如果您有一个大型 Code First 模型，则使用独立关联对视图生成的影响相同。</span><span class="sxs-lookup"><span data-stu-id="f31ac-307">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="f31ac-308">可以通过将外键属性包括在从属对象的类中来避免这种影响，尽管一些开发人员会认为这会污染其对象模型。</span><span class="sxs-lookup"><span data-stu-id="f31ac-308">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="f31ac-309">您可以在 中找到\<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>有关此主题的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f31ac-309">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="f31ac-310">使用时</span><span class="sxs-lookup"><span data-stu-id="f31ac-310">When using</span></span>      | <span data-ttu-id="f31ac-311">操作</span><span class="sxs-lookup"><span data-stu-id="f31ac-311">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f31ac-312">实体设计器</span><span class="sxs-lookup"><span data-stu-id="f31ac-312">Entity Designer</span></span> | <span data-ttu-id="f31ac-313">在两个实体之间添加关联后，请确保具有引用约束。</span><span class="sxs-lookup"><span data-stu-id="f31ac-313">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="f31ac-314">引用约束告诉实体框架使用外键而不是独立关联。</span><span class="sxs-lookup"><span data-stu-id="f31ac-314">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="f31ac-315">有关其他详细信息，\<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>请访问 。</span><span class="sxs-lookup"><span data-stu-id="f31ac-315">For additional details visit \<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>.</span></span> |
| <span data-ttu-id="f31ac-316">EDMGen</span><span class="sxs-lookup"><span data-stu-id="f31ac-316">EDMGen</span></span>          | <span data-ttu-id="f31ac-317">当使用 EDMGen 从数据库生成文件时，您的外键将受到尊重并添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-317">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="f31ac-318">有关 EDMGen 公开的不同选项的更多信息，请访问[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-318">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="f31ac-319">Code First</span><span class="sxs-lookup"><span data-stu-id="f31ac-319">Code First</span></span>      | <span data-ttu-id="f31ac-320">有关在使用代码优先时如何将外键属性包含在从属对象上的信息，请参阅[代码优先约定](~/ef6/modeling/code-first/conventions/built-in.md)主题的"关系约定"部分。</span><span class="sxs-lookup"><span data-stu-id="f31ac-320">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="f31ac-321">2.4.2 将模型移动到单独的装配体</span><span class="sxs-lookup"><span data-stu-id="f31ac-321">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="f31ac-322">当模型直接包含在应用程序的项目中，并且通过预生成事件或 T4 模板生成视图时，每当重建项目时，即使模型未更改，也会进行视图生成和验证。</span><span class="sxs-lookup"><span data-stu-id="f31ac-322">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="f31ac-323">如果将模型移动到单独的程序集并从应用程序的项目中引用它，则可以对应用程序进行其他更改，而无需重新生成包含模型的项目。</span><span class="sxs-lookup"><span data-stu-id="f31ac-323">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="f31ac-324">*注意：* 将模型移动到单独的程序集时，请记住将模型的连接字符串复制到客户端项目的应用程序配置文件中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-324">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="f31ac-325">2.4.3 禁用基于 edmx 的模型验证</span><span class="sxs-lookup"><span data-stu-id="f31ac-325">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="f31ac-326">EDMX 模型在编译时进行验证，即使模型保持不变。</span><span class="sxs-lookup"><span data-stu-id="f31ac-326">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="f31ac-327">如果模型已过验证，则可以通过在属性窗口中将"在生成上验证"属性设置为 false 来禁止编译时验证。</span><span class="sxs-lookup"><span data-stu-id="f31ac-327">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="f31ac-328">更改映射或模型时，可以临时重新启用验证以验证更改。</span><span class="sxs-lookup"><span data-stu-id="f31ac-328">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="f31ac-329">请注意，实体框架 6 的实体框架设计器的性能得到了提高，"在生成时验证"的成本比设计器的早期版本要低得多。</span><span class="sxs-lookup"><span data-stu-id="f31ac-329">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="f31ac-330">3 实体框架中的缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-330">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="f31ac-331">实体框架具有以下形式的缓存内置：</span><span class="sxs-lookup"><span data-stu-id="f31ac-331">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="f31ac-332">对象缓存 - 内置到 ObjectContext 实例中的 ObjectStateManager 在使用该实例检索的对象的内存中跟踪该对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-332">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="f31ac-333">这也称为第一级缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-333">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="f31ac-334">查询计划缓存 - 多次执行查询时重用生成的存储命令。</span><span class="sxs-lookup"><span data-stu-id="f31ac-334">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="f31ac-335">元数据缓存 - 跨与同一模型的不同连接共享模型的元数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-335">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="f31ac-336">除了 EF 开箱即用的缓存外，一种称为包装提供程序的特殊ADO.NET数据提供程序还可用于扩展实体框架，并缓存从数据库检索的结果（也称为二级缓存）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-336">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="f31ac-337">3.1 对象缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-337">3.1 Object Caching</span></span>

<span data-ttu-id="f31ac-338">默认情况下，当在查询结果中返回实体时，就在 EF 实现该实体之前，ObjectContext 将检查具有相同密钥的实体是否已加载到其 ObjectStateManager 中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-338">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="f31ac-339">如果具有相同密钥的实体已存在，EF 将在查询结果中包括该密钥。</span><span class="sxs-lookup"><span data-stu-id="f31ac-339">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="f31ac-340">尽管 EF 仍将针对数据库发出查询，但此行为可以绕过实体多次实现的大部分成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-340">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="f31ac-341">3.1.1 使用 DbContext 查找从对象缓存获取实体</span><span class="sxs-lookup"><span data-stu-id="f31ac-341">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="f31ac-342">与常规查询不同，DbSet 中的 Find 方法（在 EF 4.1 中首次包含 API）将在内存中执行搜索，甚至针对数据库发出查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-342">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="f31ac-343">请务必注意，两个不同的 ObjectObjectContext 实例将具有两个不同的 ObjectStateManager 实例，这意味着它们具有单独的对象缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-343">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="f31ac-344">Find 使用主键值尝试查找由上下文跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-344">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="f31ac-345">如果实体不在上下文中，则将针对数据库执行和评估查询，如果在上下文中或数据库中找不到该实体，则返回 null。</span><span class="sxs-lookup"><span data-stu-id="f31ac-345">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="f31ac-346">请注意，Find 还会返回已添加到上下文但尚未保存到数据库的实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-346">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="f31ac-347">使用 Find 时需要考虑性能。</span><span class="sxs-lookup"><span data-stu-id="f31ac-347">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="f31ac-348">默认情况下，调用此方法将触发对象缓存的验证，以检测仍挂起的提交到数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="f31ac-348">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="f31ac-349">如果对象缓存或大型对象图形中有大量对象添加到对象缓存中，此过程可能非常昂贵，但也可以禁用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-349">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="f31ac-350">在某些情况下，在禁用自动检测更改时，在调用 Find 方法时，您可能会感觉到在调用 Find 方法时，差异程度超过一个数量级。</span><span class="sxs-lookup"><span data-stu-id="f31ac-350">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="f31ac-351">但是，当对象实际位于缓存中时，以及必须从数据库中检索对象时，会感知到第二个数量级。</span><span class="sxs-lookup"><span data-stu-id="f31ac-351">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="f31ac-352">下面是一个示例图，使用我们的一些微基准（以毫秒表示）进行测量，负载为 5000 个实体：</span><span class="sxs-lookup"><span data-stu-id="f31ac-352">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="f31ac-353">![.NET 4.5 对数刻度](~/ef6/media/net45logscale.png ".NET 4.5 - 对数刻度")</span><span class="sxs-lookup"><span data-stu-id="f31ac-353">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="f31ac-354">禁用自动检测更改的查找示例：</span><span class="sxs-lookup"><span data-stu-id="f31ac-354">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="f31ac-355">使用 Find 方法时必须考虑的是：</span><span class="sxs-lookup"><span data-stu-id="f31ac-355">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="f31ac-356">如果对象不在缓存中，则 Find 的好处将被否定，但语法仍比按键查询更简单。</span><span class="sxs-lookup"><span data-stu-id="f31ac-356">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="f31ac-357">如果启用自动检测更改，Find 方法的成本可能会增加一个数量级，甚至更多取决于模型的复杂性和对象缓存中的实体数量。</span><span class="sxs-lookup"><span data-stu-id="f31ac-357">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="f31ac-358">此外，请记住，Find 仅返回要查找的实体，如果关联实体尚未在对象缓存中，则不会自动加载它们。</span><span class="sxs-lookup"><span data-stu-id="f31ac-358">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="f31ac-359">如果需要检索关联的实体，可以使用按键进行查询，并进行热切加载。</span><span class="sxs-lookup"><span data-stu-id="f31ac-359">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="f31ac-360">有关详细信息，请参阅**8.1 延迟加载与热加载**。</span><span class="sxs-lookup"><span data-stu-id="f31ac-360">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="f31ac-361">3.1.2 当对象缓存具有多个实体时的性能问题</span><span class="sxs-lookup"><span data-stu-id="f31ac-361">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="f31ac-362">对象缓存有助于提高实体框架的总体响应能力。</span><span class="sxs-lookup"><span data-stu-id="f31ac-362">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="f31ac-363">但是，当对象缓存加载的实体量非常大时，可能会影响某些操作，如添加、删除、查找、输入、保存更改等。</span><span class="sxs-lookup"><span data-stu-id="f31ac-363">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="f31ac-364">特别是，触发调用检测更改的操作将受到非常大的对象缓存的负面影响。</span><span class="sxs-lookup"><span data-stu-id="f31ac-364">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="f31ac-365">检测更改将对象图形与对象状态管理器同步，其性能将直接由对象图形的大小决定。</span><span class="sxs-lookup"><span data-stu-id="f31ac-365">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="f31ac-366">有关检测更改的详细信息，请参阅跟踪[POCO 实体中的更改](https://msdn.microsoft.com/library/dd456848.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-366">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="f31ac-367">使用实体框架 6 时，开发人员可以直接在 DbSet 上调用 AddRange 和 RemoveRange，而不是在集合上迭代，并按实例调用一次 Add。</span><span class="sxs-lookup"><span data-stu-id="f31ac-367">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="f31ac-368">使用范围方法的优点是，检测更改的成本仅针对整个实体集支付一次，而不是每个添加的实体支付一次。</span><span class="sxs-lookup"><span data-stu-id="f31ac-368">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="f31ac-369">3.2 查询计划缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-369">3.2 Query Plan Caching</span></span>

<span data-ttu-id="f31ac-370">第一次执行查询时，它会通过内部计划编译器将概念查询转换为存储命令（例如，在对 SQL Server 运行时执行的 T-SQL）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-370">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span><span data-ttu-id="f31ac-371">如果启用了查询计划缓存，则下次执行查询时，将直接从查询计划缓存中检索存储命令以执行，绕过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-371">  If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="f31ac-372">查询计划缓存在同一 AppDomain 中的 ObjectContext 实例之间共享。</span><span class="sxs-lookup"><span data-stu-id="f31ac-372">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="f31ac-373">您无需保留 ObjectContext 实例即可从查询计划缓存中获益。</span><span class="sxs-lookup"><span data-stu-id="f31ac-373">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="f31ac-374">3.2.1 关于查询计划缓存的一些注释</span><span class="sxs-lookup"><span data-stu-id="f31ac-374">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="f31ac-375">查询计划缓存为所有查询类型共享：实体 SQL、LINQ 到实体和编译查询对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-375">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="f31ac-376">默认情况下，为实体 SQL 查询启用查询计划缓存，无论是通过实体命令还是通过对象查询执行。</span><span class="sxs-lookup"><span data-stu-id="f31ac-376">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="f31ac-377">默认情况下，在 .NET 4.5 上的实体框架中，在实体框架中，在实体框架中，LINQ 对实体查询也启用了它，</span><span class="sxs-lookup"><span data-stu-id="f31ac-377">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="f31ac-378">通过将启用计划缓存属性（在实体命令或对象查询上）设置为 false，可以禁用查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-378">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="f31ac-379">例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-379">For example:</span></span>
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
-   <span data-ttu-id="f31ac-380">对于参数化查询，更改参数的值仍将命中缓存的查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-380">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="f31ac-381">但是，更改参数的分面（例如，大小、精度或比例）将击中缓存中的不同条目。</span><span class="sxs-lookup"><span data-stu-id="f31ac-381">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="f31ac-382">使用实体 SQL 时，查询字符串是密钥的一部分。</span><span class="sxs-lookup"><span data-stu-id="f31ac-382">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="f31ac-383">更改查询将会导致不同的缓存条目，即使查询在功能上等效。</span><span class="sxs-lookup"><span data-stu-id="f31ac-383">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="f31ac-384">这包括对套管或空白的更改。</span><span class="sxs-lookup"><span data-stu-id="f31ac-384">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="f31ac-385">使用 LINQ 时，将处理查询以生成密钥的一部分。</span><span class="sxs-lookup"><span data-stu-id="f31ac-385">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="f31ac-386">因此，更改 LINQ 表达式将生成不同的键。</span><span class="sxs-lookup"><span data-stu-id="f31ac-386">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="f31ac-387">可能适用其他技术限制;有关详细信息，请参阅自动编译查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-387">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322-cache-eviction-algorithm"></a><span data-ttu-id="f31ac-388">3.2.2 缓存逐出算法</span><span class="sxs-lookup"><span data-stu-id="f31ac-388">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="f31ac-389">了解内部算法的工作原理将帮助您确定何时启用或禁用查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-389">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="f31ac-390">清理算法如下：</span><span class="sxs-lookup"><span data-stu-id="f31ac-390">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="f31ac-391">一旦缓存包含一组条目数 （800），我们将启动一个计时器，定期（每分钟一次）扫描缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-391">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="f31ac-392">在缓存扫描期间，条目会以 LFRU（最不频繁和最近使用）为基础从缓存中删除。</span><span class="sxs-lookup"><span data-stu-id="f31ac-392">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="f31ac-393">此算法在决定弹出哪些条目时，会考虑命中计数和年龄。</span><span class="sxs-lookup"><span data-stu-id="f31ac-393">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="f31ac-394">在每个缓存扫描结束时，缓存再次包含 800 个条目。</span><span class="sxs-lookup"><span data-stu-id="f31ac-394">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="f31ac-395">在确定要驱逐的条目时，所有缓存条目都一视同仁。</span><span class="sxs-lookup"><span data-stu-id="f31ac-395">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="f31ac-396">这意味着编译查询的存储命令与实体 SQL 查询的存储命令具有相同的逐出机会。</span><span class="sxs-lookup"><span data-stu-id="f31ac-396">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="f31ac-397">请注意，缓存驱逐计时器在缓存中有 800 个实体时被踢中，但缓存仅在启动此计时器 60 秒后被扫描。</span><span class="sxs-lookup"><span data-stu-id="f31ac-397">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="f31ac-398">这意味着，最多 60 秒，您的缓存可能会增长到相当大。</span><span class="sxs-lookup"><span data-stu-id="f31ac-398">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="f31ac-399">3.2.3 演示查询计划缓存性能的测试指标</span><span class="sxs-lookup"><span data-stu-id="f31ac-399">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="f31ac-400">为了演示查询计划缓存对应用程序性能的影响，我们执行了一个测试，其中针对 Navision 模型执行了许多实体 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-400">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="f31ac-401">有关 Navision 模型的说明以及已执行的查询类型，请参阅附录。</span><span class="sxs-lookup"><span data-stu-id="f31ac-401">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="f31ac-402">在此测试中，我们首先迭代查询列表，并执行每个查询一次以将其添加到缓存（如果启用了缓存）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-402">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="f31ac-403">此步骤是非时无刻的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-403">This step is untimed.</span></span> <span data-ttu-id="f31ac-404">接下来，我们将主线程休眠超过 60 秒，以便进行缓存扫描;最后，我们第二次遍通列表以执行缓存的查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-404">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="f31ac-405">此外，在执行每组查询之前刷新 SQL Server 计划缓存，以便我们获取的时间准确反映查询计划缓存提供的好处。</span><span class="sxs-lookup"><span data-stu-id="f31ac-405">Additionally, the SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-test-results"></a><span data-ttu-id="f31ac-406">3.2.3.1 测试结果</span><span class="sxs-lookup"><span data-stu-id="f31ac-406">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="f31ac-407">测试</span><span class="sxs-lookup"><span data-stu-id="f31ac-407">Test</span></span>                                                                   | <span data-ttu-id="f31ac-408">EF5 无缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-408">EF5 no cache</span></span> | <span data-ttu-id="f31ac-409">EF5 缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-409">EF5 cached</span></span> | <span data-ttu-id="f31ac-410">EF6 无缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-410">EF6 no cache</span></span> | <span data-ttu-id="f31ac-411">EF6 缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-411">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="f31ac-412">枚举所有 18723 个查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-412">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="f31ac-413">124</span><span class="sxs-lookup"><span data-stu-id="f31ac-413">124</span></span>          | <span data-ttu-id="f31ac-414">125.4</span><span class="sxs-lookup"><span data-stu-id="f31ac-414">125.4</span></span>      | <span data-ttu-id="f31ac-415">124.3</span><span class="sxs-lookup"><span data-stu-id="f31ac-415">124.3</span></span>        | <span data-ttu-id="f31ac-416">125.3</span><span class="sxs-lookup"><span data-stu-id="f31ac-416">125.3</span></span>      |
| <span data-ttu-id="f31ac-417">避免扫描（仅前 800 个查询，无论复杂性如何）</span><span class="sxs-lookup"><span data-stu-id="f31ac-417">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="f31ac-418">41.7</span><span class="sxs-lookup"><span data-stu-id="f31ac-418">41.7</span></span>         | <span data-ttu-id="f31ac-419">5.5</span><span class="sxs-lookup"><span data-stu-id="f31ac-419">5.5</span></span>        | <span data-ttu-id="f31ac-420">40.5</span><span class="sxs-lookup"><span data-stu-id="f31ac-420">40.5</span></span>         | <span data-ttu-id="f31ac-421">5.4</span><span class="sxs-lookup"><span data-stu-id="f31ac-421">5.4</span></span>        |
| <span data-ttu-id="f31ac-422">仅聚合子总计查询（总计 178 个 - 避免扫描）</span><span class="sxs-lookup"><span data-stu-id="f31ac-422">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="f31ac-423">39.5</span><span class="sxs-lookup"><span data-stu-id="f31ac-423">39.5</span></span>         | <span data-ttu-id="f31ac-424">4.5</span><span class="sxs-lookup"><span data-stu-id="f31ac-424">4.5</span></span>        | <span data-ttu-id="f31ac-425">38.1</span><span class="sxs-lookup"><span data-stu-id="f31ac-425">38.1</span></span>         | <span data-ttu-id="f31ac-426">4.6</span><span class="sxs-lookup"><span data-stu-id="f31ac-426">4.6</span></span>        |

<span data-ttu-id="f31ac-427">*所有时间（以秒为单位）。*</span><span class="sxs-lookup"><span data-stu-id="f31ac-427">*All times in seconds.*</span></span>

<span data-ttu-id="f31ac-428">道德 - 在执行大量不同的查询（例如，动态创建的查询）时，缓存不起作用，并且生成的缓存刷新可以使从计划缓存中从实际使用中受益最大的查询保持。</span><span class="sxs-lookup"><span data-stu-id="f31ac-428">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="f31ac-429">聚合子总计查询是测试的查询中最复杂的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-429">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="f31ac-430">正如预期的那样，查询越复杂，您从查询计划缓存中看到的好处就越大。</span><span class="sxs-lookup"><span data-stu-id="f31ac-430">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="f31ac-431">由于编译查询实际上是一个 LINQ 查询，其计划缓存，因此编译查询与等效实体 SQL 查询的比较应具有类似的结果。</span><span class="sxs-lookup"><span data-stu-id="f31ac-431">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="f31ac-432">事实上，如果应用具有大量动态实体 SQL 查询，则使用查询填充缓存也会有效地导致编译查询在从缓存中刷新时"去编译"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-432">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="f31ac-433">在这种情况下，可以通过禁用动态查询的缓存来确定编译查询的优先级，从而提高性能。</span><span class="sxs-lookup"><span data-stu-id="f31ac-433">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="f31ac-434">当然，更好的是重写应用以使用参数化查询而不是动态查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-434">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="f31ac-435">3.3 使用编译查询提高 LINQ 查询的性能</span><span class="sxs-lookup"><span data-stu-id="f31ac-435">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="f31ac-436">我们的测试表明，使用编译查询可以带来 7% 的好处比自动编译的 LINQ 查询;这意味着您将在实体框架堆栈中执行代码的时间减少 7%;这并不意味着您的应用程序将加快 7%。</span><span class="sxs-lookup"><span data-stu-id="f31ac-436">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="f31ac-437">一般来说，在 EF 5.0 中写入和维护编译查询对象的成本与收益相比可能不值得麻烦。</span><span class="sxs-lookup"><span data-stu-id="f31ac-437">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="f31ac-438">您的里程可能会有所不同，因此，如果您的项目需要额外的推送，请执行此选项。</span><span class="sxs-lookup"><span data-stu-id="f31ac-438">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="f31ac-439">请注意，编译查询仅与对象上下文派生模型兼容，并且与 DbContext 派生的模型不兼容。</span><span class="sxs-lookup"><span data-stu-id="f31ac-439">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="f31ac-440">有关创建和调用编译查询的详细信息，请参阅[编译查询（LINQ 到实体）。](https://msdn.microsoft.com/library/bb896297.aspx)</span><span class="sxs-lookup"><span data-stu-id="f31ac-440">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="f31ac-441">使用 Compilequery 时，您需要考虑两个注意事项，即使用静态实例的要求及其可组合性问题。</span><span class="sxs-lookup"><span data-stu-id="f31ac-441">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="f31ac-442">下面对这两个注意事项进行了深入解释。</span><span class="sxs-lookup"><span data-stu-id="f31ac-442">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-use-static-compiledquery-instances"></a><span data-ttu-id="f31ac-443">3.3.1 使用静态编译查询实例</span><span class="sxs-lookup"><span data-stu-id="f31ac-443">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="f31ac-444">由于编译 LINQ 查询是一个耗时的过程，因此我们不希望每次需要从数据库获取数据时都这样做。</span><span class="sxs-lookup"><span data-stu-id="f31ac-444">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="f31ac-445">编译查询实例允许您编译一次并运行多次，但您必须小心并采购，以便每次都重复使用同一个编译查询实例，而不是反复编译它。</span><span class="sxs-lookup"><span data-stu-id="f31ac-445">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="f31ac-446">使用静态成员来存储编译查询实例是必要的;否则，您不会看到任何好处。</span><span class="sxs-lookup"><span data-stu-id="f31ac-446">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="f31ac-447">例如，假设您的页面具有以下方法正文来处理显示所选类别的产品：</span><span class="sxs-lookup"><span data-stu-id="f31ac-447">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

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

<span data-ttu-id="f31ac-448">在这种情况下，每次调用方法时，您都会动态创建新的编译查询实例。</span><span class="sxs-lookup"><span data-stu-id="f31ac-448">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="f31ac-449">CompiledQuery 不会通过从查询计划缓存中检索存储命令来看到性能优势，而是每次创建新实例时都会通过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-449">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="f31ac-450">事实上，每次调用该方法时，您都会使用新的编译查询条目来污染查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-450">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="f31ac-451">相反，您希望创建已编译查询的静态实例，以便每次调用方法时都调用相同的已编译查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-451">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="f31ac-452">这样做的一种方法是添加编译查询实例作为对象上下文的成员。</span><span class="sxs-lookup"><span data-stu-id="f31ac-452">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span><span data-ttu-id="f31ac-453">然后，您可以通过帮助器方法访问编译查询，使事情变得更干净一些：</span><span class="sxs-lookup"><span data-stu-id="f31ac-453">  You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

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

<span data-ttu-id="f31ac-454">此帮助器方法将调用如下：</span><span class="sxs-lookup"><span data-stu-id="f31ac-454">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a><span data-ttu-id="f31ac-455">3.3.2 通过编译查询进行组合</span><span class="sxs-lookup"><span data-stu-id="f31ac-455">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="f31ac-456">通过任何 LINQ 查询进行撰写的能力非常有用;为此，只需在*IQuery（）* 或*Count（）* 之后调用方法。</span><span class="sxs-lookup"><span data-stu-id="f31ac-456">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="f31ac-457">但是，这样做实质上会返回一个新的可查询对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-457">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="f31ac-458">虽然在技术上没有什么可以阻止您通过 CompiledQuery 进行创作，但这样做将导致生成需要再次通过计划编译器的新 IQuery 对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-458">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="f31ac-459">某些组件将使用组合的 IQuery 对象来实现高级功能。</span><span class="sxs-lookup"><span data-stu-id="f31ac-459">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="f31ac-460">例如，ASP。NET 的 GridView 可以通过 SelectMethod 属性将数据绑定到 IQuery 对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-460">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="f31ac-461">然后，GridView 将在此可查询对象上进行组合，以允许对数据模型进行排序和分页。</span><span class="sxs-lookup"><span data-stu-id="f31ac-461">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="f31ac-462">如您所见，使用 GridView 的编译查询不会命中已编译的查询，而是生成新的自动编译查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-462">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="f31ac-463">在一个可能遇到这种情况的地方是向查询添加累进筛选器时。</span><span class="sxs-lookup"><span data-stu-id="f31ac-463">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="f31ac-464">例如，假设您有一个客户页面，该页面包含多个可选筛选器的下拉列表（例如，国家/地区订单和订单计数）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-464">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="f31ac-465">您可以在编译查询的 IQuery 可查询结果上编写这些筛选器，但这样做将导致每次执行计划编译器时新查询都会经过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-465">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

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

 <span data-ttu-id="f31ac-466">为了避免此重新编译，您可以重写编译查询，以考虑可能的筛选器：</span><span class="sxs-lookup"><span data-stu-id="f31ac-466">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="f31ac-467">将在 UI 中调用，例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-467">Which would be invoked in the UI like:</span></span>

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

 <span data-ttu-id="f31ac-468">此处的权衡是生成的存储命令将始终具有带有空检查的筛选器，但对于数据库服务器来说，优化这些筛选器应该相当简单：</span><span class="sxs-lookup"><span data-stu-id="f31ac-468">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="f31ac-469">3.4 元数据缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-469">3.4 Metadata caching</span></span>

<span data-ttu-id="f31ac-470">实体框架还支持元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-470">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="f31ac-471">这实质上是跨不同连接到同一模型的类型信息和类型到数据库映射信息。</span><span class="sxs-lookup"><span data-stu-id="f31ac-471">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="f31ac-472">元数据缓存对于每个 AppDomain 是唯一的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-472">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="f31ac-473">3.4.1 元数据缓存算法</span><span class="sxs-lookup"><span data-stu-id="f31ac-473">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="f31ac-474">模型的元数据信息存储在每个实体连接的项目集合中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-474">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="f31ac-475">作为旁注，模型的不同部分有不同的 ItemCollection 对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-475">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="f31ac-476">例如，StoreItem集合包含有关数据库模型的信息;因此，它包含有关数据库模型的信息。ObjectItemCollection 包含有关数据模型的信息;EdmItemCollection 包含有关概念模型的信息。</span><span class="sxs-lookup"><span data-stu-id="f31ac-476">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="f31ac-477">如果两个连接使用相同的连接字符串，它们将共享相同的 ItemCollection 实例。</span><span class="sxs-lookup"><span data-stu-id="f31ac-477">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="f31ac-478">功能上等效，但文本上不同的连接字符串可能会导致不同的元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-478">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="f31ac-479">我们使用令牌化连接字符串，因此只需更改令牌的顺序即可生成共享元数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-479">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="f31ac-480">但是，在标记化后，两个在功能上看起来相同的连接字符串可能不会被计算为相同。</span><span class="sxs-lookup"><span data-stu-id="f31ac-480">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="f31ac-481">定期检查项目集合以表示使用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-481">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="f31ac-482">如果确定最近未访问工作区，则将在下一次缓存扫描时将其标记为清理。</span><span class="sxs-lookup"><span data-stu-id="f31ac-482">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="f31ac-483">仅仅创建实体连接就会导致创建元数据缓存（尽管在打开连接之前不会初始化其中的项集合）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-483">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="f31ac-484">此工作区将保留在内存中，直到缓存算法确定它不"正在使用"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-484">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="f31ac-485">客户咨询团队撰写了一篇博客文章，其中描述了对 ItemCollection 的引用，以避免在使用大型模型时"弃用"： \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>。</span><span class="sxs-lookup"><span data-stu-id="f31ac-485">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="f31ac-486">3.4.2 元数据缓存和查询计划缓存之间的关系</span><span class="sxs-lookup"><span data-stu-id="f31ac-486">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="f31ac-487">查询计划缓存实例位于元数据工作区的存储类型的项目集合中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-487">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="f31ac-488">这意味着缓存存储命令将用于查询使用给定元数据工作区实例化的任何上下文。</span><span class="sxs-lookup"><span data-stu-id="f31ac-488">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="f31ac-489">这也意味着，如果您有两个连接字符串略有不同，在标记后不匹配，则将具有不同的查询计划缓存实例。</span><span class="sxs-lookup"><span data-stu-id="f31ac-489">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="f31ac-490">3.5 结果缓存</span><span class="sxs-lookup"><span data-stu-id="f31ac-490">3.5 Results caching</span></span>

<span data-ttu-id="f31ac-491">使用结果缓存（也称为"第二级缓存"），您将查询的结果保存在本地缓存中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-491">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="f31ac-492">发出查询时，首先在查询存储之前，先查看结果是否在本地可用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-492">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="f31ac-493">虽然实体框架并不直接支持结果缓存，但可以使用包装提供程序添加第二级缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-493">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="f31ac-494">一个带有第二级缓存的包装提供程序的示例是 Alachisoft[基于 NCache 的实体框架第二级缓存](https://www.alachisoft.com/ncache/entity-framework.html)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-494">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](https://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="f31ac-495">二级缓存的实现是在 LINQ 表达式计算（和 fee）并从第一级缓存中计算或检索查询执行计划之后发生的注入功能。</span><span class="sxs-lookup"><span data-stu-id="f31ac-495">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="f31ac-496">然后，第二级缓存将仅存储原始数据库结果，因此具体化管道在之后仍执行。</span><span class="sxs-lookup"><span data-stu-id="f31ac-496">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="f31ac-497">3.5.1 使用包装提供程序进行结果缓存的其他引用</span><span class="sxs-lookup"><span data-stu-id="f31ac-497">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="f31ac-498">Julie Lerman 撰写了一篇"实体框架和 Windows Azure 中的二级缓存"MSDN 文章，其中包括如何更新示例包装提供程序以使用 Windows Server AppFabric 缓存：[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="f31ac-498">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="f31ac-499">如果您正在使用实体框架 5，则团队博客有一篇文章，说明如何使用实体框架 5 的缓存提供程序运行内容： \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>。</span><span class="sxs-lookup"><span data-stu-id="f31ac-499">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>.</span></span> <span data-ttu-id="f31ac-500">它还包括 T4 模板，以帮助自动将二级缓存添加到项目中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-500">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="f31ac-501">4 自动编译查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-501">4 Autocompiled Queries</span></span>

<span data-ttu-id="f31ac-502">使用实体框架对数据库发出查询时，它必须经过一系列步骤才能实际实现结果;其中一个步骤是查询编译。</span><span class="sxs-lookup"><span data-stu-id="f31ac-502">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="f31ac-503">实体 SQL 查询在自动缓存时具有良好的性能，因此，在进行同一查询的第二次或第三次时，它可以跳过计划编译器并使用缓存的计划。</span><span class="sxs-lookup"><span data-stu-id="f31ac-503">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="f31ac-504">实体框架 5 还引入了 LINQ 到实体查询的自动缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-504">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="f31ac-505">在以往版本的实体框架中，创建编译查询以加快性能是一种常见做法，因为这将使 LINQ 到实体查询可缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-505">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="f31ac-506">由于缓存现在无需使用编译查询即可自动完成，因此我们将此功能称为"自动编译查询"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-506">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="f31ac-507">有关查询计划缓存及其机制的详细信息，请参阅查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-507">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="f31ac-508">实体框架检测查询何时需要重新编译，并且在调用查询时（即使以前已编译）也执行。</span><span class="sxs-lookup"><span data-stu-id="f31ac-508">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="f31ac-509">导致重新编译查询的常见条件是：</span><span class="sxs-lookup"><span data-stu-id="f31ac-509">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="f31ac-510">更改与您的查询关联的合并选项。</span><span class="sxs-lookup"><span data-stu-id="f31ac-510">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="f31ac-511">将不会使用缓存的查询，而是计划编译器将再次运行，并缓存新创建的计划。</span><span class="sxs-lookup"><span data-stu-id="f31ac-511">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="f31ac-512">更改上下文选项的值。</span><span class="sxs-lookup"><span data-stu-id="f31ac-512">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="f31ac-513">您将获得与更改合并选项相同的效果。</span><span class="sxs-lookup"><span data-stu-id="f31ac-513">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="f31ac-514">其他条件可能会阻止查询使用缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-514">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="f31ac-515">常见示例包括：</span><span class="sxs-lookup"><span data-stu-id="f31ac-515">Common examples are:</span></span>

-   <span data-ttu-id="f31ac-516">使用 IE500T&lt;&gt;。包含&lt;&gt;（T 值）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-516">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="f31ac-517">使用生成具有常量的查询的函数。</span><span class="sxs-lookup"><span data-stu-id="f31ac-517">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="f31ac-518">使用非映射对象的属性。</span><span class="sxs-lookup"><span data-stu-id="f31ac-518">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="f31ac-519">将查询链接到需要重新编译的另一个查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-519">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="f31ac-520">4.1 使用 IE500T&lt;。&gt;包含&lt;T（T&gt;值）</span><span class="sxs-lookup"><span data-stu-id="f31ac-520">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="f31ac-521">实体框架不缓存调用 IE55T&lt;的&gt;查询。包含&lt;针对内存&gt;中集合的 T（T 值），因为集合的值被视为易失性。</span><span class="sxs-lookup"><span data-stu-id="f31ac-521">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="f31ac-522">以下示例查询将不会缓存，因此计划编译器将始终对其进行处理：</span><span class="sxs-lookup"><span data-stu-id="f31ac-522">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

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

<span data-ttu-id="f31ac-523">请注意，执行"包含"的 IE55 的大小的确定查询的编译速度或速度。</span><span class="sxs-lookup"><span data-stu-id="f31ac-523">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="f31ac-524">使用大型集合（如上例中所示的集合）时，性能可能会受到显著影响。</span><span class="sxs-lookup"><span data-stu-id="f31ac-524">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="f31ac-525">实体框架 6 包含对 IE50t&lt;T&gt;方式的优化。在执行查询&lt;时&gt;，包含 T（T 值）工作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-525">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="f31ac-526">生成的 SQL 代码生成速度要快得多，而且可读性更强，在大多数情况下，它还在服务器中执行得更快。</span><span class="sxs-lookup"><span data-stu-id="f31ac-526">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="f31ac-527">4.2 使用生成具有常量的查询的函数</span><span class="sxs-lookup"><span data-stu-id="f31ac-527">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="f31ac-528">Skip（）、Take（）、包含（）和 DefautIfEmpty（） LINQ 运算符不生成具有参数的 SQL 查询，而是将传递给它们的值作为常量传递给它们。</span><span class="sxs-lookup"><span data-stu-id="f31ac-528">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="f31ac-529">因此，否则可能相同的查询最终会污染 EF 堆栈和数据库服务器上的查询计划缓存，除非在后续查询执行中使用相同的常量，否则不会重新使用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-529">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="f31ac-530">例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-530">For example:</span></span>

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

<span data-ttu-id="f31ac-531">在此示例中，每次执行此查询时，使用 ID 的不同值，查询将编译为新计划。</span><span class="sxs-lookup"><span data-stu-id="f31ac-531">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="f31ac-532">在进行分页时，尤其要注意使用跳过和取。</span><span class="sxs-lookup"><span data-stu-id="f31ac-532">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="f31ac-533">在 EF6 中，这些方法具有 lambda 重载，可有效地使缓存的查询计划可重用，因为 EF 可以捕获传递给这些方法的变量并将其转换为 SQL 参数。</span><span class="sxs-lookup"><span data-stu-id="f31ac-533">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="f31ac-534">这也有助于保持缓存更干净，否则每个查询使用不同的常量为 Skip 和 Take 将获得其自己的查询计划缓存条目。</span><span class="sxs-lookup"><span data-stu-id="f31ac-534">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="f31ac-535">请考虑以下代码，该代码不理想，但仅用于举例说明此类查询：</span><span class="sxs-lookup"><span data-stu-id="f31ac-535">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="f31ac-536">此代码的更快版本将涉及调用使用 lambda 的跳过：</span><span class="sxs-lookup"><span data-stu-id="f31ac-536">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="f31ac-537">第二个代码段的运行速度可能加快 11%，因为每次运行查询时都使用相同的查询计划，从而节省了 CPU 时间并避免污染查询缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-537">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="f31ac-538">此外，由于 Skip 的参数位于闭包中，因此代码现在可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="f31ac-538">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="f31ac-539">4.3 使用非映射对象的属性</span><span class="sxs-lookup"><span data-stu-id="f31ac-539">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="f31ac-540">当查询使用非映射对象类型的属性作为参数时，查询将不会被缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-540">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="f31ac-541">例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-541">For example:</span></span>

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

<span data-ttu-id="f31ac-542">在此示例中，假设类非映射类型不是实体模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="f31ac-542">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="f31ac-543">可以轻松地更改此查询，以不使用非映射类型，而是使用本地变量作为查询的参数：</span><span class="sxs-lookup"><span data-stu-id="f31ac-543">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

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

<span data-ttu-id="f31ac-544">在这种情况下，查询将能够获得缓存，并从查询计划缓存中获益。</span><span class="sxs-lookup"><span data-stu-id="f31ac-544">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="f31ac-545">4.4 链接到需要重新编译的查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-545">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="f31ac-546">按照上述相同的示例，如果您有第二个依赖于需要重新编译的查询，则整个第二个查询也将重新编译。</span><span class="sxs-lookup"><span data-stu-id="f31ac-546">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="f31ac-547">下面是一个示例来说明此方案：</span><span class="sxs-lookup"><span data-stu-id="f31ac-547">Here’s an example to illustrate this scenario:</span></span>

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

<span data-ttu-id="f31ac-548">该示例是泛型的，但它说明了链接到第一查询如何导致第二查询无法缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-548">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="f31ac-549">如果第一查询不是需要重新编译的查询，则第二查询将被缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-549">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="f31ac-550">5 无跟踪查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-550">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="f31ac-551">5.1 禁用更改跟踪以减少状态管理开销</span><span class="sxs-lookup"><span data-stu-id="f31ac-551">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="f31ac-552">如果您处于只读方案，并且希望避免将对象加载到 ObjectStateManager 中的开销，则可以发出"无跟踪"查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-552">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span><span data-ttu-id="f31ac-553">可以在查询级别禁用更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="f31ac-553">  Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="f31ac-554">但请注意，通过禁用更改跟踪，您实际上正在关闭对象缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-554">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="f31ac-555">当您查询实体时，我们不能通过从 ObjectStateManager 提取以前具体化的查询结果来跳过具体化。</span><span class="sxs-lookup"><span data-stu-id="f31ac-555">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="f31ac-556">如果在同一上下文中反复查询相同的实体，则实际上可能会看到启用更改跟踪的性能优势。</span><span class="sxs-lookup"><span data-stu-id="f31ac-556">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="f31ac-557">使用 ObjectContext 查询时，ObjectQuery 和 ObjectSet 实例将在设置合并选项后记住它，并且在它们上组成的查询将继承父查询的有效 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="f31ac-557">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="f31ac-558">使用 DbContext 时，可以通过在 DbSet 上调用 AsNo 跟踪（） 修改器来禁用跟踪。</span><span class="sxs-lookup"><span data-stu-id="f31ac-558">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="f31ac-559">5.1.1 禁用使用 DbContext 时查询的更改跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-559">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="f31ac-560">通过将调用链接到查询中的 AsNoTrack（） 方法，可以将查询模式切换到 NoTrack。</span><span class="sxs-lookup"><span data-stu-id="f31ac-560">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="f31ac-561">与对象查询不同，DbContext API 中的 DbSet 和 DbQuery 类没有 MergeOption 的可变属性。</span><span class="sxs-lookup"><span data-stu-id="f31ac-561">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="f31ac-562">5.1.2 使用 ObjectContext 禁用查询级别的更改跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-562">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="f31ac-563">5.1.3 使用 ObjectContext 禁用整个实体集的更改跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-563">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="f31ac-564">5.2 测试指标，演示 NoTrack 查询的性能优势</span><span class="sxs-lookup"><span data-stu-id="f31ac-564">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="f31ac-565">在此测试中，我们将通过将跟踪与 Navision 模型的 NoTrack 查询进行比较来查看填充 ObjectStateManager 的成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-565">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="f31ac-566">有关 Navision 模型的说明以及已执行的查询类型，请参阅附录。</span><span class="sxs-lookup"><span data-stu-id="f31ac-566">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="f31ac-567">在此测试中，我们迭代查询列表并执行每个查询一次。</span><span class="sxs-lookup"><span data-stu-id="f31ac-567">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="f31ac-568">我们运行了两个测试变体，一次使用 NoTrack 查询，另一次使用默认合并选项"仅追加"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-568">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="f31ac-569">我们运行每个变体 3 次，并获取运行的平均值。</span><span class="sxs-lookup"><span data-stu-id="f31ac-569">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="f31ac-570">在测试之间，我们清除 SQL Server 上的查询缓存，并通过运行以下命令来收缩 tempdb：</span><span class="sxs-lookup"><span data-stu-id="f31ac-570">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="f31ac-571">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="f31ac-571">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="f31ac-572">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="f31ac-572">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="f31ac-573">DBCC SHRINK 数据库（tempdb， 0）</span><span class="sxs-lookup"><span data-stu-id="f31ac-573">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="f31ac-574">测试结果，3 次以上的中值：</span><span class="sxs-lookup"><span data-stu-id="f31ac-574">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="f31ac-575">无跟踪 + 工作集</span><span class="sxs-lookup"><span data-stu-id="f31ac-575">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="f31ac-576">无跟踪 + 时间</span><span class="sxs-lookup"><span data-stu-id="f31ac-576">NO TRACKING – TIME</span></span> | <span data-ttu-id="f31ac-577">仅追加 = 工作集</span><span class="sxs-lookup"><span data-stu-id="f31ac-577">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="f31ac-578">仅追加 = 时间</span><span class="sxs-lookup"><span data-stu-id="f31ac-578">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="f31ac-579">**实体框架 5**</span><span class="sxs-lookup"><span data-stu-id="f31ac-579">**Entity Framework 5**</span></span> | <span data-ttu-id="f31ac-580">460361728</span><span class="sxs-lookup"><span data-stu-id="f31ac-580">460361728</span></span>                 | <span data-ttu-id="f31ac-581">1163536 毫秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-581">1163536 ms</span></span>         | <span data-ttu-id="f31ac-582">596545536</span><span class="sxs-lookup"><span data-stu-id="f31ac-582">596545536</span></span>                 | <span data-ttu-id="f31ac-583">1273042 毫秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-583">1273042 ms</span></span>         |
| <span data-ttu-id="f31ac-584">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="f31ac-584">**Entity Framework 6**</span></span> | <span data-ttu-id="f31ac-585">647127040</span><span class="sxs-lookup"><span data-stu-id="f31ac-585">647127040</span></span>                 | <span data-ttu-id="f31ac-586">190228 毫秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-586">190228 ms</span></span>          | <span data-ttu-id="f31ac-587">832798720</span><span class="sxs-lookup"><span data-stu-id="f31ac-587">832798720</span></span>                 | <span data-ttu-id="f31ac-588">195521 毫秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-588">195521 ms</span></span>          |

<span data-ttu-id="f31ac-589">实体框架 5 在运行结束时的内存占用量将比实体框架 6 少。</span><span class="sxs-lookup"><span data-stu-id="f31ac-589">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="f31ac-590">实体框架 6 使用的额外内存是启用新功能和更好性能的额外内存结构和代码的结果。</span><span class="sxs-lookup"><span data-stu-id="f31ac-590">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="f31ac-591">使用 ObjectStateManager 时，内存占用空间也有明显差异。</span><span class="sxs-lookup"><span data-stu-id="f31ac-591">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="f31ac-592">实体框架 5 在跟踪我们从数据库中实现的所有实体时，其占用空间增加了 30%。</span><span class="sxs-lookup"><span data-stu-id="f31ac-592">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="f31ac-593">实体框架 6 这样做时占用空间增加了 28%。</span><span class="sxs-lookup"><span data-stu-id="f31ac-593">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="f31ac-594">在时间方面，实体框架 6 在此测试中以较大优势优于实体框架 5。</span><span class="sxs-lookup"><span data-stu-id="f31ac-594">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="f31ac-595">实体框架 6 在实体框架 5 消耗的时间大约 16% 的时间内完成了测试。</span><span class="sxs-lookup"><span data-stu-id="f31ac-595">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="f31ac-596">此外，使用 ObjectStateManager 时，实体框架 5 需要 9% 的时间才能完成。</span><span class="sxs-lookup"><span data-stu-id="f31ac-596">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="f31ac-597">相比之下，实体框架 6 在使用 ObjectStateManager 时使用的时间要长 3%。</span><span class="sxs-lookup"><span data-stu-id="f31ac-597">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="f31ac-598">6 查询执行选项</span><span class="sxs-lookup"><span data-stu-id="f31ac-598">6 Query Execution Options</span></span>

<span data-ttu-id="f31ac-599">实体框架提供了几种不同的查询方法。</span><span class="sxs-lookup"><span data-stu-id="f31ac-599">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="f31ac-600">我们将介绍以下选项，比较每个选项的优缺点，并检查它们的性能特征：</span><span class="sxs-lookup"><span data-stu-id="f31ac-600">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="f31ac-601">林Q 到实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-601">LINQ to Entities.</span></span>
-   <span data-ttu-id="f31ac-602">没有跟踪 LINQ 到实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-602">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="f31ac-603">对象查询的实体 SQL。</span><span class="sxs-lookup"><span data-stu-id="f31ac-603">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="f31ac-604">实体命令的实体 SQL。</span><span class="sxs-lookup"><span data-stu-id="f31ac-604">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="f31ac-605">执行存储查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-605">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="f31ac-606">SqlQuery。</span><span class="sxs-lookup"><span data-stu-id="f31ac-606">SqlQuery.</span></span>
-   <span data-ttu-id="f31ac-607">编译查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-607">CompiledQuery.</span></span>

### <a name="61-linq-to-entities-queries"></a><span data-ttu-id="f31ac-608">6.1 LINQ 到实体查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-608">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="f31ac-609">**优点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-609">**Pros**</span></span>

-   <span data-ttu-id="f31ac-610">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-610">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="f31ac-611">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-611">Fully materialized objects.</span></span>
-   <span data-ttu-id="f31ac-612">最简单的编写语法内置于编程语言中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-612">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="f31ac-613">性能好。</span><span class="sxs-lookup"><span data-stu-id="f31ac-613">Good performance.</span></span>

<span data-ttu-id="f31ac-614">**缺点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-614">**Cons**</span></span>

-   <span data-ttu-id="f31ac-615">某些技术限制，例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-615">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="f31ac-616">对外部 JOIN 查询使用默认IfEmpty的模式会导致比实体 SQL 中简单的外部 JOIN 语句更复杂的查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-616">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="f31ac-617">您仍然不能将 LIKE 用于常规模式匹配。</span><span class="sxs-lookup"><span data-stu-id="f31ac-617">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-no-tracking-linq-to-entities-queries"></a><span data-ttu-id="f31ac-618">6.2 没有跟踪 LINQ 到实体查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-618">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="f31ac-619">当上下文派生对象上下文时：</span><span class="sxs-lookup"><span data-stu-id="f31ac-619">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="f31ac-620">当上下文派生 DbContext 时：</span><span class="sxs-lookup"><span data-stu-id="f31ac-620">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="f31ac-621">**优点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-621">**Pros**</span></span>

-   <span data-ttu-id="f31ac-622">与常规 LINQ 查询性能提高。</span><span class="sxs-lookup"><span data-stu-id="f31ac-622">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="f31ac-623">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-623">Fully materialized objects.</span></span>
-   <span data-ttu-id="f31ac-624">最简单的编写语法内置于编程语言中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-624">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="f31ac-625">**缺点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-625">**Cons**</span></span>

-   <span data-ttu-id="f31ac-626">不适合 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-626">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="f31ac-627">某些技术限制，例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-627">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="f31ac-628">对外部 JOIN 查询使用默认IfEmpty的模式会导致比实体 SQL 中简单的外部 JOIN 语句更复杂的查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-628">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="f31ac-629">您仍然不能将 LIKE 用于常规模式匹配。</span><span class="sxs-lookup"><span data-stu-id="f31ac-629">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="f31ac-630">请注意，即使未指定 NoTracking，也不会跟踪项目标量属性的查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-630">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="f31ac-631">例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-631">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="f31ac-632">此特定查询未显式指定为 NoTracking，但由于它未具体化对象状态管理器已知的类型，因此不会跟踪具体化结果。</span><span class="sxs-lookup"><span data-stu-id="f31ac-632">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-entity-sql-over-an-objectquery"></a><span data-ttu-id="f31ac-633">6.3 对象查询的实体 SQL</span><span class="sxs-lookup"><span data-stu-id="f31ac-633">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="f31ac-634">**优点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-634">**Pros**</span></span>

-   <span data-ttu-id="f31ac-635">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-635">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="f31ac-636">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-636">Fully materialized objects.</span></span>
-   <span data-ttu-id="f31ac-637">支持查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="f31ac-637">Supports query plan caching.</span></span>

<span data-ttu-id="f31ac-638">**缺点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-638">**Cons**</span></span>

-   <span data-ttu-id="f31ac-639">涉及文本查询字符串，与内置于语言中的查询构造相比，这些字符串更容易出现用户错误。</span><span class="sxs-lookup"><span data-stu-id="f31ac-639">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-entity-sql-over-an-entity-command"></a><span data-ttu-id="f31ac-640">6.4 实体命令的实体 SQL</span><span class="sxs-lookup"><span data-stu-id="f31ac-640">6.4       Entity SQL over an Entity Command</span></span>

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

<span data-ttu-id="f31ac-641">**优点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-641">**Pros**</span></span>

-   <span data-ttu-id="f31ac-642">支持 .NET 4.0 中的查询计划缓存（.NET 4.5 中的所有其他查询类型都支持计划缓存）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-642">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="f31ac-643">**缺点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-643">**Cons**</span></span>

-   <span data-ttu-id="f31ac-644">涉及文本查询字符串，与内置于语言中的查询构造相比，这些字符串更容易出现用户错误。</span><span class="sxs-lookup"><span data-stu-id="f31ac-644">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="f31ac-645">不适合 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-645">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="f31ac-646">结果不会自动具体化，必须从数据读取器读取。</span><span class="sxs-lookup"><span data-stu-id="f31ac-646">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-sqlquery-and-executestorequery"></a><span data-ttu-id="f31ac-647">6.5 SqlQuery 和执行存储查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-647">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="f31ac-648">数据库上的 SqlQuery：</span><span class="sxs-lookup"><span data-stu-id="f31ac-648">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="f31ac-649">DbSet 上的 SqlQuery：</span><span class="sxs-lookup"><span data-stu-id="f31ac-649">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="f31ac-650">ExecyteStore查询：</span><span class="sxs-lookup"><span data-stu-id="f31ac-650">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="f31ac-651">**优点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-651">**Pros**</span></span>

-   <span data-ttu-id="f31ac-652">通常性能最快，因为计划编译器是绕过的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-652">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="f31ac-653">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-653">Fully materialized objects.</span></span>
-   <span data-ttu-id="f31ac-654">适用于从 DbSet 使用的 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-654">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="f31ac-655">**缺点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-655">**Cons**</span></span>

-   <span data-ttu-id="f31ac-656">查询是文本和容易出错的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-656">Query is textual and error prone.</span></span>
-   <span data-ttu-id="f31ac-657">通过使用存储语义而不是概念语义，查询绑定到特定的后端。</span><span class="sxs-lookup"><span data-stu-id="f31ac-657">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="f31ac-658">存在继承时，手工制作的查询需要考虑所请求类型的映射条件。</span><span class="sxs-lookup"><span data-stu-id="f31ac-658">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-compiledquery"></a><span data-ttu-id="f31ac-659">6.6 编译查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-659">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="f31ac-660">**优点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-660">**Pros**</span></span>

-   <span data-ttu-id="f31ac-661">与常规 LINQ 查询（LINQ 查询）提供高达 7% 的性能改进。</span><span class="sxs-lookup"><span data-stu-id="f31ac-661">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="f31ac-662">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-662">Fully materialized objects.</span></span>
-   <span data-ttu-id="f31ac-663">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-663">Suitable for CUD operations.</span></span>

<span data-ttu-id="f31ac-664">**缺点**</span><span class="sxs-lookup"><span data-stu-id="f31ac-664">**Cons**</span></span>

-   <span data-ttu-id="f31ac-665">增加了复杂性和编程开销。</span><span class="sxs-lookup"><span data-stu-id="f31ac-665">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="f31ac-666">在编译的查询上作曲时，性能改进将丢失。</span><span class="sxs-lookup"><span data-stu-id="f31ac-666">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="f31ac-667">某些 LINQ 查询不能编写为编译查询 - 例如，匿名类型的投影。</span><span class="sxs-lookup"><span data-stu-id="f31ac-667">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-performance-comparison-of-different-query-options"></a><span data-ttu-id="f31ac-668">6.7 不同查询选项的性能比较</span><span class="sxs-lookup"><span data-stu-id="f31ac-668">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="f31ac-669">在未进行时间创建时的简单微观基准测试已过。</span><span class="sxs-lookup"><span data-stu-id="f31ac-669">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="f31ac-670">我们在受控环境中对一组非缓存实体进行了 5000 次查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-670">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="f31ac-671">这些数字应该带有警告：它们并不反映应用程序产生的实际数字，而是非常准确地测量了在比较 Apple 到 apple 的不同查询选项时存在的性能差异量，不包括创建新上下文的成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-671">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="f31ac-672">EF</span><span class="sxs-lookup"><span data-stu-id="f31ac-672">EF</span></span>  | <span data-ttu-id="f31ac-673">测试</span><span class="sxs-lookup"><span data-stu-id="f31ac-673">Test</span></span>                                 | <span data-ttu-id="f31ac-674">时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="f31ac-674">Time (ms)</span></span> | <span data-ttu-id="f31ac-675">内存</span><span class="sxs-lookup"><span data-stu-id="f31ac-675">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="f31ac-676">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-676">EF5</span></span> | <span data-ttu-id="f31ac-677">对象上下文 ESQL</span><span class="sxs-lookup"><span data-stu-id="f31ac-677">ObjectContext ESQL</span></span>                   | <span data-ttu-id="f31ac-678">2414</span><span class="sxs-lookup"><span data-stu-id="f31ac-678">2414</span></span>      | <span data-ttu-id="f31ac-679">38801408</span><span class="sxs-lookup"><span data-stu-id="f31ac-679">38801408</span></span> |
| <span data-ttu-id="f31ac-680">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-680">EF5</span></span> | <span data-ttu-id="f31ac-681">对象上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-681">ObjectContext Linq Query</span></span>             | <span data-ttu-id="f31ac-682">2692</span><span class="sxs-lookup"><span data-stu-id="f31ac-682">2692</span></span>      | <span data-ttu-id="f31ac-683">38277120</span><span class="sxs-lookup"><span data-stu-id="f31ac-683">38277120</span></span> |
| <span data-ttu-id="f31ac-684">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-684">EF5</span></span> | <span data-ttu-id="f31ac-685">数据库上下文林克查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-685">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="f31ac-686">2818</span><span class="sxs-lookup"><span data-stu-id="f31ac-686">2818</span></span>      | <span data-ttu-id="f31ac-687">41840640</span><span class="sxs-lookup"><span data-stu-id="f31ac-687">41840640</span></span> |
| <span data-ttu-id="f31ac-688">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-688">EF5</span></span> | <span data-ttu-id="f31ac-689">数据库上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-689">DbContext Linq Query</span></span>                 | <span data-ttu-id="f31ac-690">2930</span><span class="sxs-lookup"><span data-stu-id="f31ac-690">2930</span></span>      | <span data-ttu-id="f31ac-691">41771008</span><span class="sxs-lookup"><span data-stu-id="f31ac-691">41771008</span></span> |
| <span data-ttu-id="f31ac-692">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-692">EF5</span></span> | <span data-ttu-id="f31ac-693">对象上下文林q查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-693">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="f31ac-694">3013</span><span class="sxs-lookup"><span data-stu-id="f31ac-694">3013</span></span>      | <span data-ttu-id="f31ac-695">38412288</span><span class="sxs-lookup"><span data-stu-id="f31ac-695">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="f31ac-696">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-696">EF6</span></span> | <span data-ttu-id="f31ac-697">对象上下文 ESQL</span><span class="sxs-lookup"><span data-stu-id="f31ac-697">ObjectContext ESQL</span></span>                   | <span data-ttu-id="f31ac-698">2059</span><span class="sxs-lookup"><span data-stu-id="f31ac-698">2059</span></span>      | <span data-ttu-id="f31ac-699">46039040</span><span class="sxs-lookup"><span data-stu-id="f31ac-699">46039040</span></span> |
| <span data-ttu-id="f31ac-700">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-700">EF6</span></span> | <span data-ttu-id="f31ac-701">对象上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-701">ObjectContext Linq Query</span></span>             | <span data-ttu-id="f31ac-702">3074</span><span class="sxs-lookup"><span data-stu-id="f31ac-702">3074</span></span>      | <span data-ttu-id="f31ac-703">45248512</span><span class="sxs-lookup"><span data-stu-id="f31ac-703">45248512</span></span> |
| <span data-ttu-id="f31ac-704">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-704">EF6</span></span> | <span data-ttu-id="f31ac-705">数据库上下文林克查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-705">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="f31ac-706">3125</span><span class="sxs-lookup"><span data-stu-id="f31ac-706">3125</span></span>      | <span data-ttu-id="f31ac-707">47575040</span><span class="sxs-lookup"><span data-stu-id="f31ac-707">47575040</span></span> |
| <span data-ttu-id="f31ac-708">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-708">EF6</span></span> | <span data-ttu-id="f31ac-709">数据库上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-709">DbContext Linq Query</span></span>                 | <span data-ttu-id="f31ac-710">3420</span><span class="sxs-lookup"><span data-stu-id="f31ac-710">3420</span></span>      | <span data-ttu-id="f31ac-711">47652864</span><span class="sxs-lookup"><span data-stu-id="f31ac-711">47652864</span></span> |
| <span data-ttu-id="f31ac-712">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-712">EF6</span></span> | <span data-ttu-id="f31ac-713">对象上下文林q查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-713">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="f31ac-714">3593</span><span class="sxs-lookup"><span data-stu-id="f31ac-714">3593</span></span>      | <span data-ttu-id="f31ac-715">45260800</span><span class="sxs-lookup"><span data-stu-id="f31ac-715">45260800</span></span> |

![EF5 微型基准，5000 个暖迭代](~/ef6/media/ef5micro5000warm.png)

![EF6 微型基准，5000 个暖迭代](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="f31ac-718">微基准对代码中的小更改非常敏感。</span><span class="sxs-lookup"><span data-stu-id="f31ac-718">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="f31ac-719">在这种情况下，实体框架 5 和实体框架 6 的成本之间的差额是由于[增加了拦截](~/ef6/fundamentals/logging-and-interception.md)和[事务性改进](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-719">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="f31ac-720">然而，这些微观基准数字是实体框架所做操作的一个很小的碎片中放大的视野。</span><span class="sxs-lookup"><span data-stu-id="f31ac-720">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="f31ac-721">从实体框架 5 升级到实体框架 6 时，暖查询的实际方案不应看到性能回归。</span><span class="sxs-lookup"><span data-stu-id="f31ac-721">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="f31ac-722">为了比较不同查询选项的实际性能，我们创建了 5 个单独的测试变体，其中我们使用不同的查询选项来选择类别名称为"饮料"的所有产品。</span><span class="sxs-lookup"><span data-stu-id="f31ac-722">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="f31ac-723">每个迭代包括创建上下文的成本，以及实现所有返回的实体的成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-723">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="f31ac-724">在采用 1000 个有时算迭代的总和之前，将不及时运行 10 个迭代。</span><span class="sxs-lookup"><span data-stu-id="f31ac-724">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="f31ac-725">显示的结果是从每次测试的 5 次运行中获取的中位运行。</span><span class="sxs-lookup"><span data-stu-id="f31ac-725">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="f31ac-726">有关详细信息，请参阅附录 B，其中包含测试的代码。</span><span class="sxs-lookup"><span data-stu-id="f31ac-726">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="f31ac-727">EF</span><span class="sxs-lookup"><span data-stu-id="f31ac-727">EF</span></span>  | <span data-ttu-id="f31ac-728">测试</span><span class="sxs-lookup"><span data-stu-id="f31ac-728">Test</span></span>                                        | <span data-ttu-id="f31ac-729">时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="f31ac-729">Time (ms)</span></span> | <span data-ttu-id="f31ac-730">内存</span><span class="sxs-lookup"><span data-stu-id="f31ac-730">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="f31ac-731">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-731">EF5</span></span> | <span data-ttu-id="f31ac-732">对象上下文实体命令</span><span class="sxs-lookup"><span data-stu-id="f31ac-732">ObjectContext Entity Command</span></span>                | <span data-ttu-id="f31ac-733">621</span><span class="sxs-lookup"><span data-stu-id="f31ac-733">621</span></span>       | <span data-ttu-id="f31ac-734">39350272</span><span class="sxs-lookup"><span data-stu-id="f31ac-734">39350272</span></span> |
| <span data-ttu-id="f31ac-735">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-735">EF5</span></span> | <span data-ttu-id="f31ac-736">数据库上的 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-736">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="f31ac-737">825</span><span class="sxs-lookup"><span data-stu-id="f31ac-737">825</span></span>       | <span data-ttu-id="f31ac-738">37519360</span><span class="sxs-lookup"><span data-stu-id="f31ac-738">37519360</span></span> |
| <span data-ttu-id="f31ac-739">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-739">EF5</span></span> | <span data-ttu-id="f31ac-740">对象上下文存储查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-740">ObjectContext Store Query</span></span>                   | <span data-ttu-id="f31ac-741">878</span><span class="sxs-lookup"><span data-stu-id="f31ac-741">878</span></span>       | <span data-ttu-id="f31ac-742">39460864</span><span class="sxs-lookup"><span data-stu-id="f31ac-742">39460864</span></span> |
| <span data-ttu-id="f31ac-743">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-743">EF5</span></span> | <span data-ttu-id="f31ac-744">对象上下文林q查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-744">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="f31ac-745">969</span><span class="sxs-lookup"><span data-stu-id="f31ac-745">969</span></span>       | <span data-ttu-id="f31ac-746">38293504</span><span class="sxs-lookup"><span data-stu-id="f31ac-746">38293504</span></span> |
| <span data-ttu-id="f31ac-747">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-747">EF5</span></span> | <span data-ttu-id="f31ac-748">使用对象查询的对象上下文实体 Sql</span><span class="sxs-lookup"><span data-stu-id="f31ac-748">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="f31ac-749">1089</span><span class="sxs-lookup"><span data-stu-id="f31ac-749">1089</span></span>      | <span data-ttu-id="f31ac-750">38981632</span><span class="sxs-lookup"><span data-stu-id="f31ac-750">38981632</span></span> |
| <span data-ttu-id="f31ac-751">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-751">EF5</span></span> | <span data-ttu-id="f31ac-752">对象上下文编译查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-752">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="f31ac-753">1099</span><span class="sxs-lookup"><span data-stu-id="f31ac-753">1099</span></span>      | <span data-ttu-id="f31ac-754">38682624</span><span class="sxs-lookup"><span data-stu-id="f31ac-754">38682624</span></span> |
| <span data-ttu-id="f31ac-755">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-755">EF5</span></span> | <span data-ttu-id="f31ac-756">对象上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-756">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="f31ac-757">1152</span><span class="sxs-lookup"><span data-stu-id="f31ac-757">1152</span></span>      | <span data-ttu-id="f31ac-758">38178816</span><span class="sxs-lookup"><span data-stu-id="f31ac-758">38178816</span></span> |
| <span data-ttu-id="f31ac-759">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-759">EF5</span></span> | <span data-ttu-id="f31ac-760">数据库上下文林克查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-760">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="f31ac-761">1208</span><span class="sxs-lookup"><span data-stu-id="f31ac-761">1208</span></span>      | <span data-ttu-id="f31ac-762">41803776</span><span class="sxs-lookup"><span data-stu-id="f31ac-762">41803776</span></span> |
| <span data-ttu-id="f31ac-763">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-763">EF5</span></span> | <span data-ttu-id="f31ac-764">DbSet 上的数据库上下文 Sql 查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-764">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="f31ac-765">1414</span><span class="sxs-lookup"><span data-stu-id="f31ac-765">1414</span></span>      | <span data-ttu-id="f31ac-766">37982208</span><span class="sxs-lookup"><span data-stu-id="f31ac-766">37982208</span></span> |
| <span data-ttu-id="f31ac-767">EF5</span><span class="sxs-lookup"><span data-stu-id="f31ac-767">EF5</span></span> | <span data-ttu-id="f31ac-768">数据库上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-768">DbContext Linq Query</span></span>                        | <span data-ttu-id="f31ac-769">1574</span><span class="sxs-lookup"><span data-stu-id="f31ac-769">1574</span></span>      | <span data-ttu-id="f31ac-770">41738240</span><span class="sxs-lookup"><span data-stu-id="f31ac-770">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="f31ac-771">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-771">EF6</span></span> | <span data-ttu-id="f31ac-772">对象上下文实体命令</span><span class="sxs-lookup"><span data-stu-id="f31ac-772">ObjectContext Entity Command</span></span>                | <span data-ttu-id="f31ac-773">480</span><span class="sxs-lookup"><span data-stu-id="f31ac-773">480</span></span>       | <span data-ttu-id="f31ac-774">47247360</span><span class="sxs-lookup"><span data-stu-id="f31ac-774">47247360</span></span> |
| <span data-ttu-id="f31ac-775">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-775">EF6</span></span> | <span data-ttu-id="f31ac-776">对象上下文存储查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-776">ObjectContext Store Query</span></span>                   | <span data-ttu-id="f31ac-777">493</span><span class="sxs-lookup"><span data-stu-id="f31ac-777">493</span></span>       | <span data-ttu-id="f31ac-778">46739456</span><span class="sxs-lookup"><span data-stu-id="f31ac-778">46739456</span></span> |
| <span data-ttu-id="f31ac-779">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-779">EF6</span></span> | <span data-ttu-id="f31ac-780">数据库上的 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-780">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="f31ac-781">614</span><span class="sxs-lookup"><span data-stu-id="f31ac-781">614</span></span>       | <span data-ttu-id="f31ac-782">41607168</span><span class="sxs-lookup"><span data-stu-id="f31ac-782">41607168</span></span> |
| <span data-ttu-id="f31ac-783">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-783">EF6</span></span> | <span data-ttu-id="f31ac-784">对象上下文林q查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-784">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="f31ac-785">684</span><span class="sxs-lookup"><span data-stu-id="f31ac-785">684</span></span>       | <span data-ttu-id="f31ac-786">46333952</span><span class="sxs-lookup"><span data-stu-id="f31ac-786">46333952</span></span> |
| <span data-ttu-id="f31ac-787">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-787">EF6</span></span> | <span data-ttu-id="f31ac-788">使用对象查询的对象上下文实体 Sql</span><span class="sxs-lookup"><span data-stu-id="f31ac-788">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="f31ac-789">767</span><span class="sxs-lookup"><span data-stu-id="f31ac-789">767</span></span>       | <span data-ttu-id="f31ac-790">48865280</span><span class="sxs-lookup"><span data-stu-id="f31ac-790">48865280</span></span> |
| <span data-ttu-id="f31ac-791">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-791">EF6</span></span> | <span data-ttu-id="f31ac-792">对象上下文编译查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-792">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="f31ac-793">788</span><span class="sxs-lookup"><span data-stu-id="f31ac-793">788</span></span>       | <span data-ttu-id="f31ac-794">48467968</span><span class="sxs-lookup"><span data-stu-id="f31ac-794">48467968</span></span> |
| <span data-ttu-id="f31ac-795">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-795">EF6</span></span> | <span data-ttu-id="f31ac-796">数据库上下文林克查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="f31ac-796">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="f31ac-797">878</span><span class="sxs-lookup"><span data-stu-id="f31ac-797">878</span></span>       | <span data-ttu-id="f31ac-798">47554560</span><span class="sxs-lookup"><span data-stu-id="f31ac-798">47554560</span></span> |
| <span data-ttu-id="f31ac-799">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-799">EF6</span></span> | <span data-ttu-id="f31ac-800">对象上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-800">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="f31ac-801">953</span><span class="sxs-lookup"><span data-stu-id="f31ac-801">953</span></span>       | <span data-ttu-id="f31ac-802">47632384</span><span class="sxs-lookup"><span data-stu-id="f31ac-802">47632384</span></span> |
| <span data-ttu-id="f31ac-803">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-803">EF6</span></span> | <span data-ttu-id="f31ac-804">DbSet 上的数据库上下文 Sql 查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-804">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="f31ac-805">1023</span><span class="sxs-lookup"><span data-stu-id="f31ac-805">1023</span></span>      | <span data-ttu-id="f31ac-806">41992192</span><span class="sxs-lookup"><span data-stu-id="f31ac-806">41992192</span></span> |
| <span data-ttu-id="f31ac-807">EF6</span><span class="sxs-lookup"><span data-stu-id="f31ac-807">EF6</span></span> | <span data-ttu-id="f31ac-808">数据库上下文林克查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-808">DbContext Linq Query</span></span>                        | <span data-ttu-id="f31ac-809">1290</span><span class="sxs-lookup"><span data-stu-id="f31ac-809">1290</span></span>      | <span data-ttu-id="f31ac-810">47529984</span><span class="sxs-lookup"><span data-stu-id="f31ac-810">47529984</span></span> |


![EF5 暖查询 1000 次迭代](~/ef6/media/ef5warmquery1000.png)

![EF6 暖查询 1000 次迭代](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="f31ac-813">为完整，我们包括一个变体，其中我们在实体命令上执行实体 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-813">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="f31ac-814">但是，由于此类查询的结果未具体化，因此比较不一定是苹果到苹果。</span><span class="sxs-lookup"><span data-stu-id="f31ac-814">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="f31ac-815">测试包括接近具体化，试图使比较更公平。</span><span class="sxs-lookup"><span data-stu-id="f31ac-815">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="f31ac-816">在此端到端案例中，实体框架 6 优于实体框架 5，因为对堆栈的几个部分进行了性能改进，包括更轻的 DbContext 初始化和更快的元数据收集&lt;T&gt;查找。</span><span class="sxs-lookup"><span data-stu-id="f31ac-816">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="f31ac-817">7 设计时间性能注意事项</span><span class="sxs-lookup"><span data-stu-id="f31ac-817">7 Design time performance considerations</span></span>

### <a name="71-inheritance-strategies"></a><span data-ttu-id="f31ac-818">7.1 继承策略</span><span class="sxs-lookup"><span data-stu-id="f31ac-818">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="f31ac-819">使用实体框架时的另一个性能考虑是您使用的继承策略。</span><span class="sxs-lookup"><span data-stu-id="f31ac-819">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="f31ac-820">实体框架支持 3 种基本类型的继承及其组合：</span><span class="sxs-lookup"><span data-stu-id="f31ac-820">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="f31ac-821">每个层次结构的表 （TPH） - 其中每个继承集映射到具有区分列的表，以指示在行中表示层次结构中的特定类型。</span><span class="sxs-lookup"><span data-stu-id="f31ac-821">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="f31ac-822">每个类型 （TPT） 的表 - 其中每种类型在数据库中都有自己的表;子表仅定义父表不包含的列。</span><span class="sxs-lookup"><span data-stu-id="f31ac-822">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="f31ac-823">每个类的表 （TPC） - 其中每种类型的在数据库中都有自己的完整表;子表定义其所有字段，包括父类型中定义的字段。</span><span class="sxs-lookup"><span data-stu-id="f31ac-823">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="f31ac-824">如果模型使用 TPT 继承，则生成的查询将比其他继承策略生成的查询复杂，这可能导致存储上的执行时间较长。</span><span class="sxs-lookup"><span data-stu-id="f31ac-824">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span><span data-ttu-id="f31ac-825">通过 TPT 模型生成查询并实现结果对象通常需要更长时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-825">  It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="f31ac-826">请参阅"在实体框架中使用 TPT（按类型表）继承时的性能注意事项"MSDN 博客文章： \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>。</span><span class="sxs-lookup"><span data-stu-id="f31ac-826">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>.</span></span>

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="f31ac-827">7.1.1 避免在模型优先或代码优先应用程序中使用 TPT</span><span class="sxs-lookup"><span data-stu-id="f31ac-827">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="f31ac-828">当您在具有 TPT 架构的现有数据库上创建模型时，您没有很多选项。</span><span class="sxs-lookup"><span data-stu-id="f31ac-828">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="f31ac-829">但是，在使用 Model First 或代码优先创建应用程序时，出于性能问题，应避免 TPT 继承。</span><span class="sxs-lookup"><span data-stu-id="f31ac-829">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="f31ac-830">在实体设计器向导中使用模型优先时，您将获得模型中任何继承的 TPT。</span><span class="sxs-lookup"><span data-stu-id="f31ac-830">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="f31ac-831">如果要使用 Model First 切换到 TPH 继承策略，可以使用 Visual Studio 库 （中的\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)实体设计器数据库生成电源包）中的"实体设计器数据库生成电源包"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-831">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="f31ac-832">当使用 Code First 配置具有继承的模型映射时，EF 默认将使用 TPH，因此继承层次结构中的所有实体都将映射到同一表。</span><span class="sxs-lookup"><span data-stu-id="f31ac-832">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="f31ac-833">有关详细信息，请参阅 MSDN 杂志 （）[http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)中"实体框架4.1中代码优先"一文中的"使用流畅 API 映射"部分。</span><span class="sxs-lookup"><span data-stu-id="f31ac-833">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="f31ac-834">7.2 从 EF4 升级以提高模型生成时间</span><span class="sxs-lookup"><span data-stu-id="f31ac-834">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="f31ac-835">生成模型存储层 （SSDL） 的算法的 SQL Server 特定改进可在实体框架 5 和 6 中提供，并在安装 Visual Studio 2010 SP1 时作为实体框架 4 的更新提供。</span><span class="sxs-lookup"><span data-stu-id="f31ac-835">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="f31ac-836">以下测试结果演示了生成非常大的模型时的改进，在这种情况下，Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="f31ac-836">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="f31ac-837">有关附录 C 的更多详细信息，请参阅附录 C。</span><span class="sxs-lookup"><span data-stu-id="f31ac-837">See Appendix C for more details about it.</span></span>

<span data-ttu-id="f31ac-838">该模型包含 1005 个实体集和 4227 个关联集。</span><span class="sxs-lookup"><span data-stu-id="f31ac-838">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="f31ac-839">配置</span><span class="sxs-lookup"><span data-stu-id="f31ac-839">Configuration</span></span>                              | <span data-ttu-id="f31ac-840">所消耗的时间细目</span><span class="sxs-lookup"><span data-stu-id="f31ac-840">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f31ac-841">视觉工作室 2010， 实体框架 4</span><span class="sxs-lookup"><span data-stu-id="f31ac-841">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="f31ac-842">SSDL 生成： 2 小时 27 分钟</span><span class="sxs-lookup"><span data-stu-id="f31ac-842">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="f31ac-843">映射生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-843">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-844">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-844">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-845">对象层生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-845">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-846">视图生成： 2 小时 14 分钟</span><span class="sxs-lookup"><span data-stu-id="f31ac-846">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="f31ac-847">可视化工作室 2010 SP1， 实体框架 4</span><span class="sxs-lookup"><span data-stu-id="f31ac-847">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="f31ac-848">SSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-848">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-849">映射生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-850">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-851">对象层生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-852">视图生成： 1 小时 53 分钟</span><span class="sxs-lookup"><span data-stu-id="f31ac-852">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="f31ac-853">视觉工作室 2013， 实体框架 5</span><span class="sxs-lookup"><span data-stu-id="f31ac-853">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="f31ac-854">SSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-855">映射生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-856">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-857">对象层生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-858">视图生成： 65 分钟</span><span class="sxs-lookup"><span data-stu-id="f31ac-858">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="f31ac-859">视觉工作室 2013， 实体框架 6</span><span class="sxs-lookup"><span data-stu-id="f31ac-859">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="f31ac-860">SSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-861">映射生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-862">CSDL 生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-863">对象层生成：1 秒</span><span class="sxs-lookup"><span data-stu-id="f31ac-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="f31ac-864">视图生成：28 秒。</span><span class="sxs-lookup"><span data-stu-id="f31ac-864">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="f31ac-865">值得注意的是，在生成 SSDL 时，负载几乎完全花费在 SQL Server 上，而客户端开发计算机正在等待从服务器返回的结果。</span><span class="sxs-lookup"><span data-stu-id="f31ac-865">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="f31ac-866">DBA 应特别赞赏这一改进。</span><span class="sxs-lookup"><span data-stu-id="f31ac-866">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="f31ac-867">还值得注意的是，模型生成的全部成本基本上都发生在视图生成中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-867">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="f31ac-868">7.3 使用数据库优先和模型第一拆分大型模型</span><span class="sxs-lookup"><span data-stu-id="f31ac-868">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="f31ac-869">随着模型尺寸的增加，设计器表面变得杂乱无章且难以使用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-869">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="f31ac-870">我们通常认为具有 300 多个实体的模型太大，无法有效地使用设计器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-870">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="f31ac-871">以下博客文章描述了拆分大型模型的几个选项： \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>。</span><span class="sxs-lookup"><span data-stu-id="f31ac-871">The following blog post describes several options for splitting large models: \<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>.</span></span>

<span data-ttu-id="f31ac-872">该帖子是为实体框架的第一个版本编写的，但步骤仍然适用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-872">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="f31ac-873">7.4 实体数据源控制的性能注意事项</span><span class="sxs-lookup"><span data-stu-id="f31ac-873">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="f31ac-874">我们已经看到多线程性能和压力测试中的情况，其中使用 EntityDataSource 控件的 Web 应用程序的性能显著恶化。</span><span class="sxs-lookup"><span data-stu-id="f31ac-874">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="f31ac-875">根本原因是 EntityDataSource 反复调用 Web 应用程序引用的程序集上的元数据工作区.LoadFromAssembly，以发现要用作实体的类型。</span><span class="sxs-lookup"><span data-stu-id="f31ac-875">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="f31ac-876">解决方案是将实体数据源的上下文类型名称设置为派生对象上下文类的类型名称。</span><span class="sxs-lookup"><span data-stu-id="f31ac-876">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="f31ac-877">这将关闭扫描所有引用的程序集的实体类型的机制。</span><span class="sxs-lookup"><span data-stu-id="f31ac-877">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="f31ac-878">设置 ContextTypeName 字段还可以防止功能问题，即 .NET 4.0 中的实体数据源在无法通过反射从程序集加载类型时引发反射类型加载异常。</span><span class="sxs-lookup"><span data-stu-id="f31ac-878">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="f31ac-879">此问题已在 .NET 4.5 中修复。</span><span class="sxs-lookup"><span data-stu-id="f31ac-879">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="f31ac-880">7.5 POCO 实体和更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="f31ac-880">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="f31ac-881">实体框架使您能够将自定义数据类与数据模型一起使用，而无需对数据类本身进行任何修改。</span><span class="sxs-lookup"><span data-stu-id="f31ac-881">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="f31ac-882">这意味着可以将“纯旧式”CLR 对象 (POCO)（例如，现有的域对象）与数据模型一起使用。</span><span class="sxs-lookup"><span data-stu-id="f31ac-882">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="f31ac-883">这些 POCO 数据类（也称为持久性无知对象）映射到数据模型中定义的实体，支持大多数相同的查询，插入、更新和删除实体数据模型工具生成的实体类型的行为。</span><span class="sxs-lookup"><span data-stu-id="f31ac-883">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="f31ac-884">实体框架还可以创建从 POCO 类型派生的代理类，当您希望启用诸如 POCO 实体上的延迟加载和自动更改跟踪等功能时，将使用这些类。</span><span class="sxs-lookup"><span data-stu-id="f31ac-884">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="f31ac-885">您的 POCO 类必须满足某些要求，才能允许实体框架使用代理，如下所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-885">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="f31ac-886">每次实体的任何属性发生更改时，机会跟踪代理都会通知对象状态管理器，因此实体框架会一直了解实体的实际状态。</span><span class="sxs-lookup"><span data-stu-id="f31ac-886">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="f31ac-887">这是通过将通知事件添加到属性的 setter 方法的正文，以及让对象状态管理器处理此类事件来实现的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-887">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="f31ac-888">请注意，由于实体框架创建了一组附加的事件，创建代理实体通常比创建非代理 POCO 实体的成本更高。</span><span class="sxs-lookup"><span data-stu-id="f31ac-888">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="f31ac-889">当 POCO 实体没有更改跟踪代理时，可以通过将实体的内容与以前保存状态的副本进行比较来找到更改。</span><span class="sxs-lookup"><span data-stu-id="f31ac-889">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="f31ac-890">当您的上下文中有许多实体时，或者当实体具有非常大的属性时，即使自上次比较以来，这些属性均未更改，这种深层比较将成为一个漫长的过程。</span><span class="sxs-lookup"><span data-stu-id="f31ac-890">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="f31ac-891">总之：创建更改跟踪代理时，您将支付性能影响，但当实体具有许多属性或模型中有许多实体时，更改跟踪将帮助您加快更改检测过程。</span><span class="sxs-lookup"><span data-stu-id="f31ac-891">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="f31ac-892">对于具有少量属性的实体，其中实体数量不会增长太多，因此具有更改跟踪代理可能没有多大好处。</span><span class="sxs-lookup"><span data-stu-id="f31ac-892">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="f31ac-893">8 加载相关实体</span><span class="sxs-lookup"><span data-stu-id="f31ac-893">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="f31ac-894">8.1 延迟加载与热负荷</span><span class="sxs-lookup"><span data-stu-id="f31ac-894">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="f31ac-895">实体框架提供了几种加载与目标实体相关的实体的不同方法。</span><span class="sxs-lookup"><span data-stu-id="f31ac-895">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="f31ac-896">例如，当您查询产品时，相关订单将加载到对象状态管理器中的方式不同。</span><span class="sxs-lookup"><span data-stu-id="f31ac-896">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="f31ac-897">从性能角度来看，加载相关实体时要考虑的最大问题是是使用延迟加载还是"渴望加载"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-897">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="f31ac-898">使用"热加载"时，相关实体将随目标实体集一起加载。</span><span class="sxs-lookup"><span data-stu-id="f31ac-898">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="f31ac-899">在查询中使用"包含"语句来指示要引入哪些相关实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-899">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="f31ac-900">使用延迟加载时，初始查询仅引入目标实体集。</span><span class="sxs-lookup"><span data-stu-id="f31ac-900">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="f31ac-901">但是，每当访问导航属性时，都会针对存储发出另一个查询以加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-901">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="f31ac-902">加载实体后，实体的任何进一步查询都将直接从对象状态管理器加载它，无论您是使用延迟加载还是热要加载。</span><span class="sxs-lookup"><span data-stu-id="f31ac-902">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="f31ac-903">8.2 如何在延迟加载和渴望加载之间进行选择</span><span class="sxs-lookup"><span data-stu-id="f31ac-903">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="f31ac-904">重要的是，您了解延迟加载和热加载之间的区别，以便您可以为您的应用程序做出正确的选择。</span><span class="sxs-lookup"><span data-stu-id="f31ac-904">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="f31ac-905">这将有助于评估针对数据库的多个请求与可能包含大型负载的单个请求之间的权衡。</span><span class="sxs-lookup"><span data-stu-id="f31ac-905">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="f31ac-906">在应用程序的某些部分使用热加载和其他部分的延迟加载可能是合适的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-906">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="f31ac-907">作为引擎盖下所发生的事情的一个示例，假设您要查询居住在英国的客户及其订单数量。</span><span class="sxs-lookup"><span data-stu-id="f31ac-907">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="f31ac-908">**使用热负荷**</span><span class="sxs-lookup"><span data-stu-id="f31ac-908">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="f31ac-909">**使用延迟加载**</span><span class="sxs-lookup"><span data-stu-id="f31ac-909">**Using Lazy Loading**</span></span>

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

<span data-ttu-id="f31ac-910">使用热要加载时，您将发出一个查询，返回所有客户和所有订单。</span><span class="sxs-lookup"><span data-stu-id="f31ac-910">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="f31ac-911">存储命令如下所示：</span><span class="sxs-lookup"><span data-stu-id="f31ac-911">The store command looks like:</span></span>

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

<span data-ttu-id="f31ac-912">使用延迟加载时，最初将发出以下查询：</span><span class="sxs-lookup"><span data-stu-id="f31ac-912">When using lazy loading, you'll issue the following query initially:</span></span>

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

<span data-ttu-id="f31ac-913">每次访问客户的"订单"导航属性时，都会针对商店发出另一个查询，如下所示：</span><span class="sxs-lookup"><span data-stu-id="f31ac-913">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

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

<span data-ttu-id="f31ac-914">有关详细信息，请参阅[加载相关对象](https://msdn.microsoft.com/library/bb896272.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-914">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="f31ac-915">8.2.1 延迟加载与渴望加载备忘单</span><span class="sxs-lookup"><span data-stu-id="f31ac-915">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="f31ac-916">选择热切装载和延迟装载，没有一刀切的东西。</span><span class="sxs-lookup"><span data-stu-id="f31ac-916">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="f31ac-917">首先尝试了解两种策略之间的差异，以便您可以做出明智的决策;此外，请考虑您的代码是否适合以下任何方案：</span><span class="sxs-lookup"><span data-stu-id="f31ac-917">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="f31ac-918">场景</span><span class="sxs-lookup"><span data-stu-id="f31ac-918">Scenario</span></span>                                                                    | <span data-ttu-id="f31ac-919">我们的建议</span><span class="sxs-lookup"><span data-stu-id="f31ac-919">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="f31ac-920">是否需要从提取的实体访问许多导航属性？</span><span class="sxs-lookup"><span data-stu-id="f31ac-920">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="f31ac-921">**否**- 两个选项都可能都能做到。</span><span class="sxs-lookup"><span data-stu-id="f31ac-921">**No** - Both options will probably do.</span></span> <span data-ttu-id="f31ac-922">但是，如果查询带来的有效负载不是太大，则使用 Eager 加载可能会获得性能优势，因为它需要较少的网络往返行程来实现对象。</span><span class="sxs-lookup"><span data-stu-id="f31ac-922">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="f31ac-923">**是**- 如果需要从实体访问许多导航属性，则可以通过在查询中使用多个包含语句来使用"渴望加载"来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-923">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="f31ac-924">包含的实体越多，查询返回的有效负载就越大。</span><span class="sxs-lookup"><span data-stu-id="f31ac-924">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="f31ac-925">将三个或多个实体包含在查询中后，请考虑切换到延迟加载。</span><span class="sxs-lookup"><span data-stu-id="f31ac-925">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="f31ac-926">您确切知道运行时需要哪些数据吗？</span><span class="sxs-lookup"><span data-stu-id="f31ac-926">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="f31ac-927">**否**- 延迟加载将更好地为您。</span><span class="sxs-lookup"><span data-stu-id="f31ac-927">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="f31ac-928">否则，您最终可能会查询不需要的数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-928">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="f31ac-929">**是的**- 渴望加载可能是您最好的选择;这将有助于更快地加载整个集。</span><span class="sxs-lookup"><span data-stu-id="f31ac-929">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="f31ac-930">如果查询需要获取大量数据，并且速度太慢，请尝试"延迟加载"。</span><span class="sxs-lookup"><span data-stu-id="f31ac-930">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="f31ac-931">您的代码执行是否远离数据库？</span><span class="sxs-lookup"><span data-stu-id="f31ac-931">Is your code executing far from your database?</span></span> <span data-ttu-id="f31ac-932">（增加网络延迟）</span><span class="sxs-lookup"><span data-stu-id="f31ac-932">(increased network latency)</span></span>  | <span data-ttu-id="f31ac-933">**否**- 当网络延迟不是问题时，使用延迟加载可能会简化代码。</span><span class="sxs-lookup"><span data-stu-id="f31ac-933">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="f31ac-934">请记住，应用程序的拓扑可能会更改，因此不要认为数据库邻近性是理所当然的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-934">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="f31ac-935">**是**- 当网络出现问题时，只有您才能决定哪种方案更适合您的方案。</span><span class="sxs-lookup"><span data-stu-id="f31ac-935">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="f31ac-936">通常，热负荷会更好，因为它需要更少的往返行程。</span><span class="sxs-lookup"><span data-stu-id="f31ac-936">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a><span data-ttu-id="f31ac-937">8.2.2 具有多个包括的性能问题</span><span class="sxs-lookup"><span data-stu-id="f31ac-937">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="f31ac-938">当我们听到涉及服务器响应时间问题的性能问题时，问题的根源是经常使用多个 Include 语句进行查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-938">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="f31ac-939">虽然在查询中包含相关实体很强大，但了解封面下发生的情况非常重要。</span><span class="sxs-lookup"><span data-stu-id="f31ac-939">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="f31ac-940">包含多个 Include 语句的查询需要相当长的时间才能通过内部计划编译器来生成存储命令。</span><span class="sxs-lookup"><span data-stu-id="f31ac-940">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="f31ac-941">这一大部分时间都花在尝试优化生成的查询上。</span><span class="sxs-lookup"><span data-stu-id="f31ac-941">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="f31ac-942">生成的存储命令将包含每个"包含"的外部联接或联合，具体取决于您的映射。</span><span class="sxs-lookup"><span data-stu-id="f31ac-942">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="f31ac-943">像这样的查询将在单个负载中从数据库引入大型连接图形，从而消除任何带宽问题，尤其是在有效负载中存在大量冗余时（例如，当多个级别的 Include 用于在一对多方向上遍历关联时）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-943">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="f31ac-944">通过使用 ToTraceString 并在 SQL 服务器管理工作室中执行存储命令以查看有效负载大小，可以检查查询返回过大的有效负载的情况。</span><span class="sxs-lookup"><span data-stu-id="f31ac-944">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="f31ac-945">在这种情况下，您可以尝试减少查询中的包含语句数，以便仅引入所需的数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-945">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="f31ac-946">或者，您可能能够将查询分解为较小的子查询序列，例如：</span><span class="sxs-lookup"><span data-stu-id="f31ac-946">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="f31ac-947">**在断开查询之前：**</span><span class="sxs-lookup"><span data-stu-id="f31ac-947">**Before breaking the query:**</span></span>

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

<span data-ttu-id="f31ac-948">**中断查询后：**</span><span class="sxs-lookup"><span data-stu-id="f31ac-948">**After breaking the query:**</span></span>

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

<span data-ttu-id="f31ac-949">这仅适用于跟踪的查询，因为我们正在利用上下文自动执行标识解析和关联修复的能力。</span><span class="sxs-lookup"><span data-stu-id="f31ac-949">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="f31ac-950">与延迟加载一样，权衡将是对较小负载的更多查询。</span><span class="sxs-lookup"><span data-stu-id="f31ac-950">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="f31ac-951">您还可以使用单个属性的预测来仅显式选择每个实体所需的数据，但在这种情况下，您将不会加载实体，并且不支持更新。</span><span class="sxs-lookup"><span data-stu-id="f31ac-951">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="f31ac-952">8.2.3 获取延迟加载属性的解决方法</span><span class="sxs-lookup"><span data-stu-id="f31ac-952">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="f31ac-953">实体框架当前不支持延迟加载标量或复杂属性。</span><span class="sxs-lookup"><span data-stu-id="f31ac-953">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="f31ac-954">但是，如果表包含大型对象（如 BLOB），则可以使用表拆分将大型属性分隔为单独的实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-954">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="f31ac-955">例如，假设您有一个包含 varbinary 照片列的产品表。</span><span class="sxs-lookup"><span data-stu-id="f31ac-955">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="f31ac-956">如果查询中频繁不需要访问此属性，则可以使用表拆分仅引入通常需要的实体部分。</span><span class="sxs-lookup"><span data-stu-id="f31ac-956">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="f31ac-957">仅当您明确需要产品照片时，才会加载表示产品照片的实体。</span><span class="sxs-lookup"><span data-stu-id="f31ac-957">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="f31ac-958">吉尔·芬克的"实体框架中的表拆分"博客文章是演示如何启用表拆分的一个很好的资源： \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span><span class="sxs-lookup"><span data-stu-id="f31ac-958">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="f31ac-959">9 其他注意事项</span><span class="sxs-lookup"><span data-stu-id="f31ac-959">9 Other considerations</span></span>

### <a name="91-server-garbage-collection"></a><span data-ttu-id="f31ac-960">9.1 服务器垃圾回收</span><span class="sxs-lookup"><span data-stu-id="f31ac-960">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="f31ac-961">某些用户可能会遇到资源争用，从而限制在垃圾回收器未正确配置时期望的并行性。</span><span class="sxs-lookup"><span data-stu-id="f31ac-961">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="f31ac-962">每当在多线程方案中使用 EF 时，或任何类似于服务器端系统的应用程序中，请确保启用服务器垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="f31ac-962">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="f31ac-963">这是通过应用程序配置文件中的简单设置完成的：</span><span class="sxs-lookup"><span data-stu-id="f31ac-963">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="f31ac-964">这将减少线程争用，并在 CPU 饱和方案中将吞吐量提高高达 30%。</span><span class="sxs-lookup"><span data-stu-id="f31ac-964">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="f31ac-965">通常，您应该始终使用经典垃圾回收（更好地针对 UI 和客户端方案）以及服务器垃圾回收测试应用程序的表现。</span><span class="sxs-lookup"><span data-stu-id="f31ac-965">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92-autodetectchanges"></a><span data-ttu-id="f31ac-966">9.2 自动检测更改</span><span class="sxs-lookup"><span data-stu-id="f31ac-966">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="f31ac-967">如前所述，当对象缓存具有多个实体时，实体框架可能会显示性能问题。</span><span class="sxs-lookup"><span data-stu-id="f31ac-967">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="f31ac-968">某些操作（如添加、删除、查找、输入和保存更改）会触发对检测更改的调用，这些调用可能会根据对象缓存变得多大而消耗大量 CPU。</span><span class="sxs-lookup"><span data-stu-id="f31ac-968">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="f31ac-969">原因是对象缓存和对象状态管理器尝试在对上下文执行的每个操作上保持尽可能同步，以便在各种方案下保证生成的数据正确。</span><span class="sxs-lookup"><span data-stu-id="f31ac-969">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="f31ac-970">通常，最好在应用程序的整个生命周期内启用实体框架的自动更改检测。</span><span class="sxs-lookup"><span data-stu-id="f31ac-970">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="f31ac-971">如果您的方案受到 CPU 使用率高的负面影响，并且您的配置文件指示罪魁祸首是调用检测更改，请考虑暂时关闭代码敏感部分中的自动检测更改：</span><span class="sxs-lookup"><span data-stu-id="f31ac-971">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

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

<span data-ttu-id="f31ac-972">在关闭自动检测更改之前，最好了解这可能会导致实体框架失去跟踪有关实体上发生的更改的某些信息的能力。</span><span class="sxs-lookup"><span data-stu-id="f31ac-972">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="f31ac-973">如果处理不正确，则可能会导致应用程序上的数据不一致。</span><span class="sxs-lookup"><span data-stu-id="f31ac-973">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="f31ac-974">有关关闭自动检测更改的详细信息，请阅读\<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。</span><span class="sxs-lookup"><span data-stu-id="f31ac-974">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93-context-per-request"></a><span data-ttu-id="f31ac-975">9.3 每个请求的上下文</span><span class="sxs-lookup"><span data-stu-id="f31ac-975">9.3      Context per request</span></span>

<span data-ttu-id="f31ac-976">实体框架的上下文旨在用作短期实例，以便提供最佳的性能体验。</span><span class="sxs-lookup"><span data-stu-id="f31ac-976">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="f31ac-977">上下文预期是短暂的和丢弃的，因此已经实现为非常轻量级，并尽可能重用元数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-977">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="f31ac-978">在 Web 方案中，请务必牢记这一点，并且不应具有超过单个请求持续时间的上下文。</span><span class="sxs-lookup"><span data-stu-id="f31ac-978">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="f31ac-979">同样，在非 Web 方案中，应根据您对实体框架中不同缓存级别的理解而丢弃上下文。</span><span class="sxs-lookup"><span data-stu-id="f31ac-979">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="f31ac-980">一般来说，在应用程序的整个生命周期中，应避免使用上下文实例，以及每个线程和静态上下文的上下文。</span><span class="sxs-lookup"><span data-stu-id="f31ac-980">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94-database-null-semantics"></a><span data-ttu-id="f31ac-981">9.4 数据库无效语义</span><span class="sxs-lookup"><span data-stu-id="f31ac-981">9.4      Database null semantics</span></span>

<span data-ttu-id="f31ac-982">默认情况下，实体框架将生成具有 C\#空比较语义的 SQL 代码。</span><span class="sxs-lookup"><span data-stu-id="f31ac-982">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="f31ac-983">请参考以下示例查询：</span><span class="sxs-lookup"><span data-stu-id="f31ac-983">Consider the following example query:</span></span>

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

<span data-ttu-id="f31ac-984">在此示例中，我们将许多可空变量与实体上的空属性（如供应商 ID 和 UnitPrice）进行比较。</span><span class="sxs-lookup"><span data-stu-id="f31ac-984">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="f31ac-985">此查询生成的 SQL 将询问参数值是否与列值相同，或者参数和列值是否为空。</span><span class="sxs-lookup"><span data-stu-id="f31ac-985">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="f31ac-986">这将隐藏数据库服务器处理空的方式，并将跨不同的数据库供应商提供一致的\#C null 体验。</span><span class="sxs-lookup"><span data-stu-id="f31ac-986">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="f31ac-987">另一方面，生成的代码有点复杂，当查询语句中的比较量增长到大量时，可能无法很好地执行。</span><span class="sxs-lookup"><span data-stu-id="f31ac-987">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="f31ac-988">处理这种情况的一种方法是使用数据库空语义。</span><span class="sxs-lookup"><span data-stu-id="f31ac-988">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="f31ac-989">请注意，这可能与 C\# null 语义的行为不同，因为现在实体框架将生成更简单的 SQL，公开数据库引擎处理空值的方式。</span><span class="sxs-lookup"><span data-stu-id="f31ac-989">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="f31ac-990">数据库空语义可以针对上下文配置使用单个配置行激活每个上下文：</span><span class="sxs-lookup"><span data-stu-id="f31ac-990">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="f31ac-991">使用数据库空语义时，中小型查询不会显示可察觉的性能改进，但在具有大量潜在空比较的查询上，差异将变得更加明显。</span><span class="sxs-lookup"><span data-stu-id="f31ac-991">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="f31ac-992">在上面的示例查询中，在受控环境中运行的微基准中，性能差异小于 2%。</span><span class="sxs-lookup"><span data-stu-id="f31ac-992">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95-async"></a><span data-ttu-id="f31ac-993">9.5 异步</span><span class="sxs-lookup"><span data-stu-id="f31ac-993">9.5      Async</span></span>

<span data-ttu-id="f31ac-994">实体框架 6 在 .NET 4.5 或更高版本上运行时引入了对异步操作的支持。</span><span class="sxs-lookup"><span data-stu-id="f31ac-994">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="f31ac-995">在大多数情况下，具有 IO 相关争用的应用程序将从使用异步查询和保存操作中受益最大。</span><span class="sxs-lookup"><span data-stu-id="f31ac-995">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="f31ac-996">如果应用程序没有受到 IO 争用的影响，则在最好情况下，使用异步将同步运行，并在与同步调用相同的时间内返回结果，或者在最坏的情况下，只需将执行推迟到异步任务，并添加额外的时间来完成您的方案。</span><span class="sxs-lookup"><span data-stu-id="f31ac-996">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="f31ac-997">有关异步编程的工作原理的信息，这将有助于您确定异步程序是否会提高应用程序访问[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)的性能。</span><span class="sxs-lookup"><span data-stu-id="f31ac-997">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="f31ac-998">有关在实体框架上使用异步操作的详细信息，请参阅[异步查询和保存](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-998">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96-ngen"></a><span data-ttu-id="f31ac-999">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="f31ac-999">9.6      NGEN</span></span>

<span data-ttu-id="f31ac-1000">实体框架 6 不在 .NET 框架的默认安装中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1000">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="f31ac-1001">因此，默认情况下，实体框架程序集不是 NGEN d，这意味着所有实体框架代码都受与任何其他 MSIL 程序集相同的 JIT 成本的约束。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1001">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="f31ac-1002">这可能会降低 F5 在开发时的经验，也会降低应用程序在生产环境中的冷启动。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1002">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="f31ac-1003">为了降低 JIT 的 CPU 和内存成本，建议酌情使用实体框架映像进行 NGEN。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1003">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="f31ac-1004">有关如何使用 NGEN 提高实体框架 6 的启动性能的详细信息，请参阅[使用 NGen 提高启动性能](~/ef6/fundamentals/performance/ngen.md)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1004">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97-code-first-versus-edmx"></a><span data-ttu-id="f31ac-1005">9.7 代码优先与 EDMX</span><span class="sxs-lookup"><span data-stu-id="f31ac-1005">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="f31ac-1006">实体框架通过具有概念模型（对象）、存储架构（数据库）和两者之间映射的内存中表示形式，导致面向对象编程和关系数据库之间的阻抗不匹配问题。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1006">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="f31ac-1007">此元数据称为实体数据模型，简称 EDM。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1007">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="f31ac-1008">从这个 EDM 中，实体框架将从内存中的对象派生到往返数据的视图到数据库并返回。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1008">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="f31ac-1009">当实体框架与正式指定概念模型、存储架构和映射的 EDMX 文件一起使用时，模型加载阶段只需验证 EDM 是否正确（例如，确保没有缺少映射），然后生成视图，然后验证视图并准备好使用此元数据。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1009">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="f31ac-1010">只有这样，才能执行查询或将新数据保存到数据存储。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1010">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="f31ac-1011">代码优先方法的核心是一个复杂的实体数据模型生成器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1011">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="f31ac-1012">实体框架必须从提供的代码生成 EDM;它通过分析模型中涉及的类、应用约定并通过 Fluent API 配置模型来达到这一要求。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1012">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="f31ac-1013">构建 EDM 后，实体框架的工作方式与项目中存在 EDMX 文件的方式相同。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1013">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="f31ac-1014">因此，从 Code First 构建模型会增加额外的复杂性，与使用 EDMX 相比，实体框架的启动时间会变慢。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1014">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="f31ac-1015">成本完全取决于正在构建的模型的大小和复杂性。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1015">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="f31ac-1016">在选择使用 EDMX 与代码优先时，请务必了解代码 First 引入的灵活性会增加首次构建模型的成本。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1016">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="f31ac-1017">如果应用程序能够承受此首次加载的成本，那么通常 Code First 将是首选方式。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1017">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="f31ac-1018">10 调查性能</span><span class="sxs-lookup"><span data-stu-id="f31ac-1018">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="f31ac-1019">10.1 使用可视化工作室探查器</span><span class="sxs-lookup"><span data-stu-id="f31ac-1019">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="f31ac-1020">如果实体框架存在性能问题，则可以使用 Visual Studio 中内置的探查器来查看应用程序花费的时间。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1020">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="f31ac-1021">这是我们用于在"探索ADO.NET实体框架的性能 - 第 1 部分"博客文章（\<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>)显示实体框架在冷热查询期间花费时间）中生成饼图的工具。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1021">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="f31ac-1022">数据和建模客户咨询团队编写的"使用 Visual Studio 2010 探查器分析实体框架"博客文章显示了他们如何使用探查器调查性能问题的真实示例。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1022">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span><span data-ttu-id="f31ac-1023">\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>.</span><span class="sxs-lookup"><span data-stu-id="f31ac-1023">  \<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>.</span></span> <span data-ttu-id="f31ac-1024">这篇文章是为窗口应用程序编写的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1024">This post was written for a windows application.</span></span> <span data-ttu-id="f31ac-1025">如果需要分析 Web 应用程序，Windows 性能记录仪 （WPR） 和 Windows 性能分析器 （WPA） 工具可能比在 Visual Studio 工作更好。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1025">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="f31ac-1026">WPR 和 WPA 是 Windows 性能工具包的一部分，该工具包包含在 Windows[http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)评估和部署工具包 （） 中。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1026">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="f31ac-1027">10.2 应用程序/数据库分析</span><span class="sxs-lookup"><span data-stu-id="f31ac-1027">10.2 Application/Database profiling</span></span>

<span data-ttu-id="f31ac-1028">像可视化工作室中内置的探查器这样的工具告诉您应用程序将花时间的位置。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1028">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span><span data-ttu-id="f31ac-1029">另一种类型的探查器可用于根据需求在生产或预生产中对正在运行的应用程序进行动态分析，并查找常见的陷阱和数据库访问的抗模式。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1029">  Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="f31ac-1030">两个市售的探查器是实体框架探查\<http://efprof.com>)器（和 ORMProfiler（。 \< http://ormprofiler.com>)</span><span class="sxs-lookup"><span data-stu-id="f31ac-1030">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="f31ac-1031">如果您的应用程序是使用代码优先的 MVC 应用程序，则可以使用 StackExchange 的 Mini Profiler。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1031">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="f31ac-1032">斯科特·汉塞尔曼在他的博客中描述了这个工具： \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span><span class="sxs-lookup"><span data-stu-id="f31ac-1032">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="f31ac-1033">有关分析应用程序的数据库活动的详细信息，请参阅 Julie Lerman 的 MSDN 杂志文章，标题为[实体框架中的分析数据库活动](https://msdn.microsoft.com/magazine/gg490349.aspx)。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1033">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="f31ac-1034">10.3 数据库记录器</span><span class="sxs-lookup"><span data-stu-id="f31ac-1034">10.3 Database logger</span></span>

<span data-ttu-id="f31ac-1035">如果使用实体框架 6，还考虑使用内置日志记录功能。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1035">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="f31ac-1036">可以指示上下文的数据库属性通过简单的单行配置记录其活动：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1036">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="f31ac-1037">在此示例中，数据库活动将记录到控制台，但可以将 Log 属性配置为调用任何操作&lt;字符串&gt;委托。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1037">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="f31ac-1038">如果要在不重新编译的情况下启用数据库日志记录，并且正在使用实体框架 6.1 或更高版本，则可以通过在应用程序的 Web.config 或 app.config 文件中添加拦截器来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1038">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="f31ac-1039">有关如何在不重新编译的情况下添加日志记录的详细信息，\<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>请访问 。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1039">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="f31ac-1040">11 附录</span><span class="sxs-lookup"><span data-stu-id="f31ac-1040">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="f31ac-1041">11.1 A. 测试环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1041">11.1 A. Test Environment</span></span>

<span data-ttu-id="f31ac-1042">此环境使用 2 台计算机设置，数据库与客户端应用程序分开的计算机上。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1042">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="f31ac-1043">计算机位于同一机架中，因此网络延迟相对较低，但比单机环境更逼真。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1043">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-app-server"></a><span data-ttu-id="f31ac-1044">11.1.1 应用服务器</span><span class="sxs-lookup"><span data-stu-id="f31ac-1044">11.1.1       App Server</span></span>

##### <a name="11111-software-environment"></a><span data-ttu-id="f31ac-1045">11.1.1.1 软件环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1045">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="f31ac-1046">实体框架 4 软件环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1046">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="f31ac-1047">操作系统名称：Windows 服务器 2008 R2 企业 SP1。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1047">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="f31ac-1048">视觉工作室 2010 – 终极。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1048">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="f31ac-1049">可视化工作室 2010 SP1 （仅适用于某些比较）。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1049">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="f31ac-1050">实体框架 5 和 6 软件环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1050">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="f31ac-1051">操作系统名称：Windows 8.1 企业版</span><span class="sxs-lookup"><span data-stu-id="f31ac-1051">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="f31ac-1052">视觉工作室 2013 – 终极。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1052">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112-hardware-environment"></a><span data-ttu-id="f31ac-1053">11.1.1.2 硬件环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1053">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="f31ac-1054">双处理器：英特尔（R） Xeon（R） CPU L5520 W3530 = 2.27GHz，2261 Mhz8 GHz，4 核（s），84 逻辑处理器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1054">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="f31ac-1055">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1055">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="f31ac-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s 驱动器拆分为 4 个分区。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-db-server"></a><span data-ttu-id="f31ac-1057">11.1.2 数据库服务器</span><span class="sxs-lookup"><span data-stu-id="f31ac-1057">11.1.2       DB server</span></span>

##### <a name="11121-software-environment"></a><span data-ttu-id="f31ac-1058">11.1.2.1 软件环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1058">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="f31ac-1059">操作系统名称：Windows 服务器 2008 R28.1 企业 SP1。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1059">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="f31ac-1060">SQL 服务器 2008 R22012。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1060">SQL Server 2008 R22012.</span></span>

##### <a name="11122-hardware-environment"></a><span data-ttu-id="f31ac-1061">11.1.2.2 硬件环境</span><span class="sxs-lookup"><span data-stu-id="f31ac-1061">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="f31ac-1062">单处理器：英特尔（R） Xeon（R） CPU L5520 = 2.27GHz，2261 MhzES-1620 0 = 3.60GHz，4 个核心（s），8 个逻辑处理器。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1062">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="f31ac-1063">824 GB 拉姆拉姆。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1063">824 GB RamRAM.</span></span>
-   <span data-ttu-id="f31ac-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s 驱动器拆分为 4 个分区。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112-b-query-performance-comparison-tests"></a><span data-ttu-id="f31ac-1065">11.2 B. 查询性能比较测试</span><span class="sxs-lookup"><span data-stu-id="f31ac-1065">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="f31ac-1066">北风模型用于执行这些测试。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1066">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="f31ac-1067">它是使用实体框架设计器从数据库生成的。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1067">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="f31ac-1068">然后，使用以下代码比较查询执行选项的性能：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1068">Then, the following code was used to compare the performance of the query execution options:</span></span>

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

### <a name="113-c-navision-model"></a><span data-ttu-id="f31ac-1069">11.3 C. 纳维视觉模型</span><span class="sxs-lookup"><span data-stu-id="f31ac-1069">11.3 C. Navision Model</span></span>

<span data-ttu-id="f31ac-1070">Navision 数据库是一个大型数据库，用于演示 Microsoft 动态和导航。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1070">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="f31ac-1071">生成的概念模型包含 1005 个实体集和 4227 个关联集。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1071">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="f31ac-1072">测试中使用的模型为"平面" - 未向其添加继承。</span><span class="sxs-lookup"><span data-stu-id="f31ac-1072">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="f31ac-1073">11.3.1 用于 Navision 测试的查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-1073">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="f31ac-1074">与 Navision 模型一起使用的查询列表包含 3 类实体 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1074">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="f31ac-1075">11.3.1.1 查找</span><span class="sxs-lookup"><span data-stu-id="f31ac-1075">11.3.1.1 Lookup</span></span>

<span data-ttu-id="f31ac-1076">没有聚合的简单查找查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-1076">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="f31ac-1077">数量： 16232</span><span class="sxs-lookup"><span data-stu-id="f31ac-1077">Count: 16232</span></span>
-   <span data-ttu-id="f31ac-1078">示例：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1078">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a><span data-ttu-id="f31ac-1079">11.3.1.2 单聚合</span><span class="sxs-lookup"><span data-stu-id="f31ac-1079">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="f31ac-1080">具有多个聚合但没有子值（单个查询）的普通 BI 查询</span><span class="sxs-lookup"><span data-stu-id="f31ac-1080">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="f31ac-1081">数量： 2313</span><span class="sxs-lookup"><span data-stu-id="f31ac-1081">Count: 2313</span></span>
-   <span data-ttu-id="f31ac-1082">示例：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1082">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="f31ac-1083">其中 MDF\_\_会话\_登录时间最大值（） 在模型中定义为：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1083">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a><span data-ttu-id="f31ac-1084">11.3.1.3 聚合子总计</span><span class="sxs-lookup"><span data-stu-id="f31ac-1084">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="f31ac-1085">包含聚合和小计的 BI 查询（通过全部联合）</span><span class="sxs-lookup"><span data-stu-id="f31ac-1085">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="f31ac-1086">计数： 178</span><span class="sxs-lookup"><span data-stu-id="f31ac-1086">Count: 178</span></span>
-   <span data-ttu-id="f31ac-1087">示例：</span><span class="sxs-lookup"><span data-stu-id="f31ac-1087">Example:</span></span>

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

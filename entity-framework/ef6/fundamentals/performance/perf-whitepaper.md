---
title: EF4、EF5 和 EF6 的性能注意事项-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 07eb605f0d39f0c1bcfe781540525180f0dd0b22
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78416077"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a><span data-ttu-id="cb97a-102">EF 4、5和6的性能注意事项</span><span class="sxs-lookup"><span data-stu-id="cb97a-102">Performance considerations for EF 4, 5, and 6</span></span>
<span data-ttu-id="cb97a-103">按 David Obando、Eric Dettinger 和其他</span><span class="sxs-lookup"><span data-stu-id="cb97a-103">By David Obando, Eric Dettinger and others</span></span>

<span data-ttu-id="cb97a-104">发布日期：2012年4月</span><span class="sxs-lookup"><span data-stu-id="cb97a-104">Published: April 2012</span></span>

<span data-ttu-id="cb97a-105">上次更新时间：可能为2014</span><span class="sxs-lookup"><span data-stu-id="cb97a-105">Last updated: May 2014</span></span>

------------------------------------------------------------------------

## <a name="1-introduction"></a><span data-ttu-id="cb97a-106">1. 介绍</span><span class="sxs-lookup"><span data-stu-id="cb97a-106">1. Introduction</span></span>

<span data-ttu-id="cb97a-107">对象关系映射框架是一种简便的方法，可用于在面向对象的应用程序中提供数据访问的抽象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-107">Object-Relational Mapping frameworks are a convenient way to provide an abstraction for data access in an object-oriented application.</span></span> <span data-ttu-id="cb97a-108">对于 .NET 应用程序，Microsoft 推荐的 O/RM 实体框架。</span><span class="sxs-lookup"><span data-stu-id="cb97a-108">For .NET applications, Microsoft's recommended O/RM is Entity Framework.</span></span> <span data-ttu-id="cb97a-109">但对于任何抽象，性能都可能会成为问题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-109">With any abstraction though, performance can become a concern.</span></span>

<span data-ttu-id="cb97a-110">本文的目的是为了演示使用实体框架开发应用程序时的性能注意事项，使开发人员了解可能会影响性能的实体框架内部算法，并提供调查和提高使用实体框架的应用程序的性能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-110">This whitepaper was written to show the performance considerations when developing applications using Entity Framework, to give developers an idea of the Entity Framework internal algorithms that can affect performance, and to provide tips for investigation and improving performance in their applications that use Entity Framework.</span></span> <span data-ttu-id="cb97a-111">Web 上已经提供了有关性能的很多好主题，还尝试尽可能指向这些资源。</span><span class="sxs-lookup"><span data-stu-id="cb97a-111">There are a number of good topics on performance already available on the web, and we've also tried pointing to these resources where possible.</span></span>

<span data-ttu-id="cb97a-112">性能是一个棘手的话题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-112">Performance is a tricky topic.</span></span> <span data-ttu-id="cb97a-113">本白皮书旨在提供一种资源，帮助你为使用实体框架的应用程序做出性能相关的决策。</span><span class="sxs-lookup"><span data-stu-id="cb97a-113">This whitepaper is intended as a resource to help you make performance related decisions for your applications that use Entity Framework.</span></span> <span data-ttu-id="cb97a-114">我们提供了一些测试指标来演示性能，但这些度量值并不是要在应用程序中看到的性能的绝对指标。</span><span class="sxs-lookup"><span data-stu-id="cb97a-114">We have included some test metrics to demonstrate performance, but these metrics aren't intended as absolute indicators of the performance you will see in your application.</span></span>

<span data-ttu-id="cb97a-115">出于实用的目的，本文档假设在 .net 4.0 下运行实体框架4，实体框架5和6在 .NET 4.5 下运行。</span><span class="sxs-lookup"><span data-stu-id="cb97a-115">For practical purposes, this document assumes Entity Framework 4 is run under .NET 4.0 and Entity Framework 5 and 6 are run under .NET 4.5.</span></span> <span data-ttu-id="cb97a-116">为实体框架5所做的许多性能改进都位于 .NET 4.5 随附的核心组件中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-116">Many of the performance improvements made for Entity Framework 5 reside within the core components that ship with .NET 4.5.</span></span>

<span data-ttu-id="cb97a-117">实体框架6是带外版本，不依赖于随 .NET 提供实体框架组件。</span><span class="sxs-lookup"><span data-stu-id="cb97a-117">Entity Framework 6 is an out of band release and does not depend on the Entity Framework components that ship with .NET.</span></span> <span data-ttu-id="cb97a-118">实体框架6同时适用于 .NET 4.0 和 .NET 4.5，并可为尚未从 .NET 4.0 升级但希望应用程序中的最新实体框架位的用户提供大性能优势。</span><span class="sxs-lookup"><span data-stu-id="cb97a-118">Entity Framework 6 work on both .NET 4.0 and .NET 4.5, and can offer a big performance benefit to those who haven’t upgraded from .NET 4.0 but want the latest Entity Framework bits in their application.</span></span> <span data-ttu-id="cb97a-119">当本文档提到实体框架6时，它是指在撰写本文时可用的最新版本：版本6.1.0。</span><span class="sxs-lookup"><span data-stu-id="cb97a-119">When this document mentions Entity Framework 6, it refers to the latest version available at the time of this writing: version 6.1.0.</span></span>

## <a name="2-cold-vs-warm-query-execution"></a><span data-ttu-id="cb97a-120">2. 冷查询和热查询执行</span><span class="sxs-lookup"><span data-stu-id="cb97a-120">2. Cold vs. Warm Query Execution</span></span>

<span data-ttu-id="cb97a-121">第一次对给定模型进行任何查询时，实体框架在幕后执行大量工作以加载和验证模型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-121">The very first time any query is made against a given model, the Entity Framework does a lot of work behind the scenes to load and validate the model.</span></span> <span data-ttu-id="cb97a-122">我们经常将此第一个查询称为 "冷" 查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-122">We frequently refer to this first query as a "cold" query.</span></span><span data-ttu-id="cb97a-123">  针对已加载模型的进一步查询称为 "热" 查询，并更快。</span><span class="sxs-lookup"><span data-stu-id="cb97a-123">  Further queries against an already loaded model are known as "warm" queries, and are much faster.</span></span>

<span data-ttu-id="cb97a-124">让我们大致了解一下使用实体框架执行查询时所用的时间，并查看在实体框架6中的改进情况。</span><span class="sxs-lookup"><span data-stu-id="cb97a-124">Let’s take a high-level view of where time is spent when executing a query using Entity Framework, and see where things are improving in Entity Framework 6.</span></span>

<span data-ttu-id="cb97a-125">**第一次查询执行–冷查询**</span><span class="sxs-lookup"><span data-stu-id="cb97a-125">**First Query Execution – cold query**</span></span>

| <span data-ttu-id="cb97a-126">编写代码用户</span><span class="sxs-lookup"><span data-stu-id="cb97a-126">Code User Writes</span></span>                                                                                     | <span data-ttu-id="cb97a-127">操作</span><span class="sxs-lookup"><span data-stu-id="cb97a-127">Action</span></span>                    | <span data-ttu-id="cb97a-128">EF4 性能影响</span><span class="sxs-lookup"><span data-stu-id="cb97a-128">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="cb97a-129">EF5 性能影响</span><span class="sxs-lookup"><span data-stu-id="cb97a-129">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="cb97a-130">EF6 性能影响</span><span class="sxs-lookup"><span data-stu-id="cb97a-130">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="cb97a-131">上下文创建</span><span class="sxs-lookup"><span data-stu-id="cb97a-131">Context creation</span></span>          | <span data-ttu-id="cb97a-132">中等</span><span class="sxs-lookup"><span data-stu-id="cb97a-132">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                        | <span data-ttu-id="cb97a-133">中等</span><span class="sxs-lookup"><span data-stu-id="cb97a-133">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | <span data-ttu-id="cb97a-134">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-134">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="cb97a-135">查询表达式创建</span><span class="sxs-lookup"><span data-stu-id="cb97a-135">Query expression creation</span></span> | <span data-ttu-id="cb97a-136">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-136">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="cb97a-137">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-137">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="cb97a-138">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-138">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="cb97a-139">LINQ 查询执行</span><span class="sxs-lookup"><span data-stu-id="cb97a-139">LINQ query execution</span></span>      | <span data-ttu-id="cb97a-140">-元数据加载：高但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-140">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="cb97a-141">-视图生成：可能非常高但已被缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-141">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="cb97a-142">-参数求值：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-142">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="cb97a-143">-查询转换：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-143">- Query translation: Medium</span></span> <br/> <span data-ttu-id="cb97a-144">-Materializer 生成：中型但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-144">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="cb97a-145">-数据库查询执行：可能很高</span><span class="sxs-lookup"><span data-stu-id="cb97a-145">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="cb97a-146">+ 连接打开</span><span class="sxs-lookup"><span data-stu-id="cb97a-146">+ Connection.Open</span></span> <br/> <span data-ttu-id="cb97a-147">+ ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cb97a-147">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cb97a-148">+ DataReader。读取</span><span class="sxs-lookup"><span data-stu-id="cb97a-148">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cb97a-149">对象具体化：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-149">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cb97a-150">-Identity lookup：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-150">- Identity lookup: Medium</span></span> | <span data-ttu-id="cb97a-151">-元数据加载：高但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-151">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="cb97a-152">-视图生成：可能非常高但已被缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-152">- View generation: Potentially very high but cached</span></span> <br/> <span data-ttu-id="cb97a-153">-参数求值：低</span><span class="sxs-lookup"><span data-stu-id="cb97a-153">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cb97a-154">-查询转换：中型但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-154">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="cb97a-155">-Materializer 生成：中型但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-155">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="cb97a-156">-数据库查询执行：可能很高（某些情况下更好的查询）</span><span class="sxs-lookup"><span data-stu-id="cb97a-156">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cb97a-157">+ 连接打开</span><span class="sxs-lookup"><span data-stu-id="cb97a-157">+ Connection.Open</span></span> <br/> <span data-ttu-id="cb97a-158">+ ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cb97a-158">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cb97a-159">+ DataReader。读取</span><span class="sxs-lookup"><span data-stu-id="cb97a-159">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cb97a-160">对象具体化：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-160">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cb97a-161">-Identity lookup：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-161">- Identity lookup: Medium</span></span> | <span data-ttu-id="cb97a-162">-元数据加载：高但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-162">- Metadata loading: High but cached</span></span> <br/> <span data-ttu-id="cb97a-163">-视图生成：中型但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-163">- View generation: Medium but cached</span></span> <br/> <span data-ttu-id="cb97a-164">-参数求值：低</span><span class="sxs-lookup"><span data-stu-id="cb97a-164">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cb97a-165">-查询转换：中型但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-165">- Query translation: Medium but cached</span></span> <br/> <span data-ttu-id="cb97a-166">-Materializer 生成：中型但已缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-166">- Materializer generation: Medium but cached</span></span> <br/> <span data-ttu-id="cb97a-167">-数据库查询执行：可能很高（某些情况下更好的查询）</span><span class="sxs-lookup"><span data-stu-id="cb97a-167">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cb97a-168">+ 连接打开</span><span class="sxs-lookup"><span data-stu-id="cb97a-168">+ Connection.Open</span></span> <br/> <span data-ttu-id="cb97a-169">+ ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cb97a-169">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cb97a-170">+ DataReader。读取</span><span class="sxs-lookup"><span data-stu-id="cb97a-170">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cb97a-171">对象具体化：中型（速度快于 EF5）</span><span class="sxs-lookup"><span data-stu-id="cb97a-171">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="cb97a-172">-Identity lookup：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-172">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="cb97a-173">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="cb97a-173">Connection.Close</span></span>          | <span data-ttu-id="cb97a-174">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-174">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="cb97a-175">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-175">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="cb97a-176">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-176">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


<span data-ttu-id="cb97a-177">**第二个查询执行–热查询**</span><span class="sxs-lookup"><span data-stu-id="cb97a-177">**Second Query Execution – warm query**</span></span>

| <span data-ttu-id="cb97a-178">编写代码用户</span><span class="sxs-lookup"><span data-stu-id="cb97a-178">Code User Writes</span></span>                                                                                     | <span data-ttu-id="cb97a-179">操作</span><span class="sxs-lookup"><span data-stu-id="cb97a-179">Action</span></span>                    | <span data-ttu-id="cb97a-180">EF4 性能影响</span><span class="sxs-lookup"><span data-stu-id="cb97a-180">EF4 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="cb97a-181">EF5 性能影响</span><span class="sxs-lookup"><span data-stu-id="cb97a-181">EF5 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="cb97a-182">EF6 性能影响</span><span class="sxs-lookup"><span data-stu-id="cb97a-182">EF6 Performance Impact</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | <span data-ttu-id="cb97a-183">上下文创建</span><span class="sxs-lookup"><span data-stu-id="cb97a-183">Context creation</span></span>          | <span data-ttu-id="cb97a-184">中等</span><span class="sxs-lookup"><span data-stu-id="cb97a-184">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | <span data-ttu-id="cb97a-185">中等</span><span class="sxs-lookup"><span data-stu-id="cb97a-185">Medium</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | <span data-ttu-id="cb97a-186">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-186">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | <span data-ttu-id="cb97a-187">查询表达式创建</span><span class="sxs-lookup"><span data-stu-id="cb97a-187">Query expression creation</span></span> | <span data-ttu-id="cb97a-188">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-188">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="cb97a-189">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-189">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="cb97a-190">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-190">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | <span data-ttu-id="cb97a-191">LINQ 查询执行</span><span class="sxs-lookup"><span data-stu-id="cb97a-191">LINQ query execution</span></span>      | <span data-ttu-id="cb97a-192">-元数据~~加载~~查找：~~高但高速缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-192">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-193">-查看~~生成~~查找：~~可能非常高但缓存~~较低</span><span class="sxs-lookup"><span data-stu-id="cb97a-193">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-194">-参数求值：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-194">- Parameter evaluation: Medium</span></span> <br/> <span data-ttu-id="cb97a-195">-查询~~转换~~查找：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-195">- Query ~~translation~~ lookup: Medium</span></span> <br/> <span data-ttu-id="cb97a-196">-Materializer~~生成~~查找：~~中速但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-196">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-197">-数据库查询执行：可能很高</span><span class="sxs-lookup"><span data-stu-id="cb97a-197">- Database query execution: Potentially high</span></span> <br/> <span data-ttu-id="cb97a-198">+ 连接打开</span><span class="sxs-lookup"><span data-stu-id="cb97a-198">+ Connection.Open</span></span> <br/> <span data-ttu-id="cb97a-199">+ ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cb97a-199">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cb97a-200">+ DataReader。读取</span><span class="sxs-lookup"><span data-stu-id="cb97a-200">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cb97a-201">对象具体化：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-201">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cb97a-202">-Identity lookup：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-202">- Identity lookup: Medium</span></span> | <span data-ttu-id="cb97a-203">-元数据~~加载~~查找：~~高但高速缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-203">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-204">-查看~~生成~~查找：~~可能非常高但缓存~~较低</span><span class="sxs-lookup"><span data-stu-id="cb97a-204">- View ~~generation~~ lookup: ~~Potentially very high but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-205">-参数求值：低</span><span class="sxs-lookup"><span data-stu-id="cb97a-205">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cb97a-206">-查询~~转换~~查找：~~中但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-206">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-207">-Materializer~~生成~~查找：~~中速但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-207">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-208">-数据库查询执行：可能很高（某些情况下更好的查询）</span><span class="sxs-lookup"><span data-stu-id="cb97a-208">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cb97a-209">+ 连接打开</span><span class="sxs-lookup"><span data-stu-id="cb97a-209">+ Connection.Open</span></span> <br/> <span data-ttu-id="cb97a-210">+ ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cb97a-210">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cb97a-211">+ DataReader。读取</span><span class="sxs-lookup"><span data-stu-id="cb97a-211">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cb97a-212">对象具体化：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-212">Object materialization: Medium</span></span> <br/> <span data-ttu-id="cb97a-213">-Identity lookup：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-213">- Identity lookup: Medium</span></span> | <span data-ttu-id="cb97a-214">-元数据~~加载~~查找：~~高但高速缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-214">- Metadata ~~loading~~ lookup: ~~High but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-215">-查看~~生成~~查找：~~中但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-215">- View ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-216">-参数求值：低</span><span class="sxs-lookup"><span data-stu-id="cb97a-216">- Parameter evaluation: Low</span></span> <br/> <span data-ttu-id="cb97a-217">-查询~~转换~~查找：~~中但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-217">- Query ~~translation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-218">-Materializer~~生成~~查找：~~中速但缓存~~低</span><span class="sxs-lookup"><span data-stu-id="cb97a-218">- Materializer ~~generation~~ lookup: ~~Medium but cached~~ Low</span></span> <br/> <span data-ttu-id="cb97a-219">-数据库查询执行：可能很高（某些情况下更好的查询）</span><span class="sxs-lookup"><span data-stu-id="cb97a-219">- Database query execution: Potentially high (Better queries in some situations)</span></span> <br/> <span data-ttu-id="cb97a-220">+ 连接打开</span><span class="sxs-lookup"><span data-stu-id="cb97a-220">+ Connection.Open</span></span> <br/> <span data-ttu-id="cb97a-221">+ ExecuteReader</span><span class="sxs-lookup"><span data-stu-id="cb97a-221">+ Command.ExecuteReader</span></span> <br/> <span data-ttu-id="cb97a-222">+ DataReader。读取</span><span class="sxs-lookup"><span data-stu-id="cb97a-222">+ DataReader.Read</span></span> <br/> <span data-ttu-id="cb97a-223">对象具体化：中型（速度快于 EF5）</span><span class="sxs-lookup"><span data-stu-id="cb97a-223">Object materialization: Medium (Faster than EF5)</span></span> <br/> <span data-ttu-id="cb97a-224">-Identity lookup：中型</span><span class="sxs-lookup"><span data-stu-id="cb97a-224">- Identity lookup: Medium</span></span> |
| `}`                                                                                                  | <span data-ttu-id="cb97a-225">Connection.Close</span><span class="sxs-lookup"><span data-stu-id="cb97a-225">Connection.Close</span></span>          | <span data-ttu-id="cb97a-226">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-226">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | <span data-ttu-id="cb97a-227">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-227">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | <span data-ttu-id="cb97a-228">低</span><span class="sxs-lookup"><span data-stu-id="cb97a-228">Low</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


<span data-ttu-id="cb97a-229">可以通过多种方式降低冷查询和热查询的性能成本，我们将在下一节中介绍这些方法。</span><span class="sxs-lookup"><span data-stu-id="cb97a-229">There are several ways to reduce the performance cost of both cold and warm queries, and we'll take a look at these in the following section.</span></span> <span data-ttu-id="cb97a-230">具体而言，我们将通过使用预先生成的视图来了解如何通过使用预先生成的视图来减少冷查询中模型加载的成本，这有助于减少在视图生成过程中遇到的性能难题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-230">Specifically, we'll look at reducing the cost of model loading in cold queries by using pre-generated views, which should help alleviate performance pains experienced during view generation.</span></span> <span data-ttu-id="cb97a-231">对于热查询，我们将介绍查询计划缓存，无跟踪查询，以及不同的查询执行选项。</span><span class="sxs-lookup"><span data-stu-id="cb97a-231">For warm queries, we'll cover query plan caching, no tracking queries, and different query execution options.</span></span>

### <a name="21-what-is-view-generation"></a><span data-ttu-id="cb97a-232">2.1 什么是视图生成？</span><span class="sxs-lookup"><span data-stu-id="cb97a-232">2.1 What is View Generation?</span></span>

<span data-ttu-id="cb97a-233">为了了解生成的视图，我们必须首先了解 "映射视图" 是什么。</span><span class="sxs-lookup"><span data-stu-id="cb97a-233">In order to understand what view generation is, we must first understand what “Mapping Views” are.</span></span> <span data-ttu-id="cb97a-234">映射视图是映射中为每个实体集和关联指定的转换的可执行表示形式。</span><span class="sxs-lookup"><span data-stu-id="cb97a-234">Mapping Views are executable representations of the transformations specified in the mapping for each entity set and association.</span></span> <span data-ttu-id="cb97a-235">在内部，这些映射视图采用 CQTs （规范查询树）的形式。</span><span class="sxs-lookup"><span data-stu-id="cb97a-235">Internally, these mapping views take the shape of CQTs (canonical query trees).</span></span> <span data-ttu-id="cb97a-236">有两种类型的映射视图：</span><span class="sxs-lookup"><span data-stu-id="cb97a-236">There are two types of mapping views:</span></span>

-   <span data-ttu-id="cb97a-237">查询视图：这表示从数据库架构转到概念模型所需的转换。</span><span class="sxs-lookup"><span data-stu-id="cb97a-237">Query views: these represent the transformation necessary to go from the database schema to the conceptual model.</span></span>
-   <span data-ttu-id="cb97a-238">更新视图：这表示从概念模型转换到数据库架构所需的转换。</span><span class="sxs-lookup"><span data-stu-id="cb97a-238">Update views: these represent the transformation necessary to go from the conceptual model to the database schema.</span></span>

<span data-ttu-id="cb97a-239">请记住，概念模型可能不同于数据库架构。</span><span class="sxs-lookup"><span data-stu-id="cb97a-239">Keep in mind that the conceptual model might differ from the database schema in various ways.</span></span> <span data-ttu-id="cb97a-240">例如，一个表可能用于存储两个不同实体类型的数据。</span><span class="sxs-lookup"><span data-stu-id="cb97a-240">For example, one single table might be used to store the data for two different entity types.</span></span> <span data-ttu-id="cb97a-241">继承和非日常映射在映射视图的复杂性方面扮演着角色。</span><span class="sxs-lookup"><span data-stu-id="cb97a-241">Inheritance and non-trivial mappings play a role in the complexity of the mapping views.</span></span>

<span data-ttu-id="cb97a-242">基于映射规范计算这些视图的过程是所谓的视图生成。</span><span class="sxs-lookup"><span data-stu-id="cb97a-242">The process of computing these views based on the specification of the mapping is what we call view generation.</span></span> <span data-ttu-id="cb97a-243">在加载模型时，或在生成时使用 "预生成的视图"，可以动态地执行视图生成;后者以实体 SQL 语句的形式序列化为 C\# 或 VB 文件。</span><span class="sxs-lookup"><span data-stu-id="cb97a-243">View generation can either take place dynamically when a model is loaded, or at build time, by using "pre-generated views"; the latter are serialized in the form of Entity SQL statements to a C\# or VB file.</span></span>

<span data-ttu-id="cb97a-244">当生成视图时，还会对其进行验证。</span><span class="sxs-lookup"><span data-stu-id="cb97a-244">When views are generated, they are also validated.</span></span> <span data-ttu-id="cb97a-245">从性能角度来看，视图生成的大部分开销实际上是对视图的验证，这可以确保实体之间的连接有意义，并且具有所有支持操作的正确基数。</span><span class="sxs-lookup"><span data-stu-id="cb97a-245">From a performance standpoint, the vast majority of the cost of view generation is actually the validation of the views which ensures that the connections between the entities make sense and have the correct cardinality for all the supported operations.</span></span>

<span data-ttu-id="cb97a-246">当执行对实体集的查询时，查询将与相应的查询视图结合使用，此组合的结果将通过计划编译器运行，以创建后备存储可以理解的查询表示形式。</span><span class="sxs-lookup"><span data-stu-id="cb97a-246">When a query over an entity set is executed, the query is combined with the corresponding query view, and the result of this composition is run through the plan compiler to create the representation of the query that the backing store can understand.</span></span> <span data-ttu-id="cb97a-247">对于 SQL Server，此编译的最终结果将为 T-sql SELECT 语句。</span><span class="sxs-lookup"><span data-stu-id="cb97a-247">For SQL Server, the final result of this compilation will be a T-SQL SELECT statement.</span></span> <span data-ttu-id="cb97a-248">第一次执行对实体集的更新时，更新视图将通过类似的进程运行，以将其转换为目标数据库的 DML 语句。</span><span class="sxs-lookup"><span data-stu-id="cb97a-248">The first time an update over an entity set is performed, the update view is run through a similar process to transform it into DML statements for the target database.</span></span>

### <a name="22-factors-that-affect-view-generation-performance"></a><span data-ttu-id="cb97a-249">影响视图生成性能的2.2 因素</span><span class="sxs-lookup"><span data-stu-id="cb97a-249">2.2 Factors that affect View Generation performance</span></span>

<span data-ttu-id="cb97a-250">视图生成步骤的性能不仅取决于模型的大小，还取决于模型的互连方式。</span><span class="sxs-lookup"><span data-stu-id="cb97a-250">The performance of view generation step not only depends on the size of your model but also on how interconnected the model is.</span></span> <span data-ttu-id="cb97a-251">如果通过继承链或关联来连接两个实体，则称它们处于连接状态。</span><span class="sxs-lookup"><span data-stu-id="cb97a-251">If two Entities are connected via an inheritance chain or an Association, they are said to be connected.</span></span> <span data-ttu-id="cb97a-252">同样，如果两个表通过外键连接，它们就会连接起来。</span><span class="sxs-lookup"><span data-stu-id="cb97a-252">Similarly if two tables are connected via a foreign key, they are connected.</span></span> <span data-ttu-id="cb97a-253">随着架构中连接的实体和表的数量增加，视图生成成本会增加。</span><span class="sxs-lookup"><span data-stu-id="cb97a-253">As the number of connected Entities and tables in your schemas increase, the view generation cost increases.</span></span>

<span data-ttu-id="cb97a-254">在最糟糕的情况下，用于生成和验证视图的算法是指数的，尽管我们确实使用某些优化来改进此功能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-254">The algorithm that we use to generate and validate views is exponential in the worst case, though we do use some optimizations to improve this.</span></span> <span data-ttu-id="cb97a-255">对性能产生负面影响的最大因素包括：</span><span class="sxs-lookup"><span data-stu-id="cb97a-255">The biggest factors that seem to negatively affect performance are:</span></span>

-   <span data-ttu-id="cb97a-256">模型大小，引用实体数以及这些实体之间的关联量。</span><span class="sxs-lookup"><span data-stu-id="cb97a-256">Model size, referring to the number of entities and the amount of associations between these entities.</span></span>
-   <span data-ttu-id="cb97a-257">模型复杂性，尤其是涉及大量类型的继承。</span><span class="sxs-lookup"><span data-stu-id="cb97a-257">Model complexity, specifically inheritance involving a large number of types.</span></span>
-   <span data-ttu-id="cb97a-258">使用独立关联，而不是外键关联。</span><span class="sxs-lookup"><span data-stu-id="cb97a-258">Using Independent Associations, instead of Foreign Key Associations.</span></span>

<span data-ttu-id="cb97a-259">对于小型的简单模型，成本可能会足够小，无法使用预先生成的视图。</span><span class="sxs-lookup"><span data-stu-id="cb97a-259">For small, simple models the cost may be small enough to not bother using pre-generated views.</span></span> <span data-ttu-id="cb97a-260">随着模型大小和复杂性的增加，可以使用多个选项来降低查看生成和验证的成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-260">As model size and complexity increase, there are several options available to reduce the cost of view generation and validation.</span></span>

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a><span data-ttu-id="cb97a-261">2.3 使用预先生成的视图缩短模型加载时间</span><span class="sxs-lookup"><span data-stu-id="cb97a-261">2.3 Using Pre-Generated Views to decrease model load time</span></span>

<span data-ttu-id="cb97a-262">有关如何使用实体框架6上预先生成的视图的详细信息，请访问[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)</span><span class="sxs-lookup"><span data-stu-id="cb97a-262">For detailed information on how to use pre-generated views on Entity Framework 6 visit [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md)</span></span>

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a><span data-ttu-id="cb97a-263">使用实体框架 Power Tools 社区版的2.3.1 预生成的视图</span><span class="sxs-lookup"><span data-stu-id="cb97a-263">2.3.1 Pre-Generated views using the Entity Framework Power Tools Community Edition</span></span>

<span data-ttu-id="cb97a-264">您可以使用[实体框架 6 Power Tools 社区版](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)通过右键单击模型类文件并使用实体框架菜单选择 "生成视图" 来生成 EDMX 和 Code First 模型的视图。</span><span class="sxs-lookup"><span data-stu-id="cb97a-264">You can use the [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) to generate views of EDMX and Code First models by right-clicking the model class file and using the Entity Framework menu to select “Generate Views”.</span></span> <span data-ttu-id="cb97a-265">实体框架 Power Tools 社区版仅适用于 DbContext 派生的上下文。</span><span class="sxs-lookup"><span data-stu-id="cb97a-265">The Entity Framework Power Tools Community Edition work only on DbContext-derived contexts.</span></span>

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a><span data-ttu-id="cb97a-266">2.3.2 如何通过 Edmgen.exe 创建的模型使用预先生成的视图</span><span class="sxs-lookup"><span data-stu-id="cb97a-266">2.3.2 How to use Pre-generated views with a model created by EDMGen</span></span>

<span data-ttu-id="cb97a-267">Edmgen.exe 是随 .NET 提供的实用程序，与实体框架4和5（而不是实体框架6）结合使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-267">EDMGen is a utility that ships with .NET and works with Entity Framework 4 and 5, but not with Entity Framework 6.</span></span> <span data-ttu-id="cb97a-268">Edmgen.exe 允许您从命令行生成模型文件、对象层和视图。</span><span class="sxs-lookup"><span data-stu-id="cb97a-268">EDMGen allows you to generate a model file, the object layer and the views from the command line.</span></span> <span data-ttu-id="cb97a-269">其中一个输出将是你选择的语言（VB 或 C）\#的视图文件。</span><span class="sxs-lookup"><span data-stu-id="cb97a-269">One of the outputs will be a Views file in your language of choice, VB or C\#.</span></span> <span data-ttu-id="cb97a-270">这是一个代码文件，其中包含每个实体集的实体 SQL 代码段。</span><span class="sxs-lookup"><span data-stu-id="cb97a-270">This is a code file containing Entity SQL snippets for each entity set.</span></span> <span data-ttu-id="cb97a-271">若要启用预生成的视图，只需将该文件包含在项目中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-271">To enable pre-generated views, you simply include the file in your project.</span></span>

<span data-ttu-id="cb97a-272">如果手动编辑模型的架构文件，则需要重新生成视图文件。</span><span class="sxs-lookup"><span data-stu-id="cb97a-272">If you manually make edits to the schema files for the model, you will need to re-generate the views file.</span></span> <span data-ttu-id="cb97a-273">可以通过使用 **/mode： ViewGeneration**标志运行 edmgen.exe 来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-273">You can do this by running EDMGen with the **/mode:ViewGeneration** flag.</span></span>

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a><span data-ttu-id="cb97a-274">2.3.3 如何使用带有 EDMX 文件的预生成视图</span><span class="sxs-lookup"><span data-stu-id="cb97a-274">2.3.3 How to use Pre-Generated Views with an EDMX file</span></span>

<span data-ttu-id="cb97a-275">你还可以使用 Edmgen.exe 为 EDMX 文件生成视图-前面引用的 MSDN 主题介绍如何添加预先生成事件来执行此操作，但这很复杂，但在某些情况下，不可能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-275">You can also use EDMGen to generate views for an EDMX file - the previously referenced MSDN topic describes how to add a pre-build event to do this - but this is complicated and there are some cases where it isn't possible.</span></span> <span data-ttu-id="cb97a-276">通常，在模型位于 edmx 文件中时，使用 T4 模板生成视图通常更容易。</span><span class="sxs-lookup"><span data-stu-id="cb97a-276">It's generally easier to use a T4 template to generate the views when your model is in an edmx file.</span></span>

<span data-ttu-id="cb97a-277">ADO.NET 团队博客提供了一篇文章，介绍如何使用 T4 模板生成视图（\<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-277">The ADO.NET team blog has a post that describes how to use a T4 template for view generation ( \<http://blogs.msdn.com/b/adonet/archive/2008/06/20/how-to-use-a-t4-template-for-view-generation.aspx>).</span></span> <span data-ttu-id="cb97a-278">此文章包含可下载并添加到项目中的模板。</span><span class="sxs-lookup"><span data-stu-id="cb97a-278">This post includes a template that can be downloaded and added to your project.</span></span> <span data-ttu-id="cb97a-279">模板是为实体框架的第一个版本而编写的，因此不能保证它们使用最新版本的实体框架。</span><span class="sxs-lookup"><span data-stu-id="cb97a-279">The template was written for the first version of Entity Framework, so they aren’t guaranteed to work with the latest versions of Entity Framework.</span></span> <span data-ttu-id="cb97a-280">不过，你可以为实体框架4和5from 的 Visual Studio 库下载更多最新的视图生成模板集：</span><span class="sxs-lookup"><span data-stu-id="cb97a-280">However, you can download a more up-to-date set of view generation templates for Entity Framework 4 and 5from the Visual Studio Gallery:</span></span>

-   <span data-ttu-id="cb97a-281">VB.NET： \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span><span class="sxs-lookup"><span data-stu-id="cb97a-281">VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d></span></span>
-   <span data-ttu-id="cb97a-282">C\#： \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span><span class="sxs-lookup"><span data-stu-id="cb97a-282">C\#: \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d></span></span>

<span data-ttu-id="cb97a-283">如果使用实体框架6，则可以从 Visual Studio 库中的 \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>获取查看生成 T4 模板。</span><span class="sxs-lookup"><span data-stu-id="cb97a-283">If you’re using Entity Framework 6 you can get the view generation T4 templates from the Visual Studio Gallery at \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>.</span></span>

### <a name="24-reducing-the-cost-of-view-generation"></a><span data-ttu-id="cb97a-284">2.4 降低视图生成成本</span><span class="sxs-lookup"><span data-stu-id="cb97a-284">2.4 Reducing the cost of view generation</span></span>

<span data-ttu-id="cb97a-285">使用预先生成的视图会将视图生成的成本从模型加载（运行时）移动到设计时。</span><span class="sxs-lookup"><span data-stu-id="cb97a-285">Using pre-generated views moves the cost of view generation from model loading (run time) to design time.</span></span> <span data-ttu-id="cb97a-286">虽然这在运行时提高了启动性能，但在开发时仍会遇到视图生成难点。</span><span class="sxs-lookup"><span data-stu-id="cb97a-286">While this improves startup performance at runtime, you will still experience the pain of view generation while you are developing.</span></span> <span data-ttu-id="cb97a-287">在编译时和运行时，有几个其他技巧可以帮助降低视图生成的成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-287">There are several additional tricks that can help reduce the cost of view generation, both at compile time and run time.</span></span>

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a><span data-ttu-id="cb97a-288">2.4.1 使用外键关联降低视图生成成本</span><span class="sxs-lookup"><span data-stu-id="cb97a-288">2.4.1 Using Foreign Key Associations to reduce view generation cost</span></span>

<span data-ttu-id="cb97a-289">我们发现了很多情况，即从独立的关联关联到外键关联，将模型中的关联切换到外键关联大大缩短了视图生成所花费的时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-289">We have seen a number of cases where switching the associations in the model from Independent Associations to Foreign Key Associations dramatically improved the time spent in view generation.</span></span>

<span data-ttu-id="cb97a-290">为了演示这种改进，我们使用 Edmgen.exe 生成了两个版本的 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-290">To demonstrate this improvement, we generated two versions of the Navision model by using EDMGen.</span></span> <span data-ttu-id="cb97a-291">*注意：有关 Navision 模型的说明，请参阅附录 C。*</span><span class="sxs-lookup"><span data-stu-id="cb97a-291">*Note: see appendix C for a description of the Navision model.*</span></span> <span data-ttu-id="cb97a-292">由于在此练习中，Navision 模型的实体和关系非常多，因此它很感兴趣。</span><span class="sxs-lookup"><span data-stu-id="cb97a-292">The Navision model is interesting for this exercise due to its very large amount of entities and relationships between them.</span></span>

<span data-ttu-id="cb97a-293">此非常大的模型的一个版本是通过外键关联生成的，另一个版本是通过独立关联生成的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-293">One version of this very large model was generated with Foreign Keys Associations and the other was generated with Independent Associations.</span></span> <span data-ttu-id="cb97a-294">接下来，我们将为每个模型生成视图所需的时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-294">We then timed how long it took to generate the views for each model.</span></span> <span data-ttu-id="cb97a-295">实体框架5测试使用 EntityViewGenerator 类中的 GenerateViews （）方法来生成视图，而实体框架6测试使用类 StorageMappingItemCollection 中的 GenerateViews （）方法。</span><span class="sxs-lookup"><span data-stu-id="cb97a-295">Entity Framework 5 test used the GenerateViews() method from class EntityViewGenerator to generate the views, while the Entity Framework 6 test used the GenerateViews() method from class StorageMappingItemCollection.</span></span> <span data-ttu-id="cb97a-296">这是因为实体框架6代码库中发生了代码重构。</span><span class="sxs-lookup"><span data-stu-id="cb97a-296">This due to code restructuring that occurred in the Entity Framework 6 codebase.</span></span>

<span data-ttu-id="cb97a-297">使用实体框架5，在实验室计算机中使用外键查看模型的生成耗时为65分钟。</span><span class="sxs-lookup"><span data-stu-id="cb97a-297">Using Entity Framework 5, view generation for the model with Foreign Keys took 65 minutes in a lab machine.</span></span> <span data-ttu-id="cb97a-298">未知的是为使用独立关联的模型生成视图所需的时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-298">It's unknown how long it would have taken to generate the views for the model that used independent associations.</span></span> <span data-ttu-id="cb97a-299">在实验室中，我们将运行的测试留给了在实验室中重新启动计算机以安装每月更新。</span><span class="sxs-lookup"><span data-stu-id="cb97a-299">We left the test running for over a month before the machine was rebooted in our lab to install monthly updates.</span></span>

<span data-ttu-id="cb97a-300">使用实体框架6，在同一实验室计算机中使用外键查看模型的生成时间为28秒。</span><span class="sxs-lookup"><span data-stu-id="cb97a-300">Using Entity Framework 6, view generation for the model with Foreign Keys took 28 seconds in the same lab machine.</span></span> <span data-ttu-id="cb97a-301">使用独立关联的模型的视图生成花费了58秒。</span><span class="sxs-lookup"><span data-stu-id="cb97a-301">View generation for the model that uses Independent Associations took 58 seconds.</span></span> <span data-ttu-id="cb97a-302">对视图生成代码实体框架6进行的改进意味着许多项目不需要预生成的视图即可获得更快的启动时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-302">The improvements done to Entity Framework 6 on its view generation code mean that many projects won’t need pre-generated views to obtain faster startup times.</span></span>

<span data-ttu-id="cb97a-303">请注意，在实体框架4和5中预先生成的视图可以通过 Edmgen.exe 或实体框架的增强工具来完成。</span><span class="sxs-lookup"><span data-stu-id="cb97a-303">It’s important to remark that pre-generating views in Entity Framework 4 and 5 can be done with EDMGen or the Entity Framework Power Tools.</span></span> <span data-ttu-id="cb97a-304">对于实体框架6视图，可以通过实体框架的 Power Tools 或编程方式完成，如[预生成的映射视图](~/ef6/fundamentals/performance/pre-generated-views.md)中所述。</span><span class="sxs-lookup"><span data-stu-id="cb97a-304">For Entity Framework 6 view generation can be done via the Entity Framework Power Tools or programmatically as described in [Pre-Generated Mapping Views](~/ef6/fundamentals/performance/pre-generated-views.md).</span></span>

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a><span data-ttu-id="cb97a-305">2.4.1.1 如何使用外键，而不是独立关联</span><span class="sxs-lookup"><span data-stu-id="cb97a-305">2.4.1.1 How to use Foreign Keys instead of Independent Associations</span></span>

<span data-ttu-id="cb97a-306">在 Visual Studio 中使用 Edmgen.exe 或 Entity Designer 时，默认情况下会获取 Fk，并只使用单个 checkbox 或命令行标志在 Fk 和 IAs 之间切换。</span><span class="sxs-lookup"><span data-stu-id="cb97a-306">When using EDMGen or the Entity Designer in Visual Studio, you get FKs by default, and it only takes a single checkbox or command line flag to switch between FKs and IAs.</span></span>

<span data-ttu-id="cb97a-307">如果使用的是大型 Code First 模型，则使用独立关联将对视图生成产生相同的效果。</span><span class="sxs-lookup"><span data-stu-id="cb97a-307">If you have a large Code First model, using Independent Associations will have the same effect on view generation.</span></span> <span data-ttu-id="cb97a-308">可以通过在依赖对象的类上包含外键属性来避免这种影响，尽管某些开发人员会将其视为污染其对象模型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-308">You can avoid this impact by including Foreign Key properties on the classes for your dependent objects, though some developers will consider this to be polluting their object model.</span></span> <span data-ttu-id="cb97a-309">可以在 \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>中找到有关此主题的详细信息。</span><span class="sxs-lookup"><span data-stu-id="cb97a-309">You can find more information on this subject in \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>.</span></span>

| <span data-ttu-id="cb97a-310">当使用</span><span class="sxs-lookup"><span data-stu-id="cb97a-310">When using</span></span>      | <span data-ttu-id="cb97a-311">操作</span><span class="sxs-lookup"><span data-stu-id="cb97a-311">Do this</span></span>                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cb97a-312">实体设计器</span><span class="sxs-lookup"><span data-stu-id="cb97a-312">Entity Designer</span></span> | <span data-ttu-id="cb97a-313">在两个实体之间添加关联后，请确保具有引用约束。</span><span class="sxs-lookup"><span data-stu-id="cb97a-313">After adding an association between two entities, make sure you have a referential constraint.</span></span> <span data-ttu-id="cb97a-314">引用约束告诉实体框架使用外键，而不是独立关联。</span><span class="sxs-lookup"><span data-stu-id="cb97a-314">Referential constraints tell Entity Framework to use Foreign Keys instead of Independent Associations.</span></span> <span data-ttu-id="cb97a-315">有关更多详细信息，请访问 \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-315">For additional details visit \<http://blogs.msdn.com/b/efdesign/archive/2009/03/16/foreign-keys-in-the-entity-framework.aspx>.</span></span> |
| <span data-ttu-id="cb97a-316">Edmgen.exe</span><span class="sxs-lookup"><span data-stu-id="cb97a-316">EDMGen</span></span>          | <span data-ttu-id="cb97a-317">当使用 Edmgen.exe 从数据库生成文件时，将遵循外键，并将其添加到模型中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-317">When using EDMGen to generate your files from the database, your Foreign Keys will be respected and added to the model as such.</span></span> <span data-ttu-id="cb97a-318">有关 Edmgen.exe 公开的不同选项的详细信息，请[http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-318">For more information on the different options exposed by EDMGen visit [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx).</span></span>                           |
| <span data-ttu-id="cb97a-319">Code First</span><span class="sxs-lookup"><span data-stu-id="cb97a-319">Code First</span></span>      | <span data-ttu-id="cb97a-320">有关如何在使用 Code First 时包含依赖对象的外键属性的信息，请参阅[Code First 约定](~/ef6/modeling/code-first/conventions/built-in.md)主题的 "关系约定" 部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-320">See the "Relationship Convention" section of the [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) topic for information on how to include foreign key properties on dependent objects when using Code First.</span></span>                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a><span data-ttu-id="cb97a-321">2.4.2 sections 将模型移到单独的程序集</span><span class="sxs-lookup"><span data-stu-id="cb97a-321">2.4.2 Moving your model to a separate assembly</span></span>

<span data-ttu-id="cb97a-322">如果您的模型直接包含在您的应用程序的项目中，并通过预生成事件或 T4 模板生成视图，则在重新生成项目时，将会进行查看生成和验证，即使模型未更改也是如此。</span><span class="sxs-lookup"><span data-stu-id="cb97a-322">When your model is included directly in your application's project and you generate views through a pre-build event or a T4 template, view generation and validation will take place whenever the project is rebuilt, even if the model wasn't changed.</span></span> <span data-ttu-id="cb97a-323">如果将模型移到单独的程序集，并从应用程序的项目中引用它，则可以对应用程序进行其他更改，而无需重新生成包含该模型的项目。</span><span class="sxs-lookup"><span data-stu-id="cb97a-323">If you move the model to a separate assembly and reference it from your application's project, you can make other changes to your application without needing to rebuild the project containing the model.</span></span>

<span data-ttu-id="cb97a-324">*注意  ：* 将模型移动到不同的程序集时，请记住将模型的连接字符串复制到客户端项目的应用程序配置文件中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-324">*Note:*  when moving your model to separate assemblies remember to copy the connection strings for the model into the application configuration file of the client project.</span></span>

#### <a name="243-disable-validation-of-an-edmx-based-model"></a><span data-ttu-id="cb97a-325">2.4.3 禁用基于 edmx 的模型的验证</span><span class="sxs-lookup"><span data-stu-id="cb97a-325">2.4.3 Disable validation of an edmx-based model</span></span>

<span data-ttu-id="cb97a-326">在编译时验证 EDMX 模型，即使模型未更改也是如此。</span><span class="sxs-lookup"><span data-stu-id="cb97a-326">EDMX models are validated at compile time, even if the model is unchanged.</span></span> <span data-ttu-id="cb97a-327">如果您的模型已经过验证，则可以通过在 "属性" 窗口中将 "对生成进行验证" 属性设置为 false，在编译时取消验证。</span><span class="sxs-lookup"><span data-stu-id="cb97a-327">If your model has already been validated, you can suppress validation at compile time by setting the "Validate on Build" property to false in the properties window.</span></span> <span data-ttu-id="cb97a-328">更改映射或模型时，可以暂时重新启用验证以验证更改。</span><span class="sxs-lookup"><span data-stu-id="cb97a-328">When you change your mapping or model, you can temporarily re-enable validation to verify your changes.</span></span>

<span data-ttu-id="cb97a-329">请注意，对实体框架6的 Entity Framework Designer 进行了性能改进，"生成时验证" 的成本要低于设计器的早期版本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-329">Note that performance improvements were made to the Entity Framework Designer for Entity Framework 6, and the cost of the “Validate on Build” is much lower than in previous versions of the designer.</span></span>

## <a name="3-caching-in-the-entity-framework"></a><span data-ttu-id="cb97a-330">实体框架中的3种缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-330">3 Caching in the Entity Framework</span></span>

<span data-ttu-id="cb97a-331">实体框架提供以下形式的缓存内置功能：</span><span class="sxs-lookup"><span data-stu-id="cb97a-331">Entity Framework has the following forms of caching built-in:</span></span>

1.  <span data-ttu-id="cb97a-332">对象缓存-在 ObjectContext 实例中内置的 ObjectStateManager 将跟踪使用该实例检索的对象的内存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-332">Object caching – the ObjectStateManager built into an ObjectContext instance keeps track in memory of the objects that have been retrieved using that instance.</span></span> <span data-ttu-id="cb97a-333">这也称为第一级缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-333">This is also known as first-level cache.</span></span>
2.  <span data-ttu-id="cb97a-334">查询计划缓存-多次执行查询时重复使用生成的存储命令。</span><span class="sxs-lookup"><span data-stu-id="cb97a-334">Query Plan Caching - reusing the generated store command when a query is executed more than once.</span></span>
3.  <span data-ttu-id="cb97a-335">元数据缓存-跨不同连接将模型的元数据共享到同一个模型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-335">Metadata caching - sharing the metadata for a model across different connections to the same model.</span></span>

<span data-ttu-id="cb97a-336">除了 EF 提供的缓存外，还可以使用一种特殊类型的 ADO.NET 数据提供程序（称为包装提供程序），通过缓存为从数据库检索到的结果（也称为二级缓存）来扩展实体框架。</span><span class="sxs-lookup"><span data-stu-id="cb97a-336">Besides the caches that EF provides out of the box, a special kind of ADO.NET data provider known as a wrapping provider can also be used to extend Entity Framework with a cache for the results retrieved from the database, also known as second-level caching.</span></span>

### <a name="31-object-caching"></a><span data-ttu-id="cb97a-337">3.1 对象缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-337">3.1 Object Caching</span></span>

<span data-ttu-id="cb97a-338">默认情况下，在查询结果中返回实体时，在 EF 具体化它之前，ObjectContext 会检查是否已将具有相同键的实体加载到其 ObjectStateManager 中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-338">By default when an entity is returned in the results of a query, just before EF materializes it, the ObjectContext will check if an entity with the same key has already been loaded into its ObjectStateManager.</span></span> <span data-ttu-id="cb97a-339">如果已存在具有相同键的实体，则 EF 会将其包含在查询结果中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-339">If an entity with the same keys is already present EF will include it in the results of the query.</span></span> <span data-ttu-id="cb97a-340">尽管 EF 仍会对数据库发出查询，但这种行为可能会绕过多个时间具体化实体的开销。</span><span class="sxs-lookup"><span data-stu-id="cb97a-340">Although EF will still issue the query against the database, this behavior can bypass much of the cost of materializing the entity multiple times.</span></span>

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a><span data-ttu-id="cb97a-341">使用 DbContext 查找从对象缓存获取实体的3.1。1</span><span class="sxs-lookup"><span data-stu-id="cb97a-341">3.1.1 Getting entities from the object cache using DbContext Find</span></span>

<span data-ttu-id="cb97a-342">与常规查询不同，DbSet 中的 Find 方法（第一次在 EF 4.1 中包含的 Api）将在内存中执行搜索，甚至对数据库发出查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-342">Unlike a regular query, the Find method in DbSet (APIs included for the first time in EF 4.1) will perform a search in memory before even issuing the query against the database.</span></span> <span data-ttu-id="cb97a-343">请注意，两个不同的 ObjectContext 实例将具有两个不同的 ObjectStateManager 实例，这意味着它们具有单独的对象缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-343">It’s important to note that two different ObjectContext instances will have two different ObjectStateManager instances, meaning that they have separate object caches.</span></span>

<span data-ttu-id="cb97a-344">查找使用主键值来尝试查找上下文跟踪的实体。</span><span class="sxs-lookup"><span data-stu-id="cb97a-344">Find uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="cb97a-345">如果实体不在上下文中，则将针对数据库执行并评估查询，如果在上下文中或在数据库中找不到该实体，则返回 null。</span><span class="sxs-lookup"><span data-stu-id="cb97a-345">If the entity is not in the context then a query will be executed and evaluated against the database, and null is returned if the entity is not found in the context or in the database.</span></span> <span data-ttu-id="cb97a-346">请注意，Find 还会返回已添加到上下文中但尚未保存到数据库中的实体。</span><span class="sxs-lookup"><span data-stu-id="cb97a-346">Note that Find also returns entities that have been added to the context but have not yet been saved to the database.</span></span>

<span data-ttu-id="cb97a-347">使用 "查找" 时应考虑性能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-347">There is a performance consideration to be taken when using Find.</span></span> <span data-ttu-id="cb97a-348">默认情况下，对此方法的调用会触发对象缓存的验证，以检测仍处于挂起状态的对数据库的提交的更改。</span><span class="sxs-lookup"><span data-stu-id="cb97a-348">Invocations to this method by default will trigger a validation of the object cache in order to detect changes that are still pending commit to the database.</span></span> <span data-ttu-id="cb97a-349">如果对象缓存或要添加到对象缓存中的大型对象图中有大量对象，则此过程可能会非常昂贵，但也可以禁用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-349">This process can be very expensive if there are a very large number of objects in the object cache or in a large object graph being added to the object cache, but it can also be disabled.</span></span> <span data-ttu-id="cb97a-350">在某些情况下，你可能会发现在禁用自动检测更改时调用 Find 方法时的差异数量级。</span><span class="sxs-lookup"><span data-stu-id="cb97a-350">In certain cases, you may perceive over an order of magnitude of difference in calling the Find method when you disable auto detect changes.</span></span> <span data-ttu-id="cb97a-351">但当对象实际在缓存中时，以及当必须从数据库中检索对象时，还可以看到另一种数量级。</span><span class="sxs-lookup"><span data-stu-id="cb97a-351">Yet a second order of magnitude is perceived when the object actually is in the cache versus when the object has to be retrieved from the database.</span></span> <span data-ttu-id="cb97a-352">下面是一个示例图，其中使用了我们的一些 microbenchmarks （以毫秒为单位，以毫秒为单位）进行测量，负载为5000个实体：</span><span class="sxs-lookup"><span data-stu-id="cb97a-352">Here is an example graph with measurements taken using some of our microbenchmarks, expressed in milliseconds, with a load of 5000 entities:</span></span>

<span data-ttu-id="cb97a-353">![.NET 4.5 对数刻度](~/ef6/media/net45logscale.png ".NET 4.5-对数刻度")</span><span class="sxs-lookup"><span data-stu-id="cb97a-353">![.NET 4.5 logarithmic scale](~/ef6/media/net45logscale.png ".NET 4.5 - logarithmic scale")</span></span>

<span data-ttu-id="cb97a-354">禁用自动检测更改的查找示例：</span><span class="sxs-lookup"><span data-stu-id="cb97a-354">Example of Find with auto-detect changes disabled:</span></span>

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

<span data-ttu-id="cb97a-355">使用 Find 方法时必须考虑的事项如下：</span><span class="sxs-lookup"><span data-stu-id="cb97a-355">What you have to consider when using the Find method is:</span></span>

1.  <span data-ttu-id="cb97a-356">如果对象不在缓存中，则会对 Find 的优点求反，但语法仍比按键查询更简单。</span><span class="sxs-lookup"><span data-stu-id="cb97a-356">If the object is not in the cache the benefits of Find are negated, but the syntax is still simpler than a query by key.</span></span>
2.  <span data-ttu-id="cb97a-357">如果启用了自动检测更改，Find 方法的成本可能会增加一个数量级，甚至更多，具体取决于模型的复杂程度和对象缓存中的实体数量。</span><span class="sxs-lookup"><span data-stu-id="cb97a-357">If auto detect changes is enabled the cost of the Find method may increase by one order of magnitude, or even more depending on the complexity of your model and the amount of entities in your object cache.</span></span>

<span data-ttu-id="cb97a-358">另外，请记住，Find 仅返回你要查找的实体，并且它不会自动加载其关联的实体（如果它们尚未在对象缓存中）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-358">Also, keep in mind that Find only returns the entity you are looking for and it does not automatically loads its associated entities if they are not already in the object cache.</span></span> <span data-ttu-id="cb97a-359">如果需要检索关联的实体，则可以通过键进行预先加载来使用查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-359">If you need to retrieve associated entities, you can use a query by key with eager loading.</span></span> <span data-ttu-id="cb97a-360">有关详细信息，请参阅**8.1 延迟加载与预先加载**。</span><span class="sxs-lookup"><span data-stu-id="cb97a-360">For more information see **8.1 Lazy Loading vs. Eager Loading**.</span></span>

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a><span data-ttu-id="cb97a-361">当对象缓存具有多个实体时，3.1.2 性能问题</span><span class="sxs-lookup"><span data-stu-id="cb97a-361">3.1.2 Performance issues when the object cache has many entities</span></span>

<span data-ttu-id="cb97a-362">对象缓存有助于增加实体框架的总体响应能力。</span><span class="sxs-lookup"><span data-stu-id="cb97a-362">The object cache helps to increase the overall responsiveness of Entity Framework.</span></span> <span data-ttu-id="cb97a-363">但是，当对象缓存加载的实体量非常大时，可能会影响某些操作，例如添加、删除、查找、条目、SaveChanges 等。</span><span class="sxs-lookup"><span data-stu-id="cb97a-363">However, when the object cache has a very large amount of entities loaded it may affect certain operations such as Add, Remove, Find, Entry, SaveChanges and more.</span></span> <span data-ttu-id="cb97a-364">特别是，触发对 DetectChanges 的调用的操作将受到超大型对象缓存的负面影响。</span><span class="sxs-lookup"><span data-stu-id="cb97a-364">In particular, operations that trigger a call to DetectChanges will be negatively affected by very large object caches.</span></span> <span data-ttu-id="cb97a-365">DetectChanges 将对象关系图与对象状态管理器同步，其性能将由对象图的大小直接确定。</span><span class="sxs-lookup"><span data-stu-id="cb97a-365">DetectChanges synchronizes the object graph with the object state manager and its performance will determined directly by the size of the object graph.</span></span> <span data-ttu-id="cb97a-366">有关 DetectChanges 的详细信息，请参阅[跟踪 POCO 实体中的更改](https://msdn.microsoft.com/library/dd456848.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-366">For more information about DetectChanges, see [Tracking Changes in POCO Entities](https://msdn.microsoft.com/library/dd456848.aspx).</span></span>

<span data-ttu-id="cb97a-367">当使用实体框架6时，开发人员可以直接在 DbSet 上调用 AddRange 和 RemoveRange，而不是循环访问集合并为每个实例调用一次。</span><span class="sxs-lookup"><span data-stu-id="cb97a-367">When using Entity Framework 6, developers are able to call AddRange and RemoveRange directly on a DbSet, instead of iterating on a collection and calling Add once per instance.</span></span> <span data-ttu-id="cb97a-368">使用范围方法的优点是，只为整个实体集支付一次 DetectChanges 成本，而不是每个添加的实体的费用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-368">The advantage of using the range methods is that the cost of DetectChanges is only paid once for the entire set of entities as opposed to once per each added entity.</span></span>

### <a name="32-query-plan-caching"></a><span data-ttu-id="cb97a-369">3.2 查询计划缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-369">3.2 Query Plan Caching</span></span>

<span data-ttu-id="cb97a-370">第一次执行查询时，它会经历内部计划编译器，将概念性查询转换为存储命令（例如，在针对 SQL Server 运行时执行的 T-sql）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-370">The first time a query is executed, it goes through the internal plan compiler to translate the conceptual query into the store command (for example, the T-SQL which is executed when run against SQL Server).</span></span><span data-ttu-id="cb97a-371">  如果启用了查询计划缓存，则下一次执行查询时，将直接从查询计划缓存中检索 store 命令以便执行，绕过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-371">  If query plan caching is enabled, the next time the query is executed the store command is retrieved directly from the query plan cache for execution, bypassing the plan compiler.</span></span>

<span data-ttu-id="cb97a-372">查询计划缓存在同一 AppDomain 中跨 ObjectContext 实例共享。</span><span class="sxs-lookup"><span data-stu-id="cb97a-372">The query plan cache is shared across ObjectContext instances within the same AppDomain.</span></span> <span data-ttu-id="cb97a-373">您无需保存到 ObjectContext 实例即可受益于查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-373">You don't need to hold onto an ObjectContext instance to benefit from query plan caching.</span></span>

#### <a name="321-some-notes-about-query-plan-caching"></a><span data-ttu-id="cb97a-374">3.2.1 一些有关查询计划缓存的说明</span><span class="sxs-lookup"><span data-stu-id="cb97a-374">3.2.1 Some notes about Query Plan Caching</span></span>

-   <span data-ttu-id="cb97a-375">查询计划缓存对于所有查询类型都是共享的：实体 SQL、LINQ to Entities 和 CompiledQuery 对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-375">The query plan cache is shared for all query types: Entity SQL, LINQ to Entities, and CompiledQuery objects.</span></span>
-   <span data-ttu-id="cb97a-376">默认情况下，查询计划缓存针对实体 SQL 查询启用，无论是通过 EntityCommand 还是通过 ObjectQuery 执行。</span><span class="sxs-lookup"><span data-stu-id="cb97a-376">By default, query plan caching is enabled for Entity SQL queries, whether executed through an EntityCommand or through an ObjectQuery.</span></span> <span data-ttu-id="cb97a-377">默认情况下，对于 .NET 4.5 实体框架上的 LINQ to Entities 查询，以及在实体框架6中，它也处于启用状态。</span><span class="sxs-lookup"><span data-stu-id="cb97a-377">It is also enabled by default for LINQ to Entities queries in Entity Framework on .NET 4.5, and in Entity Framework 6</span></span>
    -   <span data-ttu-id="cb97a-378">可以通过将 EnablePlanCaching 属性（在 EntityCommand 或 ObjectQuery 上）设置为 false 来禁用查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-378">Query plan caching can be disabled by setting the EnablePlanCaching property (on EntityCommand or ObjectQuery) to false.</span></span> <span data-ttu-id="cb97a-379">例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-379">For example:</span></span>
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
-   <span data-ttu-id="cb97a-380">对于参数化查询，更改参数的值仍会命中缓存查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-380">For parameterized queries, changing the parameter's value will still hit the cached query.</span></span> <span data-ttu-id="cb97a-381">但更改参数的方面（例如，大小、精度或小数位数）会命中缓存中的其他条目。</span><span class="sxs-lookup"><span data-stu-id="cb97a-381">But changing a parameter's facets (for example, size, precision, or scale) will hit a different entry in the cache.</span></span>
-   <span data-ttu-id="cb97a-382">使用实体 SQL 时，查询字符串是密钥的一部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-382">When using Entity SQL, the query string is part of the key.</span></span> <span data-ttu-id="cb97a-383">更改查询会产生不同的缓存条目，即使查询在功能上是等效的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-383">Changing the query at all will result in different cache entries, even if the queries are functionally equivalent.</span></span> <span data-ttu-id="cb97a-384">这包括更改大小写或空白。</span><span class="sxs-lookup"><span data-stu-id="cb97a-384">This includes changes to casing or whitespace.</span></span>
-   <span data-ttu-id="cb97a-385">使用 LINQ 时，将对查询进行处理以生成密钥的一部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-385">When using LINQ, the query is processed to generate a part of the key.</span></span> <span data-ttu-id="cb97a-386">因此，更改 LINQ 表达式将生成不同的键。</span><span class="sxs-lookup"><span data-stu-id="cb97a-386">Changing the LINQ expression will therefore generate a different key.</span></span>
-   <span data-ttu-id="cb97a-387">其他技术限制可能适用;有关更多详细信息，请参阅 Autocompiled 查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-387">Other technical limitations may apply; see Autocompiled Queries for more details.</span></span>

#### <a name="322-cache-eviction-algorithm"></a><span data-ttu-id="cb97a-388">3.2.2 缓存逐出算法</span><span class="sxs-lookup"><span data-stu-id="cb97a-388">3.2.2      Cache eviction algorithm</span></span>

<span data-ttu-id="cb97a-389">了解内部算法的工作方式将有助于您确定何时启用或禁用查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-389">Understanding how the internal algorithm works will help you figure out when to enable or disable query plan caching.</span></span> <span data-ttu-id="cb97a-390">清理算法如下所示：</span><span class="sxs-lookup"><span data-stu-id="cb97a-390">The cleanup algorithm is as follows:</span></span>

1.  <span data-ttu-id="cb97a-391">缓存包含设置的条目数（800）后，我们会定期（每分钟一次）扫描缓存来启动一个计时器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-391">Once the cache contains a set number of entries (800), we start a timer that periodically (once-per-minute) sweeps the cache.</span></span>
2.  <span data-ttu-id="cb97a-392">在缓存扫描过程中，将从缓存中删除 LFRU （最不常使用的）条目。</span><span class="sxs-lookup"><span data-stu-id="cb97a-392">During cache sweeps, entries are removed from the cache on a LFRU (Least frequently – recently used) basis.</span></span> <span data-ttu-id="cb97a-393">在确定要弹出的项时，此算法会考虑命中次数和使用期限。</span><span class="sxs-lookup"><span data-stu-id="cb97a-393">This algorithm takes both hit count and age into account when deciding which entries are ejected.</span></span>
3.  <span data-ttu-id="cb97a-394">每次缓存扫描结束时，缓存将再次包含800个条目。</span><span class="sxs-lookup"><span data-stu-id="cb97a-394">At the end of each cache sweep, the cache again contains 800 entries.</span></span>

<span data-ttu-id="cb97a-395">确定要逐出哪些项时，将同等对待所有缓存项。</span><span class="sxs-lookup"><span data-stu-id="cb97a-395">All cache entries are treated equally when determining which entries to evict.</span></span> <span data-ttu-id="cb97a-396">这意味着 CompiledQuery 的 store 命令与实体 SQL 查询的存储命令的逐出方式相同。</span><span class="sxs-lookup"><span data-stu-id="cb97a-396">This means the store command for a CompiledQuery has the same chance of eviction as the store command for an Entity SQL query.</span></span>

<span data-ttu-id="cb97a-397">请注意，当缓存中存在800个实体时，将在中启动缓存逐出计时器，但缓存仅在此计时器启动后的60秒内进行扫描。</span><span class="sxs-lookup"><span data-stu-id="cb97a-397">Note that the cache eviction timer is kicked in when there are 800 entities in the cache, but the cache is only swept 60 seconds after this timer is started.</span></span> <span data-ttu-id="cb97a-398">这意味着缓存可能会增长到相当大的60秒。</span><span class="sxs-lookup"><span data-stu-id="cb97a-398">That means that for up to 60 seconds your cache may grow to be quite large.</span></span>

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a><span data-ttu-id="cb97a-399">3.2.3 测试指标演示查询计划缓存性能</span><span class="sxs-lookup"><span data-stu-id="cb97a-399">3.2.3       Test Metrics demonstrating query plan caching performance</span></span>

<span data-ttu-id="cb97a-400">为了说明查询计划缓存对应用程序性能的影响，我们执行了测试，在此测试中，我们对 Navision 模型执行了许多实体 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-400">To demonstrate the effect of query plan caching on your application's performance, we performed a test where we executed a number of Entity SQL queries against the Navision model.</span></span> <span data-ttu-id="cb97a-401">有关 Navision 模型的说明以及所执行的查询类型的说明，请参阅附录。</span><span class="sxs-lookup"><span data-stu-id="cb97a-401">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="cb97a-402">在此测试中，我们首先遍历查询列表并执行每个查询，将它们添加到缓存（如果启用了缓存）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-402">In this test, we first iterate through the list of queries and execute each one once to add them to the cache (if caching is enabled).</span></span> <span data-ttu-id="cb97a-403">此步骤为 untimed。</span><span class="sxs-lookup"><span data-stu-id="cb97a-403">This step is untimed.</span></span> <span data-ttu-id="cb97a-404">接下来，我们使主线程睡眠超过60秒，以允许进行缓存扫描;最后，我们会循环访问该列表，第二次执行缓存的查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-404">Next, we sleep the main thread for over 60 seconds to allow cache sweeping to take place; finally, we iterate through the list a 2nd time to execute the cached queries.</span></span> <span data-ttu-id="cb97a-405">此外，在执行每组查询之前会刷新 SQL Server 计划缓存，以便我们准确地反映查询计划缓存提供的好处。</span><span class="sxs-lookup"><span data-stu-id="cb97a-405">Additionally, the SQL Server plan cache is flushed before each set of queries is executed so that the times we obtain accurately reflect the benefit given by the query plan cache.</span></span>

##### <a name="3231-test-results"></a><span data-ttu-id="cb97a-406">3.2.3.1 测试结果</span><span class="sxs-lookup"><span data-stu-id="cb97a-406">3.2.3.1       Test Results</span></span>

| <span data-ttu-id="cb97a-407">测试</span><span class="sxs-lookup"><span data-stu-id="cb97a-407">Test</span></span>                                                                   | <span data-ttu-id="cb97a-408">EF5 无缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-408">EF5 no cache</span></span> | <span data-ttu-id="cb97a-409">EF5 缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-409">EF5 cached</span></span> | <span data-ttu-id="cb97a-410">EF6 无缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-410">EF6 no cache</span></span> | <span data-ttu-id="cb97a-411">EF6 缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-411">EF6 cached</span></span> |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| <span data-ttu-id="cb97a-412">枚举所有18723查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-412">Enumerating all 18723 queries</span></span>                                          | <span data-ttu-id="cb97a-413">124</span><span class="sxs-lookup"><span data-stu-id="cb97a-413">124</span></span>          | <span data-ttu-id="cb97a-414">125.4</span><span class="sxs-lookup"><span data-stu-id="cb97a-414">125.4</span></span>      | <span data-ttu-id="cb97a-415">124.3</span><span class="sxs-lookup"><span data-stu-id="cb97a-415">124.3</span></span>        | <span data-ttu-id="cb97a-416">125.3</span><span class="sxs-lookup"><span data-stu-id="cb97a-416">125.3</span></span>      |
| <span data-ttu-id="cb97a-417">避免扫描（仅限前800个查询，而不考虑复杂性）</span><span class="sxs-lookup"><span data-stu-id="cb97a-417">Avoiding sweep (just the first 800 queries, regardless of complexity)</span></span>  | <span data-ttu-id="cb97a-418">41.7</span><span class="sxs-lookup"><span data-stu-id="cb97a-418">41.7</span></span>         | <span data-ttu-id="cb97a-419">5.5</span><span class="sxs-lookup"><span data-stu-id="cb97a-419">5.5</span></span>        | <span data-ttu-id="cb97a-420">40.5</span><span class="sxs-lookup"><span data-stu-id="cb97a-420">40.5</span></span>         | <span data-ttu-id="cb97a-421">5.4</span><span class="sxs-lookup"><span data-stu-id="cb97a-421">5.4</span></span>        |
| <span data-ttu-id="cb97a-422">仅限 AggregatingSubtotals 查询（total 178）</span><span class="sxs-lookup"><span data-stu-id="cb97a-422">Just the AggregatingSubtotals queries (178 total - which avoids sweep)</span></span> | <span data-ttu-id="cb97a-423">39.5</span><span class="sxs-lookup"><span data-stu-id="cb97a-423">39.5</span></span>         | <span data-ttu-id="cb97a-424">4.5</span><span class="sxs-lookup"><span data-stu-id="cb97a-424">4.5</span></span>        | <span data-ttu-id="cb97a-425">38.1</span><span class="sxs-lookup"><span data-stu-id="cb97a-425">38.1</span></span>         | <span data-ttu-id="cb97a-426">4.6</span><span class="sxs-lookup"><span data-stu-id="cb97a-426">4.6</span></span>        |

<span data-ttu-id="cb97a-427">*所有时间（以秒为单位）。*</span><span class="sxs-lookup"><span data-stu-id="cb97a-427">*All times in seconds.*</span></span>

<span data-ttu-id="cb97a-428">道德-执行大量不同的查询（例如动态创建的查询）时，缓存不起作用，并且生成的缓存刷新可使从计划缓存中获益最大的查询从实际使用到它。</span><span class="sxs-lookup"><span data-stu-id="cb97a-428">Moral - when executing lots of distinct queries (for example,  dynamically created queries), caching doesn't help and the resulting flushing of the cache can keep the queries that would benefit the most from plan caching from actually using it.</span></span>

<span data-ttu-id="cb97a-429">AggregatingSubtotals 查询是我们测试过的查询中最复杂的查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-429">The AggregatingSubtotals queries are the most complex of the queries we tested with.</span></span> <span data-ttu-id="cb97a-430">查询越复杂，查询计划缓存中的优势就越多。</span><span class="sxs-lookup"><span data-stu-id="cb97a-430">As expected, the more complex the query is, the more benefit you will see from query plan caching.</span></span>

<span data-ttu-id="cb97a-431">由于 CompiledQuery 确实是缓存了其计划的 LINQ 查询，因此 CompiledQuery 与等效实体 SQL 查询的比较应具有类似的结果。</span><span class="sxs-lookup"><span data-stu-id="cb97a-431">Because a CompiledQuery is really a LINQ query with its plan cached, the comparison of a CompiledQuery versus the equivalent Entity SQL query should have similar results.</span></span> <span data-ttu-id="cb97a-432">事实上，如果应用具有大量的动态实体 SQL 查询，则使用查询填充缓存也会导致 CompiledQueries 从缓存中刷新时进行 "反编译"。</span><span class="sxs-lookup"><span data-stu-id="cb97a-432">In fact, if an app has lots of dynamic Entity SQL queries, filling the cache with queries will also effectively cause CompiledQueries to “decompile” when they are flushed from the cache.</span></span> <span data-ttu-id="cb97a-433">在这种情况下，可以通过在动态查询上禁用缓存来设置 CompiledQueries 的优先级，从而提高性能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-433">In this scenario, performance may be improved by disabling caching on the dynamic queries to prioritize the CompiledQueries.</span></span> <span data-ttu-id="cb97a-434">当然，更好的做法是重写应用程序以使用参数化查询，而不是动态查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-434">Better yet, of course, would be to rewrite the app to use parameterized queries instead of dynamic queries.</span></span>

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a><span data-ttu-id="cb97a-435">3.3 使用 CompiledQuery 通过 LINQ 查询提高性能</span><span class="sxs-lookup"><span data-stu-id="cb97a-435">3.3 Using CompiledQuery to improve performance with LINQ queries</span></span>

<span data-ttu-id="cb97a-436">我们的测试表明，使用 CompiledQuery 可以带来 7% over autocompiled LINQ 查询的好处;这意味着，从实体框架堆栈中执行代码的时间减少了 7%;这并不意味着应用程序的速度将更快7%。</span><span class="sxs-lookup"><span data-stu-id="cb97a-436">Our tests indicate that using CompiledQuery can bring a benefit of 7% over autocompiled LINQ queries; this means that you’ll spend 7% less time executing code from the Entity Framework stack; it does not mean your application will be 7% faster.</span></span> <span data-ttu-id="cb97a-437">一般而言，与好处相比，在 EF 5.0 中编写和维护 CompiledQuery 对象的成本可能不值得。</span><span class="sxs-lookup"><span data-stu-id="cb97a-437">Generally speaking, the cost of writing and maintaining CompiledQuery objects in EF 5.0 may not be worth the trouble when compared to the benefits.</span></span> <span data-ttu-id="cb97a-438">你的里程可能会有所不同，因此，如果你的项目需要额外的推送，则请执行此选项。</span><span class="sxs-lookup"><span data-stu-id="cb97a-438">Your mileage may vary, so exercise this option if your project requires the extra push.</span></span> <span data-ttu-id="cb97a-439">请注意，CompiledQueries 仅与 ObjectContext 派生模型兼容，不与 DbContext 派生模型兼容。</span><span class="sxs-lookup"><span data-stu-id="cb97a-439">Note that CompiledQueries are only compatible with ObjectContext-derived models, and not compatible with DbContext-derived models.</span></span>

<span data-ttu-id="cb97a-440">有关创建和调用 CompiledQuery 的详细信息，请参阅[已编译的查询（LINQ to Entities）](https://msdn.microsoft.com/library/bb896297.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-440">For more information on creating and invoking a CompiledQuery, see [Compiled Queries (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).</span></span>

<span data-ttu-id="cb97a-441">在使用 CompiledQuery 时，需要考虑两个注意事项，即，要求使用静态实例和它们与可组合性相关的问题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-441">There are two considerations you have to take when using a CompiledQuery, namely the requirement to use static instances and the problems they have with composability.</span></span> <span data-ttu-id="cb97a-442">下面对这两个注意事项进行了深入说明。</span><span class="sxs-lookup"><span data-stu-id="cb97a-442">Here follows an in-depth explanation of these two considerations.</span></span>

#### <a name="331-use-static-compiledquery-instances"></a><span data-ttu-id="cb97a-443">3.3.1 使用静态 CompiledQuery 实例</span><span class="sxs-lookup"><span data-stu-id="cb97a-443">3.3.1       Use static CompiledQuery instances</span></span>

<span data-ttu-id="cb97a-444">因为编译 LINQ 查询是一个耗时的过程，所以我们不希望每次需要从数据库中提取数据时都要这样做。</span><span class="sxs-lookup"><span data-stu-id="cb97a-444">Since compiling a LINQ query is a time-consuming process, we don’t want to do it every time we need to fetch data from the database.</span></span> <span data-ttu-id="cb97a-445">使用 CompiledQuery 实例可以编译一次，并多次运行，但必须小心，并获得每次都重新使用同一个 CompiledQuery 实例，而不是反复编译它。</span><span class="sxs-lookup"><span data-stu-id="cb97a-445">CompiledQuery instances allow you to compile once and run multiple times, but you have to be careful and procure to re-use the same CompiledQuery instance every time instead of compiling it over and over again.</span></span> <span data-ttu-id="cb97a-446">需要使用静态成员存储 CompiledQuery 实例。否则，你将看不到任何权益。</span><span class="sxs-lookup"><span data-stu-id="cb97a-446">The use of static members to store the CompiledQuery instances becomes necessary; otherwise you won’t see any benefit.</span></span>

<span data-ttu-id="cb97a-447">例如，假设您的页面具有以下方法主体来处理显示所选类别的产品：</span><span class="sxs-lookup"><span data-stu-id="cb97a-447">For example, suppose your page has the following method body to handle displaying the products for the selected category:</span></span>

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

<span data-ttu-id="cb97a-448">在这种情况下，每次调用该方法时，都将动态创建一个新的 CompiledQuery 实例。</span><span class="sxs-lookup"><span data-stu-id="cb97a-448">In this case, you will create a new CompiledQuery instance on the fly every time the method is called.</span></span> <span data-ttu-id="cb97a-449">每次创建新的实例时，CompiledQuery 都将经历计划编译器，而不是通过从查询计划缓存中检索存储命令来查看性能优势。</span><span class="sxs-lookup"><span data-stu-id="cb97a-449">Instead of seeing performance benefits by retrieving the store command from the query plan cache, the CompiledQuery will go through the plan compiler every time a new instance is created.</span></span> <span data-ttu-id="cb97a-450">事实上，每次调用该方法时，都将使用新的 CompiledQuery 条目来污染查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-450">In fact, you will be polluting your query plan cache with a new CompiledQuery entry every time the method is called.</span></span>

<span data-ttu-id="cb97a-451">相反，您需要创建已编译查询的静态实例，以便在每次调用该方法时都要调用相同的编译查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-451">Instead, you want to create a static instance of the compiled query, so you are invoking the same compiled query every time the method is called.</span></span> <span data-ttu-id="cb97a-452">这样做的一种方法是将 CompiledQuery 实例添加为对象上下文的成员。</span><span class="sxs-lookup"><span data-stu-id="cb97a-452">One way to so this is by adding the CompiledQuery instance as a member of your object context.</span></span><span data-ttu-id="cb97a-453">  然后，你可以通过 helper 方法访问 CompiledQuery 来使内容变得更干净：</span><span class="sxs-lookup"><span data-stu-id="cb97a-453">  You can then make things a little cleaner by accessing the CompiledQuery through a helper method:</span></span>

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

<span data-ttu-id="cb97a-454">此帮助器方法将按如下方式调用：</span><span class="sxs-lookup"><span data-stu-id="cb97a-454">This helper method would be invoked as follows:</span></span>

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a><span data-ttu-id="cb97a-455">3.3.2 在 CompiledQuery 上撰写</span><span class="sxs-lookup"><span data-stu-id="cb97a-455">3.3.2       Composing over a CompiledQuery</span></span>

<span data-ttu-id="cb97a-456">对任何 LINQ 查询进行组合的功能非常有用;为此，只需调用 IQueryable 后的方法，例如*Skip （）* 或*Count （）* 。</span><span class="sxs-lookup"><span data-stu-id="cb97a-456">The ability to compose over any LINQ query is extremely useful; to do this, you simply invoke a method after the IQueryable such as *Skip()* or *Count()*.</span></span> <span data-ttu-id="cb97a-457">但这样做实际上会返回一个新的 IQueryable 对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-457">However, doing so essentially returns a new IQueryable object.</span></span> <span data-ttu-id="cb97a-458">尽管从技术上讲，无需对 CompiledQuery 进行撰写，但这样做会导致生成新的 IQueryable 对象，该对象要求再次通过计划编译器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-458">While there’s nothing to stop you technically from composing over a CompiledQuery, doing so will cause the generation of a new IQueryable object that requires passing through the plan compiler again.</span></span>

<span data-ttu-id="cb97a-459">某些组件将使用组合的 IQueryable 对象来启用高级功能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-459">Some components will make use of composed IQueryable objects to enable advanced functionality.</span></span> <span data-ttu-id="cb97a-460">例如，ASP。NET 的 GridView 可以通过 SelectMethod 属性数据绑定到 IQueryable 对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-460">For example, ASP.NET’s GridView can be data-bound to an IQueryable object via the SelectMethod property.</span></span> <span data-ttu-id="cb97a-461">然后，GridView 将通过此 IQueryable 对象组合在一起，以允许对数据模型进行排序和分页。</span><span class="sxs-lookup"><span data-stu-id="cb97a-461">The GridView will then compose over this IQueryable object to allow sorting and paging over the data model.</span></span> <span data-ttu-id="cb97a-462">正如您所看到的，使用 GridView 的 CompiledQuery 不会命中已编译的查询，而是生成新的 autocompiled 查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-462">As you can see, using a CompiledQuery for the GridView would not hit the compiled query but would generate a new autocompiled query.</span></span>

<span data-ttu-id="cb97a-463">在将渐进式筛选器添加到查询中时，您可能会遇到这种情况。</span><span class="sxs-lookup"><span data-stu-id="cb97a-463">One place where you may run into this is when adding progressive filters to a query.</span></span> <span data-ttu-id="cb97a-464">例如，假设有一个 "客户" 页，其中包含用于可选筛选器（例如，Country 和 OrdersCount）的多个下拉列表。</span><span class="sxs-lookup"><span data-stu-id="cb97a-464">For example, suppose you had a Customers page with several drop-down lists for optional filters (for example, Country and OrdersCount).</span></span> <span data-ttu-id="cb97a-465">您可以在 CompiledQuery 的 IQueryable 结果中撰写这些筛选器，但这样做会导致新的查询每次执行时都会经历计划编译器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-465">You can compose these filters over the IQueryable results of a CompiledQuery, but doing so will result in the new query going through the plan compiler every time you execute it.</span></span>

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

 <span data-ttu-id="cb97a-466">若要避免这种重新编译，可以重写 CompiledQuery 以考虑可能的筛选器：</span><span class="sxs-lookup"><span data-stu-id="cb97a-466">To avoid this re-compilation, you can rewrite the CompiledQuery to take the possible filters into account:</span></span>

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

<span data-ttu-id="cb97a-467">这会在 UI 中调用，如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-467">Which would be invoked in the UI like:</span></span>

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

 <span data-ttu-id="cb97a-468">这里的一个折衷是，生成的存储命令将始终包含带有 null 检查的筛选器，但对于数据库服务器而言，这些筛选器应该非常简单：</span><span class="sxs-lookup"><span data-stu-id="cb97a-468">A tradeoff here is the generated store command will always have the filters with the null checks, but these should be fairly simple for the database server to optimize:</span></span>

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a><span data-ttu-id="cb97a-469">3.4 元数据缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-469">3.4 Metadata caching</span></span>

<span data-ttu-id="cb97a-470">实体框架还支持元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-470">The Entity Framework also supports Metadata caching.</span></span> <span data-ttu-id="cb97a-471">这实质上是在不同于同一模型的连接之间缓存类型信息和类型到数据库的映射信息。</span><span class="sxs-lookup"><span data-stu-id="cb97a-471">This is essentially caching of type information and type-to-database mapping information across different connections to the same model.</span></span> <span data-ttu-id="cb97a-472">元数据缓存对于每个 AppDomain 都是唯一的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-472">The Metadata cache is unique per AppDomain.</span></span>

#### <a name="341-metadata-caching-algorithm"></a><span data-ttu-id="cb97a-473">3.4.1 元数据缓存算法</span><span class="sxs-lookup"><span data-stu-id="cb97a-473">3.4.1 Metadata Caching algorithm</span></span>

1.  <span data-ttu-id="cb97a-474">模型的元数据信息存储在每个 EntityConnection 的 ItemCollection 中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-474">Metadata information for a model is stored in an ItemCollection for each EntityConnection.</span></span>
    -   <span data-ttu-id="cb97a-475">为此，模型的不同部分都有不同的 ItemCollection 对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-475">As a side note, there are different ItemCollection objects for different parts of the model.</span></span> <span data-ttu-id="cb97a-476">例如，StoreItemCollections 包含有关数据库模型的信息;ObjectItemCollection 包含有关数据模型的信息;EdmItemCollection 包含有关概念模型的信息。</span><span class="sxs-lookup"><span data-stu-id="cb97a-476">For example, StoreItemCollections contains the information about the database model; ObjectItemCollection contains information about the data model; EdmItemCollection contains information about the conceptual model.</span></span>

2.  <span data-ttu-id="cb97a-477">如果两个连接使用相同的连接字符串，则它们将共享同一 ItemCollection 实例。</span><span class="sxs-lookup"><span data-stu-id="cb97a-477">If two connections use the same connection string, they will share the same ItemCollection instance.</span></span>
3.  <span data-ttu-id="cb97a-478">在功能上等效但以字符为不同的连接字符串可能会导致不同的元数据缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-478">Functionally equivalent but textually different connection strings may result in different metadata caches.</span></span> <span data-ttu-id="cb97a-479">我们确实标记了连接字符串，因此只需更改令牌的顺序就会产生共享元数据。</span><span class="sxs-lookup"><span data-stu-id="cb97a-479">We do tokenize connection strings, so simply changing the order of the tokens should result in shared metadata.</span></span> <span data-ttu-id="cb97a-480">但在标记化后，两个看起来相同的连接字符串可能不会计算为相同。</span><span class="sxs-lookup"><span data-stu-id="cb97a-480">But two connection strings that seem functionally the same may not be evaluated as identical after tokenization.</span></span>
4.  <span data-ttu-id="cb97a-481">定期检查 ItemCollection 的使用情况。</span><span class="sxs-lookup"><span data-stu-id="cb97a-481">The ItemCollection is periodically checked for use.</span></span> <span data-ttu-id="cb97a-482">如果确定工作区最近未访问过，则会将其标记为在下次缓存扫描时清除。</span><span class="sxs-lookup"><span data-stu-id="cb97a-482">If it is determined that a workspace has not been accessed recently, it will be marked for cleanup on the next cache sweep.</span></span>
5.  <span data-ttu-id="cb97a-483">仅创建 EntityConnection 将导致创建元数据缓存（尽管在打开连接之前不会对其中的项集合进行初始化）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-483">Merely creating an EntityConnection will cause a metadata cache to be created (though the item collections in it will not be initialized until the connection is opened).</span></span> <span data-ttu-id="cb97a-484">此工作区将保留在内存中，直到缓存算法将其确定为 "正在使用"。</span><span class="sxs-lookup"><span data-stu-id="cb97a-484">This workspace will remain in-memory until the caching algorithm determines it is not “in use”.</span></span>

<span data-ttu-id="cb97a-485">客户咨询团队已经编写了一篇博客文章，其中介绍了如何保存对 ItemCollection 的引用，以便在使用大型模型时避免 "弃用"： \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-485">The Customer Advisory Team has written a blog post that describes holding a reference to an ItemCollection in order to avoid "deprecation" when using large models: \<http://blogs.msdn.com/b/appfabriccat/archive/2010/10/22/metadataworkspace-reference-in-wcf-services.aspx>.</span></span>

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a><span data-ttu-id="cb97a-486">3.4.2 元数据缓存和查询计划缓存之间的关系</span><span class="sxs-lookup"><span data-stu-id="cb97a-486">3.4.2 The relationship between Metadata Caching and Query Plan Caching</span></span>

<span data-ttu-id="cb97a-487">查询计划缓存实例驻留在 MetadataWorkspace 的存储区类型的 ItemCollection 中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-487">The query plan cache instance lives in the MetadataWorkspace's ItemCollection of store types.</span></span> <span data-ttu-id="cb97a-488">这意味着缓存的存储命令将用于针对使用给定 MetadataWorkspace 实例化的任何上下文的查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-488">This means that cached store commands will be used for queries against any context instantiated using a given MetadataWorkspace.</span></span> <span data-ttu-id="cb97a-489">这也意味着，如果有两个连接字符串略有不同，并且在词汇切分后不匹配，则会有不同的查询计划缓存实例。</span><span class="sxs-lookup"><span data-stu-id="cb97a-489">It also means that if you have two connections strings that are slightly different and don't match after tokenizing, you will have different query plan cache instances.</span></span>

### <a name="35-results-caching"></a><span data-ttu-id="cb97a-490">3.5 结果缓存</span><span class="sxs-lookup"><span data-stu-id="cb97a-490">3.5 Results caching</span></span>

<span data-ttu-id="cb97a-491">使用结果缓存（也称为 "二级缓存"），可以将查询结果保存在本地缓存中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-491">With results caching (also known as "second-level caching"), you keep the results of queries in a local cache.</span></span> <span data-ttu-id="cb97a-492">发出查询时，先查看在对应用商店进行查询之前，结果是否可在本地使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-492">When issuing a query, you first see if the results are available locally before you query against the store.</span></span> <span data-ttu-id="cb97a-493">尽管实体框架不直接支持结果缓存，但使用包装提供程序可以添加二级缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-493">While results caching isn't directly supported by Entity Framework, it's possible to add a second level cache by using a wrapping provider.</span></span> <span data-ttu-id="cb97a-494">使用二级缓存的示例包装提供程序是 Alachisoft 的[基于 NCache 实体框架二级缓存](https://www.alachisoft.com/ncache/entity-framework.html)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-494">An example wrapping provider with a second-level cache is Alachisoft's [Entity Framework Second Level Cache based on NCache](https://www.alachisoft.com/ncache/entity-framework.html).</span></span>

<span data-ttu-id="cb97a-495">此第二级缓存的实现是在计算 LINQ 表达式后发生的插入功能（和 funcletized），并且从第一级缓存计算或检索查询执行计划。</span><span class="sxs-lookup"><span data-stu-id="cb97a-495">This implementation of second-level caching is an injected functionality that takes place after the LINQ expression has been evaluated (and funcletized) and the query execution plan is computed or retrieved from the first-level cache.</span></span> <span data-ttu-id="cb97a-496">然后，第二级缓存将仅存储原始数据库结果，因此具体化管道以后仍会执行。</span><span class="sxs-lookup"><span data-stu-id="cb97a-496">The second-level cache will then store only the raw database results, so the materialization pipeline still executes afterwards.</span></span>

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a><span data-ttu-id="cb97a-497">3.5.1 用于包装提供程序的结果缓存的其他引用</span><span class="sxs-lookup"><span data-stu-id="cb97a-497">3.5.1 Additional references for results caching with the wrapping provider</span></span>

-   <span data-ttu-id="cb97a-498">Julie Lerman 在实体框架和 Windows Azure 中编写了一个 "二级缓存"，其中包括如何更新示例包装提供程序以使用 Windows Server AppFabric 缓存： [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span><span class="sxs-lookup"><span data-stu-id="cb97a-498">Julie Lerman has written a "Second-Level Caching in Entity Framework and Windows Azure" MSDN article that includes how to update the sample wrapping provider to use Windows Server AppFabric caching: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)</span></span>
-   <span data-ttu-id="cb97a-499">如果使用实体框架5，则团队博客会提供一篇文章，其中介绍了如何使用缓存提供程序为实体框架5： \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>运行操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-499">If you are working with Entity Framework 5, the team blog has a post that describes how to get things running with the caching provider for Entity Framework 5: \<http://blogs.msdn.com/b/adonet/archive/2010/09/13/ef-caching-with-jarek-kowalski-s-provider.aspx>.</span></span> <span data-ttu-id="cb97a-500">它还包含 T4 模板，以帮助自动将二级缓存添加到项目。</span><span class="sxs-lookup"><span data-stu-id="cb97a-500">It also includes a T4 template to help automate adding the 2nd-level caching to your project.</span></span>

## <a name="4-autocompiled-queries"></a><span data-ttu-id="cb97a-501">4 Autocompiled 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-501">4 Autocompiled Queries</span></span>

<span data-ttu-id="cb97a-502">使用实体框架向数据库发出查询时，必须完成一系列步骤，然后才能真正具体化结果;一个这样的步骤就是查询编译。</span><span class="sxs-lookup"><span data-stu-id="cb97a-502">When a query is issued against a database using Entity Framework, it must go through a series of steps before actually materializing the results; one such step is Query Compilation.</span></span> <span data-ttu-id="cb97a-503">已知实体 SQL 查询具有良好的性能，因为它们会自动缓存，因此，第二个或第三次执行相同的查询时，可以跳过计划编译器并改用缓存的计划。</span><span class="sxs-lookup"><span data-stu-id="cb97a-503">Entity SQL queries were known to have good performance as they are automatically cached, so the second or third time you execute the same query it can skip the plan compiler and use the cached plan instead.</span></span>

<span data-ttu-id="cb97a-504">实体框架5也为 LINQ to Entities 查询引入了自动缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-504">Entity Framework 5 introduced automatic caching for LINQ to Entities queries as well.</span></span> <span data-ttu-id="cb97a-505">在过去的几个版本中实体框架创建 CompiledQuery 以提高性能，因为这会使你的 LINQ to Entities 查询可缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-505">In past editions of Entity Framework creating a CompiledQuery to speed your performance was a common practice, as this would make your LINQ to Entities query cacheable.</span></span> <span data-ttu-id="cb97a-506">因为缓存现在会自动执行，而不使用 CompiledQuery，因此，我们会将此功能称为 "autocompiled 查询"。</span><span class="sxs-lookup"><span data-stu-id="cb97a-506">Since caching is now done automatically without the use of a CompiledQuery, we call this feature “autocompiled queries”.</span></span> <span data-ttu-id="cb97a-507">有关查询计划缓存及其机制的详细信息，请参阅查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-507">For more information about the query plan cache and its mechanics, see Query Plan Caching.</span></span>

<span data-ttu-id="cb97a-508">实体框架检测需要重新编译查询的时间，并在调用查询时进行调用（即使之前已编译过）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-508">Entity Framework detects when a query requires to be recompiled, and does so when the query is invoked even if it had been compiled before.</span></span> <span data-ttu-id="cb97a-509">导致查询重新编译的常见情况如下：</span><span class="sxs-lookup"><span data-stu-id="cb97a-509">Common conditions that cause the query to be recompiled are:</span></span>

-   <span data-ttu-id="cb97a-510">更改与查询关联的 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="cb97a-510">Changing the MergeOption associated to your query.</span></span> <span data-ttu-id="cb97a-511">将不会使用缓存查询，而是重新运行计划编译器，并缓存新创建的计划。</span><span class="sxs-lookup"><span data-stu-id="cb97a-511">The cached query will not be used, instead the plan compiler will run again and the newly created plan gets cached.</span></span>
-   <span data-ttu-id="cb97a-512">更改 ContextOptions 的值。 UseCSharpNullComparisonBehavior。</span><span class="sxs-lookup"><span data-stu-id="cb97a-512">Changing the value of ContextOptions.UseCSharpNullComparisonBehavior.</span></span> <span data-ttu-id="cb97a-513">与更改 MergeOption 的效果相同。</span><span class="sxs-lookup"><span data-stu-id="cb97a-513">You get the same effect as changing the MergeOption.</span></span>

<span data-ttu-id="cb97a-514">其他条件可能会阻止查询使用缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-514">Other conditions can prevent your query from using the cache.</span></span> <span data-ttu-id="cb97a-515">常见示例包括：</span><span class="sxs-lookup"><span data-stu-id="cb97a-515">Common examples are:</span></span>

-   <span data-ttu-id="cb97a-516">使用 IEnumerable&lt;T&gt;。包含&lt;&gt;（T 值）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-516">Using IEnumerable&lt;T&gt;.Contains&lt;&gt;(T value).</span></span>
-   <span data-ttu-id="cb97a-517">使用生成包含常量的查询的函数。</span><span class="sxs-lookup"><span data-stu-id="cb97a-517">Using functions that produce queries with constants.</span></span>
-   <span data-ttu-id="cb97a-518">使用非映射对象的属性。</span><span class="sxs-lookup"><span data-stu-id="cb97a-518">Using the properties of a non-mapped object.</span></span>
-   <span data-ttu-id="cb97a-519">将查询链接到需要重新编译的另一个查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-519">Linking your query to another query that requires to be recompiled.</span></span>

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a><span data-ttu-id="cb97a-520">4.1 使用 IEnumerable&lt;T&gt;。包含&lt;T&gt;（T 值）</span><span class="sxs-lookup"><span data-stu-id="cb97a-520">4.1 Using IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value)</span></span>

<span data-ttu-id="cb97a-521">实体框架不缓存调用 IEnumerable&lt;T&gt;的查询。包含针对内存中集合的&lt;T&gt;（T 值），因为集合的值被视为可变的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-521">Entity Framework does not cache queries that invoke IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) against an in-memory collection, since the values of the collection are considered volatile.</span></span> <span data-ttu-id="cb97a-522">下面的示例查询将不会被缓存，因此计划编译器将始终对其进行处理：</span><span class="sxs-lookup"><span data-stu-id="cb97a-522">The following example query will not be cached, so it will always be processed by the plan compiler:</span></span>

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

<span data-ttu-id="cb97a-523">请注意，执行包含的 IEnumerable 的大小决定了查询的编译速度或速度。</span><span class="sxs-lookup"><span data-stu-id="cb97a-523">Note that the size of the IEnumerable against which Contains is executed determines how fast or how slow your query is compiled.</span></span> <span data-ttu-id="cb97a-524">使用较大的集合（如上面的示例所示）时，性能可能会显著降低。</span><span class="sxs-lookup"><span data-stu-id="cb97a-524">Performance can suffer significantly when using large collections such as the one shown in the example above.</span></span>

<span data-ttu-id="cb97a-525">实体框架6包含对 IEnumerable&lt;T&gt;方式的优化。包含&lt;T&gt;（T 值）在执行查询时工作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-525">Entity Framework 6 contains optimizations to the way IEnumerable&lt;T&gt;.Contains&lt;T&gt;(T value) works when queries are executed.</span></span> <span data-ttu-id="cb97a-526">生成的 SQL 代码的速度要快得多，并且可读性更强，在大多数情况下，它在服务器上的执行速度更快。</span><span class="sxs-lookup"><span data-stu-id="cb97a-526">The SQL code that is generated is much faster to produce and more readable, and in most cases it also executes faster in the server.</span></span>

### <a name="42-using-functions-that-produce-queries-with-constants"></a><span data-ttu-id="cb97a-527">4.2 使用生成包含常量的查询的函数</span><span class="sxs-lookup"><span data-stu-id="cb97a-527">4.2 Using functions that produce queries with constants</span></span>

<span data-ttu-id="cb97a-528">Skip （）、Take （）、Contains （）和 DefautIfEmpty （） LINQ 运算符不生成带参数的 SQL 查询，而是将作为常量传递给它们的值。</span><span class="sxs-lookup"><span data-stu-id="cb97a-528">The Skip(), Take(), Contains() and DefautIfEmpty() LINQ operators do not produce SQL queries with parameters but instead put the values passed to them as constants.</span></span> <span data-ttu-id="cb97a-529">因此，除非在后续的查询执行中使用了相同的常量，否则，最终可能会完全相同的查询将污染查询计划缓存（在 EF 堆栈和数据库服务器上）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-529">Because of this, queries that might otherwise be identical end up polluting the query plan cache, both on the EF stack and on the database server, and do not get reutilized unless the same constants are used in a subsequent query execution.</span></span> <span data-ttu-id="cb97a-530">例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-530">For example:</span></span>

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

<span data-ttu-id="cb97a-531">在此示例中，每次使用不同于 id 的值执行此查询时，查询都将编译为新计划。</span><span class="sxs-lookup"><span data-stu-id="cb97a-531">In this example, each time this query is executed with a different value for id the query will be compiled into a new plan.</span></span>

<span data-ttu-id="cb97a-532">特别要注意的是在进行分页时使用 Skip 和 Take。</span><span class="sxs-lookup"><span data-stu-id="cb97a-532">In particular pay attention to the use of Skip and Take when doing paging.</span></span> <span data-ttu-id="cb97a-533">在 EF6 中，这些方法具有一个 lambda 重载，可有效地使缓存查询计划可重复使用，因为 EF 可以捕获传递给这些方法的变量并将其转换为 SQLparameters。</span><span class="sxs-lookup"><span data-stu-id="cb97a-533">In EF6 these methods have a lambda overload that effectively makes the cached query plan reusable because EF can capture variables passed to these methods and translate them to SQLparameters.</span></span> <span data-ttu-id="cb97a-534">这也有助于保留缓存清理器，因为另外，每个具有不同常量的查询都可用于 Skip，并会获取其自己的查询计划缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cb97a-534">This also helps keep the cache cleaner since otherwise each query with a different constant for Skip and Take would get its own query plan cache entry.</span></span>

<span data-ttu-id="cb97a-535">请考虑以下代码，该代码是不理想的，但它只是为了求知欲此类查询：</span><span class="sxs-lookup"><span data-stu-id="cb97a-535">Consider the following code, which is suboptimal but is only meant to exemplify this class of queries:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="cb97a-536">此相同代码的更快版本涉及到使用 lambda 调用 Skip：</span><span class="sxs-lookup"><span data-stu-id="cb97a-536">A faster version of this same code would involve calling Skip with a lambda:</span></span>

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

<span data-ttu-id="cb97a-537">由于每次运行查询时都使用相同的查询计划，因此第二个代码段的运行速度最多可达11%，这会节省 CPU 时间，并避免查询缓存的污染。</span><span class="sxs-lookup"><span data-stu-id="cb97a-537">The second snippet may run up to 11% faster because the same query plan is used every time the query is run, which saves CPU time and avoids polluting the query cache.</span></span> <span data-ttu-id="cb97a-538">此外，由于 Skip 参数在闭包中，代码可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="cb97a-538">Furthermore, because the parameter to Skip is in a closure the code might as well look like this now:</span></span>

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a><span data-ttu-id="cb97a-539">4.3 使用非映射对象的属性</span><span class="sxs-lookup"><span data-stu-id="cb97a-539">4.3 Using the properties of a non-mapped object</span></span>

<span data-ttu-id="cb97a-540">如果查询使用非映射对象类型的属性作为参数，则不会缓存查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-540">When a query uses the properties of a non-mapped object type as a parameter then the query will not get cached.</span></span> <span data-ttu-id="cb97a-541">例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-541">For example:</span></span>

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

<span data-ttu-id="cb97a-542">在此示例中，假定类 NonMappedType 不是实体模型的一部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-542">In this example, assume that class NonMappedType is not part of the Entity model.</span></span> <span data-ttu-id="cb97a-543">可以轻松地将此查询更改为不使用非映射类型，而是使用局部变量作为查询的参数：</span><span class="sxs-lookup"><span data-stu-id="cb97a-543">This query can easily be changed to not use a non-mapped type and instead use a local variable as the parameter to the query:</span></span>

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

<span data-ttu-id="cb97a-544">在这种情况下，查询将能够缓存，并将受益于查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-544">In this case, the query will be able to get cached and will benefit from the query plan cache.</span></span>

### <a name="44-linking-to-queries-that-require-recompiling"></a><span data-ttu-id="cb97a-545">4.4 链接到需要重新编译的查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-545">4.4 Linking to queries that require recompiling</span></span>

<span data-ttu-id="cb97a-546">按照上述示例所示，如果第二个查询依赖需要重新编译的查询，则还将重新编译整个第二个查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-546">Following the same example as above, if you have a second query that relies on a query that needs to be recompiled, your entire second query will also be recompiled.</span></span> <span data-ttu-id="cb97a-547">下面是一个说明此方案的示例：</span><span class="sxs-lookup"><span data-stu-id="cb97a-547">Here’s an example to illustrate this scenario:</span></span>

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

<span data-ttu-id="cb97a-548">示例是泛型的，但它说明了链接到 firstQuery 如何导致 secondQuery 无法缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-548">The example is generic, but it illustrates how linking to firstQuery is causing secondQuery to be unable to get cached.</span></span> <span data-ttu-id="cb97a-549">如果 firstQuery 不是需要重新编译的查询，则 secondQuery 将被缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-549">If firstQuery had not been a query that requires recompiling, then secondQuery would have been cached.</span></span>

## <a name="5-notracking-queries"></a><span data-ttu-id="cb97a-550">5 NoTracking 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-550">5 NoTracking Queries</span></span>

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a><span data-ttu-id="cb97a-551">5.1 禁用更改跟踪以减少状态管理开销</span><span class="sxs-lookup"><span data-stu-id="cb97a-551">5.1 Disabling change tracking to reduce state management overhead</span></span>

<span data-ttu-id="cb97a-552">如果你处于只读方案，并且想要避免将对象加载到 ObjectStateManager 中的开销，则可以发出 "无跟踪" 查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-552">If you are in a read-only scenario and want to avoid the overhead of loading the objects into the ObjectStateManager, you can issue "No Tracking" queries.</span></span><span data-ttu-id="cb97a-553">  可以在查询级别禁用更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="cb97a-553">  Change tracking can be disabled at the query level.</span></span>

<span data-ttu-id="cb97a-554">但请注意，通过禁用更改跟踪，可以有效地关闭对象缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-554">Note though that by disabling change tracking you are effectively turning off the object cache.</span></span> <span data-ttu-id="cb97a-555">查询实体时，无法通过从 ObjectStateManager 中拉取先前具体化的查询结果来跳过具体化。</span><span class="sxs-lookup"><span data-stu-id="cb97a-555">When you query for an entity, we can't skip materialization by pulling the previously-materialized query results from the ObjectStateManager.</span></span> <span data-ttu-id="cb97a-556">如果在同一上下文中重复查询相同的实体，则可能会发现启用更改跟踪的性能优势。</span><span class="sxs-lookup"><span data-stu-id="cb97a-556">If you are repeatedly querying for the same entities on the same context, you might actually see a performance benefit from enabling change tracking.</span></span>

<span data-ttu-id="cb97a-557">使用 ObjectContext 进行查询时，ObjectQuery 和 ObjectSet 实例在设置后将记住 MergeOption，而在其上编写的查询将继承父查询的有效 MergeOption。</span><span class="sxs-lookup"><span data-stu-id="cb97a-557">When querying using ObjectContext, ObjectQuery and ObjectSet instances will remember a MergeOption once it is set, and queries that are composed on them will inherit the effective MergeOption of the parent query.</span></span> <span data-ttu-id="cb97a-558">当使用 DbContext 时，可以通过对 DbSet 调用 AsNoTracking （）修饰符来禁用跟踪。</span><span class="sxs-lookup"><span data-stu-id="cb97a-558">When using DbContext, tracking can be disabled by calling the AsNoTracking() modifier on the DbSet.</span></span>

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a><span data-ttu-id="cb97a-559">5.1.1 在使用 DbContext 时禁用查询的更改跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-559">5.1.1 Disabling change tracking for a query when using DbContext</span></span>

<span data-ttu-id="cb97a-560">可以通过将对查询中的 AsNoTracking （）方法的调用链接到 NoTracking，将查询的模式切换为 NoTracking。</span><span class="sxs-lookup"><span data-stu-id="cb97a-560">You can switch the mode of a query to NoTracking by chaining a call to the AsNoTracking() method in the query.</span></span> <span data-ttu-id="cb97a-561">与 ObjectQuery 不同，DbContext API 中的 DbSet 和 DbQuery 类没有 MergeOption 的可变属性。</span><span class="sxs-lookup"><span data-stu-id="cb97a-561">Unlike ObjectQuery, the DbSet and DbQuery classes in the DbContext API don’t have a mutable property for the MergeOption.</span></span>

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a><span data-ttu-id="cb97a-562">5.1.2 使用 ObjectContext 禁用查询级别的更改跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-562">5.1.2 Disabling change tracking at the query level using ObjectContext</span></span>

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a><span data-ttu-id="cb97a-563">使用 ObjectContext 禁用整个实体集的更改跟踪（5.1.3）</span><span class="sxs-lookup"><span data-stu-id="cb97a-563">5.1.3 Disabling change tracking for an entire entity set using ObjectContext</span></span>

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a><span data-ttu-id="cb97a-564">5.2 测试度量值演示了 NoTracking 查询的性能优势</span><span class="sxs-lookup"><span data-stu-id="cb97a-564">5.2 Test Metrics demonstrating the performance benefit of NoTracking queries</span></span>

<span data-ttu-id="cb97a-565">在此测试中，我们将通过比较跟踪到 Navision 模型的 NoTracking 查询来查看填充 ObjectStateManager 的成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-565">In this test we look at the cost of filling the ObjectStateManager by comparing Tracking to NoTracking queries for the Navision model.</span></span> <span data-ttu-id="cb97a-566">有关 Navision 模型的说明以及所执行的查询类型的说明，请参阅附录。</span><span class="sxs-lookup"><span data-stu-id="cb97a-566">See the appendix for a description of the Navision model and the types of queries which were executed.</span></span> <span data-ttu-id="cb97a-567">在此测试中，我们循环访问一个查询列表并逐个执行。</span><span class="sxs-lookup"><span data-stu-id="cb97a-567">In this test, we iterate through the list of queries and execute each one once.</span></span> <span data-ttu-id="cb97a-568">我们运行了两种不同的测试，一次是通过 NoTracking 查询，一次是 "AppendOnly" 的默认合并选项。</span><span class="sxs-lookup"><span data-stu-id="cb97a-568">We ran two variations of the test, once with NoTracking queries and once with the default merge option of "AppendOnly".</span></span> <span data-ttu-id="cb97a-569">每个变体运行3次，并采用运行的平均值。</span><span class="sxs-lookup"><span data-stu-id="cb97a-569">We ran each variation 3 times and take the mean value of the runs.</span></span> <span data-ttu-id="cb97a-570">在测试之间，我们清除 SQL Server 上的查询缓存，并通过运行以下命令来缩减 tempdb：</span><span class="sxs-lookup"><span data-stu-id="cb97a-570">Between the tests we clear the query cache on the SQL Server and shrink the tempdb by running the following commands:</span></span>

1.  <span data-ttu-id="cb97a-571">DBCC DROPCLEANBUFFERS</span><span class="sxs-lookup"><span data-stu-id="cb97a-571">DBCC DROPCLEANBUFFERS</span></span>
2.  <span data-ttu-id="cb97a-572">DBCC FREEPROCCACHE</span><span class="sxs-lookup"><span data-stu-id="cb97a-572">DBCC FREEPROCCACHE</span></span>
3.  <span data-ttu-id="cb97a-573">DBCC SHRINKDATABASE （tempdb，0）</span><span class="sxs-lookup"><span data-stu-id="cb97a-573">DBCC SHRINKDATABASE (tempdb, 0)</span></span>

<span data-ttu-id="cb97a-574">测试结果中，3个运行的中间值：</span><span class="sxs-lookup"><span data-stu-id="cb97a-574">Test Results, median over 3 runs:</span></span>

|                        | <span data-ttu-id="cb97a-575">无跟踪–工作集</span><span class="sxs-lookup"><span data-stu-id="cb97a-575">NO TRACKING – WORKING SET</span></span> | <span data-ttu-id="cb97a-576">无跟踪–时间</span><span class="sxs-lookup"><span data-stu-id="cb97a-576">NO TRACKING – TIME</span></span> | <span data-ttu-id="cb97a-577">仅追加–工作集</span><span class="sxs-lookup"><span data-stu-id="cb97a-577">APPEND ONLY – WORKING SET</span></span> | <span data-ttu-id="cb97a-578">仅追加–时间</span><span class="sxs-lookup"><span data-stu-id="cb97a-578">APPEND ONLY – TIME</span></span> |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| <span data-ttu-id="cb97a-579">**实体框架5**</span><span class="sxs-lookup"><span data-stu-id="cb97a-579">**Entity Framework 5**</span></span> | <span data-ttu-id="cb97a-580">460361728</span><span class="sxs-lookup"><span data-stu-id="cb97a-580">460361728</span></span>                 | <span data-ttu-id="cb97a-581">1163536 ms</span><span class="sxs-lookup"><span data-stu-id="cb97a-581">1163536 ms</span></span>         | <span data-ttu-id="cb97a-582">596545536</span><span class="sxs-lookup"><span data-stu-id="cb97a-582">596545536</span></span>                 | <span data-ttu-id="cb97a-583">1273042 ms</span><span class="sxs-lookup"><span data-stu-id="cb97a-583">1273042 ms</span></span>         |
| <span data-ttu-id="cb97a-584">**Entity Framework 6**</span><span class="sxs-lookup"><span data-stu-id="cb97a-584">**Entity Framework 6**</span></span> | <span data-ttu-id="cb97a-585">647127040</span><span class="sxs-lookup"><span data-stu-id="cb97a-585">647127040</span></span>                 | <span data-ttu-id="cb97a-586">190228 ms</span><span class="sxs-lookup"><span data-stu-id="cb97a-586">190228 ms</span></span>          | <span data-ttu-id="cb97a-587">832798720</span><span class="sxs-lookup"><span data-stu-id="cb97a-587">832798720</span></span>                 | <span data-ttu-id="cb97a-588">195521 ms</span><span class="sxs-lookup"><span data-stu-id="cb97a-588">195521 ms</span></span>          |

<span data-ttu-id="cb97a-589">在运行结束时，实体框架5的内存占用量要低于实体框架6。</span><span class="sxs-lookup"><span data-stu-id="cb97a-589">Entity Framework 5 will have a smaller memory footprint at the end of the run than Entity Framework 6 does.</span></span> <span data-ttu-id="cb97a-590">实体框架6占用的额外内存是附加内存结构和可提高性能的代码的结果。</span><span class="sxs-lookup"><span data-stu-id="cb97a-590">The additional memory consumed by Entity Framework 6 is the result of additional memory structures and code that enable new features and better performance.</span></span>

<span data-ttu-id="cb97a-591">使用 ObjectStateManager 时，内存占用量也有明显差异。</span><span class="sxs-lookup"><span data-stu-id="cb97a-591">There’s also a clear difference in memory footprint when using the ObjectStateManager.</span></span> <span data-ttu-id="cb97a-592">在跟踪从数据库具体化的所有实体时，实体框架5增加了30% 的占用量。</span><span class="sxs-lookup"><span data-stu-id="cb97a-592">Entity Framework 5 increased its footprint by 30% when keeping track of all the entities we materialized from the database.</span></span> <span data-ttu-id="cb97a-593">执行此操作时，实体框架6增加了28%。</span><span class="sxs-lookup"><span data-stu-id="cb97a-593">Entity Framework 6 increased its footprint by 28% when doing so.</span></span>

<span data-ttu-id="cb97a-594">就时间而言，实体框架6在此测试中的实体框架5比大型边距高。</span><span class="sxs-lookup"><span data-stu-id="cb97a-594">In terms of time, Entity Framework 6 outperforms Entity Framework 5 in this test by a large margin.</span></span> <span data-ttu-id="cb97a-595">实体框架6在实体框架5消耗的大约16% 的时间内完成了测试。</span><span class="sxs-lookup"><span data-stu-id="cb97a-595">Entity Framework 6 completed the test in roughly 16% of the time consumed by Entity Framework 5.</span></span> <span data-ttu-id="cb97a-596">此外，在使用 ObjectStateManager 时，实体框架5需要更多的时间来完成。</span><span class="sxs-lookup"><span data-stu-id="cb97a-596">Additionally, Entity Framework 5 takes 9% more time to complete when the ObjectStateManager is being used.</span></span> <span data-ttu-id="cb97a-597">相比之下，使用 ObjectStateManager 时，实体框架6使用了3% 的时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-597">In comparison, Entity Framework 6 is using 3% more time when using the ObjectStateManager.</span></span>

## <a name="6-query-execution-options"></a><span data-ttu-id="cb97a-598">6查询执行选项</span><span class="sxs-lookup"><span data-stu-id="cb97a-598">6 Query Execution Options</span></span>

<span data-ttu-id="cb97a-599">实体框架提供了几种不同的查询方法。</span><span class="sxs-lookup"><span data-stu-id="cb97a-599">Entity Framework offers several different ways to query.</span></span> <span data-ttu-id="cb97a-600">我们将查看以下选项，比较每个选项的优点和缺点，并检查其性能特征：</span><span class="sxs-lookup"><span data-stu-id="cb97a-600">We'll take a look at the following options, compare the pros and cons of each, and examine their performance characteristics:</span></span>

-   <span data-ttu-id="cb97a-601">LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="cb97a-601">LINQ to Entities.</span></span>
-   <span data-ttu-id="cb97a-602">无跟踪 LINQ to Entities。</span><span class="sxs-lookup"><span data-stu-id="cb97a-602">No Tracking LINQ to Entities.</span></span>
-   <span data-ttu-id="cb97a-603">通过 ObjectQuery 的实体 SQL。</span><span class="sxs-lookup"><span data-stu-id="cb97a-603">Entity SQL over an ObjectQuery.</span></span>
-   <span data-ttu-id="cb97a-604">通过 EntityCommand 的实体 SQL。</span><span class="sxs-lookup"><span data-stu-id="cb97a-604">Entity SQL over an EntityCommand.</span></span>
-   <span data-ttu-id="cb97a-605">System.data.objects.objectcontext.executestorequery.</span><span class="sxs-lookup"><span data-stu-id="cb97a-605">ExecuteStoreQuery.</span></span>
-   <span data-ttu-id="cb97a-606">SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="cb97a-606">SqlQuery.</span></span>
-   <span data-ttu-id="cb97a-607">CompiledQuery.</span><span class="sxs-lookup"><span data-stu-id="cb97a-607">CompiledQuery.</span></span>

### <a name="61-linq-to-entities-queries"></a><span data-ttu-id="cb97a-608">6.1 LINQ to Entities 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-608">6.1       LINQ to Entities queries</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="cb97a-609">**优点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-609">**Pros**</span></span>

-   <span data-ttu-id="cb97a-610">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-610">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="cb97a-611">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-611">Fully materialized objects.</span></span>
-   <span data-ttu-id="cb97a-612">最简单的方式是用编程语言内置的语法编写。</span><span class="sxs-lookup"><span data-stu-id="cb97a-612">Simplest to write with syntax built into the programming language.</span></span>
-   <span data-ttu-id="cb97a-613">性能良好。</span><span class="sxs-lookup"><span data-stu-id="cb97a-613">Good performance.</span></span>

<span data-ttu-id="cb97a-614">**缺点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-614">**Cons**</span></span>

-   <span data-ttu-id="cb97a-615">某些技术限制，例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-615">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="cb97a-616">对外部联接查询使用 System.linq.enumerable.defaultifempty 的模式将导致更复杂的查询，而不是实体 SQL 中的简单外部联接语句。</span><span class="sxs-lookup"><span data-stu-id="cb97a-616">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="cb97a-617">仍不能将 LIKE 与常规模式匹配一起使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-617">You still can’t use LIKE with general pattern matching.</span></span>

### <a name="62-no-tracking-linq-to-entities-queries"></a><span data-ttu-id="cb97a-618">6.2 无跟踪 LINQ to Entities 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-618">6.2       No Tracking LINQ to Entities queries</span></span>

<span data-ttu-id="cb97a-619">当上下文派生 ObjectContext 时：</span><span class="sxs-lookup"><span data-stu-id="cb97a-619">When the context derives ObjectContext:</span></span>

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="cb97a-620">当上下文派生 DbContext：</span><span class="sxs-lookup"><span data-stu-id="cb97a-620">When the context derives DbContext:</span></span>

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

<span data-ttu-id="cb97a-621">**优点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-621">**Pros**</span></span>

-   <span data-ttu-id="cb97a-622">通过常规 LINQ 查询提高了性能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-622">Improved performance over regular LINQ queries.</span></span>
-   <span data-ttu-id="cb97a-623">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-623">Fully materialized objects.</span></span>
-   <span data-ttu-id="cb97a-624">最简单的方式是用编程语言内置的语法编写。</span><span class="sxs-lookup"><span data-stu-id="cb97a-624">Simplest to write with syntax built into the programming language.</span></span>

<span data-ttu-id="cb97a-625">**缺点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-625">**Cons**</span></span>

-   <span data-ttu-id="cb97a-626">不适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-626">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="cb97a-627">某些技术限制，例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-627">Certain technical restrictions, such as:</span></span>
    -   <span data-ttu-id="cb97a-628">对外部联接查询使用 System.linq.enumerable.defaultifempty 的模式将导致更复杂的查询，而不是实体 SQL 中的简单外部联接语句。</span><span class="sxs-lookup"><span data-stu-id="cb97a-628">Patterns using DefaultIfEmpty for OUTER JOIN queries result in more complex queries than simple OUTER JOIN statements in Entity SQL.</span></span>
    -   <span data-ttu-id="cb97a-629">仍不能将 LIKE 与常规模式匹配一起使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-629">You still can’t use LIKE with general pattern matching.</span></span>

<span data-ttu-id="cb97a-630">请注意，即使未指定 NoTracking，也不会跟踪项目标量属性的查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-630">Note that queries that project scalar properties are not tracked even if the NoTracking is not specified.</span></span> <span data-ttu-id="cb97a-631">例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-631">For example:</span></span>

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

<span data-ttu-id="cb97a-632">此特定查询未显式指定为 NoTracking，但由于它不是对象状态管理器已知的类型，因此不会跟踪具体化的结果。</span><span class="sxs-lookup"><span data-stu-id="cb97a-632">This particular query doesn’t explicitly specify being NoTracking, but since it’s not materializing a type that’s known to the object state manager then the materialized result is not tracked.</span></span>

### <a name="63-entity-sql-over-an-objectquery"></a><span data-ttu-id="cb97a-633">6.3 实体 SQL ObjectQuery</span><span class="sxs-lookup"><span data-stu-id="cb97a-633">6.3       Entity SQL over an ObjectQuery</span></span>

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

<span data-ttu-id="cb97a-634">**优点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-634">**Pros**</span></span>

-   <span data-ttu-id="cb97a-635">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-635">Suitable for CUD operations.</span></span>
-   <span data-ttu-id="cb97a-636">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-636">Fully materialized objects.</span></span>
-   <span data-ttu-id="cb97a-637">支持查询计划缓存。</span><span class="sxs-lookup"><span data-stu-id="cb97a-637">Supports query plan caching.</span></span>

<span data-ttu-id="cb97a-638">**缺点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-638">**Cons**</span></span>

-   <span data-ttu-id="cb97a-639">涉及与语言中内置的查询构造更容易出现用户错误的文本查询字符串。</span><span class="sxs-lookup"><span data-stu-id="cb97a-639">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>

### <a name="64-entity-sql-over-an-entity-command"></a><span data-ttu-id="cb97a-640">6.4 通过 Entity 命令实体 SQL</span><span class="sxs-lookup"><span data-stu-id="cb97a-640">6.4       Entity SQL over an Entity Command</span></span>

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

<span data-ttu-id="cb97a-641">**优点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-641">**Pros**</span></span>

-   <span data-ttu-id="cb97a-642">支持 .NET 4.0 中的查询计划缓存（.NET 4.5 中的所有其他查询类型都支持计划缓存）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-642">Supports query plan caching in .NET 4.0 (plan caching is supported by all other query types in .NET 4.5).</span></span>

<span data-ttu-id="cb97a-643">**缺点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-643">**Cons**</span></span>

-   <span data-ttu-id="cb97a-644">涉及与语言中内置的查询构造更容易出现用户错误的文本查询字符串。</span><span class="sxs-lookup"><span data-stu-id="cb97a-644">Involves textual query strings which are more prone to user error than query constructs built into the language.</span></span>
-   <span data-ttu-id="cb97a-645">不适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-645">Not suitable for CUD operations.</span></span>
-   <span data-ttu-id="cb97a-646">结果不会自动具体化，必须从数据读取器读取。</span><span class="sxs-lookup"><span data-stu-id="cb97a-646">Results are not automatically materialized, and must be read from the data reader.</span></span>

### <a name="65-sqlquery-and-executestorequery"></a><span data-ttu-id="cb97a-647">6.5 SqlQuery 和 System.data.objects.objectcontext.executestorequery</span><span class="sxs-lookup"><span data-stu-id="cb97a-647">6.5       SqlQuery and ExecuteStoreQuery</span></span>

<span data-ttu-id="cb97a-648">数据库上的 SqlQuery：</span><span class="sxs-lookup"><span data-stu-id="cb97a-648">SqlQuery on Database:</span></span>

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

<span data-ttu-id="cb97a-649">DbSet 上的 SqlQuery：</span><span class="sxs-lookup"><span data-stu-id="cb97a-649">SqlQuery on DbSet:</span></span>

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

<span data-ttu-id="cb97a-650">ExecyteStoreQuery:</span><span class="sxs-lookup"><span data-stu-id="cb97a-650">ExecyteStoreQuery:</span></span>

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

<span data-ttu-id="cb97a-651">**优点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-651">**Pros**</span></span>

-   <span data-ttu-id="cb97a-652">由于计划编译器被绕过，因此速度一般最快。</span><span class="sxs-lookup"><span data-stu-id="cb97a-652">Generally fastest performance since plan compiler is bypassed.</span></span>
-   <span data-ttu-id="cb97a-653">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-653">Fully materialized objects.</span></span>
-   <span data-ttu-id="cb97a-654">适用于从 DbSet 中使用的 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-654">Suitable for CUD operations when used from the DbSet.</span></span>

<span data-ttu-id="cb97a-655">**缺点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-655">**Cons**</span></span>

-   <span data-ttu-id="cb97a-656">查询的文本和容易出错。</span><span class="sxs-lookup"><span data-stu-id="cb97a-656">Query is textual and error prone.</span></span>
-   <span data-ttu-id="cb97a-657">查询通过使用存储语义而不是概念语义绑定到特定的后端。</span><span class="sxs-lookup"><span data-stu-id="cb97a-657">Query is tied to a specific backend by using store semantics instead of conceptual semantics.</span></span>
-   <span data-ttu-id="cb97a-658">存在继承时，手动查询需要考虑请求类型的映射条件。</span><span class="sxs-lookup"><span data-stu-id="cb97a-658">When inheritance is present, handcrafted query needs to account for mapping conditions for the type requested.</span></span>

### <a name="66-compiledquery"></a><span data-ttu-id="cb97a-659">6.6 CompiledQuery</span><span class="sxs-lookup"><span data-stu-id="cb97a-659">6.6       CompiledQuery</span></span>

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

<span data-ttu-id="cb97a-660">**优点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-660">**Pros**</span></span>

-   <span data-ttu-id="cb97a-661">通过常规 LINQ 查询提高了7% 的性能改进。</span><span class="sxs-lookup"><span data-stu-id="cb97a-661">Provides up to a 7% performance improvement over regular LINQ queries.</span></span>
-   <span data-ttu-id="cb97a-662">完全具体化的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-662">Fully materialized objects.</span></span>
-   <span data-ttu-id="cb97a-663">适用于 CUD 操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-663">Suitable for CUD operations.</span></span>

<span data-ttu-id="cb97a-664">**缺点**</span><span class="sxs-lookup"><span data-stu-id="cb97a-664">**Cons**</span></span>

-   <span data-ttu-id="cb97a-665">增加了复杂性和编程开销。</span><span class="sxs-lookup"><span data-stu-id="cb97a-665">Increased complexity and programming overhead.</span></span>
-   <span data-ttu-id="cb97a-666">在已编译的查询顶部编写时，性能改进会丢失。</span><span class="sxs-lookup"><span data-stu-id="cb97a-666">The performance improvement is lost when composing on top of a compiled query.</span></span>
-   <span data-ttu-id="cb97a-667">某些 LINQ 查询不能编写为 CompiledQuery （例如，匿名类型的投影）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-667">Some LINQ queries can't be written as a CompiledQuery - for example, projections of anonymous types.</span></span>

### <a name="67-performance-comparison-of-different-query-options"></a><span data-ttu-id="cb97a-668">6.7 不同查询选项的性能比较</span><span class="sxs-lookup"><span data-stu-id="cb97a-668">6.7       Performance Comparison of different query options</span></span>

<span data-ttu-id="cb97a-669">简单的 microbenchmarks，其中未计时上下文创建操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-669">Simple microbenchmarks where the context creation was not timed were put to the test.</span></span> <span data-ttu-id="cb97a-670">对于受控环境中的一组非缓存实体，我们测量了5000次的查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-670">We measured querying 5000 times for a set of non-cached entities in a controlled environment.</span></span> <span data-ttu-id="cb97a-671">将会出现一条警告，其中包含警告：它们不反映应用程序产生的实际数量，而是对不同查询选项进行比较时的性能差异量的精确度量从苹果到苹果，不包括创建新上下文的成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-671">These numbers are to be taken with a warning: they do not reflect actual numbers produced by an application, but instead they are a very accurate measurement of how much of a performance difference there is when different querying options are compared apples-to-apples, excluding the cost of creating a new context.</span></span>

| <span data-ttu-id="cb97a-672">EF</span><span class="sxs-lookup"><span data-stu-id="cb97a-672">EF</span></span>  | <span data-ttu-id="cb97a-673">测试</span><span class="sxs-lookup"><span data-stu-id="cb97a-673">Test</span></span>                                 | <span data-ttu-id="cb97a-674">时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="cb97a-674">Time (ms)</span></span> | <span data-ttu-id="cb97a-675">内存</span><span class="sxs-lookup"><span data-stu-id="cb97a-675">Memory</span></span>   |
|:----|:-------------------------------------|:----------|:---------|
| <span data-ttu-id="cb97a-676">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-676">EF5</span></span> | <span data-ttu-id="cb97a-677">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="cb97a-677">ObjectContext ESQL</span></span>                   | <span data-ttu-id="cb97a-678">2414</span><span class="sxs-lookup"><span data-stu-id="cb97a-678">2414</span></span>      | <span data-ttu-id="cb97a-679">38801408</span><span class="sxs-lookup"><span data-stu-id="cb97a-679">38801408</span></span> |
| <span data-ttu-id="cb97a-680">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-680">EF5</span></span> | <span data-ttu-id="cb97a-681">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-681">ObjectContext Linq Query</span></span>             | <span data-ttu-id="cb97a-682">2692</span><span class="sxs-lookup"><span data-stu-id="cb97a-682">2692</span></span>      | <span data-ttu-id="cb97a-683">38277120</span><span class="sxs-lookup"><span data-stu-id="cb97a-683">38277120</span></span> |
| <span data-ttu-id="cb97a-684">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-684">EF5</span></span> | <span data-ttu-id="cb97a-685">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-685">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="cb97a-686">2818</span><span class="sxs-lookup"><span data-stu-id="cb97a-686">2818</span></span>      | <span data-ttu-id="cb97a-687">41840640</span><span class="sxs-lookup"><span data-stu-id="cb97a-687">41840640</span></span> |
| <span data-ttu-id="cb97a-688">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-688">EF5</span></span> | <span data-ttu-id="cb97a-689">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-689">DbContext Linq Query</span></span>                 | <span data-ttu-id="cb97a-690">2930</span><span class="sxs-lookup"><span data-stu-id="cb97a-690">2930</span></span>      | <span data-ttu-id="cb97a-691">41771008</span><span class="sxs-lookup"><span data-stu-id="cb97a-691">41771008</span></span> |
| <span data-ttu-id="cb97a-692">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-692">EF5</span></span> | <span data-ttu-id="cb97a-693">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-693">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="cb97a-694">3013</span><span class="sxs-lookup"><span data-stu-id="cb97a-694">3013</span></span>      | <span data-ttu-id="cb97a-695">38412288</span><span class="sxs-lookup"><span data-stu-id="cb97a-695">38412288</span></span> |
|     |                                      |           |          |
| <span data-ttu-id="cb97a-696">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-696">EF6</span></span> | <span data-ttu-id="cb97a-697">ObjectContext ESQL</span><span class="sxs-lookup"><span data-stu-id="cb97a-697">ObjectContext ESQL</span></span>                   | <span data-ttu-id="cb97a-698">2059</span><span class="sxs-lookup"><span data-stu-id="cb97a-698">2059</span></span>      | <span data-ttu-id="cb97a-699">46039040</span><span class="sxs-lookup"><span data-stu-id="cb97a-699">46039040</span></span> |
| <span data-ttu-id="cb97a-700">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-700">EF6</span></span> | <span data-ttu-id="cb97a-701">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-701">ObjectContext Linq Query</span></span>             | <span data-ttu-id="cb97a-702">3074</span><span class="sxs-lookup"><span data-stu-id="cb97a-702">3074</span></span>      | <span data-ttu-id="cb97a-703">45248512</span><span class="sxs-lookup"><span data-stu-id="cb97a-703">45248512</span></span> |
| <span data-ttu-id="cb97a-704">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-704">EF6</span></span> | <span data-ttu-id="cb97a-705">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-705">DbContext Linq Query No Tracking</span></span>     | <span data-ttu-id="cb97a-706">3125</span><span class="sxs-lookup"><span data-stu-id="cb97a-706">3125</span></span>      | <span data-ttu-id="cb97a-707">47575040</span><span class="sxs-lookup"><span data-stu-id="cb97a-707">47575040</span></span> |
| <span data-ttu-id="cb97a-708">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-708">EF6</span></span> | <span data-ttu-id="cb97a-709">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-709">DbContext Linq Query</span></span>                 | <span data-ttu-id="cb97a-710">3420</span><span class="sxs-lookup"><span data-stu-id="cb97a-710">3420</span></span>      | <span data-ttu-id="cb97a-711">47652864</span><span class="sxs-lookup"><span data-stu-id="cb97a-711">47652864</span></span> |
| <span data-ttu-id="cb97a-712">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-712">EF6</span></span> | <span data-ttu-id="cb97a-713">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-713">ObjectContext Linq Query No Tracking</span></span> | <span data-ttu-id="cb97a-714">3593</span><span class="sxs-lookup"><span data-stu-id="cb97a-714">3593</span></span>      | <span data-ttu-id="cb97a-715">45260800</span><span class="sxs-lookup"><span data-stu-id="cb97a-715">45260800</span></span> |

![EF5 微基准，5000温迭代](~/ef6/media/ef5micro5000warm.png)

![EF6 微基准，5000温迭代](~/ef6/media/ef6micro5000warm.png)

<span data-ttu-id="cb97a-718">Microbenchmarks 对代码中的小更改非常敏感。</span><span class="sxs-lookup"><span data-stu-id="cb97a-718">Microbenchmarks are very sensitive to small changes in the code.</span></span> <span data-ttu-id="cb97a-719">在这种情况下，实体框架5与实体框架6的成本之间的差异是由于添加了[截取](~/ef6/fundamentals/logging-and-interception.md)和[事务改进](~/ef6/saving/transactions.md)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-719">In this case, the difference between the costs of Entity Framework 5 and Entity Framework 6 are due to the addition of [interception](~/ef6/fundamentals/logging-and-interception.md) and [transactional improvements](~/ef6/saving/transactions.md).</span></span> <span data-ttu-id="cb97a-720">不过，这些 microbenchmarks 数字是实体框架的作用的一小部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-720">These microbenchmarks numbers, however, are an amplified vision into a very small fragment of what Entity Framework does.</span></span> <span data-ttu-id="cb97a-721">在从实体框架5升级到实体框架6时，热查询的实际方案不应看到性能回归。</span><span class="sxs-lookup"><span data-stu-id="cb97a-721">Real-world scenarios of warm queries should not see a performance regression when upgrading from Entity Framework 5 to Entity Framework 6.</span></span>

<span data-ttu-id="cb97a-722">为了比较不同查询选项的实际性能，我们创建了5个单独的测试变体，其中使用不同的查询选项来选择类别名称为 "饮料" 的所有产品。</span><span class="sxs-lookup"><span data-stu-id="cb97a-722">To compare the real-world performance of the different query options, we created 5 separate test variations where we use a different query option to select all products whose category name is "Beverages".</span></span> <span data-ttu-id="cb97a-723">每个迭代都包括创建上下文的成本，以及具体化所有返回实体的成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-723">Each iteration includes the cost of creating the context, and the cost of materializing all returned entities.</span></span> <span data-ttu-id="cb97a-724">在采用1000计时迭代的总和之前，将 untimed 运行10次迭代。</span><span class="sxs-lookup"><span data-stu-id="cb97a-724">10 iterations are run untimed before taking the sum of 1000 timed iterations.</span></span> <span data-ttu-id="cb97a-725">显示的结果是从每个测试的5次运行中获取的中间值。</span><span class="sxs-lookup"><span data-stu-id="cb97a-725">The results shown are the median run taken from 5 runs of each test.</span></span> <span data-ttu-id="cb97a-726">有关详细信息，请参阅包含测试代码的附录 B。</span><span class="sxs-lookup"><span data-stu-id="cb97a-726">For more information, see Appendix B which includes the code for the test.</span></span>

| <span data-ttu-id="cb97a-727">EF</span><span class="sxs-lookup"><span data-stu-id="cb97a-727">EF</span></span>  | <span data-ttu-id="cb97a-728">测试</span><span class="sxs-lookup"><span data-stu-id="cb97a-728">Test</span></span>                                        | <span data-ttu-id="cb97a-729">时间（毫秒）</span><span class="sxs-lookup"><span data-stu-id="cb97a-729">Time (ms)</span></span> | <span data-ttu-id="cb97a-730">内存</span><span class="sxs-lookup"><span data-stu-id="cb97a-730">Memory</span></span>   |
|:----|:--------------------------------------------|:----------|:---------|
| <span data-ttu-id="cb97a-731">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-731">EF5</span></span> | <span data-ttu-id="cb97a-732">ObjectContext 实体命令</span><span class="sxs-lookup"><span data-stu-id="cb97a-732">ObjectContext Entity Command</span></span>                | <span data-ttu-id="cb97a-733">621</span><span class="sxs-lookup"><span data-stu-id="cb97a-733">621</span></span>       | <span data-ttu-id="cb97a-734">39350272</span><span class="sxs-lookup"><span data-stu-id="cb97a-734">39350272</span></span> |
| <span data-ttu-id="cb97a-735">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-735">EF5</span></span> | <span data-ttu-id="cb97a-736">对数据库进行 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-736">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="cb97a-737">825</span><span class="sxs-lookup"><span data-stu-id="cb97a-737">825</span></span>       | <span data-ttu-id="cb97a-738">37519360</span><span class="sxs-lookup"><span data-stu-id="cb97a-738">37519360</span></span> |
| <span data-ttu-id="cb97a-739">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-739">EF5</span></span> | <span data-ttu-id="cb97a-740">ObjectContext 存储查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-740">ObjectContext Store Query</span></span>                   | <span data-ttu-id="cb97a-741">878</span><span class="sxs-lookup"><span data-stu-id="cb97a-741">878</span></span>       | <span data-ttu-id="cb97a-742">39460864</span><span class="sxs-lookup"><span data-stu-id="cb97a-742">39460864</span></span> |
| <span data-ttu-id="cb97a-743">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-743">EF5</span></span> | <span data-ttu-id="cb97a-744">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-744">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="cb97a-745">969</span><span class="sxs-lookup"><span data-stu-id="cb97a-745">969</span></span>       | <span data-ttu-id="cb97a-746">38293504</span><span class="sxs-lookup"><span data-stu-id="cb97a-746">38293504</span></span> |
| <span data-ttu-id="cb97a-747">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-747">EF5</span></span> | <span data-ttu-id="cb97a-748">使用对象查询的 ObjectContext 实体 Sql</span><span class="sxs-lookup"><span data-stu-id="cb97a-748">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="cb97a-749">1089</span><span class="sxs-lookup"><span data-stu-id="cb97a-749">1089</span></span>      | <span data-ttu-id="cb97a-750">38981632</span><span class="sxs-lookup"><span data-stu-id="cb97a-750">38981632</span></span> |
| <span data-ttu-id="cb97a-751">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-751">EF5</span></span> | <span data-ttu-id="cb97a-752">ObjectContext 编译查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-752">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="cb97a-753">1099</span><span class="sxs-lookup"><span data-stu-id="cb97a-753">1099</span></span>      | <span data-ttu-id="cb97a-754">38682624</span><span class="sxs-lookup"><span data-stu-id="cb97a-754">38682624</span></span> |
| <span data-ttu-id="cb97a-755">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-755">EF5</span></span> | <span data-ttu-id="cb97a-756">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-756">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="cb97a-757">1152</span><span class="sxs-lookup"><span data-stu-id="cb97a-757">1152</span></span>      | <span data-ttu-id="cb97a-758">38178816</span><span class="sxs-lookup"><span data-stu-id="cb97a-758">38178816</span></span> |
| <span data-ttu-id="cb97a-759">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-759">EF5</span></span> | <span data-ttu-id="cb97a-760">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-760">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="cb97a-761">1208</span><span class="sxs-lookup"><span data-stu-id="cb97a-761">1208</span></span>      | <span data-ttu-id="cb97a-762">41803776</span><span class="sxs-lookup"><span data-stu-id="cb97a-762">41803776</span></span> |
| <span data-ttu-id="cb97a-763">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-763">EF5</span></span> | <span data-ttu-id="cb97a-764">DbSet 上的 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-764">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="cb97a-765">1414</span><span class="sxs-lookup"><span data-stu-id="cb97a-765">1414</span></span>      | <span data-ttu-id="cb97a-766">37982208</span><span class="sxs-lookup"><span data-stu-id="cb97a-766">37982208</span></span> |
| <span data-ttu-id="cb97a-767">EF5</span><span class="sxs-lookup"><span data-stu-id="cb97a-767">EF5</span></span> | <span data-ttu-id="cb97a-768">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-768">DbContext Linq Query</span></span>                        | <span data-ttu-id="cb97a-769">1574</span><span class="sxs-lookup"><span data-stu-id="cb97a-769">1574</span></span>      | <span data-ttu-id="cb97a-770">41738240</span><span class="sxs-lookup"><span data-stu-id="cb97a-770">41738240</span></span> |
|     |                                             |           |          |
| <span data-ttu-id="cb97a-771">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-771">EF6</span></span> | <span data-ttu-id="cb97a-772">ObjectContext 实体命令</span><span class="sxs-lookup"><span data-stu-id="cb97a-772">ObjectContext Entity Command</span></span>                | <span data-ttu-id="cb97a-773">480</span><span class="sxs-lookup"><span data-stu-id="cb97a-773">480</span></span>       | <span data-ttu-id="cb97a-774">47247360</span><span class="sxs-lookup"><span data-stu-id="cb97a-774">47247360</span></span> |
| <span data-ttu-id="cb97a-775">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-775">EF6</span></span> | <span data-ttu-id="cb97a-776">ObjectContext 存储查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-776">ObjectContext Store Query</span></span>                   | <span data-ttu-id="cb97a-777">493</span><span class="sxs-lookup"><span data-stu-id="cb97a-777">493</span></span>       | <span data-ttu-id="cb97a-778">46739456</span><span class="sxs-lookup"><span data-stu-id="cb97a-778">46739456</span></span> |
| <span data-ttu-id="cb97a-779">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-779">EF6</span></span> | <span data-ttu-id="cb97a-780">对数据库进行 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-780">DbContext Sql Query on Database</span></span>             | <span data-ttu-id="cb97a-781">614</span><span class="sxs-lookup"><span data-stu-id="cb97a-781">614</span></span>       | <span data-ttu-id="cb97a-782">41607168</span><span class="sxs-lookup"><span data-stu-id="cb97a-782">41607168</span></span> |
| <span data-ttu-id="cb97a-783">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-783">EF6</span></span> | <span data-ttu-id="cb97a-784">ObjectContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-784">ObjectContext Linq Query No Tracking</span></span>        | <span data-ttu-id="cb97a-785">684</span><span class="sxs-lookup"><span data-stu-id="cb97a-785">684</span></span>       | <span data-ttu-id="cb97a-786">46333952</span><span class="sxs-lookup"><span data-stu-id="cb97a-786">46333952</span></span> |
| <span data-ttu-id="cb97a-787">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-787">EF6</span></span> | <span data-ttu-id="cb97a-788">使用对象查询的 ObjectContext 实体 Sql</span><span class="sxs-lookup"><span data-stu-id="cb97a-788">ObjectContext Entity Sql using Object Query</span></span> | <span data-ttu-id="cb97a-789">767</span><span class="sxs-lookup"><span data-stu-id="cb97a-789">767</span></span>       | <span data-ttu-id="cb97a-790">48865280</span><span class="sxs-lookup"><span data-stu-id="cb97a-790">48865280</span></span> |
| <span data-ttu-id="cb97a-791">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-791">EF6</span></span> | <span data-ttu-id="cb97a-792">ObjectContext 编译查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-792">ObjectContext Compiled Query</span></span>                | <span data-ttu-id="cb97a-793">788</span><span class="sxs-lookup"><span data-stu-id="cb97a-793">788</span></span>       | <span data-ttu-id="cb97a-794">48467968</span><span class="sxs-lookup"><span data-stu-id="cb97a-794">48467968</span></span> |
| <span data-ttu-id="cb97a-795">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-795">EF6</span></span> | <span data-ttu-id="cb97a-796">DbContext Linq 查询无跟踪</span><span class="sxs-lookup"><span data-stu-id="cb97a-796">DbContext Linq Query No Tracking</span></span>            | <span data-ttu-id="cb97a-797">878</span><span class="sxs-lookup"><span data-stu-id="cb97a-797">878</span></span>       | <span data-ttu-id="cb97a-798">47554560</span><span class="sxs-lookup"><span data-stu-id="cb97a-798">47554560</span></span> |
| <span data-ttu-id="cb97a-799">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-799">EF6</span></span> | <span data-ttu-id="cb97a-800">ObjectContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-800">ObjectContext Linq Query</span></span>                    | <span data-ttu-id="cb97a-801">953</span><span class="sxs-lookup"><span data-stu-id="cb97a-801">953</span></span>       | <span data-ttu-id="cb97a-802">47632384</span><span class="sxs-lookup"><span data-stu-id="cb97a-802">47632384</span></span> |
| <span data-ttu-id="cb97a-803">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-803">EF6</span></span> | <span data-ttu-id="cb97a-804">DbSet 上的 DbContext Sql 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-804">DbContext Sql Query on DbSet</span></span>                | <span data-ttu-id="cb97a-805">1023</span><span class="sxs-lookup"><span data-stu-id="cb97a-805">1023</span></span>      | <span data-ttu-id="cb97a-806">41992192</span><span class="sxs-lookup"><span data-stu-id="cb97a-806">41992192</span></span> |
| <span data-ttu-id="cb97a-807">EF6</span><span class="sxs-lookup"><span data-stu-id="cb97a-807">EF6</span></span> | <span data-ttu-id="cb97a-808">DbContext Linq 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-808">DbContext Linq Query</span></span>                        | <span data-ttu-id="cb97a-809">1290</span><span class="sxs-lookup"><span data-stu-id="cb97a-809">1290</span></span>      | <span data-ttu-id="cb97a-810">47529984</span><span class="sxs-lookup"><span data-stu-id="cb97a-810">47529984</span></span> |


![EF5 温查询1000迭代](~/ef6/media/ef5warmquery1000.png)

![EF6 温查询1000迭代](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> <span data-ttu-id="cb97a-813">为完整起见，我们提供了一个变体，用于在 EntityCommand 上执行实体 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-813">For completeness, we included a variation where we execute an Entity SQL query on an EntityCommand.</span></span> <span data-ttu-id="cb97a-814">但是，因为对于此类查询，结果不是具体化的，所以比较不一定是苹果。</span><span class="sxs-lookup"><span data-stu-id="cb97a-814">However, because results are not materialized for such queries, the comparison isn't necessarily apples-to-apples.</span></span> <span data-ttu-id="cb97a-815">该测试包含一个接近近似值，旨在尝试进行比较 fairer。</span><span class="sxs-lookup"><span data-stu-id="cb97a-815">The test includes a close approximation to materializing to try making the comparison fairer.</span></span>

<span data-ttu-id="cb97a-816">在这种端到端的情况下，实体框架6比实体框架5高，因为在堆栈的几个部分中进行了性能改进，包括 DbContext 初始化更轻，&lt;&gt; 查找速度更快。</span><span class="sxs-lookup"><span data-stu-id="cb97a-816">In this end-to-end case, Entity Framework 6 outperforms Entity Framework 5 due to performance improvements made on several parts of the stack, including a much lighter DbContext initialization and faster MetadataCollection&lt;T&gt; lookups.</span></span>

## <a name="7-design-time-performance-considerations"></a><span data-ttu-id="cb97a-817">7设计时性能注意事项</span><span class="sxs-lookup"><span data-stu-id="cb97a-817">7 Design time performance considerations</span></span>

### <a name="71-inheritance-strategies"></a><span data-ttu-id="cb97a-818">7.1 继承策略</span><span class="sxs-lookup"><span data-stu-id="cb97a-818">7.1       Inheritance Strategies</span></span>

<span data-ttu-id="cb97a-819">使用实体框架时的另一个性能注意事项是使用的继承策略。</span><span class="sxs-lookup"><span data-stu-id="cb97a-819">Another performance consideration when using Entity Framework is the inheritance strategy you use.</span></span> <span data-ttu-id="cb97a-820">实体框架支持3种基本类型的继承及其组合：</span><span class="sxs-lookup"><span data-stu-id="cb97a-820">Entity Framework supports 3 basic types of inheritance and their combinations:</span></span>

-   <span data-ttu-id="cb97a-821">每个层次结构一个表（TPH）–其中每个继承集都映射到一个具有鉴别器列的表，以指示该层次结构中的哪个特定类型在行中表示。</span><span class="sxs-lookup"><span data-stu-id="cb97a-821">Table per Hierarchy (TPH) – where each inheritance set maps to a table with a discriminator column to indicate which particular type in the hierarchy is being represented in the row.</span></span>
-   <span data-ttu-id="cb97a-822">每种类型一个表（TPT）–其中每个类型在数据库中都有其自己的表;子表仅定义父表不包含的列。</span><span class="sxs-lookup"><span data-stu-id="cb97a-822">Table per Type (TPT) – where each type has its own table in the database; the child tables only define the columns that the parent table doesn’t contain.</span></span>
-   <span data-ttu-id="cb97a-823">每类表（TPC）–其中每个类型在数据库中都有其自己的完整表;子表定义其所有字段，包括在父类型中定义的字段。</span><span class="sxs-lookup"><span data-stu-id="cb97a-823">Table per Class (TPC) – where each type has its own full table in the database; the child tables define all their fields, including those defined in parent types.</span></span>

<span data-ttu-id="cb97a-824">如果模型使用 TPT 继承，则生成的查询将比使用其他继承策略生成的查询更复杂，这可能会导致存储上的执行时间较长。</span><span class="sxs-lookup"><span data-stu-id="cb97a-824">If your model uses TPT inheritance, the queries which are generated will be more complex than those that are generated with the other inheritance strategies, which may result on longer execution times on the store.</span></span><span data-ttu-id="cb97a-825">  它通常需要更长的时间来生成针对 TPT 模型的查询并具体化生成的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-825">  It will generally take longer to generate queries over a TPT model, and to materialize the resulting objects.</span></span>

<span data-ttu-id="cb97a-826">请参阅实体框架 "MSDN 博客文章： \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>中" 使用 TPT 时的性能注意事项 "（每种类型的表）继承。</span><span class="sxs-lookup"><span data-stu-id="cb97a-826">See the "Performance Considerations when using TPT (Table per Type) Inheritance in the Entity Framework" MSDN blog post: \<http://blogs.msdn.com/b/adonet/archive/2010/08/17/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework.aspx>.</span></span>

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a><span data-ttu-id="cb97a-827">7.1.1 在 Model First 或 Code First 应用程序中避免了 TPT</span><span class="sxs-lookup"><span data-stu-id="cb97a-827">7.1.1       Avoiding TPT in Model First or Code First applications</span></span>

<span data-ttu-id="cb97a-828">在具有 TPT 架构的现有数据库上创建模型时，不会有很多选项。</span><span class="sxs-lookup"><span data-stu-id="cb97a-828">When you create a model over an existing database that has a TPT schema, you don't have many options.</span></span> <span data-ttu-id="cb97a-829">但在使用 Model First 或 Code First 创建应用程序时，应避免 TPT 继承，以解决性能问题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-829">But when creating an application using Model First or Code First, you should avoid TPT inheritance for performance concerns.</span></span>

<span data-ttu-id="cb97a-830">使用 Entity Designer 向导中 Model First 时，将获取模型中的任何继承的 TPT。</span><span class="sxs-lookup"><span data-stu-id="cb97a-830">When you use Model First in the Entity Designer Wizard, you will get TPT for any inheritance in your model.</span></span> <span data-ttu-id="cb97a-831">如果要使用 Model First 切换到 TPH 继承策略，则可以使用 Visual Studio 库中提供的 "Entity Designer 数据库生成 Power Pack" （\<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-831">If you want to switch to a TPH inheritance strategy with Model First, you can use the "Entity Designer Database Generation Power Pack" available from the Visual Studio Gallery ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>).</span></span>

<span data-ttu-id="cb97a-832">当使用 Code First 配置模型与继承的映射时，默认情况下，EF 将使用 TPH，因此继承层次结构中的所有实体都将映射到同一个表。</span><span class="sxs-lookup"><span data-stu-id="cb97a-832">When using Code First to configure the mapping of a model with inheritance, EF will use TPH by default, therefore all entities in the inheritance hierarchy will be mapped to the same table.</span></span> <span data-ttu-id="cb97a-833">有关更多详细信息，请参阅 MSDN 杂志（ [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)）中 "Code First 实体框架 4.1" 文章中的 "与熟知 API 的映射" 部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-833">See the "Mapping with the Fluent API" section of the "Code First in Entity Framework4.1" article in MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)) for more details.</span></span>

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a><span data-ttu-id="cb97a-834">7.2 从 EF4 升级以改善模型生成时间</span><span class="sxs-lookup"><span data-stu-id="cb97a-834">7.2       Upgrading from EF4 to improve model generation time</span></span>

<span data-ttu-id="cb97a-835">在实体框架5和6中提供了对生成模型的应用商店层（SSDL）的算法的特定于 SQL Server 的改进，并作为在安装 Visual Studio 2010 SP1 时实体框架4的更新。</span><span class="sxs-lookup"><span data-stu-id="cb97a-835">A SQL Server-specific improvement to the algorithm that generates the store-layer (SSDL) of the model is available in Entity Framework 5 and 6, and as an update to Entity Framework 4 when Visual Studio 2010 SP1 is installed.</span></span> <span data-ttu-id="cb97a-836">以下测试结果演示了生成非常大的模型时的改进，在本例中为 Navision 模型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-836">The following test results demonstrate the improvement when generating a very big model, in this case the Navision model.</span></span> <span data-ttu-id="cb97a-837">有关详细信息，请参阅附录 C。</span><span class="sxs-lookup"><span data-stu-id="cb97a-837">See Appendix C for more details about it.</span></span>

<span data-ttu-id="cb97a-838">该模型包含1005个实体集和4227个关联集。</span><span class="sxs-lookup"><span data-stu-id="cb97a-838">The model contains 1005 entity sets and 4227 association sets.</span></span>

| <span data-ttu-id="cb97a-839">配置</span><span class="sxs-lookup"><span data-stu-id="cb97a-839">Configuration</span></span>                              | <span data-ttu-id="cb97a-840">消耗的时间细目</span><span class="sxs-lookup"><span data-stu-id="cb97a-840">Breakdown of time consumed</span></span>                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cb97a-841">Visual Studio 2010，实体框架4</span><span class="sxs-lookup"><span data-stu-id="cb97a-841">Visual Studio 2010, Entity Framework 4</span></span>     | <span data-ttu-id="cb97a-842">SSDL 生成：2小时27分钟</span><span class="sxs-lookup"><span data-stu-id="cb97a-842">SSDL Generation: 2 hr 27 min</span></span> <br/> <span data-ttu-id="cb97a-843">映射生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-843">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-844">CSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-844">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-845">ObjectLayer 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-845">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-846">视图生成： 2 h 14 分钟</span><span class="sxs-lookup"><span data-stu-id="cb97a-846">View Generation: 2 h 14 min</span></span> |
| <span data-ttu-id="cb97a-847">Visual Studio 2010 SP1，实体框架4</span><span class="sxs-lookup"><span data-stu-id="cb97a-847">Visual Studio 2010 SP1, Entity Framework 4</span></span> | <span data-ttu-id="cb97a-848">SSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-848">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-849">映射生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-849">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-850">CSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-850">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-851">ObjectLayer 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-851">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-852">视图生成：1小时53分钟</span><span class="sxs-lookup"><span data-stu-id="cb97a-852">View Generation: 1 hr 53 min</span></span>   |
| <span data-ttu-id="cb97a-853">Visual Studio 2013，实体框架5</span><span class="sxs-lookup"><span data-stu-id="cb97a-853">Visual Studio 2013, Entity Framework 5</span></span>     | <span data-ttu-id="cb97a-854">SSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-854">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-855">映射生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-855">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-856">CSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-856">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-857">ObjectLayer 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-857">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-858">视图生成：65分钟</span><span class="sxs-lookup"><span data-stu-id="cb97a-858">View Generation: 65 minutes</span></span>    |
| <span data-ttu-id="cb97a-859">Visual Studio 2013，实体框架6</span><span class="sxs-lookup"><span data-stu-id="cb97a-859">Visual Studio 2013, Entity Framework 6</span></span>     | <span data-ttu-id="cb97a-860">SSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-860">SSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-861">映射生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-861">Mapping Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-862">CSDL 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-862">CSDL Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-863">ObjectLayer 生成：1秒</span><span class="sxs-lookup"><span data-stu-id="cb97a-863">ObjectLayer Generation: 1 second</span></span> <br/> <span data-ttu-id="cb97a-864">视图生成：28秒。</span><span class="sxs-lookup"><span data-stu-id="cb97a-864">View Generation: 28 seconds.</span></span>   |


<span data-ttu-id="cb97a-865">值得注意的是，在生成 SSDL 时，负载几乎完全花费在 SQL Server 中，而客户端开发计算机等待返回的结果是从服务器返回的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-865">It's worth noting that when generating the SSDL, the load is almost entirely spent on the SQL Server, while the client development machine is waiting idle for results to come back from the server.</span></span> <span data-ttu-id="cb97a-866">Dba 应该特别感谢这一改进。</span><span class="sxs-lookup"><span data-stu-id="cb97a-866">DBAs should particularly appreciate this improvement.</span></span> <span data-ttu-id="cb97a-867">另外，值得注意的是，在生成模型的过程中，只需立即生成模型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-867">It's also worth noting that essentially the entire cost of model generation takes place in View Generation now.</span></span>

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a><span data-ttu-id="cb97a-868">7.3 将大型模型与 Database First 和 Model First 分离</span><span class="sxs-lookup"><span data-stu-id="cb97a-868">7.3       Splitting Large Models with Database First and Model First</span></span>

<span data-ttu-id="cb97a-869">随着模型大小的增加，设计器图面变得混乱且难以使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-869">As model size increases, the designer surface becomes cluttered and difficult to use.</span></span> <span data-ttu-id="cb97a-870">我们通常会考虑使用超过300个实体的模型太大，无法有效使用设计器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-870">We typically consider a model with more than 300 entities to be too large to effectively use the designer.</span></span> <span data-ttu-id="cb97a-871">以下博客文章介绍了用于拆分大型模型的几个选项： \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-871">The following blog post describes several options for splitting large models: \<http://blogs.msdn.com/b/adonet/archive/2008/11/25/working-with-large-models-in-entity-framework-part-2.aspx>.</span></span>

<span data-ttu-id="cb97a-872">张贴内容是为实体框架的第一个版本而编写的，但这些步骤仍适用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-872">The post was written for the first version of Entity Framework, but the steps still apply.</span></span>

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a><span data-ttu-id="cb97a-873">7.4 实体数据源控件的性能注意事项</span><span class="sxs-lookup"><span data-stu-id="cb97a-873">7.4       Performance considerations with the Entity Data Source Control</span></span>

<span data-ttu-id="cb97a-874">在多线程性能和压力测试中，我们已了解到，使用 EntityDataSource 控件的 web 应用程序的性能大大降低了这种情况。</span><span class="sxs-lookup"><span data-stu-id="cb97a-874">We've seen cases in multi-threaded performance and stress tests where the performance of a web application using the EntityDataSource Control deteriorates significantly.</span></span> <span data-ttu-id="cb97a-875">根本原因是，EntityDataSource 对 Web 应用程序引用的程序集重复调用 LoadFromAssembly，以发现要用作实体的类型。</span><span class="sxs-lookup"><span data-stu-id="cb97a-875">The underlying cause is that the EntityDataSource repeatedly calls MetadataWorkspace.LoadFromAssembly on the assemblies referenced by the Web application to discover the types to be used as entities.</span></span>

<span data-ttu-id="cb97a-876">解决方法是将 EntityDataSource 的 ContextTypeName 设置为派生 ObjectContext 类的类型名称。</span><span class="sxs-lookup"><span data-stu-id="cb97a-876">The solution is to set the ContextTypeName of the EntityDataSource to the type name of your derived ObjectContext class.</span></span> <span data-ttu-id="cb97a-877">这会关闭用于扫描实体类型的所有引用程序集的机制。</span><span class="sxs-lookup"><span data-stu-id="cb97a-877">This turns off the mechanism that scans all referenced assemblies for entity types.</span></span>

<span data-ttu-id="cb97a-878">设置 ContextTypeName 字段还可防止在 .NET 4.0 中的 EntityDataSource 无法通过反射从程序集加载类型时引发 ReflectionTypeLoadException 的功能问题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-878">Setting the ContextTypeName field also prevents a functional problem where the EntityDataSource in .NET 4.0 throws a ReflectionTypeLoadException when it can't load a type from an assembly via reflection.</span></span> <span data-ttu-id="cb97a-879">此问题已在 .NET 4.5 中解决。</span><span class="sxs-lookup"><span data-stu-id="cb97a-879">This issue has been fixed in .NET 4.5.</span></span>

### <a name="75-poco-entities-and-change-tracking-proxies"></a><span data-ttu-id="cb97a-880">7.5 POCO 实体和更改跟踪代理</span><span class="sxs-lookup"><span data-stu-id="cb97a-880">7.5       POCO entities and change tracking proxies</span></span>

<span data-ttu-id="cb97a-881">实体框架使你能够将自定义数据类与数据模型一起使用，而无需对数据类本身进行任何修改。</span><span class="sxs-lookup"><span data-stu-id="cb97a-881">Entity Framework enables you to use custom data classes together with your data model without making any modifications to the data classes themselves.</span></span> <span data-ttu-id="cb97a-882">这意味着可以将“纯旧式”CLR 对象 (POCO)（例如，现有的域对象）与数据模型一起使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-882">This means that you can use "plain-old" CLR objects (POCO), such as existing domain objects, with your data model.</span></span> <span data-ttu-id="cb97a-883">这些 POCO 数据类（也称为持久性未知对象）映射到数据模型中定义的实体，支持与实体数据模型工具生成的实体类型相同的查询、插入、更新和删除行为。</span><span class="sxs-lookup"><span data-stu-id="cb97a-883">These POCO data classes (also known as persistence-ignorant objects), which are mapped to entities that are defined in a data model, support most of the same query, insert, update, and delete behaviors as entity types that are generated by the Entity Data Model tools.</span></span>

<span data-ttu-id="cb97a-884">实体框架还可以创建从 POCO 类型派生的代理类，当你想要在 POCO 实体上启用延迟加载和自动更改跟踪等功能时，可以使用这些类。</span><span class="sxs-lookup"><span data-stu-id="cb97a-884">Entity Framework can also create proxy classes derived from your POCO types, which are used when you want to enable features such as lazy loading and automatic change tracking on POCO entities.</span></span> <span data-ttu-id="cb97a-885">POCO 类必须满足某些要求，才能允许实体框架使用代理，如下面所述： [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-885">Your POCO classes must meet certain requirements to allow Entity Framework to use proxies, as described here: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx).</span></span>

<span data-ttu-id="cb97a-886">每次实体的属性值发生更改时，机会跟踪代理都会通知对象状态管理器，因此实体框架知道实体的实际状态。</span><span class="sxs-lookup"><span data-stu-id="cb97a-886">Chance tracking proxies will notify the object state manager each time any of the properties of your entities has its value changed, so Entity Framework knows the actual state of your entities all the time.</span></span> <span data-ttu-id="cb97a-887">为此，可将通知事件添加到属性的 setter 方法体，并使对象状态管理器处理此类事件。</span><span class="sxs-lookup"><span data-stu-id="cb97a-887">This is done by adding notification events to the body of the setter methods of your properties, and having the object state manager processing such events.</span></span> <span data-ttu-id="cb97a-888">请注意，创建代理实体通常比创建非代理 POCO 实体更昂贵，因为添加了实体框架创建的事件集。</span><span class="sxs-lookup"><span data-stu-id="cb97a-888">Note that creating a proxy entity will typically be more expensive than creating a non-proxy POCO entity due to the added set of events created by Entity Framework.</span></span>

<span data-ttu-id="cb97a-889">如果 POCO 实体没有更改跟踪代理，则可以通过将实体的内容与以前保存的状态的副本进行比较来找到更改。</span><span class="sxs-lookup"><span data-stu-id="cb97a-889">When a POCO entity does not have a change tracking proxy, changes are found by comparing the contents of your entities against a copy of a previous saved state.</span></span> <span data-ttu-id="cb97a-890">当你在上下文中具有多个实体时，或者当你的实体具有非常多的属性（即使在上次进行比较之后它们都没有发生更改）时，此深层比较将成为一个漫长的过程。</span><span class="sxs-lookup"><span data-stu-id="cb97a-890">This deep comparison will become a lengthy process when you have many entities in your context, or when your entities have a very large amount of properties, even if none of them changed since the last comparison took place.</span></span>

<span data-ttu-id="cb97a-891">摘要：创建更改跟踪代理时需支付性能，但当实体具有很多属性时，或在模型中有多个实体时，更改跟踪将有助于加速更改检测过程。</span><span class="sxs-lookup"><span data-stu-id="cb97a-891">In summary: you’ll pay a performance hit when creating the change tracking proxy, but change tracking will help you speed up the change detection process when your entities have many properties or when you have many entities in your model.</span></span> <span data-ttu-id="cb97a-892">对于包含少量属性的实体，这些实体的实体不会增长太多，具有更改跟踪代理的好处可能并不大。</span><span class="sxs-lookup"><span data-stu-id="cb97a-892">For entities with a small number of properties where the amount of entities doesn’t grow too much, having change tracking proxies may not be of much benefit.</span></span>

## <a name="8-loading-related-entities"></a><span data-ttu-id="cb97a-893">8加载相关实体</span><span class="sxs-lookup"><span data-stu-id="cb97a-893">8 Loading Related Entities</span></span>

### <a name="81-lazy-loading-vs-eager-loading"></a><span data-ttu-id="cb97a-894">8.1 延迟加载与预先加载</span><span class="sxs-lookup"><span data-stu-id="cb97a-894">8.1 Lazy Loading vs. Eager Loading</span></span>

<span data-ttu-id="cb97a-895">实体框架提供多种不同的方法来加载与目标实体相关的实体。</span><span class="sxs-lookup"><span data-stu-id="cb97a-895">Entity Framework offers several different ways to load the entities that are related to your target entity.</span></span> <span data-ttu-id="cb97a-896">例如，在查询产品时，会有不同的方法将相关订单加载到对象状态管理器中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-896">For example, when you query for Products, there are different ways that the related Orders will be loaded into the Object State Manager.</span></span> <span data-ttu-id="cb97a-897">从性能角度来看，加载相关实体时要考虑的最重要问题是是使用延迟加载还是预先加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-897">From a performance standpoint, the biggest question to consider when loading related entities will be whether to use Lazy Loading or Eager Loading.</span></span>

<span data-ttu-id="cb97a-898">使用预先加载时，会随目标实体集一起加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="cb97a-898">When using Eager Loading, the related entities are loaded along with your target entity set.</span></span> <span data-ttu-id="cb97a-899">在查询中使用 Include 语句来指示要引入的相关实体。</span><span class="sxs-lookup"><span data-stu-id="cb97a-899">You use an Include statement in your query to indicate which related entities you want to bring in.</span></span>

<span data-ttu-id="cb97a-900">当使用延迟加载时，初始查询只会引入目标实体集。</span><span class="sxs-lookup"><span data-stu-id="cb97a-900">When using Lazy Loading, your initial query only brings in the target entity set.</span></span> <span data-ttu-id="cb97a-901">但只要您访问导航属性，就会针对该存储区发出另一个查询来加载相关实体。</span><span class="sxs-lookup"><span data-stu-id="cb97a-901">But whenever you access a navigation property, another query is issued against the store to load the related entity.</span></span>

<span data-ttu-id="cb97a-902">加载实体后，该实体的任何进一步查询都将直接从对象状态管理器加载它，无论你使用的是延迟加载还是预先加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-902">Once an entity has been loaded, any further queries for the entity will load it directly from the Object State Manager, whether you are using lazy loading or eager loading.</span></span>

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a><span data-ttu-id="cb97a-903">8.2 如何在延迟加载和预先加载之间进行选择</span><span class="sxs-lookup"><span data-stu-id="cb97a-903">8.2 How to choose between Lazy Loading and Eager Loading</span></span>

<span data-ttu-id="cb97a-904">重要的是，您了解延迟加载与预先加载之间的差异，以便您可以为应用程序做出正确的选择。</span><span class="sxs-lookup"><span data-stu-id="cb97a-904">The important thing is that you understand the difference between Lazy Loading and Eager Loading so that you can make the correct choice for your application.</span></span> <span data-ttu-id="cb97a-905">这将帮助你评估针对数据库的多个请求与可能包含大型有效负载的单个请求之间的权衡。</span><span class="sxs-lookup"><span data-stu-id="cb97a-905">This will help you evaluate the tradeoff between multiple requests against the database versus a single request that may contain a large payload.</span></span> <span data-ttu-id="cb97a-906">在应用程序的某些部分使用预先加载和其他部分中的延迟加载可能是合适的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-906">It may be appropriate to use eager loading in some parts of your application and lazy loading in other parts.</span></span>

<span data-ttu-id="cb97a-907">例如，在此期间，假设你想要查询位于英国的客户及其订单计数。</span><span class="sxs-lookup"><span data-stu-id="cb97a-907">As an example of what's happening under the hood, suppose you want to query for the customers who live in the UK and their order count.</span></span>

<span data-ttu-id="cb97a-908">**使用预先加载**</span><span class="sxs-lookup"><span data-stu-id="cb97a-908">**Using Eager Loading**</span></span>

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

<span data-ttu-id="cb97a-909">**使用延迟加载**</span><span class="sxs-lookup"><span data-stu-id="cb97a-909">**Using Lazy Loading**</span></span>

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

<span data-ttu-id="cb97a-910">使用预先加载时，您将发出一个返回所有客户和所有订单的查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-910">When using eager loading, you'll issue a single query that returns all customers and all orders.</span></span> <span data-ttu-id="cb97a-911">存储命令如下所示：</span><span class="sxs-lookup"><span data-stu-id="cb97a-911">The store command looks like:</span></span>

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

<span data-ttu-id="cb97a-912">使用延迟加载时，将最初发出以下查询：</span><span class="sxs-lookup"><span data-stu-id="cb97a-912">When using lazy loading, you'll issue the following query initially:</span></span>

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

<span data-ttu-id="cb97a-913">每次访问客户的 "订单" 导航属性时，都会对该商店发出如下查询：</span><span class="sxs-lookup"><span data-stu-id="cb97a-913">And each time you access the Orders navigation property of a customer another query like the following is issued against the store:</span></span>

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

<span data-ttu-id="cb97a-914">有关详细信息，请参阅[加载相关对象](https://msdn.microsoft.com/library/bb896272.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-914">For more information, see the [Loading Related Objects](https://msdn.microsoft.com/library/bb896272.aspx).</span></span>

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a><span data-ttu-id="cb97a-915">8.2.1 延迟加载与预先加载备忘单</span><span class="sxs-lookup"><span data-stu-id="cb97a-915">8.2.1 Lazy Loading versus Eager Loading cheat sheet</span></span>

<span data-ttu-id="cb97a-916">这种情况并不完全适合选择预先加载和延迟加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-916">There’s no such thing as a one-size-fits-all to choosing eager loading versus lazy loading.</span></span> <span data-ttu-id="cb97a-917">首先尝试了解两个策略之间的差异，以便您可以做出明智的决策;此外，如果你的代码符合以下任一方案，请考虑：</span><span class="sxs-lookup"><span data-stu-id="cb97a-917">Try first to understand the differences between both strategies so you can do a well informed decision; also, consider if your code fits to any of the following scenarios:</span></span>

| <span data-ttu-id="cb97a-918">场景</span><span class="sxs-lookup"><span data-stu-id="cb97a-918">Scenario</span></span>                                                                    | <span data-ttu-id="cb97a-919">建议</span><span class="sxs-lookup"><span data-stu-id="cb97a-919">Our Suggestion</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="cb97a-920">是否需要从提取的实体访问多个导航属性？</span><span class="sxs-lookup"><span data-stu-id="cb97a-920">Do you need to access many navigation properties from the fetched entities?</span></span> | <span data-ttu-id="cb97a-921">**否**-这两个选项都可能会。</span><span class="sxs-lookup"><span data-stu-id="cb97a-921">**No** - Both options will probably do.</span></span> <span data-ttu-id="cb97a-922">但是，如果你的查询引入的负载并不太大，则使用预先加载可能会遇到性能优势，因为它需要较少的网络往返才能具体化你的对象。</span><span class="sxs-lookup"><span data-stu-id="cb97a-922">However, if the payload your query is bringing is not too big, you may experience performance benefits by using Eager loading as it’ll require less network round trips to materialize your objects.</span></span> <br/> <br/> <span data-ttu-id="cb97a-923">**是**-如果需要从实体访问多个导航属性，则可以通过在查询中使用多个 include 语句并预先加载来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-923">**Yes** -  If you need to access many navigation properties from the entities, you’d do that by using multiple include statements in your query with Eager loading.</span></span> <span data-ttu-id="cb97a-924">包含的实体越多，查询将返回的有效负载越大。</span><span class="sxs-lookup"><span data-stu-id="cb97a-924">The more entities you include, the bigger the payload your query will return.</span></span> <span data-ttu-id="cb97a-925">将三个或更多实体添加到查询中后，请考虑切换到延迟加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-925">Once you include three or more entities into your query, consider switching to Lazy loading.</span></span> |
| <span data-ttu-id="cb97a-926">您是否确切知道运行时需要哪些数据？</span><span class="sxs-lookup"><span data-stu-id="cb97a-926">Do you know exactly what data will be needed at run time?</span></span>                   | <span data-ttu-id="cb97a-927">**无**-延迟加载将更适合你。</span><span class="sxs-lookup"><span data-stu-id="cb97a-927">**No** - Lazy loading will be better for you.</span></span> <span data-ttu-id="cb97a-928">否则，可能会最终查询不需要的数据。</span><span class="sxs-lookup"><span data-stu-id="cb97a-928">Otherwise, you may end up querying for data that you will not need.</span></span> <br/> <br/> <span data-ttu-id="cb97a-929">**是**-很可能是最好的选择;它有助于更快地加载整个集。</span><span class="sxs-lookup"><span data-stu-id="cb97a-929">**Yes** - Eager loading is probably your best bet; it will help loading entire sets faster.</span></span> <span data-ttu-id="cb97a-930">如果查询需要提取大量数据，并且速度太慢，请改为尝试延迟加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-930">If your query requires fetching a very large amount of data, and this becomes too slow, then try Lazy loading instead.</span></span>                                                                                                                                                                                                                                                       |
| <span data-ttu-id="cb97a-931">你的代码是否从数据库执行？</span><span class="sxs-lookup"><span data-stu-id="cb97a-931">Is your code executing far from your database?</span></span> <span data-ttu-id="cb97a-932">（增加网络延迟）</span><span class="sxs-lookup"><span data-stu-id="cb97a-932">(increased network latency)</span></span>  | <span data-ttu-id="cb97a-933">**否**-当网络延迟不是问题时，使用延迟加载可能会简化你的代码。</span><span class="sxs-lookup"><span data-stu-id="cb97a-933">**No** - When the network latency is not an issue, using Lazy loading may simplify your code.</span></span> <span data-ttu-id="cb97a-934">请记住，应用程序的拓扑可能会发生更改，因此，不会对其进行已授予的数据库邻近性。</span><span class="sxs-lookup"><span data-stu-id="cb97a-934">Remember that the topology of your application may change, so don’t take database proximity for granted.</span></span> <br/> <br/> <span data-ttu-id="cb97a-935">**是**-当网络出现问题时，只有你可以决定更适合你的方案。</span><span class="sxs-lookup"><span data-stu-id="cb97a-935">**Yes** - When the network is a problem, only you can decide what fits better for your scenario.</span></span> <span data-ttu-id="cb97a-936">通常，预先加载将更好，因为它需要更少的往返行程。</span><span class="sxs-lookup"><span data-stu-id="cb97a-936">Typically Eager loading will be better because it requires fewer round trips.</span></span>                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a><span data-ttu-id="cb97a-937">多个包含8.2.2 的性能问题</span><span class="sxs-lookup"><span data-stu-id="cb97a-937">8.2.2       Performance concerns with multiple Includes</span></span>

<span data-ttu-id="cb97a-938">当我们听到涉及服务器响应时间问题的性能问题时，问题的根源是经常用多个 Include 语句进行查询。</span><span class="sxs-lookup"><span data-stu-id="cb97a-938">When we hear performance questions that involve server response time problems, the source of the issue is frequently queries with multiple Include statements.</span></span> <span data-ttu-id="cb97a-939">虽然在查询中包括相关实体的功能非常强大，但必须了解所涉及的内容。</span><span class="sxs-lookup"><span data-stu-id="cb97a-939">While including related entities in a query is powerful, it's important to understand what's happening under the covers.</span></span>

<span data-ttu-id="cb97a-940">如果查询中包含多个 Include 语句，则需要相对较长的时间来生成存储命令。</span><span class="sxs-lookup"><span data-stu-id="cb97a-940">It takes a relatively long time for a query with multiple Include statements in it to go through our internal plan compiler to produce the store command.</span></span> <span data-ttu-id="cb97a-941">大多数情况下，尝试优化所生成的查询将花费大量时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-941">The majority of this time is spent trying to optimize the resulting query.</span></span> <span data-ttu-id="cb97a-942">对于每个包含，生成的存储命令将包含外部联接或 Union，具体取决于你的映射。</span><span class="sxs-lookup"><span data-stu-id="cb97a-942">The generated store command will contain an Outer Join or Union for each Include, depending on your mapping.</span></span> <span data-ttu-id="cb97a-943">类似于这样的查询将在单个有效负载中引入数据库中的大型连接图形，这会 acerbate 任何带宽问题，尤其是当有效负载中存在大量冗余时（例如，当使用多个级别的 Include 来遍历一对多方向的关联）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-943">Queries like this will bring in large connected graphs from your database in a single payload, which will acerbate any bandwidth issues, especially when there is a lot of redundancy in the payload (for example, when multiple levels of Include are used to traverse associations in the one-to-many direction).</span></span>

<span data-ttu-id="cb97a-944">可以通过使用 ToTraceString 访问查询的基础 TSQL，并在 SQL Server Management Studio 中执行存储命令来查看负载大小，来检查查询返回的负载是否过大的情况。</span><span class="sxs-lookup"><span data-stu-id="cb97a-944">You can check for cases where your queries are returning excessively large payloads by accessing the underlying TSQL for the query by using ToTraceString and executing the store command in SQL Server Management Studio to see the payload size.</span></span> <span data-ttu-id="cb97a-945">在这种情况下，您可以尝试减少查询中包含语句的数量，以便只引入您需要的数据。</span><span class="sxs-lookup"><span data-stu-id="cb97a-945">In such cases you can try to reduce the number of Include statements in your query to just bring in the data you need.</span></span> <span data-ttu-id="cb97a-946">您也可以将查询分解成较小的子查询序列，例如：</span><span class="sxs-lookup"><span data-stu-id="cb97a-946">Or you may be able to break your query into a smaller sequence of subqueries, for example:</span></span>

<span data-ttu-id="cb97a-947">**在中断查询之前：**</span><span class="sxs-lookup"><span data-stu-id="cb97a-947">**Before breaking the query:**</span></span>

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

<span data-ttu-id="cb97a-948">**中断查询后：**</span><span class="sxs-lookup"><span data-stu-id="cb97a-948">**After breaking the query:**</span></span>

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

<span data-ttu-id="cb97a-949">这仅适用于跟踪的查询，因为我们正在使用上下文自动执行标识解析和关联修正功能的能力。</span><span class="sxs-lookup"><span data-stu-id="cb97a-949">This will work only on tracked queries, as we are making use of the ability the context has to perform identity resolution and association fixup automatically.</span></span>

<span data-ttu-id="cb97a-950">与延迟加载一样，权衡更适用于较小的负载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-950">As with lazy loading, the tradeoff will be more queries for smaller payloads.</span></span> <span data-ttu-id="cb97a-951">你还可以使用各个属性的投影从每个实体显式选择所需的数据，但在这种情况下将不会加载实体，并且不支持更新。</span><span class="sxs-lookup"><span data-stu-id="cb97a-951">You can also use projections of individual properties to explicitly select only the data you need from each entity, but you will not be loading entities in this case, and updates will not be supported.</span></span>

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a><span data-ttu-id="cb97a-952">用于获取属性延迟加载的8.2.3 解决方法</span><span class="sxs-lookup"><span data-stu-id="cb97a-952">8.2.3 Workaround to get lazy loading of properties</span></span>

<span data-ttu-id="cb97a-953">实体框架当前不支持标量或复杂属性的延迟加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-953">Entity Framework currently doesn’t support lazy loading of scalar or complex properties.</span></span> <span data-ttu-id="cb97a-954">但是，如果您有一个包含大型对象（如 BLOB）的表，则可以使用表拆分将大属性分隔到一个单独的实体中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-954">However, in cases where you have a table that includes a large object such as a BLOB, you can use table splitting to separate the large properties into a separate entity.</span></span> <span data-ttu-id="cb97a-955">例如，假设您有一个包含 varbinary photo 列的产品表。</span><span class="sxs-lookup"><span data-stu-id="cb97a-955">For example, suppose you have a Product table that includes a varbinary photo column.</span></span> <span data-ttu-id="cb97a-956">如果不经常需要在查询中访问此属性，则可以使用表拆分来仅引入通常需要的实体部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-956">If you don't frequently need to access this property in your queries, you can use table splitting to bring in only the parts of the entity that you normally need.</span></span> <span data-ttu-id="cb97a-957">表示产品照片的实体仅在明确需要时才会加载。</span><span class="sxs-lookup"><span data-stu-id="cb97a-957">The entity representing the product photo will only be loaded when you explicitly need it.</span></span>

<span data-ttu-id="cb97a-958">显示如何启用表拆分的一个好资源是 Gil Fink 的 "实体框架中的表拆分" 博客文章： \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-958">A good resource that shows how to enable table splitting is Gil Fink's "Table Splitting in Entity Framework" blog post: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>.</span></span>

## <a name="9-other-considerations"></a><span data-ttu-id="cb97a-959">9其他注意事项</span><span class="sxs-lookup"><span data-stu-id="cb97a-959">9 Other considerations</span></span>

### <a name="91-server-garbage-collection"></a><span data-ttu-id="cb97a-960">9.1 服务器垃圾回收</span><span class="sxs-lookup"><span data-stu-id="cb97a-960">9.1      Server Garbage Collection</span></span>

<span data-ttu-id="cb97a-961">当垃圾回收器未正确配置时，某些用户可能会遇到资源争用，这些争用会限制它们期望的并行度。</span><span class="sxs-lookup"><span data-stu-id="cb97a-961">Some users might experience resource contention that limits the parallelism they are expecting when the Garbage Collector is not properly configured.</span></span> <span data-ttu-id="cb97a-962">每当在多线程方案中或在任何类似于服务器端系统的应用程序中使用 EF 时，请确保启用服务器垃圾回收。</span><span class="sxs-lookup"><span data-stu-id="cb97a-962">Whenever EF is used in a multithreaded scenario, or in any application that resembles a server-side system, make sure to enable Server Garbage Collection.</span></span> <span data-ttu-id="cb97a-963">这是通过应用程序配置文件中的一个简单设置来完成的：</span><span class="sxs-lookup"><span data-stu-id="cb97a-963">This is done via a simple setting in your application config file:</span></span>

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

<span data-ttu-id="cb97a-964">这会减少线程争用，并增加吞吐量，使 CPU 饱和情况最多可达30%。</span><span class="sxs-lookup"><span data-stu-id="cb97a-964">This should decrease your thread contention and increase your throughput by up to 30% in CPU saturated scenarios.</span></span> <span data-ttu-id="cb97a-965">通常情况下，应始终使用经典垃圾回收（更好地优化 UI 和客户端方案）以及服务器垃圾回收来测试应用程序的行为。</span><span class="sxs-lookup"><span data-stu-id="cb97a-965">In general terms, you should always test how your application behaves using the classic Garbage Collection (which is better tuned for UI and client side scenarios) as well as the Server Garbage Collection.</span></span>

### <a name="92-autodetectchanges"></a><span data-ttu-id="cb97a-966">9.2 AutoDetectChanges</span><span class="sxs-lookup"><span data-stu-id="cb97a-966">9.2      AutoDetectChanges</span></span>

<span data-ttu-id="cb97a-967">如前文所述，当对象缓存具有多个实体时，实体框架可能会显示性能问题。</span><span class="sxs-lookup"><span data-stu-id="cb97a-967">As mentioned earlier, Entity Framework might show performance issues when the object cache has many entities.</span></span> <span data-ttu-id="cb97a-968">某些操作（例如添加、删除、查找、入口和 SaveChanges）触发对 DetectChanges 的调用，这些调用可能会根据对象缓存的大小而消耗大量 CPU。</span><span class="sxs-lookup"><span data-stu-id="cb97a-968">Certain operations, such as Add, Remove, Find, Entry and SaveChanges, trigger calls to DetectChanges which might consume a large amount of CPU based on how large the object cache has become.</span></span> <span data-ttu-id="cb97a-969">出现这种情况的原因是，对象缓存和对象状态管理器尝试在执行到上下文的每个操作上尽可能保持同步，以便在各种方案中保证生成的数据是正确的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-969">The reason for this is that the object cache and the object state manager try to stay as synchronized as possible on each operation performed to a context so that the produced data is guaranteed to be correct under a wide array of scenarios.</span></span>

<span data-ttu-id="cb97a-970">通常，在应用程序的整个生命周期中保留实体框架的自动更改检测是一种很好的做法。</span><span class="sxs-lookup"><span data-stu-id="cb97a-970">It is generally a good practice to leave Entity Framework’s automatic change detection enabled for the entire life of your application.</span></span> <span data-ttu-id="cb97a-971">如果你的方案受到高 CPU 使用率的负面影响，并且你的配置文件指示原因是调用了 DetectChanges，请考虑在代码的敏感部分暂时关闭 AutoDetectChanges：</span><span class="sxs-lookup"><span data-stu-id="cb97a-971">If your scenario is being negatively affected by high CPU usage and your profiles indicate that the culprit is the call to DetectChanges, consider temporarily turning off AutoDetectChanges in the sensitive portion of your code:</span></span>

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

<span data-ttu-id="cb97a-972">在关闭 AutoDetectChanges 之前，最好了解这可能会导致实体框架无法跟踪有关在实体上发生的更改的特定信息。</span><span class="sxs-lookup"><span data-stu-id="cb97a-972">Before turning off AutoDetectChanges, it’s good to understand that this might cause Entity Framework to lose its ability to track certain information about the changes that are taking place on the entities.</span></span> <span data-ttu-id="cb97a-973">如果处理不当，这可能会导致应用程序的数据不一致。</span><span class="sxs-lookup"><span data-stu-id="cb97a-973">If handled incorrectly, this might cause data inconsistency on your application.</span></span> <span data-ttu-id="cb97a-974">有关关闭 AutoDetectChanges 的详细信息，请参阅 \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-974">For more information on turning off AutoDetectChanges, read \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>.</span></span>

### <a name="93-context-per-request"></a><span data-ttu-id="cb97a-975">9.3 每个请求的上下文</span><span class="sxs-lookup"><span data-stu-id="cb97a-975">9.3      Context per request</span></span>

<span data-ttu-id="cb97a-976">实体框架的上下文旨在用作生存期较短的实例，以提供最佳性能体验。</span><span class="sxs-lookup"><span data-stu-id="cb97a-976">Entity Framework’s contexts are meant to be used as short-lived instances in order to provide the most optimal performance experience.</span></span> <span data-ttu-id="cb97a-977">上下文应为生存期较短且被丢弃，因此，在可能的情况下将其实现为非常轻量并 reutilize 元数据。</span><span class="sxs-lookup"><span data-stu-id="cb97a-977">Contexts are expected to be short lived and discarded, and as such have been implemented to be very lightweight and reutilize metadata whenever possible.</span></span> <span data-ttu-id="cb97a-978">在 web 方案中，请务必记住这一点，而不是将上下文用于单个请求的持续时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-978">In web scenarios it’s important to keep this in mind and not have a context for more than the duration of a single request.</span></span> <span data-ttu-id="cb97a-979">同样，在非 web 应用场景中，应根据你对实体框架中不同缓存级别的了解来丢弃上下文。</span><span class="sxs-lookup"><span data-stu-id="cb97a-979">Similarly, in non-web scenarios, context should be discarded based on your understanding of the different levels of caching in the Entity Framework.</span></span> <span data-ttu-id="cb97a-980">一般而言，应避免在应用程序的整个生命周期中使用上下文实例，以及每个线程和静态上下文的上下文。</span><span class="sxs-lookup"><span data-stu-id="cb97a-980">Generally speaking, one should avoid having a context instance throughout the life of the application, as well as contexts per thread and static contexts.</span></span>

### <a name="94-database-null-semantics"></a><span data-ttu-id="cb97a-981">9.4 数据库 null 语义</span><span class="sxs-lookup"><span data-stu-id="cb97a-981">9.4      Database null semantics</span></span>

<span data-ttu-id="cb97a-982">默认情况下实体框架将生成具有 C\# null 比较语义的 SQL 代码。</span><span class="sxs-lookup"><span data-stu-id="cb97a-982">Entity Framework by default will generate SQL code that has C\# null comparison semantics.</span></span> <span data-ttu-id="cb97a-983">请参考以下示例查询：</span><span class="sxs-lookup"><span data-stu-id="cb97a-983">Consider the following example query:</span></span>

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

<span data-ttu-id="cb97a-984">在此示例中，我们将多个可以为 null 的变量与实体上的可为 null 的属性进行比较，如供应商和单价。</span><span class="sxs-lookup"><span data-stu-id="cb97a-984">In this example, we’re comparing a number of nullable variables against nullable properties on the entity, such as SupplierID and UnitPrice.</span></span> <span data-ttu-id="cb97a-985">此查询生成的 SQL 将询问参数值与列值是否相同，或者参数和列值是否为 null。</span><span class="sxs-lookup"><span data-stu-id="cb97a-985">The generated SQL for this query will ask if the parameter value is the same as the column value, or if both the parameter and the column values are null.</span></span> <span data-ttu-id="cb97a-986">这将隐藏数据库服务器处理 null 值的方式，并将在不同的数据库供应商之间提供一致的 C\# null 体验。</span><span class="sxs-lookup"><span data-stu-id="cb97a-986">This will hide the way the database server handles nulls and will provide a consistent C\# null experience across different database vendors.</span></span> <span data-ttu-id="cb97a-987">另一方面，生成的代码有点复杂，如果查询的 where 语句中的比较量增长到了很大的数字，则可能无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="cb97a-987">On the other hand, the generated code is a bit convoluted and may not perform well when the amount of comparisons in the where statement of the query grows to a large number.</span></span>

<span data-ttu-id="cb97a-988">处理这种情况的一种方法是使用数据库 null 语义。</span><span class="sxs-lookup"><span data-stu-id="cb97a-988">One way to deal with this situation is by using database null semantics.</span></span> <span data-ttu-id="cb97a-989">请注意，这可能会对 C\# null 语义有不同的行为，因为现在实体框架将生成更简单的 SQL，它会公开数据库引擎处理空值的方式。</span><span class="sxs-lookup"><span data-stu-id="cb97a-989">Note that this might potentially behave differently to the C\# null semantics since now Entity Framework will generate simpler SQL that exposes the way the database engine handles null values.</span></span> <span data-ttu-id="cb97a-990">对于上下文配置，可以对每个上下文激活数据库 null 语义，并提供一个配置行：</span><span class="sxs-lookup"><span data-stu-id="cb97a-990">Database null semantics can be activated per-context with one single configuration line against the context configuration:</span></span>

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

<span data-ttu-id="cb97a-991">当使用数据库 null 语义时，小型到中等大小的查询不会显示明显的性能改进，但不同之处在于具有大量可能的 null 比较的查询会变得更加明显。</span><span class="sxs-lookup"><span data-stu-id="cb97a-991">Small to medium sized queries will not display a perceptible performance improvement when using database null semantics, but the difference will become more noticeable on queries with a large number of potential null comparisons.</span></span>

<span data-ttu-id="cb97a-992">在上面的示例查询中，在受控环境中运行的 microbenchmark 中，性能差异低于2%。</span><span class="sxs-lookup"><span data-stu-id="cb97a-992">In the example query above, the performance difference was less than 2% in a microbenchmark running in a controlled environment.</span></span>

### <a name="95-async"></a><span data-ttu-id="cb97a-993">9.5 异步</span><span class="sxs-lookup"><span data-stu-id="cb97a-993">9.5      Async</span></span>

<span data-ttu-id="cb97a-994">实体框架6在 .NET 4.5 或更高版本上运行时，引入了对异步操作的支持。</span><span class="sxs-lookup"><span data-stu-id="cb97a-994">Entity Framework 6 introduced support of async operations when running on .NET 4.5 or later.</span></span> <span data-ttu-id="cb97a-995">大多数情况下，具有 IO 相关争用的应用程序将从使用异步查询和保存操作中获益最多。</span><span class="sxs-lookup"><span data-stu-id="cb97a-995">For the most part, applications that have IO related contention will benefit the most from using asynchronous query and save operations.</span></span> <span data-ttu-id="cb97a-996">如果你的应用程序不会受到 IO 争用的影响，则在最佳情况下，使用 async 会以同步方式运行，并以同步调用的相同时间量返回结果，或者在最坏情况下，只是将执行推迟到异步任务并添加额外的 time 到方案完成。</span><span class="sxs-lookup"><span data-stu-id="cb97a-996">If your application does not suffer from IO contention, the use of async will, in the best cases, run synchronously and return the result in the same amount of time as a synchronous call, or in the worst case, simply defer execution to an asynchronous task and add extra time to the completion of your scenario.</span></span>

<span data-ttu-id="cb97a-997">有关异步编程如何工作以帮助你确定 async 是否会提高应用程序的性能的详细信息，请[http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-997">For information on how asynchronous programming work that will help you deciding if async will improve the performance of your application visit [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx).</span></span> <span data-ttu-id="cb97a-998">有关在实体框架上使用异步操作的详细信息，请参阅[Async Query And Save](~/ef6/fundamentals/async.md
)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-998">For more information on the use of async operations on Entity Framework, see [Async Query and Save](~/ef6/fundamentals/async.md
).</span></span>

### <a name="96-ngen"></a><span data-ttu-id="cb97a-999">9.6 NGEN</span><span class="sxs-lookup"><span data-stu-id="cb97a-999">9.6      NGEN</span></span>

<span data-ttu-id="cb97a-1000">实体框架6不在 .NET Framework 的默认安装中。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1000">Entity Framework 6 does not come in the default installation of .NET framework.</span></span> <span data-ttu-id="cb97a-1001">因此，默认情况下，实体框架程序集并不是 NGEN，这意味着所有实体框架代码都服从与任何其他 MSIL 程序集相同的 JIT'ing 成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1001">As such, the Entity Framework assemblies are not NGEN’d by default which means that all the Entity Framework code is subject to the same JIT’ing costs as any other MSIL assembly.</span></span> <span data-ttu-id="cb97a-1002">在生产环境中开发应用程序和冷启动应用程序时，这可能会降低 F5 体验。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1002">This might degrade the F5 experience while developing and also the cold startup of your application in the production environments.</span></span> <span data-ttu-id="cb97a-1003">为了降低 JIT'ing 的 CPU 和内存成本，建议根据需要对实体框架映像进行 NGEN。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1003">In order to reduce the CPU and memory costs of JIT’ing it is advisable to NGEN the Entity Framework images as appropriate.</span></span> <span data-ttu-id="cb97a-1004">有关如何改善实体框架6与 NGEN 的启动性能的详细信息，请参阅[通过 Ngen 改善启动性能](~/ef6/fundamentals/performance/ngen.md)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1004">For more information on how to improve the startup performance of Entity Framework 6 with NGEN, see [Improving Startup Performance with NGen](~/ef6/fundamentals/performance/ngen.md).</span></span>

### <a name="97-code-first-versus-edmx"></a><span data-ttu-id="cb97a-1005">9.7 Code First 与 EDMX</span><span class="sxs-lookup"><span data-stu-id="cb97a-1005">9.7      Code First versus EDMX</span></span>

<span data-ttu-id="cb97a-1006">通过内存中的概念模型（对象）、存储架构（数据库）和映射之间的映射，实体框架面向对象的编程与关系数据库之间的阻抗不匹配问题的原因。双向.</span><span class="sxs-lookup"><span data-stu-id="cb97a-1006">Entity Framework reasons about the impedance mismatch problem between object oriented programming and relational databases by having an in-memory representation of the conceptual model (the objects), the storage schema (the database) and a mapping between the two.</span></span> <span data-ttu-id="cb97a-1007">对于 short，此元数据称为实体数据模型或 EDM。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1007">This metadata is called an Entity Data Model, or EDM for short.</span></span> <span data-ttu-id="cb97a-1008">在此 EDM 中，实体框架将派生视图，以将数据从内存中的对象往返到数据库并返回数据。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1008">From this EDM, Entity Framework will derive the views to roundtrip data from the objects in memory to the database and back.</span></span>

<span data-ttu-id="cb97a-1009">如果将实体框架用于正式指定了概念模型、存储架构和映射的 EDMX 文件，则模型加载阶段仅需验证 EDM 是否正确（例如，确保没有缺少映射），然后生成视图，然后验证视图，并使此元数据可供使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1009">When Entity Framework is used with an EDMX file that formally specifies the conceptual model, the storage schema, and the mapping, then the model loading stage only has to validate that the EDM is correct (for example, make sure that no mappings are missing), then generate the views, then validate the views and have this metadata ready for use.</span></span> <span data-ttu-id="cb97a-1010">只有这样才能执行查询，或者将新数据保存到数据存储区。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1010">Only then can a query be executed or new data be saved to the data store.</span></span>

<span data-ttu-id="cb97a-1011">Code First 方法就是一种复杂的实体数据模型生成器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1011">The Code First approach is, at its heart, a sophisticated Entity Data Model generator.</span></span> <span data-ttu-id="cb97a-1012">实体框架必须从提供的代码生成 EDM;它通过分析模型中涉及的类、应用约定并通过流畅的 API 配置模型来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1012">The Entity Framework has to produce an EDM from the provided code; it does so by analyzing the classes involved in the model, applying conventions and configuring the model via the Fluent API.</span></span> <span data-ttu-id="cb97a-1013">生成 EDM 后，实体框架本质上的行为与在项目中存在 EDMX 文件的方式相同。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1013">After the EDM is built, the Entity Framework essentially behaves the same way as it would had an EDMX file been present in the project.</span></span> <span data-ttu-id="cb97a-1014">因此，从 Code First 生成模型会增加额外的复杂性，与具有 EDMX 相比，实体框架的启动时间更慢。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1014">Thus, building the model from Code First adds extra complexity that translates into a slower startup time for the Entity Framework when compared to having an EDMX.</span></span> <span data-ttu-id="cb97a-1015">成本完全取决于正在生成的模型的大小和复杂程度。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1015">The cost is completely dependent on the size and complexity of the model that’s being built.</span></span>

<span data-ttu-id="cb97a-1016">选择使用 EDMX 与 Code First 时，必须知道 Code First 引入的灵活性会增加第一次生成模型时的成本。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1016">When choosing to use EDMX versus Code First, it’s important to know that the flexibility introduced by Code First increases the cost of building the model for the first time.</span></span> <span data-ttu-id="cb97a-1017">如果你的应用程序可以承受此首次加载的成本，则通常 Code First 将是首选方法。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1017">If your application can withstand the cost of this first-time load then typically Code First will be the preferred way to go.</span></span>

## <a name="10-investigating-performance"></a><span data-ttu-id="cb97a-1018">10调查性能</span><span class="sxs-lookup"><span data-stu-id="cb97a-1018">10 Investigating Performance</span></span>

### <a name="101-using-the-visual-studio-profiler"></a><span data-ttu-id="cb97a-1019">10.1 使用 Visual Studio 探查器</span><span class="sxs-lookup"><span data-stu-id="cb97a-1019">10.1 Using the Visual Studio Profiler</span></span>

<span data-ttu-id="cb97a-1020">如果实体框架存在性能问题，则可以使用类似于 Visual Studio 中内置的探查器来查看应用程序所花费的时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1020">If you are having performance issues with the Entity Framework, you can use a profiler like the one built into Visual Studio to see where your application is spending its time.</span></span> <span data-ttu-id="cb97a-1021">这是一种工具，用于在 "浏览 ADO.NET 实体框架-第1部分" 博客文章（\<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>)，其中显示实体框架在冷和温查询过程中花费的时间。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1021">This is the tool we used to generate the pie charts in the “Exploring the Performance of the ADO.NET Entity Framework - Part 1” blog post ( \<http://blogs.msdn.com/b/adonet/archive/2008/02/04/exploring-the-performance-of-the-ado-net-entity-framework-part-1.aspx>) that show where Entity Framework spends its time during cold and warm queries.</span></span>

<span data-ttu-id="cb97a-1022">"使用 Visual Studio 2010 探查器分析实体框架" 博客文章由数据和建模客户咨询团队介绍了如何使用探查器来调查性能问题的实际示例。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1022">The "Profiling Entity Framework using the Visual Studio 2010 Profiler" blog post written by the Data and Modeling Customer Advisory Team shows a real-world example of how they used the profiler to investigate a performance problem.</span></span><span data-ttu-id="cb97a-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1023">  \<http://blogs.msdn.com/b/dmcat/archive/2010/04/30/profiling-entity-framework-using-the-visual-studio-2010-profiler.aspx>.</span></span> <span data-ttu-id="cb97a-1024">此帖子是为 windows 应用程序编写的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1024">This post was written for a windows application.</span></span> <span data-ttu-id="cb97a-1025">如果需要对 web 应用程序进行分析，Windows 性能记录器（"）和 Windows 性能分析器（WPA）工具的工作方式可能比 Visual Studio 中的更好。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1025">If you need to profile a web application the Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA) tools may work better than working from Visual Studio.</span></span> <span data-ttu-id="cb97a-1026">"和 WPA 是 windows 评估和部署工具包（ [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)）随附的 Windows 性能工具包的一部分。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1026">WPR and WPA are part of the Windows Performance Toolkit which is included with the Windows Assessment and Deployment Kit ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)).</span></span>

### <a name="102-applicationdatabase-profiling"></a><span data-ttu-id="cb97a-1027">10.2 应用程序/数据库分析</span><span class="sxs-lookup"><span data-stu-id="cb97a-1027">10.2 Application/Database profiling</span></span>

<span data-ttu-id="cb97a-1028">Visual Studio 中内置的探查器会告诉你应用程序花费时间的位置。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1028">Tools like the profiler built into Visual Studio tell you where your application is spending time.</span></span><span data-ttu-id="cb97a-1029">  可以使用另一种类型的探查器，根据需要对正在生产或预生产环境中的运行中的应用程序执行动态分析，并查找数据库访问的常见缺陷和反模式。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1029">  Another type of profiler is available that performs dynamic analysis of your running application, either in production or pre-production depending on needs, and looks for common pitfalls and anti-patterns of database access.</span></span>

<span data-ttu-id="cb97a-1030">两个商用探查器是实体框架探查器（\<http://efprof.com>) 和 ORMProfiler （\<http://ormprofiler.com>)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1030">Two commercially available profilers are the Entity Framework Profiler ( \<http://efprof.com>) and ORMProfiler ( \<http://ormprofiler.com>).</span></span>

<span data-ttu-id="cb97a-1031">如果你的应用程序是使用 Code First 的 MVC 应用程序，则可以使用 Stackexchange.redis 的 MiniProfiler。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1031">If your application is an MVC application using Code First, you can use StackExchange's MiniProfiler.</span></span> <span data-ttu-id="cb97a-1032">Scott Hanselman 在他的博客中介绍了此工具： \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1032">Scott Hanselman describes this tool in his blog at: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>.</span></span>

<span data-ttu-id="cb97a-1033">有关分析应用程序的数据库活动的详细信息，请参阅 Julie Lerman 的 MSDN 杂志文章，其中标题[实体框架中的 "分析数据库" 活动](https://msdn.microsoft.com/magazine/gg490349.aspx)。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1033">For more information on profiling your application's database activity, see Julie Lerman's MSDN Magazine article titled [Profiling Database Activity in the Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).</span></span>

### <a name="103-database-logger"></a><span data-ttu-id="cb97a-1034">10.3 数据库记录器</span><span class="sxs-lookup"><span data-stu-id="cb97a-1034">10.3 Database logger</span></span>

<span data-ttu-id="cb97a-1035">如果使用实体框架6，还应考虑使用内置日志记录功能。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1035">If you are using Entity Framework 6 also consider using the built-in logging functionality.</span></span> <span data-ttu-id="cb97a-1036">可以通过简单的单行配置指示上下文的数据库属性记录其活动：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1036">The Database property of the context can be instructed to log its activity via a simple one-line configuration:</span></span>

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

<span data-ttu-id="cb97a-1037">在此示例中，数据库活动将记录到控制台中，但 Log 属性可以配置为调用&lt;字符串&gt; 委托的任何操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1037">In this example the database activity will be logged to the console, but the Log property can be configured to call any Action&lt;string&gt; delegate.</span></span>

<span data-ttu-id="cb97a-1038">如果要在不重新编译的情况下启用数据库日志记录，并且使用实体框架6.1 或更高版本，则可以通过在应用程序的 web.config 或 app.config 文件中添加侦听器来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1038">If you want to enable database logging without recompiling, and you are using Entity Framework 6.1 or later, you can do so by adding an interceptor in the web.config or app.config file of your application.</span></span>

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

<span data-ttu-id="cb97a-1039">有关如何添加不进行重新编译的日志记录的详细信息，请参阅 \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1039">For more information on how to add logging without recompiling go to \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>.</span></span>

## <a name="11-appendix"></a><span data-ttu-id="cb97a-1040">11附录</span><span class="sxs-lookup"><span data-stu-id="cb97a-1040">11 Appendix</span></span>

### <a name="111-a-test-environment"></a><span data-ttu-id="cb97a-1041">11.1 A. 测试环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1041">11.1 A. Test Environment</span></span>

<span data-ttu-id="cb97a-1042">此环境将两台计算机的安装程序与客户端应用程序单独的计算机上的数据库一起使用。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1042">This environment uses a 2-machine setup with the database on a separate machine from the client application.</span></span> <span data-ttu-id="cb97a-1043">计算机位于同一机架中，因此网络延迟相对较低，但比单计算机环境更为现实。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1043">Machines are in the same rack, so network latency is relatively low, but more realistic than a single-machine environment.</span></span>

#### <a name="1111-app-server"></a><span data-ttu-id="cb97a-1044">11.1.1 应用服务器</span><span class="sxs-lookup"><span data-stu-id="cb97a-1044">11.1.1       App Server</span></span>

##### <a name="11111-software-environment"></a><span data-ttu-id="cb97a-1045">11.1.1.1 软件环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1045">11.1.1.1      Software Environment</span></span>

-   <span data-ttu-id="cb97a-1046">实体框架4软件环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1046">Entity Framework 4 Software Environment</span></span>
    -   <span data-ttu-id="cb97a-1047">OS 名称： Windows Server 2008 R2 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1047">OS Name: Windows Server 2008 R2 Enterprise SP1.</span></span>
    -   <span data-ttu-id="cb97a-1048">Visual Studio 2010 –旗舰版。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1048">Visual Studio 2010 – Ultimate.</span></span>
    -   <span data-ttu-id="cb97a-1049">Visual Studio 2010 SP1 （仅适用于某些比较）。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1049">Visual Studio 2010 SP1 (only for some comparisons).</span></span>
-   <span data-ttu-id="cb97a-1050">实体框架5和6软件环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1050">Entity Framework 5 and 6 Software Environment</span></span>
    -   <span data-ttu-id="cb97a-1051">OS 名称： Windows 8.1 Enterprise</span><span class="sxs-lookup"><span data-stu-id="cb97a-1051">OS Name: Windows 8.1 Enterprise</span></span>
    -   <span data-ttu-id="cb97a-1052">Visual Studio 2013 –旗舰版。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1052">Visual Studio 2013 – Ultimate.</span></span>

##### <a name="11112-hardware-environment"></a><span data-ttu-id="cb97a-1053">11.1.1.2 硬件环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1053">11.1.1.2      Hardware Environment</span></span>

-   <span data-ttu-id="cb97a-1054">双处理器： Intel （R） L5520 W3530 @ 2.27 GHz，2261 Mhz8 GHz，4核，84逻辑处理器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1054">Dual Processor:     Intel(R) Xeon(R) CPU L5520 W3530 @ 2.27GHz, 2261 Mhz8 GHz, 4 Core(s), 84 Logical Processor(s).</span></span>
-   <span data-ttu-id="cb97a-1055">2412 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1055">2412 GB RamRAM.</span></span>
-   <span data-ttu-id="cb97a-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s 驱动器拆分为4个分区。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1056">136 GB SCSI250GB SATA 7200 rpm 3GB/s drive split into 4 partitions.</span></span>

#### <a name="1112-db-server"></a><span data-ttu-id="cb97a-1057">11.1.2 DB 服务器</span><span class="sxs-lookup"><span data-stu-id="cb97a-1057">11.1.2       DB server</span></span>

##### <a name="11121-software-environment"></a><span data-ttu-id="cb97a-1058">11.1.2.1 软件环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1058">11.1.2.1      Software Environment</span></span>

-   <span data-ttu-id="cb97a-1059">OS 名称： Windows Server 2008 R 28.1 Enterprise SP1。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1059">OS Name: Windows Server 2008 R28.1 Enterprise SP1.</span></span>
-   <span data-ttu-id="cb97a-1060">SQL Server 2008 R22012.</span><span class="sxs-lookup"><span data-stu-id="cb97a-1060">SQL Server 2008 R22012.</span></span>

##### <a name="11122-hardware-environment"></a><span data-ttu-id="cb97a-1061">11.1.2.2 硬件环境</span><span class="sxs-lookup"><span data-stu-id="cb97a-1061">11.1.2.2      Hardware Environment</span></span>

-   <span data-ttu-id="cb97a-1062">单处理器： Intel （R）至强（R） CPU L5520 @ 2.27 GHz，2261 MhzES-1620 0 @ 3.60 GHz，4核，8个逻辑处理器。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1062">Single Processor: Intel(R) Xeon(R) CPU L5520  @ 2.27GHz, 2261 MhzES-1620 0 @ 3.60GHz, 4 Core(s), 8 Logical Processor(s).</span></span>
-   <span data-ttu-id="cb97a-1063">824 GB RamRAM。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1063">824 GB RamRAM.</span></span>
-   <span data-ttu-id="cb97a-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s 驱动器拆分为4个分区。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1064">465 GB ATA500GB SATA 7200 rpm 6GB/s drive split into 4 partitions.</span></span>

### <a name="112-b-query-performance-comparison-tests"></a><span data-ttu-id="cb97a-1065">11.2 b. 查询性能比较测试</span><span class="sxs-lookup"><span data-stu-id="cb97a-1065">11.2      B. Query performance comparison tests</span></span>

<span data-ttu-id="cb97a-1066">Northwind 模型用于执行这些测试。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1066">The Northwind model was used to execute these tests.</span></span> <span data-ttu-id="cb97a-1067">它是使用实体框架设计器从数据库生成的。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1067">It was generated from the database using the Entity Framework designer.</span></span> <span data-ttu-id="cb97a-1068">然后，使用以下代码来比较查询执行选项的性能：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1068">Then, the following code was used to compare the performance of the query execution options:</span></span>

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

### <a name="113-c-navision-model"></a><span data-ttu-id="cb97a-1069">11.3 Navision 模型</span><span class="sxs-lookup"><span data-stu-id="cb97a-1069">11.3 C. Navision Model</span></span>

<span data-ttu-id="cb97a-1070">Navision 数据库是用于演示 Microsoft Dynamics –导航的大型数据库。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1070">The Navision database is a large database used to demo Microsoft Dynamics – NAV.</span></span> <span data-ttu-id="cb97a-1071">生成的概念模型包含1005个实体集和4227个关联集。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1071">The generated conceptual model contains 1005 entity sets and 4227 association sets.</span></span> <span data-ttu-id="cb97a-1072">测试中使用的模型为 "平面" –未向其添加继承。</span><span class="sxs-lookup"><span data-stu-id="cb97a-1072">The model used in the test is “flat” – no inheritance has been added to it.</span></span>

#### <a name="1131-queries-used-for-navision-tests"></a><span data-ttu-id="cb97a-1073">用于 Navision 测试的11.3.1 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-1073">11.3.1 Queries used for Navision tests</span></span>

<span data-ttu-id="cb97a-1074">与 Navision 模型一起使用的查询列表包含3类实体 SQL 查询：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1074">The queries list used with the Navision model contains 3 categories of Entity SQL queries:</span></span>

##### <a name="11311-lookup"></a><span data-ttu-id="cb97a-1075">11.3.1.1 查找</span><span class="sxs-lookup"><span data-stu-id="cb97a-1075">11.3.1.1 Lookup</span></span>

<span data-ttu-id="cb97a-1076">不带聚合的简单查找查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-1076">A simple lookup query with no aggregations</span></span>

-   <span data-ttu-id="cb97a-1077">计数：16232</span><span class="sxs-lookup"><span data-stu-id="cb97a-1077">Count: 16232</span></span>
-   <span data-ttu-id="cb97a-1078">示例：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1078">Example:</span></span>

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a><span data-ttu-id="cb97a-1079">11.3.1.2 SingleAggregating</span><span class="sxs-lookup"><span data-stu-id="cb97a-1079">11.3.1.2 SingleAggregating</span></span>

<span data-ttu-id="cb97a-1080">具有多个聚合但没有小计（单个查询）的普通 BI 查询</span><span class="sxs-lookup"><span data-stu-id="cb97a-1080">A normal BI query with multiple aggregations, but no subtotals (single query)</span></span>

-   <span data-ttu-id="cb97a-1081">计数：2313</span><span class="sxs-lookup"><span data-stu-id="cb97a-1081">Count: 2313</span></span>
-   <span data-ttu-id="cb97a-1082">示例：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1082">Example:</span></span>

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

<span data-ttu-id="cb97a-1083">其中 MDF\_SessionLogin\_Time\_Max （）在模型中定义为：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1083">Where MDF\_SessionLogin\_Time\_Max() is defined in the model as:</span></span>

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a><span data-ttu-id="cb97a-1084">11.3.1.3 AggregatingSubtotals</span><span class="sxs-lookup"><span data-stu-id="cb97a-1084">11.3.1.3 AggregatingSubtotals</span></span>

<span data-ttu-id="cb97a-1085">具有聚合和小计的 BI 查询（通过 union all）</span><span class="sxs-lookup"><span data-stu-id="cb97a-1085">A BI query with aggregations and subtotals (via union all)</span></span>

-   <span data-ttu-id="cb97a-1086">计数：178</span><span class="sxs-lookup"><span data-stu-id="cb97a-1086">Count: 178</span></span>
-   <span data-ttu-id="cb97a-1087">示例：</span><span class="sxs-lookup"><span data-stu-id="cb97a-1087">Example:</span></span>

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

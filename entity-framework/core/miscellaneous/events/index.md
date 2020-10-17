---
title: 日志记录和侦听的概述 - EF Core
description: 概要介绍了 EF Core 的日志记录、事件、侦听器和诊断
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066493"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="f03ef-103">日志记录和侦听的概述</span><span class="sxs-lookup"><span data-stu-id="f03ef-103">Overview of logging and interception</span></span>

<span data-ttu-id="f03ef-104">Entity Framework Core (EF Core) 包含一些用于生成日志、响应事件和获取诊断结果的机制。</span><span class="sxs-lookup"><span data-stu-id="f03ef-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="f03ef-105">其中每种机制都针对不同情况进行了定制，有必要针对手头上的任务选择最佳机制，即使是多个机制都适用也是如此。</span><span class="sxs-lookup"><span data-stu-id="f03ef-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="f03ef-106">例如，数据库侦听器可用于记录 SQL，但由某个日志记录特定的机制来处理效果更好。</span><span class="sxs-lookup"><span data-stu-id="f03ef-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="f03ef-107">本页面概要介绍了上述每种机制，描述了每种机制的使用场景。</span><span class="sxs-lookup"><span data-stu-id="f03ef-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="f03ef-108">简单的日志记录</span><span class="sxs-lookup"><span data-stu-id="f03ef-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="f03ef-109">EF Core 5.0 中添加了此功能。</span><span class="sxs-lookup"><span data-stu-id="f03ef-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="f03ef-110">可在以下情况下使用 [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) 从任意类型的应用程序访问 EF Core 日志：</span><span class="sxs-lookup"><span data-stu-id="f03ef-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="f03ef-111">[配置 DbContext 实例](xref:core/miscellaneous/configuring-dbcontext)时。</span><span class="sxs-lookup"><span data-stu-id="f03ef-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="f03ef-112">此配置通常通过替代 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 来完成。</span><span class="sxs-lookup"><span data-stu-id="f03ef-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="f03ef-113">例如：</span><span class="sxs-lookup"><span data-stu-id="f03ef-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="f03ef-114">此概念与 EF6 中的 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> 相类似。</span><span class="sxs-lookup"><span data-stu-id="f03ef-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="f03ef-115">有关详细信息，请参阅[简单日志记录](xref:core/miscellaneous/events/simple-logging)。</span><span class="sxs-lookup"><span data-stu-id="f03ef-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>

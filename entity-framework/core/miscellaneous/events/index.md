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
# <a name="overview-of-logging-and-interception"></a>日志记录和侦听的概述

Entity Framework Core (EF Core) 包含一些用于生成日志、响应事件和获取诊断结果的机制。 其中每种机制都针对不同情况进行了定制，有必要针对手头上的任务选择最佳机制，即使是多个机制都适用也是如此。 例如，数据库侦听器可用于记录 SQL，但由某个日志记录特定的机制来处理效果更好。 本页面概要介绍了上述每种机制，描述了每种机制的使用场景。

## <a name="simple-logging"></a>简单的日志记录

> [!NOTE]
> EF Core 5.0 中添加了此功能。

可在以下情况下使用 [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) 从任意类型的应用程序访问 EF Core 日志： <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> [配置 DbContext 实例](xref:core/miscellaneous/configuring-dbcontext)时。 此配置通常通过替代 <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> 来完成。 例如：

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

此概念与 EF6 中的 <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> 相类似。

有关详细信息，请参阅[简单日志记录](xref:core/miscellaneous/events/simple-logging)。

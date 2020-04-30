---
title: 编写数据库提供程序-EF Core
author: anmiller
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
uid: core/providers/writing-a-provider
ms.openlocfilehash: 662c7e386bbdf0ff1e4e5051349d6a5c56a3df7b
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538462"
---
# <a name="writing-a-database-provider"></a>编写数据库提供程序

有关编写 Entity Framework Core 数据库提供程序的信息，请参阅，希望通过[Arthur Vickers](https://github.com/ajcvickers)[编写 EF Core 提供程序](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)。

> [!NOTE]
> 自 EF Core 1.1 起，这些文章尚未更新，因此该时间以来发生了重大更改。  
[问题 681](https://github.com/dotnet/EntityFramework.Docs/issues/681)正在跟踪对此文档的更新。

EF Core 基本代码是开放源代码，并且包含多个可用作参考的数据库提供程序。 可以在<https://github.com/aspnet/EntityFrameworkCore>中找到源代码。 查看常用的第三方提供程序（例如[Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL)、 [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)和[SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact)）的代码可能也很有帮助。 特别是，设置这些项目是为了从和运行我们在 NuGet 上发布的功能测试。 强烈建议使用这种设置。

## <a name="keeping-up-to-date-with-provider-changes"></a>提供提供程序更改的最新状态

从2.1 版本开始，开始使用后，我们创建了一个可能需要对提供程序代码进行相应更改的[更改日志](provider-log.md)。 这旨在帮助更新现有提供程序以使用 EF Core 的新版本。

在2.1 之前，我们使用了[`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware)和[`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi)标签作为 GitHub 问题，并提供了类似用途的拉取请求。 我们将 continiue 将这些标签用于问题，指出给定版本中的哪些工作项可能还需要在提供程序中完成工作。 `providers-beware`标签通常表示工作项的实现可能会中断提供程序，而`providers-fyi`标签通常意味着提供程序将不会中断，但可能仍需要更改代码，例如，启用新功能。

## <a name="suggested-naming-of-third-party-providers"></a>建议的第三方提供程序命名

建议为 NuGet 包使用以下命名。 这与 EF Core 团队传递的包的名称一致。

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

例如：

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`

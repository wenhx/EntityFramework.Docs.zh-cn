---
title: 设计时服务-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: ff0243a588d5e957aed89fcf1ce7462b5b9a747a
ms.sourcegitcommit: 2355447d89496a8ca6bcbfc0a68a14a0bf7f0327
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72811949"
---
# <a name="design-time-services"></a>设计时服务

这些工具使用的某些服务仅在设计时使用。 这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。 若要替代其中的某个服务（例如，生成迁移文件的服务），请将 `IDesignTimeServices` 的实现添加到启动项目。

``` csharp
class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
        => services.AddSingleton<IMigrationsCodeGenerator, MyMigrationsCodeGenerator>()
}
```

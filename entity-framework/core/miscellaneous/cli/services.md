---
title: 设计时服务-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 57294ab41e7c251b1dafae9d573aa98676c5d939
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655823"
---
# <a name="design-time-services"></a><span data-ttu-id="ee865-102">设计时服务</span><span class="sxs-lookup"><span data-stu-id="ee865-102">Design-time services</span></span>

<span data-ttu-id="ee865-103">这些工具使用的某些服务仅在设计时使用。</span><span class="sxs-lookup"><span data-stu-id="ee865-103">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="ee865-104">这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。</span><span class="sxs-lookup"><span data-stu-id="ee865-104">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="ee865-105">若要替代其中的某个服务（例如，生成迁移文件的服务），请将 `IDesignTimeServices` 的实现添加到启动项目。</span><span class="sxs-lookup"><span data-stu-id="ee865-105">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]

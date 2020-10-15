---
title: 设计时服务-EF Core
description: Entity Framework Core 设计时服务的信息
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061939"
---
# <a name="design-time-services"></a><span data-ttu-id="eea76-103">设计时服务</span><span class="sxs-lookup"><span data-stu-id="eea76-103">Design-time services</span></span>

<span data-ttu-id="eea76-104">这些工具使用的某些服务仅在设计时使用。</span><span class="sxs-lookup"><span data-stu-id="eea76-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="eea76-105">这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。</span><span class="sxs-lookup"><span data-stu-id="eea76-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="eea76-106">若要替代其中某个服务 (例如，服务) 生成迁移文件，请将的实现添加 `IDesignTimeServices` 到启动项目。</span><span class="sxs-lookup"><span data-stu-id="eea76-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]

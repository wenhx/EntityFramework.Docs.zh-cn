---
title: 设计时服务-EF Core
description: Entity Framework Core 设计时服务的信息
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617847"
---
# <a name="design-time-services"></a>设计时服务

这些工具使用的某些服务仅在设计时使用。 这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。 若要替代其中某个服务 (例如，服务) 生成迁移文件，请将的实现添加 `IDesignTimeServices` 到启动项目。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]

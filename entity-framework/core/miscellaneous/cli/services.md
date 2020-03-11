---
title: 设计时服务-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 57294ab41e7c251b1dafae9d573aa98676c5d939
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414205"
---
# <a name="design-time-services"></a>设计时服务

这些工具使用的某些服务仅在设计时使用。 这些服务独立于 EF Core 的运行时服务进行管理，以防止这些服务与你的应用程序一起部署。 若要替代其中的某个服务（例如，生成迁移文件的服务），请将 `IDesignTimeServices` 的实现添加到启动项目。

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]

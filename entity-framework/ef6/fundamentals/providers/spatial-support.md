---
title: 提供程序对空间类型的支持-EF6
description: 提供程序支持实体框架6中的空间类型
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: e2a543bc309001b14fb770d747a0dea22bcc91ca
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062901"
---
# <a name="provider-support-for-spatial-types"></a>提供程序对空间类型的支持
实体框架支持通过 DbGeography 或 DbGeometry 类处理空间数据。 这些类依赖于由实体框架提供程序提供的数据库特定功能。 并非所有提供程序都支持空间数据，其中可能有其他先决条件，如安装空间类型程序集。 下面提供了有关为空间类型提供程序支持的详细信息。  

有关如何在应用程序中使用空间类型的其他信息，请参阅两个演练，一个用于 Code First，另一个用于 Database First 或 Model First：  

- [Code First 中的空间数据类型](xref:ef6/modeling/code-first/data-types/spatial)  
- [EF 设计器中的空间数据类型](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a>支持空间类型的 EF 版本  

EF5 中引入了对空间类型的支持。 但是，仅当应用程序在 .NET 4.5 上面向和运行时，才支持 EF5 空间类型。  

对于面向 .NET 4 和 .NET 4.5 的应用程序，支持从 EF6 空间类型开始。  

## <a name="ef-providers-that-support-spatial-types"></a>支持空间类型的 EF 提供程序  

### <a name="ef5"></a>EF5  

我们意识到支持空间类型的 EF5 的实体框架提供程序是：  

- Microsoft SQL Server 提供程序  
    - 此提供程序作为 EF5 的一部分提供。  
    - 此提供程序依赖于一些可能需要安装的其他低级别库，请参阅下面的详细信息。  
- [适用于 Oracle 的 Devart dotConnect](https://www.devart.com/dotconnect/oracle/)  
    - 这是 Devart 中的第三方提供程序。  

如果你知道支持空间类型的 EF5 提供程序，请联系，我们将很高兴地将其添加到此列表。  

### <a name="ef6"></a>EF6  

我们意识到支持空间类型的 EF6 的实体框架提供程序是：  

- Microsoft SQL Server 提供程序  
    - 此提供程序作为 EF6 的一部分提供。  
    - 此提供程序依赖于一些可能需要安装的其他低级别库，请参阅下面的详细信息。  
- [适用于 Oracle 的 Devart dotConnect](https://www.devart.com/dotconnect/oracle/)  
    - 这是 Devart 中的第三方提供程序。  

如果你知道支持空间类型的 EF6 提供程序，请联系，我们将很高兴地将其添加到此列表。  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a>具有 Microsoft SQL Server 的空间类型的先决条件  

SQL Server 空间支持取决于低级别、SQL Server 特定类型 SqlGeography 和 SqlGeometry。 这些类型存在于 Microsoft.SqlServer.Types.dll 程序集中，并且此程序集不作为 EF 的一部分或作为 .NET Framework 的一部分。  

安装 Visual Studio 时，它通常还会安装 SQL Server 的版本，这将包括 Microsoft.SqlServer.Types.dll 的安装。  

如果 SQL Server 未安装在要使用空间类型的计算机上，或者如果从 SQL Server 安装中排除了空间类型，则需要手动安装它们。 类型可以使用安装 `SQLSysClrTypes.msi` ，这是 Microsoft SQL Server 功能包的一部分。 空间类型 SQL Server 特定于版本，因此我们建议在 Microsoft 下载中心 [搜索 "SQL Server 功能包"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) ，然后选择和下载与将使用的 SQL Server 版本相对应的选项。

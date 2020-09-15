---
title: 提供程序对空间类型的支持-EF6
description: 提供程序支持实体框架6中的空间类型
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/providers/spatial-support
ms.openlocfilehash: d6214ec503af8ea02b586cfddfd958ec7b47e42f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070440"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="fbc37-103">提供程序对空间类型的支持</span><span class="sxs-lookup"><span data-stu-id="fbc37-103">Provider Support for Spatial Types</span></span>
<span data-ttu-id="fbc37-104">实体框架支持通过 DbGeography 或 DbGeometry 类处理空间数据。</span><span class="sxs-lookup"><span data-stu-id="fbc37-104">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="fbc37-105">这些类依赖于由实体框架提供程序提供的数据库特定功能。</span><span class="sxs-lookup"><span data-stu-id="fbc37-105">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="fbc37-106">并非所有提供程序都支持空间数据，其中可能有其他先决条件，如安装空间类型程序集。</span><span class="sxs-lookup"><span data-stu-id="fbc37-106">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="fbc37-107">下面提供了有关为空间类型提供程序支持的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fbc37-107">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="fbc37-108">有关如何在应用程序中使用空间类型的其他信息，请参阅两个演练，一个用于 Code First，另一个用于 Database First 或 Model First：</span><span class="sxs-lookup"><span data-stu-id="fbc37-108">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="fbc37-109">Code First 中的空间数据类型</span><span class="sxs-lookup"><span data-stu-id="fbc37-109">Spatial Data Types in Code First</span></span>](xref:ef6/modeling/code-first/data-types/spatial)  
- [<span data-ttu-id="fbc37-110">EF 设计器中的空间数据类型</span><span class="sxs-lookup"><span data-stu-id="fbc37-110">Spatial Data Types in EF Designer</span></span>](xref:ef6/modeling/designer/data-types/spatial)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="fbc37-111">支持空间类型的 EF 版本</span><span class="sxs-lookup"><span data-stu-id="fbc37-111">EF releases that support spatial types</span></span>  

<span data-ttu-id="fbc37-112">EF5 中引入了对空间类型的支持。</span><span class="sxs-lookup"><span data-stu-id="fbc37-112">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="fbc37-113">但是，仅当应用程序在 .NET 4.5 上面向和运行时，才支持 EF5 空间类型。</span><span class="sxs-lookup"><span data-stu-id="fbc37-113">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="fbc37-114">对于面向 .NET 4 和 .NET 4.5 的应用程序，支持从 EF6 空间类型开始。</span><span class="sxs-lookup"><span data-stu-id="fbc37-114">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="fbc37-115">支持空间类型的 EF 提供程序</span><span class="sxs-lookup"><span data-stu-id="fbc37-115">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="fbc37-116">EF5</span><span class="sxs-lookup"><span data-stu-id="fbc37-116">EF5</span></span>  

<span data-ttu-id="fbc37-117">我们意识到支持空间类型的 EF5 的实体框架提供程序是：</span><span class="sxs-lookup"><span data-stu-id="fbc37-117">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="fbc37-118">Microsoft SQL Server 提供程序</span><span class="sxs-lookup"><span data-stu-id="fbc37-118">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="fbc37-119">此提供程序作为 EF5 的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="fbc37-119">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="fbc37-120">此提供程序依赖于一些可能需要安装的其他低级别库，请参阅下面的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fbc37-120">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="fbc37-121">适用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="fbc37-121">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="fbc37-122">这是 Devart 中的第三方提供程序。</span><span class="sxs-lookup"><span data-stu-id="fbc37-122">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="fbc37-123">如果你知道支持空间类型的 EF5 提供程序，请联系，我们将很高兴地将其添加到此列表。</span><span class="sxs-lookup"><span data-stu-id="fbc37-123">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="fbc37-124">EF6</span><span class="sxs-lookup"><span data-stu-id="fbc37-124">EF6</span></span>  

<span data-ttu-id="fbc37-125">我们意识到支持空间类型的 EF6 的实体框架提供程序是：</span><span class="sxs-lookup"><span data-stu-id="fbc37-125">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="fbc37-126">Microsoft SQL Server 提供程序</span><span class="sxs-lookup"><span data-stu-id="fbc37-126">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="fbc37-127">此提供程序作为 EF6 的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="fbc37-127">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="fbc37-128">此提供程序依赖于一些可能需要安装的其他低级别库，请参阅下面的详细信息。</span><span class="sxs-lookup"><span data-stu-id="fbc37-128">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="fbc37-129">适用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="fbc37-129">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="fbc37-130">这是 Devart 中的第三方提供程序。</span><span class="sxs-lookup"><span data-stu-id="fbc37-130">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="fbc37-131">如果你知道支持空间类型的 EF6 提供程序，请联系，我们将很高兴地将其添加到此列表。</span><span class="sxs-lookup"><span data-stu-id="fbc37-131">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="fbc37-132">具有 Microsoft SQL Server 的空间类型的先决条件</span><span class="sxs-lookup"><span data-stu-id="fbc37-132">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="fbc37-133">SQL Server 空间支持取决于低级别、SQL Server 特定类型 SqlGeography 和 SqlGeometry。</span><span class="sxs-lookup"><span data-stu-id="fbc37-133">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="fbc37-134">这些类型存在于 Microsoft.SqlServer.Types.dll 程序集中，并且此程序集不作为 EF 的一部分或作为 .NET Framework 的一部分。</span><span class="sxs-lookup"><span data-stu-id="fbc37-134">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="fbc37-135">安装 Visual Studio 时，它通常还会安装 SQL Server 的版本，这将包括 Microsoft.SqlServer.Types.dll 的安装。</span><span class="sxs-lookup"><span data-stu-id="fbc37-135">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="fbc37-136">如果 SQL Server 未安装在要使用空间类型的计算机上，或者如果从 SQL Server 安装中排除了空间类型，则需要手动安装它们。</span><span class="sxs-lookup"><span data-stu-id="fbc37-136">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="fbc37-137">类型可以使用安装 `SQLSysClrTypes.msi` ，这是 Microsoft SQL Server 功能包的一部分。</span><span class="sxs-lookup"><span data-stu-id="fbc37-137">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="fbc37-138">空间类型 SQL Server 特定于版本，因此我们建议在 Microsoft 下载中心 [搜索 "SQL Server 功能包"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) ，然后选择和下载与将使用的 SQL Server 版本相对应的选项。</span><span class="sxs-lookup"><span data-stu-id="fbc37-138">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>

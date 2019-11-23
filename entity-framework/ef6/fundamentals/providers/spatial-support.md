---
title: 提供程序对空间类型的支持-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 1097cb00-15f5-453d-90ed-bff9403d23e3
ms.openlocfilehash: 863f1b4551bd62160915eba90fee7ba6c49c169c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181596"
---
# <a name="provider-support-for-spatial-types"></a><span data-ttu-id="34e6b-102">提供程序对空间类型的支持</span><span class="sxs-lookup"><span data-stu-id="34e6b-102">Provider Support for Spatial Types</span></span>
<span data-ttu-id="34e6b-103">实体框架支持通过 DbGeography 或 DbGeometry 类处理空间数据。</span><span class="sxs-lookup"><span data-stu-id="34e6b-103">Entity Framework supports working with spatial data through the DbGeography or DbGeometry classes.</span></span> <span data-ttu-id="34e6b-104">这些类依赖于由实体框架提供程序提供的数据库特定功能。</span><span class="sxs-lookup"><span data-stu-id="34e6b-104">These classes rely on database-specific functionality offered by the Entity Framework provider.</span></span> <span data-ttu-id="34e6b-105">并非所有提供程序都支持空间数据，其中可能有其他先决条件，如安装空间类型程序集。</span><span class="sxs-lookup"><span data-stu-id="34e6b-105">Not all providers support spatial data and those that do may have additional prerequisites such as the installation of spatial type assemblies.</span></span> <span data-ttu-id="34e6b-106">下面提供了有关为空间类型提供程序支持的详细信息。</span><span class="sxs-lookup"><span data-stu-id="34e6b-106">More information about provider support for spatial types is provided below.</span></span>  

<span data-ttu-id="34e6b-107">有关如何在应用程序中使用空间类型的其他信息，请参阅两个演练，一个用于 Code First，另一个用于 Database First 或 Model First：</span><span class="sxs-lookup"><span data-stu-id="34e6b-107">Additional information on how to use spatial types in an application can be found in two walkthroughs, one for Code First, the other for Database First or Model First:</span></span>  

- [<span data-ttu-id="34e6b-108">Code First 中的空间数据类型</span><span class="sxs-lookup"><span data-stu-id="34e6b-108">Spatial Data Types in Code First</span></span>](~/ef6/modeling/code-first/data-types/spatial.md)  
- [<span data-ttu-id="34e6b-109">EF 设计器中的空间数据类型</span><span class="sxs-lookup"><span data-stu-id="34e6b-109">Spatial Data Types in EF Designer</span></span>](~/ef6/modeling/designer/data-types/spatial.md)  

## <a name="ef-releases-that-support-spatial-types"></a><span data-ttu-id="34e6b-110">支持空间类型的 EF 版本</span><span class="sxs-lookup"><span data-stu-id="34e6b-110">EF releases that support spatial types</span></span>  

<span data-ttu-id="34e6b-111">EF5 中引入了对空间类型的支持。</span><span class="sxs-lookup"><span data-stu-id="34e6b-111">Support for spatial types was introduced in EF5.</span></span> <span data-ttu-id="34e6b-112">但是，仅当应用程序在 .NET 4.5 上面向和运行时，才支持 EF5 空间类型。</span><span class="sxs-lookup"><span data-stu-id="34e6b-112">However, in EF5 spatial types are only supported when the application targets and runs on .NET 4.5.</span></span>  

<span data-ttu-id="34e6b-113">对于面向 .NET 4 和 .NET 4.5 的应用程序，支持从 EF6 空间类型开始。</span><span class="sxs-lookup"><span data-stu-id="34e6b-113">Starting with EF6 spatial types are supported for applications targeting both .NET 4 and .NET 4.5.</span></span>  

## <a name="ef-providers-that-support-spatial-types"></a><span data-ttu-id="34e6b-114">支持空间类型的 EF 提供程序</span><span class="sxs-lookup"><span data-stu-id="34e6b-114">EF providers that support spatial types</span></span>  

### <a name="ef5"></a><span data-ttu-id="34e6b-115">EF5</span><span class="sxs-lookup"><span data-stu-id="34e6b-115">EF5</span></span>  

<span data-ttu-id="34e6b-116">我们意识到支持空间类型的 EF5 的实体框架提供程序是：</span><span class="sxs-lookup"><span data-stu-id="34e6b-116">The Entity Framework providers for EF5 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="34e6b-117">Microsoft SQL Server 提供程序</span><span class="sxs-lookup"><span data-stu-id="34e6b-117">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="34e6b-118">此提供程序作为 EF5 的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="34e6b-118">This provider is shipped as part of EF5.</span></span>  
    - <span data-ttu-id="34e6b-119">此提供程序依赖于一些可能需要安装的其他低级别库，请参阅下面的详细信息。</span><span class="sxs-lookup"><span data-stu-id="34e6b-119">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="34e6b-120">适用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="34e6b-120">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="34e6b-121">这是 Devart 中的第三方提供程序。</span><span class="sxs-lookup"><span data-stu-id="34e6b-121">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="34e6b-122">如果你知道支持空间类型的 EF5 提供程序，请联系，我们将很高兴地将其添加到此列表。</span><span class="sxs-lookup"><span data-stu-id="34e6b-122">If you know of an EF5 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

### <a name="ef6"></a><span data-ttu-id="34e6b-123">EF6</span><span class="sxs-lookup"><span data-stu-id="34e6b-123">EF6</span></span>  

<span data-ttu-id="34e6b-124">我们意识到支持空间类型的 EF6 的实体框架提供程序是：</span><span class="sxs-lookup"><span data-stu-id="34e6b-124">The Entity Framework providers for EF6 that we are aware of that support spatial types are:</span></span>  

- <span data-ttu-id="34e6b-125">Microsoft SQL Server 提供程序</span><span class="sxs-lookup"><span data-stu-id="34e6b-125">Microsoft SQL Server provider</span></span>  
    - <span data-ttu-id="34e6b-126">此提供程序作为 EF6 的一部分提供。</span><span class="sxs-lookup"><span data-stu-id="34e6b-126">This provider is shipped as part of EF6.</span></span>  
    - <span data-ttu-id="34e6b-127">此提供程序依赖于一些可能需要安装的其他低级别库，请参阅下面的详细信息。</span><span class="sxs-lookup"><span data-stu-id="34e6b-127">This provider depends on some additional low-level libraries that may need to be installed—see below for details.</span></span>  
- [<span data-ttu-id="34e6b-128">适用于 Oracle 的 Devart dotConnect</span><span class="sxs-lookup"><span data-stu-id="34e6b-128">Devart dotConnect for Oracle</span></span>](https://www.devart.com/dotconnect/oracle/)  
    - <span data-ttu-id="34e6b-129">这是 Devart 中的第三方提供程序。</span><span class="sxs-lookup"><span data-stu-id="34e6b-129">This is a third-party provider from Devart.</span></span>  

<span data-ttu-id="34e6b-130">如果你知道支持空间类型的 EF6 提供程序，请联系，我们将很高兴地将其添加到此列表。</span><span class="sxs-lookup"><span data-stu-id="34e6b-130">If you know of an EF6 provider that supports spatial types then please get in contact and we will be happy to add it to this list.</span></span>  

## <a name="prerequisites-for-spatial-types-with-microsoft-sql-server"></a><span data-ttu-id="34e6b-131">具有 Microsoft SQL Server 的空间类型的先决条件</span><span class="sxs-lookup"><span data-stu-id="34e6b-131">Prerequisites for spatial types with Microsoft SQL Server</span></span>  

<span data-ttu-id="34e6b-132">SQL Server 空间支持取决于低级别、SQL Server 特定类型 SqlGeography 和 SqlGeometry。</span><span class="sxs-lookup"><span data-stu-id="34e6b-132">SQL Server spatial support depends on the low-level, SQL Server-specific types SqlGeography and SqlGeometry.</span></span> <span data-ttu-id="34e6b-133">这些类型存在于 mscorlib.dll 程序集中，并且此程序集不作为 EF 的一部分或作为 .NET Framework 的一部分。</span><span class="sxs-lookup"><span data-stu-id="34e6b-133">These types live in Microsoft.SqlServer.Types.dll assembly, and this assembly is not shipped as part of EF or as part of the .NET Framework.</span></span>  

<span data-ttu-id="34e6b-134">安装 Visual Studio 时，它通常还会安装 SQL Server 的版本，这将包括安装 Microsoft。</span><span class="sxs-lookup"><span data-stu-id="34e6b-134">When Visual Studio is installed it will often also install a version of SQL Server, and this will include installation of the Microsoft.SqlServer.Types.dll.</span></span>  

<span data-ttu-id="34e6b-135">如果 SQL Server 未安装在要使用空间类型的计算机上，或者如果从 SQL Server 安装中排除了空间类型，则需要手动安装它们。</span><span class="sxs-lookup"><span data-stu-id="34e6b-135">If SQL Server is not installed on the machine where you want to use spatial types, or if spatial types were excluded from the SQL Server installation, then you will need to install them manually.</span></span> <span data-ttu-id="34e6b-136">可以使用 `SQLSysClrTypes.msi`（这是 Microsoft SQL Server 功能包的一部分）安装类型。</span><span class="sxs-lookup"><span data-stu-id="34e6b-136">The types can be installed using `SQLSysClrTypes.msi`, which is part of Microsoft SQL Server Feature Pack.</span></span> <span data-ttu-id="34e6b-137">空间类型 SQL Server 特定于版本，因此我们建议在 Microsoft 下载中心[搜索 "SQL Server 功能包"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) ，然后选择和下载与将使用的 SQL Server 版本相对应的选项。</span><span class="sxs-lookup"><span data-stu-id="34e6b-137">Spatial types are SQL Server version-specific, so we recommend [search for "SQL Server Feature Pack"](https://www.microsoft.com/search/result.aspx?q=sql+server+feature+pack) in the Microsoft Download Center, then select and download the option that corresponds to the version of SQL Server you will use.</span></span>

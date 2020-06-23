---
title: EF Core 5.0 中的中断性变更 - EF Core
author: bricelam
ms.date: 06/05/2020
uid: core/what-is-new/ef-core-5.0/breaking-changes
ms.openlocfilehash: 0ffe62d9b21dd901c2cdaf0ea5d416d14938497f
ms.sourcegitcommit: 92d54fe3702e0c92e198334da22bacb42e9842b1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666167"
---
# <a name="breaking-changes-in-ef-core-50"></a><span data-ttu-id="deb80-102">EF Core 5.0 中的中断性变更</span><span class="sxs-lookup"><span data-stu-id="deb80-102">Breaking changes in EF Core 5.0</span></span>

<span data-ttu-id="deb80-103">API 和行为的下列更改有可能导致现有应用程序在更新到 EF Core 5.0.0 时中断。</span><span class="sxs-lookup"><span data-stu-id="deb80-103">The following API and behavior changes have the potential to break existing applications updating to EF Core 5.0.0.</span></span>

## <a name="summary"></a><span data-ttu-id="deb80-104">摘要</span><span class="sxs-lookup"><span data-stu-id="deb80-104">Summary</span></span>

| <span data-ttu-id="deb80-105">**中断性变更**</span><span class="sxs-lookup"><span data-stu-id="deb80-105">**Breaking change**</span></span>                                                                                                               | <span data-ttu-id="deb80-106">**影响**</span><span class="sxs-lookup"><span data-stu-id="deb80-106">**Impact**</span></span> |
|:----------------------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="deb80-107">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="deb80-107">Removed HasGeometricDimension method from SQLite NTS extension</span></span>](#removed-hasgeometricdimension-method-from-sqlite-nts-extension) | <span data-ttu-id="deb80-108">低</span><span class="sxs-lookup"><span data-stu-id="deb80-108">Low</span></span>        |

### <a name="removed-hasgeometricdimension-method-from-sqlite-nts-extension"></a><span data-ttu-id="deb80-109">从 SQLite NTS 扩展中删除了 HasGeometricDimension 方法</span><span class="sxs-lookup"><span data-stu-id="deb80-109">Removed HasGeometricDimension method from SQLite NTS extension</span></span>

[<span data-ttu-id="deb80-110">跟踪问题 #14257</span><span class="sxs-lookup"><span data-stu-id="deb80-110">Tracking Issue #14257</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14257)

<span data-ttu-id="deb80-111">**旧行为**</span><span class="sxs-lookup"><span data-stu-id="deb80-111">**Old behavior**</span></span>

<span data-ttu-id="deb80-112">HasGeometricDimension 过去用于在几何列上启用其他维度（Z 和 M）。</span><span class="sxs-lookup"><span data-stu-id="deb80-112">HasGeometricDimension was used to enable additional dimensions (Z and M) on geometry columns.</span></span> <span data-ttu-id="deb80-113">但是，之前它只影响数据库创建。</span><span class="sxs-lookup"><span data-stu-id="deb80-113">However, it only ever affected database creation.</span></span> <span data-ttu-id="deb80-114">不需要指定它来查询具有其他维度的值。</span><span class="sxs-lookup"><span data-stu-id="deb80-114">It was unnecessary to specify it to query values with additional dimensions.</span></span> <span data-ttu-id="deb80-115">之前，在插入或更新具有其他维度的值时，它也无法正常工作（请参见 [see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)）。</span><span class="sxs-lookup"><span data-stu-id="deb80-115">It also didn't work correctly when inserting or updating values with additional dimensions ([see #14257](https://github.com/aspnet/EntityFrameworkCore/issues/14257)).</span></span>

<span data-ttu-id="deb80-116">**新行为**</span><span class="sxs-lookup"><span data-stu-id="deb80-116">**New behavior**</span></span>

<span data-ttu-id="deb80-117">要能够插入和更新具有其他维度（Z 和 M）的几何值，需要将维度指定为列类型名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="deb80-117">To enable inserting and updating geometry values with additional dimensions (Z and M), the dimension needs to be specified as part of the column type name.</span></span> <span data-ttu-id="deb80-118">这与 SpatiaLite 的 AddGeometryColumn 函数的基本行为匹配度更高。</span><span class="sxs-lookup"><span data-stu-id="deb80-118">This more closely matches the underlying behavior of SpatiaLite's AddGeometryColumn function.</span></span>

<span data-ttu-id="deb80-119">**为什么**</span><span class="sxs-lookup"><span data-stu-id="deb80-119">**Why**</span></span>

<span data-ttu-id="deb80-120">在列类型中指定维度后，不需要使用 HasGeometricDimension，该方法也很多余，因此我们已将它彻底删除。</span><span class="sxs-lookup"><span data-stu-id="deb80-120">Using HasGeometricDimension after specifying the dimension in the column type is unnecessary and redundant, so we removed HasGeometricDimension entirely.</span></span>

<span data-ttu-id="deb80-121">**缓解措施**</span><span class="sxs-lookup"><span data-stu-id="deb80-121">**Mitigations**</span></span>

<span data-ttu-id="deb80-122">使用 `HasColumnType` 指定维度：</span><span class="sxs-lookup"><span data-stu-id="deb80-122">Use `HasColumnType` to specify the dimension:</span></span>

```cs
modelBuilder.Entity<GeoEntity>(
    x =>
    {
        // Allow any GEOMETRY value with optional Z and M values
        x.Property(e => e.Geometry).HasColumnType("GEOMETRYZM");

        // Allow only POINT values with an optional Z value
        x.Property(e => e.Point).HasColumnType("POINTZ");
    });
```

---
title: 最大长度-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: e54d3671f378b96a49eaf4cb312e72072813fc6d
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996186"
---
# <a name="maximum-length"></a><span data-ttu-id="97f6e-102">最大长度</span><span class="sxs-lookup"><span data-stu-id="97f6e-102">Maximum Length</span></span>

<span data-ttu-id="97f6e-103">配置最大长度可为数据存储提供有关要对给定属性使用的相应数据类型的提示。</span><span class="sxs-lookup"><span data-stu-id="97f6e-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="97f6e-104">最大长度仅适用于数组数据类型，如 `string` 和 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="97f6e-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="97f6e-105">将数据传递到提供程序之前，实体框架不会执行任何最大长度验证。</span><span class="sxs-lookup"><span data-stu-id="97f6e-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="97f6e-106">由提供程序或数据存储在适当时机进行验证。</span><span class="sxs-lookup"><span data-stu-id="97f6e-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="97f6e-107">例如，如果以 SQL Server 为目标，超过最大长度将引发异常，因为基础列的数据类型不允许存储过多数据。</span><span class="sxs-lookup"><span data-stu-id="97f6e-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="97f6e-108">约定</span><span class="sxs-lookup"><span data-stu-id="97f6e-108">Conventions</span></span>

<span data-ttu-id="97f6e-109">按照约定，它是应由数据库提供程序选择适当的数据类型的属性。</span><span class="sxs-lookup"><span data-stu-id="97f6e-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="97f6e-110">对于具有长度的属性，数据库提供程序通常将选择允许的最长长度的数据的数据类型。</span><span class="sxs-lookup"><span data-stu-id="97f6e-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="97f6e-111">例如，将使用 Microsoft SQL Server`nvarchar(max)`有关`string`属性 (或`nvarchar(450)`如果列用作键)。</span><span class="sxs-lookup"><span data-stu-id="97f6e-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="97f6e-112">数据注释</span><span class="sxs-lookup"><span data-stu-id="97f6e-112">Data Annotations</span></span>

<span data-ttu-id="97f6e-113">可以使用数据注释来配置属性的最大长度。</span><span class="sxs-lookup"><span data-stu-id="97f6e-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="97f6e-114">此示例面向 SQL Server，因此使用数据类型 `nvarchar(500)`。</span><span class="sxs-lookup"><span data-stu-id="97f6e-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/MaxLength.cs?highlight=4)] -->
``` csharp
public class Blog
{
    public int BlogId { get; set; }
    [MaxLength(500)]
    public string Url { get; set; }
}
```

## <a name="fluent-api"></a><span data-ttu-id="97f6e-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="97f6e-115">Fluent API</span></span>

<span data-ttu-id="97f6e-116">可以使用 Fluent API 配置属性的最大长度。</span><span class="sxs-lookup"><span data-stu-id="97f6e-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="97f6e-117">此示例面向 SQL Server，因此使用数据类型 `nvarchar(500)`。</span><span class="sxs-lookup"><span data-stu-id="97f6e-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/MaxLength.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .HasMaxLength(500);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

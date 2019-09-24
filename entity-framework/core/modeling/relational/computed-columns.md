---
title: 计算列-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: e9d81f06-805d-45c9-97c2-3546df654829
uid: core/modeling/relational/computed-columns
ms.openlocfilehash: da106c94698a202744d7cd465aa84d0d72802833
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197231"
---
# <a name="computed-columns"></a><span data-ttu-id="96a6e-102">计算列</span><span class="sxs-lookup"><span data-stu-id="96a6e-102">Computed Columns</span></span>

> [!NOTE]  
> <span data-ttu-id="96a6e-103">一般而言，本部分中的配置适用于关系数据库。</span><span class="sxs-lookup"><span data-stu-id="96a6e-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="96a6e-104">安装关系数据库提供程序时，此处显示的扩展方法将变为可用（原因在于共享的 Microsoft.EntityFrameworkCore.Relational 包）。</span><span class="sxs-lookup"><span data-stu-id="96a6e-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="96a6e-105">计算列是在数据库中计算其值的列。</span><span class="sxs-lookup"><span data-stu-id="96a6e-105">A computed column is a column whose value is calculated in the database.</span></span> <span data-ttu-id="96a6e-106">计算列可以使用表中的其他列来计算其值。</span><span class="sxs-lookup"><span data-stu-id="96a6e-106">A computed column can use other columns in the table to calculate its value.</span></span>

## <a name="conventions"></a><span data-ttu-id="96a6e-107">约定</span><span class="sxs-lookup"><span data-stu-id="96a6e-107">Conventions</span></span>

<span data-ttu-id="96a6e-108">按照约定，不会在模型中创建计算列。</span><span class="sxs-lookup"><span data-stu-id="96a6e-108">By convention, computed columns are not created in the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="96a6e-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="96a6e-109">Data Annotations</span></span>

<span data-ttu-id="96a6e-110">计算列不能配置数据批注。</span><span class="sxs-lookup"><span data-stu-id="96a6e-110">Computed columns can not be configured with Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="96a6e-111">Fluent API</span><span class="sxs-lookup"><span data-stu-id="96a6e-111">Fluent API</span></span>

<span data-ttu-id="96a6e-112">您可以使用熟知的 API 来指定属性应映射到计算列。</span><span class="sxs-lookup"><span data-stu-id="96a6e-112">You can use the Fluent API to specify that a property should map to a computed column.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/ComputedColumn.cs?highlight=9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .Property(p => p.DisplayName)
            .HasComputedColumnSql("[LastName] + ', ' + [FirstName]");
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string DisplayName { get; set; }
}
```

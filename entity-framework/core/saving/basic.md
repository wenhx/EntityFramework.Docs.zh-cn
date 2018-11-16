---
title: 基本保存 - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
uid: core/saving/basic
ms.openlocfilehash: 23e0e4611f642d59048fca5a808d0782b22caa1e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994796"
---
# <a name="basic-save"></a><span data-ttu-id="87c54-102">基本保存</span><span class="sxs-lookup"><span data-stu-id="87c54-102">Basic Save</span></span>

<span data-ttu-id="87c54-103">了解如何使用上下文和实体类添加、修改和删除数据。</span><span class="sxs-lookup"><span data-stu-id="87c54-103">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="87c54-104">可在 GitHub 上查看此文章的[示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/)。</span><span class="sxs-lookup"><span data-stu-id="87c54-104">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="87c54-105">添加数据</span><span class="sxs-lookup"><span data-stu-id="87c54-105">Adding Data</span></span>

<span data-ttu-id="87c54-106">使用 *DbSet.Add* 方法添加实体类的新实例。</span><span class="sxs-lookup"><span data-stu-id="87c54-106">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="87c54-107">调用 *SaveChanges* 时，数据将插入到数据库中。</span><span class="sxs-lookup"><span data-stu-id="87c54-107">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> <span data-ttu-id="87c54-108">添加、附加和更新方法全部呈现在传递给这些方法的实体的完整关系图上，如[相关数据](related-data.md)部分中所述。</span><span class="sxs-lookup"><span data-stu-id="87c54-108">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](related-data.md) section.</span></span> <span data-ttu-id="87c54-109">此外，还可以使用 EntityEntry.State 属性仅设置单个实体的状态。</span><span class="sxs-lookup"><span data-stu-id="87c54-109">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="87c54-110">例如 `context.Entry(blog).State = EntityState.Modified`。</span><span class="sxs-lookup"><span data-stu-id="87c54-110">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="87c54-111">更新数据</span><span class="sxs-lookup"><span data-stu-id="87c54-111">Updating Data</span></span>

<span data-ttu-id="87c54-112">EF 将自动检测对由上下文跟踪的现有实体所做的更改。</span><span class="sxs-lookup"><span data-stu-id="87c54-112">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="87c54-113">这包括从数据库加载/查询的实体，以及之前添加并保存到数据库的实体。</span><span class="sxs-lookup"><span data-stu-id="87c54-113">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="87c54-114">只需通过赋值来修改属性，然后调用 *SaveChanges* 即可。</span><span class="sxs-lookup"><span data-stu-id="87c54-114">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="87c54-115">删除数据</span><span class="sxs-lookup"><span data-stu-id="87c54-115">Deleting Data</span></span>

<span data-ttu-id="87c54-116">使用 DbSet.Remove 方法删除实体类的实例。</span><span class="sxs-lookup"><span data-stu-id="87c54-116">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="87c54-117">如果实体已存在于数据库中，则将在“SaveChanges”期间删除该实体。</span><span class="sxs-lookup"><span data-stu-id="87c54-117">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="87c54-118">如果实体尚未保存到数据库（即跟踪为“已添加”），则在调用“SaveChanges”时，该实体会从上下文中删除且不再插入。</span><span class="sxs-lookup"><span data-stu-id="87c54-118">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="87c54-119">单个 SaveChanges 中的多个操作</span><span class="sxs-lookup"><span data-stu-id="87c54-119">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="87c54-120">可以将多个添加/更新/删除操作合并到对“SaveChanges”的单个调用。</span><span class="sxs-lookup"><span data-stu-id="87c54-120">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="87c54-121">对于大多数数据库提供程序，“SaveChanges”是事务性的。</span><span class="sxs-lookup"><span data-stu-id="87c54-121">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="87c54-122">这意味着所有操作将一起成功或一起失败，绝不会部分的应用这些操作。</span><span class="sxs-lookup"><span data-stu-id="87c54-122">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]

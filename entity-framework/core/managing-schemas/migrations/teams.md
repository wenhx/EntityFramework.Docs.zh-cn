---
title: 团队环境中的迁移-EF Core
description: 在团队环境中管理迁移和解决冲突的最佳实践与 Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062498"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="6725f-103">团队环境中的迁移</span><span class="sxs-lookup"><span data-stu-id="6725f-103">Migrations in Team Environments</span></span>

<span data-ttu-id="6725f-104">在团队环境中使用迁移时，请特别注意模型快照文件。</span><span class="sxs-lookup"><span data-stu-id="6725f-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="6725f-105">此文件可以告诉你队友的迁移是否与你的迁移完全合并，或者是否需要在共享之前重新创建迁移来解决冲突。</span><span class="sxs-lookup"><span data-stu-id="6725f-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="6725f-106">合并</span><span class="sxs-lookup"><span data-stu-id="6725f-106">Merging</span></span>

<span data-ttu-id="6725f-107">合并团队成员的迁移时，可能会在模型快照文件中出现冲突。</span><span class="sxs-lookup"><span data-stu-id="6725f-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="6725f-108">如果这两个更改不相关，则合并很简单，两个迁移可以共存。</span><span class="sxs-lookup"><span data-stu-id="6725f-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="6725f-109">例如，你可能会在客户实体类型配置中出现合并冲突，如下所示：</span><span class="sxs-lookup"><span data-stu-id="6725f-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="6725f-110">由于这两个属性都需要存在于最终模型中，因此请通过添加这两个属性来完成合并。</span><span class="sxs-lookup"><span data-stu-id="6725f-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="6725f-111">在许多情况下，版本控制系统可能会自动合并此类更改。</span><span class="sxs-lookup"><span data-stu-id="6725f-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="6725f-112">在这些情况下，迁移和队友的迁移彼此独立。</span><span class="sxs-lookup"><span data-stu-id="6725f-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="6725f-113">由于可以首先应用其中的任何一个，因此在与团队共享之前，无需对迁移进行任何其他更改。</span><span class="sxs-lookup"><span data-stu-id="6725f-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="6725f-114">解决冲突</span><span class="sxs-lookup"><span data-stu-id="6725f-114">Resolving conflicts</span></span>

<span data-ttu-id="6725f-115">当合并模型快照模型时，有时会遇到真正冲突。</span><span class="sxs-lookup"><span data-stu-id="6725f-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="6725f-116">例如，你和你的队友可以重命名同一个属性。</span><span class="sxs-lookup"><span data-stu-id="6725f-116">For example, you and your teammate may each have renamed the same property.</span></span>

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="6725f-117">如果遇到这种冲突，请通过重新创建迁移解决此问题。</span><span class="sxs-lookup"><span data-stu-id="6725f-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="6725f-118">请执行下列步骤：</span><span class="sxs-lookup"><span data-stu-id="6725f-118">Follow these steps:</span></span>

1. <span data-ttu-id="6725f-119">在合并前中止合并并回退到工作目录</span><span class="sxs-lookup"><span data-stu-id="6725f-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="6725f-120">删除迁移 (但保留模型更改) </span><span class="sxs-lookup"><span data-stu-id="6725f-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="6725f-121">将队友的更改合并到工作目录</span><span class="sxs-lookup"><span data-stu-id="6725f-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="6725f-122">重新添加迁移</span><span class="sxs-lookup"><span data-stu-id="6725f-122">Re-add your migration</span></span>

<span data-ttu-id="6725f-123">执行此操作后，可以按正确的顺序应用两个迁移。</span><span class="sxs-lookup"><span data-stu-id="6725f-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="6725f-124">首先应用其迁移，将列重命名为 *Alias*，此后，迁移会将其重命名为 *Username*。</span><span class="sxs-lookup"><span data-stu-id="6725f-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="6725f-125">可以安全地与团队的其他成员共享你的迁移。</span><span class="sxs-lookup"><span data-stu-id="6725f-125">Your migration can safely be shared with the rest of the team.</span></span>

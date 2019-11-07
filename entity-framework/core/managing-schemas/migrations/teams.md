---
title: 团队环境中的迁移-EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 6c17c56277821159962884aef72d46c624442e20
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655546"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="b3113-102">团队环境中的迁移</span><span class="sxs-lookup"><span data-stu-id="b3113-102">Migrations in Team Environments</span></span>

<span data-ttu-id="b3113-103">在团队环境中使用迁移时，请特别注意模型快照文件。</span><span class="sxs-lookup"><span data-stu-id="b3113-103">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="b3113-104">此文件可以告诉你队友的迁移是否与你的迁移完全合并，或者是否需要在共享之前重新创建迁移来解决冲突。</span><span class="sxs-lookup"><span data-stu-id="b3113-104">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="b3113-105">合并</span><span class="sxs-lookup"><span data-stu-id="b3113-105">Merging</span></span>

<span data-ttu-id="b3113-106">合并团队成员的迁移时，可能会在模型快照文件中出现冲突。</span><span class="sxs-lookup"><span data-stu-id="b3113-106">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="b3113-107">如果这两个更改不相关，则合并很简单，两个迁移可以共存。</span><span class="sxs-lookup"><span data-stu-id="b3113-107">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="b3113-108">例如，你可能会在客户实体类型配置中出现合并冲突，如下所示：</span><span class="sxs-lookup"><span data-stu-id="b3113-108">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="b3113-109">由于这两个属性都需要存在于最终模型中，因此请通过添加这两个属性来完成合并。</span><span class="sxs-lookup"><span data-stu-id="b3113-109">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="b3113-110">在许多情况下，版本控制系统可能会自动合并此类更改。</span><span class="sxs-lookup"><span data-stu-id="b3113-110">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="b3113-111">在这些情况下，迁移和队友的迁移彼此独立。</span><span class="sxs-lookup"><span data-stu-id="b3113-111">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="b3113-112">由于可以首先应用其中的任何一个，因此在与团队共享之前，无需对迁移进行任何其他更改。</span><span class="sxs-lookup"><span data-stu-id="b3113-112">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="b3113-113">解决冲突</span><span class="sxs-lookup"><span data-stu-id="b3113-113">Resolving conflicts</span></span>

<span data-ttu-id="b3113-114">当合并模型快照模型时，有时会遇到真正冲突。</span><span class="sxs-lookup"><span data-stu-id="b3113-114">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="b3113-115">例如，你和你的队友可以重命名同一个属性。</span><span class="sxs-lookup"><span data-stu-id="b3113-115">For example, you and your teammate may each have renamed the same property.</span></span>

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="b3113-116">如果遇到这种冲突，请通过重新创建迁移解决此问题。</span><span class="sxs-lookup"><span data-stu-id="b3113-116">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="b3113-117">请执行这些步骤：</span><span class="sxs-lookup"><span data-stu-id="b3113-117">Follow these steps:</span></span>

1. <span data-ttu-id="b3113-118">在合并前中止合并并回退到工作目录</span><span class="sxs-lookup"><span data-stu-id="b3113-118">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="b3113-119">删除你的迁移（但保留你的模型更改）</span><span class="sxs-lookup"><span data-stu-id="b3113-119">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="b3113-120">将队友的更改合并到工作目录</span><span class="sxs-lookup"><span data-stu-id="b3113-120">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="b3113-121">重新添加迁移</span><span class="sxs-lookup"><span data-stu-id="b3113-121">Re-add your migration</span></span>

<span data-ttu-id="b3113-122">执行此操作后，可以按正确的顺序应用两个迁移。</span><span class="sxs-lookup"><span data-stu-id="b3113-122">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="b3113-123">首先应用其迁移，将列重命名为*Alias*，此后，迁移会将其重命名为*Username*。</span><span class="sxs-lookup"><span data-stu-id="b3113-123">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="b3113-124">可以安全地与团队的其他成员共享你的迁移。</span><span class="sxs-lookup"><span data-stu-id="b3113-124">Your migration can safely be shared with the rest of the team.</span></span>

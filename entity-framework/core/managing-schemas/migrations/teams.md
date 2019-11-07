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
# <a name="migrations-in-team-environments"></a>团队环境中的迁移

在团队环境中使用迁移时，请特别注意模型快照文件。 此文件可以告诉你队友的迁移是否与你的迁移完全合并，或者是否需要在共享之前重新创建迁移来解决冲突。

## <a name="merging"></a>合并

合并团队成员的迁移时，可能会在模型快照文件中出现冲突。 如果这两个更改不相关，则合并很简单，两个迁移可以共存。 例如，你可能会在客户实体类型配置中出现合并冲突，如下所示：

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

由于这两个属性都需要存在于最终模型中，因此请通过添加这两个属性来完成合并。 在许多情况下，版本控制系统可能会自动合并此类更改。

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

在这些情况下，迁移和队友的迁移彼此独立。 由于可以首先应用其中的任何一个，因此在与团队共享之前，无需对迁移进行任何其他更改。

## <a name="resolving-conflicts"></a>解决冲突

当合并模型快照模型时，有时会遇到真正冲突。 例如，你和你的队友可以重命名同一个属性。

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

如果遇到这种冲突，请通过重新创建迁移解决此问题。 请执行这些步骤：

1. 在合并前中止合并并回退到工作目录
2. 删除你的迁移（但保留你的模型更改）
3. 将队友的更改合并到工作目录
4. 重新添加迁移

执行此操作后，可以按正确的顺序应用两个迁移。 首先应用其迁移，将列重命名为*Alias*，此后，迁移会将其重命名为*Username*。

可以安全地与团队的其他成员共享你的迁移。

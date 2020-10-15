---
title: 使用现有数据库 Code First 迁移-EF6
description: 使用实体框架6中的现有数据库 Code First 迁移
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/existing-database
ms.openlocfilehash: 239948809083acdcd42ab569a947548cd561697d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065103"
---
# <a name="code-first-migrations-with-an-existing-database"></a>使用现有数据库 Code First 迁移
> [!NOTE]
> **Ef 4.3 仅向前** -在实体框架4.1 中引入了本页中所述的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。

本文介绍如何将 Code First 迁移与现有数据库（不是由实体框架创建的数据库）结合使用。

> [!NOTE]
> 本文假设你知道如何在基本方案中使用 Code First 迁移。 如果没有，则需要先阅读 [Code First 迁移](xref:ef6/modeling/code-first/migrations/index) ，然后再继续。

## <a name="screencasts"></a>屏幕广播

如果你想要观看 screencast 而不是阅读本文，以下两个视频会涵盖与本文相同的内容。

### <a name="video-one-migrations---under-the-hood"></a>视频一： "迁移-在后台"

[此 screencast](https://channel9.msdn.com/blogs/ef/migrations-under-the-hood) 介绍了迁移如何跟踪并使用有关模型的信息来检测模型更改。

### <a name="video-two-migrations---existing-databases"></a>视频两个： "迁移-现有数据库"

基于前一视频中的概念生成， [此 screencast](https://channel9.msdn.com/blogs/ef/migrations-existing-databases) 介绍了如何启用和使用现有数据库的迁移。

## <a name="step-1-create-a-model"></a>步骤1：创建模型

第一步是创建一个面向现有数据库的 Code First 模型。 " [Code First 现有数据库](xref:ef6/modeling/code-first/workflows/existing-database) " 主题提供有关如何执行此操作的详细指导。

>[!NOTE]
> 在对模型进行任何需要更改数据库架构的更改之前，请务必遵循本主题中的其余步骤。 以下步骤要求模型与数据库架构保持同步。

## <a name="step-2-enable-migrations"></a>步骤2：启用迁移

下一步是启用迁移。 可以通过在 Package Manager Console 中运行 " **启用-迁移** " 命令来执行此操作。

此命令将在你的解决方案中创建一个名为 "迁移" 的文件夹，并在其中放置一个名为 "配置" 的类。 配置类是你为应用程序配置迁移的位置，你可以在 [Code First 迁移](xref:ef6/modeling/code-first/migrations/index) 主题中找到有关它的详细信息。

## <a name="step-3-add-an-initial-migration"></a>步骤3：添加初始迁移

创建迁移并将迁移应用到本地数据库后，您可能还需要将这些更改应用到其他数据库。 例如，您的本地数据库可能是一个测试数据库，您可能最终还需要对生产数据库和/或其他开发人员测试数据库应用所做的更改。 此步骤有两个选项，应选择的选项取决于任何其他数据库的架构是否为空，或当前是否与本地数据库的架构匹配。

-   **选项一：使用现有架构作为起始点。** 如果将来将应用迁移到的其他数据库将具有与本地数据库当前具有的架构相同的架构，则应使用此方法。 例如，如果本地测试数据库当前与生产数据库的 v1 相匹配，则可以使用此项，稍后会应用这些迁移，将生产数据库更新为 v2。
-   **选项2：使用空数据库作为起始点。** 如果将来将应用迁移到的其他数据库为空 (或) 尚不存在，则应使用此方法。 例如，如果使用测试数据库开始开发应用程序，但不使用迁移，则可以使用此示例，以后要从头开始创建生产数据库。

### <a name="option-one-use-existing-schema-as-a-starting-point"></a>选项一：使用现有架构作为起始点

Code First 迁移使用最新迁移中存储的模型的快照来检测对模型所做的更改 (可以在 [团队环境](xref:ef6/modeling/code-first/migrations/teams)) 中 Code First 迁移找到有关此内容的详细信息。 由于我们将假设数据库已具有当前模型的架构，因此，我们将生成一个空的 (无操作) 迁移，将当前模型作为快照。

1.  在 Package Manager Console 中运行 **Add-迁移 InitialCreate – IgnoreChanges** 命令。 这将创建一个空迁移，并将当前模型作为快照。
2.  在 Package Manager Console 中运行 " **更新数据库** " 命令。 这会将 InitialCreate 迁移应用到数据库。 由于实际迁移不包含任何更改，因此只需在 MigrationsHistory 表中添加一行 \_ \_ ，指出已应用此迁移。

### <a name="option-two-use-empty-database-as-a-starting-point"></a>选项2：使用空数据库作为起始点

在此方案中，我们需要迁移才能从头开始创建整个数据库（包括本地数据库中已存在的表）。 我们将生成一个 InitialCreate 迁移，其中包含用于创建现有架构的逻辑。 然后，将使现有的数据库看起来像此迁移已应用。

1.  在 Package Manager Console 中运行 **Add-迁移 InitialCreate** 命令。 这将创建一个迁移来创建现有架构。
2.  注释掉新创建的迁移的 Up 方法中的所有代码。 这样，我们就可以 "应用" 将迁移到本地数据库，而无需尝试重新创建所有表，等等。
3.  在 Package Manager Console 中运行 " **更新数据库** " 命令。 这会将 InitialCreate 迁移应用到数据库。 由于实际迁移不包含任何更改 (因为我们暂时将其注释掉) ，所以只需在 MigrationsHistory 表中添加一行， \_ \_ 指出已应用此迁移。
4.  取消注释 Up 方法中的代码。 这意味着，将此迁移应用于未来的数据库时，迁移将创建本地数据库中已经存在的架构。

## <a name="things-to-be-aware-of"></a>注意事项

对现有数据库使用迁移时，需要注意几个事项。

### <a name="defaultcalculated-names-may-not-match-existing-schema"></a>默认/计算的名称可能与现有架构不匹配

迁移时，迁移会显式指定列和表的名称基架。 但是，在应用迁移时，迁移会为其他数据库对象计算默认名称。 这包括索引和外键约束。 当以现有架构为目标时，这些计算的名称可能与数据库中实际存在的名称不匹配。

下面是需要注意的一些示例：

**如果使用步骤3中的 "选项一：使用现有架构作为起点"：**

-   如果模型中的未来更改需要更改或删除一个名为不同的数据库对象，则需要修改基架迁移来指定正确的名称。 迁移 Api 有一个可选的 Name 参数，可让你执行此操作。
    例如，你的现有架构可能有一个带有名为 IndexFk BlogId 的索引的 BlogId 外键列的 Post 表 \_ 。 但是，默认情况下，迁移会将此索引命名为 IX \_ BlogId。 如果对模型进行更改，导致删除此索引，则需要修改基架 DropIndex 调用以指定 IndexFk \_ BlogId 名称。

**如果使用步骤3中的 "Option 2：使用空数据库作为起始点"：**

-   尝试运行初始迁移的关闭方法 (即，针对本地数据库还原到空数据库) 可能会失败，因为迁移将尝试使用不正确的名称删除索引和外键约束。 这只会影响本地数据库，因为将使用初始迁移的 Up 方法从头开始创建其他数据库。
    如果要将现有的本地数据库降级到空状态，可以通过删除数据库或删除所有表，手动执行此操作，这是最简单的方法。 在此初始降级后，将用默认名称重新创建所有数据库对象，因此，此问题不会再次出现。
-   如果您的模型中的未来更改需要更改或删除一个名为不同的数据库对象，则这对您的现有本地数据库不起作用，因为这些名称与默认值不匹配。 但是，它将适用于 "从头开始创建" 的数据库，因为它们将使用迁移选择的默认名称。
    您可以在本地现有数据库上手动进行这些更改，也可以考虑让迁移从头开始重新创建数据库，因为它将在其他计算机上进行。
-   使用初始迁移的 Up 方法创建的数据库可能与本地数据库略有不同，因为将使用计算的索引和外键约束的默认名称。 你还可能最终会获得额外的索引，因为迁移将默认在外键列上创建索引-这可能不是你的原始本地数据库中的情况。

### <a name="not-all-database-objects-are-represented-in-the-model"></a>不是所有数据库对象都是在模型中表示的

不属于您的模型的数据库对象将不会被迁移处理。 这可能包括视图、存储过程、权限、不是模型一部分的表、附加索引等。

下面是需要注意的一些示例：

-   无论你在 "步骤 3" 中选择哪个选项，如果将来对模型进行更改，则需要更改或删除这些其他对象迁移将不知道进行这些更改。 例如，如果您删除了一个具有其他索引的列，迁移将不知道删除该索引。 你将需要手动将其添加到基架迁移。
-   如果使用了 "Option 2：使用空数据库作为起始点"，则将不会通过初始迁移的 "向上" 方法创建这些附加的对象。
    如果需要，可以修改向上和向下方法来处理这些额外的对象。 对于迁移 API 中不是本机支持的对象（如视图），可以使用 [Sql](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.sql.aspx) 方法运行原始 Sql 来创建/删除它们。

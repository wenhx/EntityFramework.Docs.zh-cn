---
title: 创建和删除 Api-EF Core
description: 用于创建和删除具有 Entity Framework Core 的数据库的 Api
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 25e7352269531e881e83e44ea90108f12d4dcbea
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619225"
---
# <a name="create-and-drop-apis"></a>创建和删除 API

EnsureCreated 和 EnsureDeleted 方法提供了用于 [迁移](xref:core/managing-schemas/migrations/index) 的轻型替代方法，用于管理数据库架构。 这些方法在以下情况下很有用：数据是暂时性的，可以在架构更改时删除。 例如，在原型中、在测试中，或在本地缓存中。

某些提供程序 () 不支持迁移的特别是非关系提供程序。 对于这些提供程序，EnsureCreated 通常是初始化数据库架构的最简单方法。

> [!WARNING]
> EnsureCreated 和迁移不能很好地协同工作。 如果使用的是迁移，请不要使用 EnsureCreated 来初始化架构。

从 EnsureCreated 过渡到迁移并不是一种流畅的体验。 要执行此操作，最简单的方法是删除数据库，然后使用迁移重新创建数据库。 如果预计将来使用迁移，最好只开始迁移，而不是使用 EnsureCreated。

## <a name="ensuredeleted"></a>EnsureDeleted

EnsureDeleted 方法会删除数据库（如果存在）。 如果你没有相应的权限，则会引发异常。

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

如果数据库不存在，EnsureCreated 将创建数据库并初始化数据库架构。 如果存在任何表 (包括其他 DbContext 类) 的表，则不会初始化该架构。

``` csharp
// Create the database if it doesn't exist
dbContext.Database.EnsureCreated();
```

> [!TIP]
> 这些方法的异步版本也可用。

## <a name="sql-script"></a>SQL 脚本

若要获取 EnsureCreated 使用的 SQL，可以使用 GenerateCreateScript 方法。

``` csharp
var sql = dbContext.Database.GenerateCreateScript();
```

## <a name="multiple-dbcontext-classes"></a>多个 DbContext 类

EnsureCreated 仅在数据库中不存在任何表时有效。 如果需要，您可以编写自己的检查来查看是否需要初始化架构，并使用基础 IRelationalDatabaseCreator 服务来初始化架构。

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```

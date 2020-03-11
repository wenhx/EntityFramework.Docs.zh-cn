---
title: 创建和删除 Api-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2018
uid: core/managing-schemas/ensure-created
ms.openlocfilehash: 32ac6cd043df73cd041780ec4c8805675adc5ab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414319"
---
# <a name="create-and-drop-apis"></a>创建和删除 API

EnsureCreated 和 EnsureDeleted 方法提供了用于[迁移](migrations/index.md)的轻型替代方法，用于管理数据库架构。 这些方法在以下情况下很有用：数据是暂时性的，可以在架构更改时删除。 例如在原型制作、测试期间或用于本地缓存。

某些提供程序（尤其是非关系的）不支持迁移。 对于这些提供程序，EnsureCreated 通常是初始化数据库架构的最简单方法。

> [!WARNING]
> EnsureCreated 和迁移不能很好地协同工作。 如果使用了“迁移”，则不要使用 EnsureCreated 来初始化架构。

从 EnsureCreated 到“迁移”的过渡不是无缝体验。 要执行此操作，最简单的方法是删除数据库，然后使用迁移重新创建数据库。 如果预计将来使用迁移，最好只开始迁移，而不是使用 EnsureCreated。

## <a name="ensuredeleted"></a>EnsureDeleted

如果数据库存在，EnsureDeleted 方法会将其删除。 如果你没有相应的权限，则会引发异常。

``` csharp
// Drop the database if it exists
dbContext.Database.EnsureDeleted();
```

## <a name="ensurecreated"></a>EnsureCreated

如果数据库不存在，EnsureCreated 将创建该数据库，并初始化该数据库的架构。 如果存在任何表 （包括另一个 DbContext 类的表），则不会初始化架构。

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

仅当数据库中不存在表格时，EnsureCreated 才会起作用。 如果有必要，也可以编写自己的检查，来查看架构是否需要进行初始化，并可通过基础 IRelationalDatabaseCreator 服务来初始化架构。

``` csharp
// TODO: Check whether the schema needs to be initialized

// Initialize the schema for this DbContext
var databaseCreator = dbContext.GetService<IRelationalDatabaseCreator>();
databaseCreator.CreateTables();
```

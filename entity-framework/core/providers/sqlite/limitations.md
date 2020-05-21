---
title: SQLite 数据库提供程序-限制-EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: 17e97da9dfffefeb507fde744b710e6936bff69b
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672776"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core 数据库提供程序限制

SQLite 提供程序有很多迁移限制。 其中的大多数限制是由基础 SQLite 数据库引擎中的限制引起的，并不特定于 EF。

## <a name="modeling-limitations"></a>建模限制

公共关系库（由实体框架关系数据库提供程序共享）定义了用于建模大多数关系数据库引擎所共有的概念的 Api。 SQLite 提供程序不支持其中几个概念。

* 架构
* 序列

## <a name="query-limitations"></a>查询限制

SQLite 本身并不支持以下数据类型。 EF Core 可以读取和写入这些类型的值，也支持查询是否相等（ `where e.Property == value` ）。 但其他操作（如比较和排序）将需要对客户端进行评估。

* DateTimeOffset
* 小数
* TimeSpan
* UInt64

`DateTimeOffset`建议使用 DateTime 值，而不是。 处理多个时区时，建议在保存之前将值转换为 UTC，然后将其转换回适当的时区。

`Decimal`类型提供了较高的精度。 但是，如果不需要该级别的精度，则建议改为使用 double。 您可以使用[值转换器](../../modeling/value-conversions.md)在类中继续使用 decimal。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>迁移限制

SQLite 数据库引擎不支持许多其他关系数据库所支持的架构操作。 如果尝试将不受支持的操作之一应用于 SQLite 数据库，则 `NotSupportedException` 会引发。

| 操作            | 支持？ | 需要版本 |
|:---------------------|:-----------|:-----------------|
| AddColumn            | ✔          | 1.0              |
| AddForeignKey        | ✗          |                  |
| AddPrimaryKey        | ✗          |                  |
| AddUniqueConstraint  | ✗          |                  |
| AlterColumn          | ✗          |                  |
| CreateIndex          | ✔          | 1.0              |
| CreateTable          | ✔          | 1.0              |
| DropColumn           | ✗          |                  |
| DropForeignKey       | ✗          |                  |
| DropIndex            | ✔          | 1.0              |
| DropPrimaryKey       | ✗          |                  |
| DropTable            | ✔          | 1.0              |
| DropUniqueConstraint | ✗          |                  |
| RenameColumn         | ✔          | 2.2.2            |
| RenameIndex          | ✔          | 2.1              |
| RenameTable          | ✔          | 1.0              |
| EnsureSchema         | ✔（无操作）  | 2.0              |
| DropSchema           | ✔（无操作）  | 2.0              |
| 插入               | ✔          | 2.0              |
| 更新               | ✔          | 2.0              |
| 删除               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>迁移限制解决方法

通过在迁移中手动编写代码来执行表重新生成，可以解决其中一些限制。 表重新生成包括重命名现有表、创建新表、将数据复制到新表和删除旧表。 你将需要使用 `Sql(string)` 方法来执行其中一些步骤。

有关更多详细信息，请参阅在 SQLite 文档中[进行其他类型的表架构更改](https://sqlite.org/lang_altertable.html#otheralter)。

将来，EF 可以通过使用表中的 "重新生成" 方法来支持这些操作。 你可以[在 GitHub 项目上跟踪此功能](https://github.com/aspnet/EntityFrameworkCore/issues/329)。

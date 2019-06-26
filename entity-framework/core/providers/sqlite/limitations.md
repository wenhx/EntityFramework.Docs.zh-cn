---
title: SQLite 数据库提供程序的限制的 EF Core
author: rowanmiller
ms.date: 04/09/2017
ms.assetid: 94ab4800-c460-4caa-a5e8-acdfee6e6ce2
uid: core/providers/sqlite/limitations
ms.openlocfilehash: eaa7d5b1496172e4f3821433a1cd098ee7e8b737
ms.sourcegitcommit: 9bd64a1a71b7f7aeb044aeecc7c4785b57db1ec9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394804"
---
# <a name="sqlite-ef-core-database-provider-limitations"></a>SQLite EF Core 数据库提供程序限制

SQLite 提供程序具有许多迁移限制。 其中大多数限制是基础的 SQLite 数据库引擎中的限制的结果并不特定于 EF。

## <a name="modeling-limitations"></a>建模限制

（由实体框架关系数据库提供程序共享） 的公共关系库定义建模概念所共有的大多数关系数据库引擎的 Api。 SQLite 提供程序不支持几个这些概念。

* 架构
* 序列
* 计算的列

## <a name="query-limitations"></a>查询限制

SQLite 不以本机方式支持以下数据类型。 EF Core 可以读取和写入这些类型和查询是否相等的值 (`where e.Property == value`)，它还支持。 其他操作，但是，如比较和排序将需要在客户端上的评估。

* DateTimeOffset
* 十进制
* TimeSpan
* UInt64

而不是`DateTimeOffset`，我们建议使用日期时间值。 在处理多个时区，我们建议将值转换为 UTC 之前保存并将转换回相应的时区。

`Decimal`类型提供了高的精度。 如果不需要该级别的精度，但是，建议改为使用双精度。 可以使用[值转换器](../../modeling/value-conversions.md)若要继续使用您的类中的小数。

``` csharp
modelBuilder.Entity<MyEntity>()
    .Property(e => e.DecimalProperty)
    .HasConversion<double>();
```

## <a name="migrations-limitations"></a>迁移限制

SQLite 数据库引擎不支持多个架构操作支持的其他关系数据库的大多数。 如果你尝试将其中一个不受支持的操作应用到 SQLite 数据库则`NotSupportedException`将引发。

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
| EnsureSchema         | ✔ (no-op)  | 2.0              |
| DropSchema           | ✔ (no-op)  | 2.0              |
| Insert               | ✔          | 2.0              |
| 更新               | ✔          | 2.0              |
| 删除               | ✔          | 2.0              |

## <a name="migrations-limitations-workaround"></a>迁移限制的解决方法

可以解决一些通过手动编写代码在你迁移执行表中的这些限制的重新生成。 表重新生成包括重命名现有表、创建新表、将数据复制到新表和删除旧表。 将需要使用`Sql(string)`方法来执行这些步骤中的一部分。

请参阅[进行其他类型的表架构更改](http://sqlite.org/lang_altertable.html#otheralter)SQLite 文档了解详细信息中。

将来，EF 可能支持某些操作使用在后台的表重新生成方法。 你可以[跟踪此功能在我们的 GitHub 项目](https://github.com/aspnet/EntityFrameworkCore/issues/329)。

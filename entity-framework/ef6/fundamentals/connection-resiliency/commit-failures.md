---
title: 处理事务提交失败-EF6
description: 处理实体框架6中的事务提交失败
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
uid: ef6/fundamentals/connection-resiliency/commit-failures
ms.openlocfilehash: 10cb67837264bea37a0621aa078e1753af954c2f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616282"
---
# <a name="handling-transaction-commit-failures"></a>处理事务提交失败

> [!NOTE]
> **Ef 6.1 仅向前** -在实体框架6.1 中引入了本页中讨论的功能、api 等。 如果使用的是早期版本，则部分或全部信息不适用。  

作为6.1 的一部分，我们将为 EF 引入新的连接复原功能：当暂时性连接故障影响事务提交确认时，自动检测和恢复。 有关该方案的完整详细信息，请参阅博客文章 [SQL 数据库连接和幂等性问题](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)。  总的来说，方案是在事务提交期间引发异常时，有两个可能的原因：  

1. 服务器上的事务提交失败
2. 服务器上的事务提交成功，但连接问题阻止成功通知到达客户端  

在第一种情况下，应用程序或用户可以重试该操作，但当发生第二种情况时，应避免重试，并且应用程序可能会自动恢复。 难点在于，如果不能检测到在提交期间报告异常的实际原因，应用程序将无法选择正确的操作过程。 EF 6.1 中的新功能允许 EF 在事务成功并以透明方式执行正确操作的过程中仔细检查数据库。  

## <a name="using-the-feature"></a>使用功能  

若要启用此功能，需要在**DbConfiguration**的构造函数中包括对[SetTransactionHandler](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx)的调用。 如果不熟悉 **DbConfiguration**，请参阅 [基于代码的配置](xref:ef6/fundamentals/configuring/code-based)。 此功能可以与我们在 EF6 中引入的自动重试结合使用，这有助于在发生暂时性故障的情况下，事务实际上未能在服务器上提交：  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

public class MyConfiguration : DbConfiguration  
{
  public MyConfiguration()  
  {  
    SetTransactionHandler(SqlProviderServices.ProviderInvariantName, () => new CommitFailureHandler());  
    SetExecutionStrategy(SqlProviderServices.ProviderInvariantName, () => new SqlAzureExecutionStrategy());  
  }  
}
```  

## <a name="how-transactions-are-tracked"></a>如何跟踪事务  

启用此功能后，EF 会自动将新表添加到名为 **__Transactions**的数据库。 在此表中，每次创建事务时都会在此表中插入一个新行，并在提交期间发生事务失败时检查该行是否存在。  

尽管 EF 在不再需要时可以最大程度地修剪表中的行，但如果应用程序过早退出，则表可能会增长，因此，在某些情况下，可能需要手动清除该表。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>如何处理以前版本的提交失败

EF 6.1 之前没有处理 EF 产品中的提交失败的机制。 可以通过多种方式来处理可应用于早期版本的 EF6 的情况：  

* 选项 1-不执行任何操作  

  在事务提交期间连接失败的可能性较低，因此，如果实际发生此情况，应用程序就会失败。  

* 选项 2-使用数据库重置状态  

  1. 放弃当前 DbContext  
  2. 创建新的 DbContext 并从数据库还原应用程序的状态  
  3. 通知用户上一次操作可能未成功完成  

* 选项 3-手动跟踪事务  

  1. 将非跟踪表添加到用于跟踪事务状态的数据库。  
  2. 在每个事务开头的表中插入一行。  
  3. 如果在提交期间连接失败，请检查数据库中是否存在相应的行。  
     * 如果行存在，则继续正常，因为事务已成功提交  
     * 如果该行不存在，请使用执行策略重试当前操作。  
  4. 如果提交成功，则删除相应的行以避免表增长。  

[此博客文章](/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue) 包含 SQL Azure 的示例代码。  

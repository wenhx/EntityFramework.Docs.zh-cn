---
title: 处理事务提交失败 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 5b1f7a7d-1b24-4645-95ec-5608a31ef577
ms.openlocfilehash: cf2722496e207a8ecaa9cfaa4ca61e7248e5e58f
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434131"
---
# <a name="handling-transaction-commit-failures"></a>处理事务提交失败
> [!NOTE]
> **仅 EF6.1 继续**- 本页中讨论的功能、API 等在实体框架 6.1 中介绍。 如果使用的是早期版本，则部分或全部信息不适用。  

作为 6.1 的一部分，我们引入了 EF 的新连接恢复功能：当瞬态连接故障影响事务提交确认时自动检测和恢复的能力。 场景的完整细节最好在博客文章[SQL 数据库连接和阳萎问题](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)中描述。  总之，当在事务提交期间引发异常时，有两个可能的原因：  

1. 服务器上的事务提交失败
2. 事务提交在服务器上成功，但连接问题阻止成功通知到达客户端  

当第一种情况发生时，应用程序或用户可以重试操作，但当第二种情况发生时，应避免重试，并且应用程序可以自动恢复。 挑战在于，如果没有能力检测在提交过程中报告异常的实际原因，应用程序无法选择正确的操作方案。 EF 6.1 中的新功能允许 EF 在事务成功时与数据库进行仔细检查，并透明地执行正确的操作过程。  

## <a name="using-the-feature"></a>使用功能  

为了启用该功能，您需要在**Db 配置**的构造函数中调用[SetTransactionHandler。](https://msdn.microsoft.com/library/system.data.entity.dbconfiguration.setdefaulttransactionhandler.aspx) 如果您不熟悉 Db**配置**，请参阅[基于代码的配置](~/ef6/fundamentals/configuring/code-based.md)。 此功能可与我们在 EF6 中引入的自动重试结合使用，这有助于在事务因暂时性故障而实际无法在服务器上提交的情况：  

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

## <a name="how-transactions-are-tracked"></a>如何跟踪交易记录  

启用此功能后，EF 将自动向名为 **__Transactions**的数据库添加新表。 每次 EF 创建事务时，都会在此表中插入一个新行，如果提交期间发生事务失败，则检查该行是否存在。  

尽管 EF 将尽力在不再需要表时修剪行，但如果应用程序过早退出，则该表可能会增长，因此在某些情况下您可能需要手动清除表。  

## <a name="how-to-handle-commit-failures-with-previous-versions"></a>如何处理具有早期版本的提交失败

在 EF 6.1 之前，没有处理 EF 产品中的提交故障的机制。 有几种方法可以处理这种情况，可以应用于 EF6 的早期版本：  

* 选项 1 - 不执行任何操作  

  事务提交期间连接失败的可能性很低，因此，如果实际发生此情况，应用程序可能会失败。  

* 选项 2 - 使用数据库重置状态  

  1. 放弃当前数据库上下文  
  2. 创建新的 DbContext 并从数据库还原应用程序的状态  
  3. 通知用户上次操作可能尚未成功完成  

* 选项 3 - 手动跟踪交易  

  1. 将非跟踪表添加到用于跟踪事务状态的数据库。  
  2. 在每个事务开始时将一行插入到表中。  
  3. 如果在提交过程中连接失败，请检查数据库中是否存在相应的行。  
     - 如果行存在，请正常继续，因为事务已成功提交  
     - 如果行不存在，请使用执行策略重试当前操作。  
  4. 如果提交成功，请删除相应的行以避免表的增长。  

[此博客文章](https://docs.microsoft.com/archive/blogs/adonet/sql-database-connectivity-and-the-idempotency-issue)包含用于在 SQL Azure 上完成此任务的示例代码。  

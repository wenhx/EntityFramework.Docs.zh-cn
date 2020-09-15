---
title: 使用 DbContext-EF6
description: 使用实体框架6中的 DbContext
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 83f3f318c6cd1b0ea440bd472a651ff4454716f1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072143"
---
# <a name="working-with-dbcontext"></a>使用 DbContext

若要使用实体框架来使用 .NET 对象查询、插入、更新和删除数据，您首先需要 [创建一个模型](xref:ef6/modeling/index) ，该模型将模型中定义的实体和关系映射到数据库中的表。

有了模型后，应用程序与之交互的主类 `System.Data.Entity.DbContext` (通常称为上下文类) 。 您可以使用与模型关联的 DbContext 来执行以下操作：
- 编写和执行查询   
- 将查询结果具体化为实体对象
- 跟踪对这些对象进行的更改
- 将对象更改保存回数据库
- 将内存中的对象绑定到 UI 控件

本页提供有关如何管理上下文类的一些指导。  

## <a name="defining-a-dbcontext-derived-class"></a>定义 DbContext 派生类  

使用上下文的建议方法是定义从 DbContext 派生的类，并公开 DbSet 属性，这些属性表示上下文中指定实体的集合。 如果使用的是 EF 设计器，则会为您生成上下文。 如果使用 Code First，则通常会自行编写上下文。  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

有了上下文后，您就可以使用或方法来查询、添加 (， `Add` `Attach` ) 或) 使用 `Remove` 这些属性在上下文中删除 (。 访问 `DbSet` 上下文对象上的属性表示一个启动查询，该查询返回指定类型的所有实体。 请注意，仅访问属性不会执行查询。 执行以下操作时执行查询：  

- `foreach` (C#) 或 `For Each` (Visual Basic) 语句枚举对象。  
- 它通过集合操作（如、或）进行枚举 `ToArray` `ToDictionary` `ToList` 。  
- `First` `Any` 在查询的最外面部分指定 LINQ 运算符（如或）。  
- `Load` `DbEntityEntry.Reload` `Database.ExecuteSqlCommand` 如果在 `DbSet<T>.Find` 上下文中未找到具有指定键的实体，则调用以下方法之一：扩展方法、、和。  

## <a name="lifetime"></a>生存期  

上下文的生存期在实例创建时开始，并在实例被释放或被回收时结束。 如果希望在块的末尾释放上下文控制的所有资源，**请使用。** **使用时，编译器**会自动创建 try/finally 块并在**finally**块中调用 dispose。  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

下面是决定上下文生存期时的一些一般指导原则：  

- 使用 Web 应用程序时，请为每个请求使用上下文实例。  
- 使用 Windows Presentation Foundation (WPF) 或 Windows 窗体时，请在每个窗体中使用上下文实例。 这使你可以使用上下文提供的更改跟踪功能。  
- 如果上下文实例是由依赖关系注入容器创建的，则该容器通常负责释放上下文。
- 如果上下文是在应用程序代码中创建的，请记得在不再需要时释放上下文。  
- 使用长时间运行的上下文时，请考虑以下事项：  
    - 在将更多对象及其引用加载到内存中时，上下文的内存消耗可能会迅速增加。 这可能会导致性能问题。  
    - 上下文不是线程安全的，因此不应在多个线程上同时对其执行工作。
    - 如果异常导致上下文处于不可恢复的状态，则整个应用程序可能会终止。  
    - 随着查询数据的时间和更新数据的时间的差距增大，出现与并发性相关的问题的可能性将会增加。  

## <a name="connections"></a>连接  

默认情况下，上下文管理与数据库的连接。 上下文会根据需要打开和关闭连接。 例如，上下文打开一个连接来执行查询，然后在处理完所有结果集后关闭连接。  

在某些情况下，您需要加强控制应在哪些情况下打开和关闭连接。 例如，使用 SQL Server Compact 时，通常建议在应用程序的生存期内维护单独的数据库打开连接，以提高性能。 您可以使用 `Connection` 属性手动管理此过程。  

---
title: EF Core 3.0 中的中断性变更 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: EE2878C9-71F9-4FA5-9BC4-60517C7C9830
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: a7e1a03bf1131cd53123f5cc39b07bed94619b44
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463359"
---
# <a name="breaking-changes-included-in-ef-core-30-currently-in-preview"></a>EF Core 3.0 中包含的中断性变更（目前处于预览状态）

> [!IMPORTANT]
> 请注意，将来版本的功能集和计划会发生更改，尽管我们会尽力使此页面保持最新状态，但它可能不会始终反映我们的最新计划。

以下 API 和行为更改有可能在将 EF Core 2.2.x 升级到 3.0.0 时中断为其开发的应用程序。
我们将仅影响数据库提供程序的更改记录在[提供程序更改](../../providers/provider-log.md)下。
其中没有记录从一个 3.0 预览版引入到另一个 3.0 预览版的新功能的中断。

## <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a>不再在客户端上计算 LINQ 查询

[跟踪问题 #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)

> [!IMPORTANT]
> 我们会预先宣布此中断。
它尚未在任何 3.0 预览版中发布。

**旧行为**

在 3.0 之前，当 EF Core 无法将查询中的表达式转换为 SQL 或参数时，它会在客户端上自动计算表达式的值。
默认情况下，客户端对潜在的昂贵表达式的计算仅触发警告。

**新行为**

从 3.0 开始，EF Core 仅允许在客户端上计算顶级投影中的表达式（查询中的最后一个 `Select()` 调用）。
当查询的任何其他部分中的表达式无法转换为 SQL 或参数时，将引发异常。

**为什么**

自动的客户端查询计算允许执行许多查询，即使它们的重要组成部分无法转换。
此行为可能导致意外且具有潜在破坏性的行为，这些行为可能仅在生产中变得明显。
例如，`Where()` 调用中无法转换的条件可能导致表中的所有行从数据库服务器传输且筛选器应用于客户端。
如果在开发中表中只包含几行，则不容易检测到这种情况，但是当应用程序转入生产环节时，由于表中可能包含数百万行，这种情况会非常严重。
在开发过程中，客户端求值警告也很容易被忽视。

除此之外，自动客户端计算可能会导致问题，其中改进特定表达式的查询转换会导致版本之间发生意外中断性变更。

**缓解措施**

如果无法完全转换查询，则以可转换的形式重写查询，或使用 `AsEnumerable()`、`ToList()` 或类似信息将数据显式返回客户端，然后可以进一步使用 LINQ 到对象处理。

## <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a>Entity Framework Core 不再是 ASP.NET Core 共享框架的一部分

[跟踪问题公告 #325](https://github.com/aspnet/Announcements/issues/325)

此更改是在 ASP.NET Core 3.0-preview 1 中引入的。 

**旧行为**

在 ASP.NET Core 3.0 之前，当向 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 添加包引用时，它将包括 EF Core 和一些 EF Core 数据提供程序（如 SQL Server 提供程序）。

**新行为**

从 3.0 开始，ASP.NET Core 共享框架不包括 EF Core 或任何 EF Core 数据提供程序。

**为什么**

在此更改之前，获取 EF Core 需要不同的步骤，具体取决于应用程序是否是面向 ASP.NET Core 和 SQL Server。 此外，升级 ASP.NET Core 会强制升级 EF Core 和 SQL Server 提供程序，这并不总是可取的。

通过此更改，通过所有提供程序、支持的 .NET 实现和应用程序类型获取 EF Core 的体验都是一致的。
开发人员现在还可以准确控制何时升级 EF Core 和 EF Core 数据提供程序。

**缓解措施**

若要在 ASP.NET Core 3.0 应用程序或任何其他受支持的应用程序中使用 EF Core，请显式添加对应用程序将使用的 EF Core 数据库提供程序的包引用。

## <a name="query-execution-is-logged-at-debug-level"></a>在调试级别记录查询执行

[跟踪问题 #14523](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

EF Core 3.0 之前，是在 `Info` 级别记录查询和其他命令的执行。

**新行为**

从 EF Core 3.0 开始，将在 `Debug` 级别记录命令/SQL 的执行。

**为什么**

此更改是为了降低 `Info` 日志级别的噪音。

**缓解措施**

此日志记录事件由 `RelationalEventId.CommandExecuting` 定义，事件 ID 为 20100。
若要再次在 `Info` 级别记录 SQL，请启用 `Debug` 级别的日志记录并筛选到仅此一个事件。


## <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a>不再在实体实例上设置临时键值

[跟踪问题 #12378](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

此更改是在 EF Core 3.0-preview 2 中引入的。

**旧行为**

在 EF Core 3.0 之前，临时值已分配给所有键属性，这些属性稍后将具有数据库生成的实际值。
通常这些临时值是较大负数。

**新行为**

从 3.0 开始，EF Core 将临时键值存储为实体跟踪信息的一部分，并保持键属性本身不变。

**为什么**

此更改是为了防止当之前由某个 `DbContext` 实例跟踪的实体移动到另一个 `DbContext` 实例时，临时键值错误地变成永久值。 

**缓解措施**

如果主键是存储生成的并且属于 `Added` 状态的实体，则将主键值分配到外键以在实体之间形成关联的应用可能会依赖于旧行为。
可通过以下方式避免：
* 不使用存储生成的密钥。
* 设置导航属性以形成关系，而不是设置外键值。
* 从实体的跟踪信息中获取实际的临时键值。
例如，即使 `blog.Id` 本身尚未设置，`context.Entry(blog).Property(e => e.Id).CurrentValue` 也将返回临时值。

## <a name="detectchanges-honors-store-generated-key-values"></a>DetectChanges 遵循存储生成的键值

[跟踪问题 #14616](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，`DetectChanges` 找到的未跟踪实体将在 `Added` 状态中被跟踪，并在调用 `SaveChanges` 时作为新行插入。

**新行为**

从 EF Core 3.0 开始，如果实体使用生成的键值并设置了某个键值，则将在 `Modified` 状态下跟踪实体。
这意味着假定存在实体的行，并且在调用 `SaveChanges` 时将更新该行。
如果未设置键值，或者实体类型未使用生成的键，则新实体仍将像先前版本一样被作为 `Added` 跟踪。

**为什么**

进行此更改是为了在使用存储生成的键时更轻松、更一致地使用断开连接的实体图。

**缓解措施**

如果将实体类型配置为使用生成的键，但为新实例显式设置了键值，则此更改可能会中断应用程序。
解决方案是显式配置键属性，使其不使用生成的值。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

或使用数据注释：

```C#
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

## <a name="cascade-deletions-now-happen-immediately-by-default"></a>默认情况下，现在会立即发生级联删除

[跟踪问题 #10114](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 3.0 之前，直到调用 SaveChanges 时，EF Core 才会应用级联操作（删除所需主体时或者在切断与所需主体的关系时删除依赖实体）。

**新行为**

从 3.0 开始，一旦检测到触发条件，EF Core 就会应用级联操作。
例如，调用 `context.Remove()` 来删除主体实体将导致所有跟踪的相关必需依赖项也立即设置为 `Deleted`。

**为什么**

此更改是为了改善数据绑定和审核方案的体验，在相关体验中，需要了解在调用 `SaveChanges` _之前_会删除哪些实体。

**缓解措施**

可以通过 `context.ChangedTracker` 上的设置还原以前的行为。
例如:

```C#
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

## <a name="query-types-are-consolidated-with-entity-types"></a>查询类型与实体类型合并

[跟踪问题 #14194](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，[查询类型](xref:core/modeling/query-types)是一种查询未以结构化方式定义主键的数据的方法。
也就是说，查询类型用于映射没有键的实体类型（更可能来自视图，但也可能来自表），而当有可用的键时则使用常规实体类型（更可能来自表，但也可能来自视图）。

**新行为**

现在，查询类型只是一个没有主键的实体类型。
无键实体类型与先前版本中的查询类型具有相同的功能。

**为什么**

这样做是为了减少对查询类型用途的混淆。
具体来说，它们是无键实体类型，因此本质上是只读的，但是不应该仅仅因为实体类型需要是只读的就使用它们。
同样，它们通常映射到视图，但这只是因为视图通常不定义键。

**缓解措施**

API 的以下部分现已过时：
* **`ModelBuilder.Query<>()`** - 需要调用 `ModelBuilder.Entity<>().HasNoKey()` 来将实体类型标记为没有键。
这仍然不会按约定配置，以避免在需要主键但是与约定不匹配时发生配置错误。
* **`DbQuery<>`** - 应使用 `DbSet<>`。
* **`DbContext.Query<>()`** - 应使用 `DbContext.Set<>()`。

## <a name="configuration-api-for-owned-type-relationships-has-changed"></a>从属类型关系的配置 API 已更改

[跟踪问题 #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[跟踪问题 #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[跟踪问题 #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

EF Core 3.0 之前，会在 `OwnsOne` 或 `OwnsMany` 调用之后直接执行所拥有关系的配置。 

**新行为**

从 EF Core 3.0 开始，Fluent API 会使用 `WithOwner()` 为所有者配置导航属性。
例如:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

与所有者之间关系相关的配置现会在 `WithOwner()` 之后关联起来，就像配置其他关系一样。
但从属类型本身的配置仍会在 `OwnsOne()/OwnsMany()` 之后关联。
例如:

```C#
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

此外，使用固有类型目标调用 `Entity()``HasOne()` 或 `Set()` 现将引发异常。

**为什么**

此更改是为了更清晰的区分从属类型本身的配置和与从属类型的_关系_的配置。
这反过来消除了诸如 `HasForeignKey` 之类的方法的模糊性和混淆。

**缓解措施**

更改从属类型关系的配置以使用新的 API 曲面，如上例所示。

## <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a>外键属性约定不再匹配与主体属性相同的名称

[跟踪问题 #13274](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

考虑下列模型：
```C#
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}

```
在 EF Core 3.0 之前，`CustomerId` 属性将按约定用于外键。
但是，如果 `Order` 是从属类型，那么这也会使 `CustomerId` 成为主键，这通常不是预期结果。

**新行为**

从 3.0 开始，如果外键具有与主体属性相同的名称，则 EF Core 不会尝试按约定使用外键属性。
与主体属性名称关联的主体类型名称和与主体属性名称模式关联的导航名称仍然相匹配。
例如:

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```C#
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

**为什么**

此更改是为了避免错误地在从属类型上定义主键属性。

**缓解措施**

如果该属性将成为外键，即为主键的一部分，则显式配置它。

## <a name="each-property-uses-independent-in-memory-integer-key-generation"></a>每个属性使用独立的内存中整数键生成

[跟踪问题 #6872](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

此更改将在 EF Core 3.0-preview 4 中引入。

**旧行为**

在 EF Core 3.0 之前，一个共享值生成器用于所有内存中的整数键属性。

**新行为**

从 EF Core 3.0 开始，每个整数键属性在使用内存数据库时都会获取其自己的值生成器。
此外，如果删除了数据库，则会为所有表重置键生成。

**为什么**

此更改的目的是使内存中的键生成更接近于实际的数据库键生成，并改进在使用内存中的数据库时隔离测试的功能。

**缓解措施**

这可能会中断依赖于特定内存中键值的应用程序。
请考虑不依赖于特定键值，或者进行更新以匹配新行为。

## <a name="backing-fields-are-used-by-default"></a>默认情况下使用支持字段

[跟踪问题 #12430](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

此更改是在 EF Core 3.0-preview 2 中引入的。

**旧行为**

在 3.0 之前，即使已知属性的支持字段，EF Core 仍将默认使用属性 getter 和 setter 方法读取和写入属性值。
例外情况是查询执行，如果已知，将直接设置支持字段。

**新行为**

从 EF Core 3.0 开始，如果已知属性的支持字段，则始终使用支持字段读取和写入该属性。
如果应用程序依赖于编码到 getter 或 setter 方法中的其他行为，则可能导致应用程序中断。

**为什么**

此更改是为了防止 EF Core 在执行涉及实体的数据库操作时默认错误地触发业务逻辑。

**缓解措施**

可以通过在 modelBuilder fluent API 中配置属性访问模式来恢复 3.0 之前的行为。
例如:

```C#
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

## <a name="throw-if-multiple-compatible-backing-fields-are-found"></a>如果找到多个兼容的支持字段，则引发

[跟踪问题 #12523](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

此更改将在 EF Core 3.0-preview 4 中引入。

**旧行为**

在 EF Core 3.0 之前，如果多个字段与查找属性的后备字段的规则匹配，则将基于某种优先顺序选择一个字段。
这可能导致在不明确的情况下使用错误字段。

**新行为**

从 EF Core 3.0 开始，如果多个字段与同一属性匹配，则引发异常。

**为什么**

此更改是为了避免在只有一个字段是正确的情况下无提示地使用另一个字段。

**缓解措施**

具有不明确的支持字段的属性必须具有显式指定的字段。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

## <a name="dbcontextentry-now-performs-a-local-detectchanges"></a>DbContext.Entry 现在执行本地 DetectChanges

[跟踪问题 #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，调用 `DbContext.Entry` 将导致检测到所有被跟踪实体的更改。
这确保了 `EntityEntry` 中暴露的状态是最新的。

**新行为**

从 EF Core 3.0 开始，调用 `DbContext.Entry` 现在只会尝试检测给定实体和与之相关的任何跟踪主体实体的更改。
这意味着可能无法通过调用此方法检测到其他位置的更改，这可能会影响应用程序状态。

请注意，如果 `ChangeTracker.AutoDetectChangesEnabled` 设置为 `false`，则即使是本地更改检测也将被禁用。

导致更改检测的其他方法（例如 `ChangeTracker.Entries` 和 `SaveChanges`）仍然会导致所有被跟踪实体的完整 `DetectChanges`。

**为什么**

此更改是为了提高使用 `context.Entry` 的默认性能。

**缓解措施**

在调用 `Entry` 之前显式调用 `ChgangeTracker.DetectChanges()` 以确保 3.0 之前的行为。

## <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a>默认情况下，字符串和字节数组键不是客户端生成的

[跟踪问题 #14617](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

此更改将在 EF Core 3.0-preview 4 中引入。

**旧行为**

在 EF Core 3.0 之前，可以使用 `string` 和 `byte[]` 键属性，而不需要显式地设置非 null 值。
在这种情况下，键值将在客户端上生成为 GUID，并序列化为 `byte[]` 的字节。

**新行为**

从 EF Core 3.0 开始，将引发异常，指示未设置任何键值。

**为什么**

之所以进行此更改是因为客户端生成的 `string`/`byte[]` 值通常没有用，并且默认行为使得很难以通用方式推断生成的键值。

**缓解措施**

如果没有设置其他非 null 值，则可以通过显式指定键属性应使用生成的值来获得 3.0 之前的行为。
例如，使用 Fluent API：

```C#
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

或使用数据注释：

```C#
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

## <a name="iloggerfactory-is-now-a-scoped-service"></a>ILoggerFactory 现在是一个在一定范围内有效的服务

[跟踪问题 #14698](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，`ILoggerFactory` 被注册为单一实例服务。

**新行为**

从 EF Core 3.0 开始，`ILoggerFactory` 现已注册为作用域。

**为什么**

此更改是为了允许记录器与 `DbContext` 实例关联，从而启用其他功能并删除一些反常行为，例如内部服务提供商爆炸式增长的情况。

**缓解措施**

除非在 EF Core 内部服务提供商上注册和使用自定义服务，否则此更改不应影响应用程序代码。
这并不常见。
在这些情况下，大多数事情仍然有效，但是需要更改依赖于 `ILoggerFactory` 的任何单一实例服务以便以不同的方式获取 `ILoggerFactory`。

如果遇到此类情况，请在 [EF Core GitHub 问题跟踪程序](https://github.com/aspnet/EntityFrameworkCore/issues)上提交一个问题，让我们知道你是如何使用 `ILoggerFactory` 的，以便我们更好地理解今后如何避免这种情况再次发生。

## <a name="idbcontextoptionsextensionwithdebuginfo-merged-into-idbcontextoptionsextension"></a>IDbContextOptionsExtensionWithDebugInfo 合并为 IDbContextOptionsExtension

[跟踪问题 #13552](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

`IDbContextOptionsExtensionWithDebugInfo` 是从 `IDbContextOptionsExtension` 扩展的附加可选接口，以避免在 2.x 发布周期期间对接口进行重大更改。

**新行为**

接口现在合并到 `IDbContextOptionsExtension` 中。

**为什么**

之所以进行此更改，是因为所有接口在概念上是一个接口。

**缓解措施**

任何 `IDbContextOptionsExtension` 实现都需要更新以支持新成员。

## <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a>延迟加载代理不再假定导航属性已完全加载

[跟踪问题 #12780](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

此更改将在 EF Core 3.0-preview 4 中引入。

**旧行为**

在 EF Core 3.0 之前，一旦 `DbContext` 被处置，就无法知道从该上下文获得的实体上的给定导航属性是否已完全加载。
相反，如果导航有一个非 null 值，代理将假定加载一个引用导航，如果导航非空，则假定加载集合导航。
在这些情况下，尝试延迟加载将是无效的。

**新行为**

从 EF Core 3.0 开始，代理会跟踪是否加载了导航属性。
这意味着如果尝试访问在释放了上下文之后加载的导航属性，其结果始终是无操作，即使加载的导航为空或为 null。
相反，即使导航属性是非空集合，尝试访问未加载的导航属性也会引发异常。
如果出现这种情况，则表示应用程序代码在无效时间尝试使用延迟加载，应将应用程序更改为不执行此操作。

**为什么**

此更改是为了在尝试对已释放的 `DbContext` 实例进行延迟加载时使行为保持一致和正确。

**缓解措施**

更新应用程序代码，以避免尝试对已释放的上下文进行延迟加载，或者将其配置为无操作，如异常消息中所述。

## <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a>默认情况下，现在过度创建内部服务提供程序是一个错误

[跟踪问题 #10236](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，对于创建了大量内部服务提供程序的应用程序，将会记录一个警告。

**新行为**

从 EF Core 3.0 开始，现在会考虑此警告，并引发错误和异常。 

**为什么**

此更改是为了通过更明显地暴露这个病态案例来驱动生成更好的应用程序代码。

**缓解措施**

遇到此错误时，最合适的操作是了解根本原因并停止创建如此多的内部服务提供程序。
但是，可以通过 `DbContextOptionsBuilder` 上的配置将错误转换回警告（或忽略）。
例如:

```C#
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

## <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a>关系式：TypeMapping 注释现在只是 TypeMapping

[跟踪问题 #9913](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

此更改是在 EF Core 3.0-preview 2 中引入的。

**旧行为**

类型映射注释的注释名称是“Relational：TypeMapping”。

**新行为**

类型映射注释的注释名称现在是“TypeMapping”。

**为什么**

类型映射现在不仅用于关系数据库提供程序。

**缓解措施**

这只会中断直接作为注释访问类型映射的应用程序，这不常见。
最合适的修复操作是使用 API 曲面来访问类型映射，而不是直接使用注释。

## <a name="totable-on-a-derived-type-throws-an-exception"></a>派生类型上的 ToTable 会引发异常 

[跟踪问题 #11811](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，将忽略调用派生类型的 `ToTable()`，因为只有继承映射策略是 TPH，这是无效的。 

**新行为**

从 EF Core 3.0 开始，同时为在以后的版本中添加 TPT 和 TPC 支持做准备，调用派生类型的 `ToTable()` 现在将引发异常，以避免将来发生意外的映射更改。

**为什么**

目前，将派生类型映射到不同的表是无效的。
这种改变避免了在将来当它变为有效时被中断。

**缓解措施**

删除将派生类型映射到其他表的任何尝试。

## <a name="forsqlserverhasindex-replaced-with-hasindex"></a>用 HasIndex 替换 ForSqlServerHasIndex 

[跟踪问题 #12366](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，`ForSqlServerHasIndex().ForSqlServerInclude()` 提供了一种配置与 `INCLUDE` 一起使用的列的方法。

**新行为**

从 EF Core 3.0 开始，现在支持在关系级别上对索引使用 `Include`。
请使用 `HasIndex().ForSqlServerInclude()`。

**为什么**

此更改是为了将用于索引的 API 与 `Includes` 合并到一个位置，以供所有数据库提供程序使用。

**缓解措施**

使用新的 API，如上所示。

## <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a>EF Core 不再发送 pragma 来执行 SQLite FK

[跟踪问题 #12151](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

此更改是在 EF Core 3.0-preview 3 中引入的。

**旧行为**

在 EF Core 3.0 之前，当打开与 SQLite 的连接时，EF Core 会发送 `PRAGMA foreign_keys = 1`。

**新行为**

从 EF Core 3.0 开始，当打开到 SQLite 的连接时，EF Core 不再发送 `PRAGMA foreign_keys = 1`。

**为什么**

之所以进行此更改，是因为 EF Core 默认使用 `SQLitePCLRaw.bundle_e_sqlite3`，这意味着 FK 强制执行操作在默认情况下是打开的，并且不需要在每次打开连接时显式启用。

**缓解措施**

默认情况下，SQLitePCLRaw.bundle_e_sqlite3 中启用了默认用于 EF Core 的外键。
对于其他情况，可以通过在连接字符串中指定 `Foreign Keys=True` 来启用外键。

## <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundleesqlite3"></a>Microsoft.EntityFrameworkCore.Sqlite 现在依赖于 SQLitePCLRaw.bundle_e_sqlite3

**旧行为**

在 EF Core 3.0 之前，EF Core 使用 `SQLitePCLRaw.bundle_green`。

**新行为**

从 EF Core 3.0 开始，EF Core 使用 `SQLitePCLRaw.bundle_e_sqlite3`。

**为什么**

此更改是为了使 iOS 上使用的 SQLite 版本与其他平台一致。

**缓解措施**

若要在 iOS 上使用本机 SQLite 版本，请配置 `Microsoft.Data.Sqlite` 以使用其他 `SQLitePCLRaw` 捆绑包。

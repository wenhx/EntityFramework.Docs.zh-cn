---
title: EF Core 2.0 中的新增功能 - EF Core
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 28b2180e898b91d233b590b1639674a464f8c679
ms.sourcegitcommit: 0cc9578fd49802789a00c0044b4e57325476ca2e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271431"
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="d7dfa-102">EF Core 2.0 中的新增功能</span><span class="sxs-lookup"><span data-stu-id="d7dfa-102">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="d7dfa-103">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="d7dfa-103">.NET Standard 2.0</span></span>
<span data-ttu-id="d7dfa-104">EF Core 现面向 .NET Standard 2.0，这意味着它可用于 .NET Core 2.0、.NET Framework 4.6.1 以及其他实现 .NET Standard 2.0 的库。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-104">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="d7dfa-105">有关支持功能的更多详细信息，请参阅[支持的 .NET 实现](../platforms/index.md)。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-105">See [Supported .NET Implementations](../platforms/index.md) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="d7dfa-106">建模</span><span class="sxs-lookup"><span data-stu-id="d7dfa-106">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="d7dfa-107">表拆分</span><span class="sxs-lookup"><span data-stu-id="d7dfa-107">Table splitting</span></span>

<span data-ttu-id="d7dfa-108">现可将两个或多个实体类型映射到同一个表，其中主键列处于共享状态，每行对应两个或多个实体。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-108">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="d7dfa-109">要使用表拆分，必须在共享该表的所有实体类型之间配置识别关系（其中外键属性构成主键）：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-109">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```
<span data-ttu-id="d7dfa-110">阅读[有关表拆分的部分](xref:core/modeling/table-splitting)，详细了解此功能。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-110">Read the [section on table splitting](xref:core/modeling/table-splitting) for more information on this feature.</span></span>

### <a name="owned-types"></a><span data-ttu-id="d7dfa-111">固有类型</span><span class="sxs-lookup"><span data-stu-id="d7dfa-111">Owned types</span></span>

<span data-ttu-id="d7dfa-112">固有实体类型可与另一个固有实体类型共享同一 CLR 类型，但是由于它不能仅由 CLR 类型标识，因此必须从另一个实体类型导航到该类型。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-112">An owned entity type can share the same CLR type with another owned entity type, but since it cannot be identified just by the CLR type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="d7dfa-113">包含定义导航的实体是所有者。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-113">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="d7dfa-114">查询所有者时，固有类型将默认包含在内。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-114">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="d7dfa-115">依照约定，将为固有类型创建一个阴影主键，并通过表拆分将其映射到与所有者相同的表。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-115">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="d7dfa-116">这样就可以通过类似于 EF6 中复杂类型的用法来使用固有类型：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-116">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```
<span data-ttu-id="d7dfa-117">阅读[有关固有实体类型的部分](xref:core/modeling/owned-entities)，详细了解此功能。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-117">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="d7dfa-118">模型级别查询筛选器</span><span class="sxs-lookup"><span data-stu-id="d7dfa-118">Model-level query filters</span></span>

<span data-ttu-id="d7dfa-119">EF Core 2.0 包含一个称为“模型级别查询筛选器”的新功能。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-119">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="d7dfa-120">凭借此功能，可在元数据模型（通常为 OnModelCreating）的实体类型上直接定义 LINQ 查询谓词（通常传递给 LINQ Where 查询运算符的布尔表达式）。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-120">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="d7dfa-121">此类筛选器自动应用于涉及这些实体类型（包括通过使用 Include 或直接导航属性引用等方式间接引用的实体类型）的所有 LINQ 查询。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-121">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="d7dfa-122">此功能的一些常见应用如下：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-122">Some common applications of this feature are:</span></span>

- <span data-ttu-id="d7dfa-123">软删除 - 实体类型定义 IsDeleted 属性。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-123">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="d7dfa-124">多租户 - 实体类型定义 TenantId 属性。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-124">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="d7dfa-125">以下简单示例演示了此功能在上述两种方案中的应用：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-125">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId );
    }
}
```
<span data-ttu-id="d7dfa-126">我们为 `Post` 实体类型的实例定义一个实现多租户和软删除的模型级别筛选器。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-126">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the `Post` Entity Type.</span></span> <span data-ttu-id="d7dfa-127">请留意 DbContext 实例级别属性的使用：`TenantId`。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-127">Note the use of a DbContext instance level property: `TenantId`.</span></span> <span data-ttu-id="d7dfa-128">模型级筛选器将使用正确上下文实例（即执行查询的上下文实例）中的值。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-128">Model-level filters will use the value from the correct context instance (that is, the context instance that is executing the query).</span></span>

<span data-ttu-id="d7dfa-129">可使用 IgnoreQueryFilters() 运算符对各个 LINQ 查询禁用筛选器。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-129">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="d7dfa-130">限制</span><span class="sxs-lookup"><span data-stu-id="d7dfa-130">Limitations</span></span>

- <span data-ttu-id="d7dfa-131">不允许导航引用。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-131">Navigation references are not allowed.</span></span> <span data-ttu-id="d7dfa-132">可根据反馈添加此功能。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-132">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="d7dfa-133">仅可在层次结构的根实体类型上定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-133">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="d7dfa-134">数据库标量函数映射</span><span class="sxs-lookup"><span data-stu-id="d7dfa-134">Database scalar function mapping</span></span>

<span data-ttu-id="d7dfa-135">EF Core 2.0 包含来自 [Paul Middleton](https://github.com/pmiddleton) 的一个重要贡献功能，该功能支持将数据库标量函数映射到方法存根，使其可用于 LINQ 查询并转换为 SQL。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-135">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="d7dfa-136">下面是如何使用该功能的简要说明：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-136">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="d7dfa-137">在 `DbContext` 上声明一种静态方法，并使用 `DbFunctionAttribute` 对其批注：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-137">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new Exception();
    }
}
```

<span data-ttu-id="d7dfa-138">此类方法会自动注册。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-138">Methods like this are automatically registered.</span></span> <span data-ttu-id="d7dfa-139">注册后，对 LINQ 查询中方法的调用可转换为 SQL 中的函数调用：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-139">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="d7dfa-140">需要注意以下事项：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-140">A few things to note:</span></span>

- <span data-ttu-id="d7dfa-141">依照约定，方法名称在生成 SQL 时会用作函数（此情况下为用户定义的函数）名称，但是你可以在方法注册期间替代名称和架构</span><span class="sxs-lookup"><span data-stu-id="d7dfa-141">By convention the name of the method is used as the name of a function (in this case a user defined function) when generating the SQL, but you can override the name and schema during method registration</span></span>
- <span data-ttu-id="d7dfa-142">当前仅支持标量函数</span><span class="sxs-lookup"><span data-stu-id="d7dfa-142">Currently only scalar functions are supported</span></span>
- <span data-ttu-id="d7dfa-143">必须在数据库中创建映射函数。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-143">You must create the mapped function in the database.</span></span> <span data-ttu-id="d7dfa-144">EF Core 迁移不创建此函数</span><span class="sxs-lookup"><span data-stu-id="d7dfa-144">EF Core migrations will not take care of creating it</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="d7dfa-145">Code First 的自包含类型配置</span><span class="sxs-lookup"><span data-stu-id="d7dfa-145">Self-contained type configuration for code first</span></span>

<span data-ttu-id="d7dfa-146">在 EF6 中可以通过从 EntityTypeConfiguration 派生来封装特定实体类型的 Code First 配置  。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-146">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="d7dfa-147">我们在 EF Core 2.0 中重新添加了此模式：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-147">In EF Core 2.0 we are bringing this pattern back:</span></span>

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
  public void Configure(EntityTypeBuilder<Customer> builder)
  {
     builder.HasKey(c => c.AlternateKey);
     builder.Property(c => c.Name).HasMaxLength(200);
   }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a><span data-ttu-id="d7dfa-148">高性能</span><span class="sxs-lookup"><span data-stu-id="d7dfa-148">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="d7dfa-149">DbContext 池</span><span class="sxs-lookup"><span data-stu-id="d7dfa-149">DbContext pooling</span></span>

<span data-ttu-id="d7dfa-150">在 ASP.NET Core 应用程序中使用 EF Core 的基本模式通常是先将自定义 DbContext 类型注册到依赖关系注入系统，然后通过控制器中的构造函数参数获取该类型的实例。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-150">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="d7dfa-151">这会为各个请求创建一个 DbContext 新实例。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-151">This means a new instance of the DbContext is created for each requests.</span></span>

<span data-ttu-id="d7dfa-152">在版本 2.0 中，我们引入了一种在依赖关系注入中注册自定义 DbContext 类型的新方法，即以透明形式引入可重用 DbContext 实例的池。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-152">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="d7dfa-153">要使用 DbContext 池，请在服务注册期间使用 `AddDbContextPool` 而不是 `AddDbContext`：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-153">To use DbContext pooling, use the `AddDbContextPool` instead of `AddDbContext` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="d7dfa-154">如果使用此方法，那么在控制器请求 DbContext 实例时，我们会首先检查池中有无可用的实例。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-154">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="d7dfa-155">请求处理完成后，实例的任何状态都将被重置，并且实例本身会返回池中。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-155">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="d7dfa-156">从概念上讲，此方法类似于连接池在 ADO.NET 提供程序中的运行原理，并具有节约 DbContext 实例初始化成本的优势。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-156">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="d7dfa-157">限制</span><span class="sxs-lookup"><span data-stu-id="d7dfa-157">Limitations</span></span>

<span data-ttu-id="d7dfa-158">此新方法对使用 DbContext 的 `OnConfiguring()` 方法可执行的操作带来了一些限制。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-158">The new method introduces a few limitations on what can be done in the `OnConfiguring()` method of the DbContext.</span></span>

> [!WARNING]  
> <span data-ttu-id="d7dfa-159">如果要在不能在请求间共享的派生的 DbContext 类中保留自己的状态（例如私有字段），请避免使用 DbContext 池。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-159">Avoid using DbContext Pooling if you maintain your own state (for example, private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="d7dfa-160">EF Core 仅会重置将 DbContext 实例添加到池之前所识别的状态。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-160">EF Core will only reset the state that is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="d7dfa-161">显式编译的查询</span><span class="sxs-lookup"><span data-stu-id="d7dfa-161">Explicitly compiled queries</span></span>

<span data-ttu-id="d7dfa-162">这是第二个选择加入性能功能，旨在为大规模方案提供优势。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-162">This is the second opt-in performance feature designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="d7dfa-163">EF 早期版本以及 LINQ to SQL 中已经提供手动或显式编译的查询 API，允许应用程序缓存查询转换，使其可仅被计算一次并执行多次。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-163">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="d7dfa-164">尽管 EF Core 通常可基于查询表达式的哈希表示法自动编译和缓存查询，但是使用此机制可绕过哈希计算和缓存查询，允许应用程序通过调用委托来使用已编译查询，从而实现性能小幅提升。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-164">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a><span data-ttu-id="d7dfa-165">更改跟踪</span><span class="sxs-lookup"><span data-stu-id="d7dfa-165">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="d7dfa-166">Attach 可跟踪新实体和现有实体的关系图。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-166">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="d7dfa-167">EF Core 支持通过多种机制自动生成键值。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-167">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="d7dfa-168">使用此功能时，如果键属性为 CLR 默认值，则会生成一个值（通常为零或 null）。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-168">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="d7dfa-169">这意味着实体的关系图可传递到 `DbContext.Attach` 或 `DbSet.Attach`，并且 EF Core 会标记键已设置为 `Unchanged` 的实体，同时将没有键集的实体标记为 `Added`。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-169">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="d7dfa-170">这样就可以轻松地在使用生成键时，附加混合了新实体和现有实体的关系图。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-170">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="d7dfa-171">`DbContext.Update` 和 `DbSet.Update` 的工作原理相同，但是有键集的实体会被标记为 `Modified` 而不是 `Unchanged`。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-171">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="d7dfa-172">查询</span><span class="sxs-lookup"><span data-stu-id="d7dfa-172">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="d7dfa-173">改进的 LINQ 转换</span><span class="sxs-lookup"><span data-stu-id="d7dfa-173">Improved LINQ Translation</span></span>

<span data-ttu-id="d7dfa-174">可成功执行的查询更多，在数据库中（而不是内存中）计算的逻辑也更多，并且从数据库中检索的不必要数据更少。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-174">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="d7dfa-175">GroupJoin 改进</span><span class="sxs-lookup"><span data-stu-id="d7dfa-175">GroupJoin improvements</span></span>

<span data-ttu-id="d7dfa-176">这改进了为组联接生成的 SQL。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-176">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="d7dfa-177">组联接通常是可选导航属性上子查询的结果。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-177">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="d7dfa-178">FromSql 和 ExecuteSqlCommand 中的字符串内插</span><span class="sxs-lookup"><span data-stu-id="d7dfa-178">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="d7dfa-179">C# 6 引入了字符串内插功能，此功能允许将 C# 表达式直接嵌入字符串文本，从而提供了一种很适合在运行时生成字符串的方法。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-179">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="d7dfa-180">在 EF Core 2.0 中，我们为两个主要 API 添加了对内插字符串的特殊支持，这两个 API 用于接收原始 SQL 字符串：`FromSql` 和 `ExecuteSqlCommand`。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-180">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: `FromSql` and `ExecuteSqlCommand`.</span></span> <span data-ttu-id="d7dfa-181">这项新支持允许以“安全”方式使用 C# 字符串内插。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-181">This new support allows C# string interpolation to be used in a 'safe' manner.</span></span> <span data-ttu-id="d7dfa-182">即，采用此方式可防止在运行时动态构造 SQL 时可能发生的常见 SQL 注入错误。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-182">That is, in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="d7dfa-183">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-183">Here is an example:</span></span>

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

<span data-ttu-id="d7dfa-184">本示例在 SQL 格式字符串中嵌入了两个变量。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-184">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="d7dfa-185">EF Core 会生成如下 SQL：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-185">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="d7dfa-186">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="d7dfa-186">EF.Functions.Like()</span></span>

<span data-ttu-id="d7dfa-187">我们已添加 EF.Functions 属性，EF Core 或提供程序可使用该属性定义映射到数据库函数或运算符的方法，从而可在 LINQ 查询中调用它们。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-187">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="d7dfa-188">此类方法的第一个示例是 Like()：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-188">The first example of such a method is Like():</span></span>

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

<span data-ttu-id="d7dfa-189">请注意，Like() 附带一个内存中实现，处理内存中数据库时或需要在客户端上进行谓词计算时，此实现可能非常方便。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-189">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="d7dfa-190">数据库管理</span><span class="sxs-lookup"><span data-stu-id="d7dfa-190">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="d7dfa-191">DbContext 基架的复数化挂钩</span><span class="sxs-lookup"><span data-stu-id="d7dfa-191">Pluralization hook for DbContext Scaffolding</span></span>

<span data-ttu-id="d7dfa-192">EF Core 2.0 引入了一种新的 IPluralizer 服务，用于单数化实体类型名称和复数化 DbSet 名称  。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-192">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="d7dfa-193">默认实现为 no-op，因此这仅仅是开发人员可轻松插入自己的复数化程序的挂钩。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-193">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="d7dfa-194">下面是开发人员挂入自己的复数化程序的示例：</span><span class="sxs-lookup"><span data-stu-id="d7dfa-194">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a><span data-ttu-id="d7dfa-195">其他</span><span class="sxs-lookup"><span data-stu-id="d7dfa-195">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="d7dfa-196">将 ADO.NET SQLite 提供程序移动到 SQLitePCL.raw</span><span class="sxs-lookup"><span data-stu-id="d7dfa-196">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>
<span data-ttu-id="d7dfa-197">这为我们提供了一个 Microsoft.Data.Sqlite 中的更强大解决方案，用以在不同平台分发本机 SQLite 二进制文件。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-197">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="d7dfa-198">每个模型仅有一个提供程序</span><span class="sxs-lookup"><span data-stu-id="d7dfa-198">Only one provider per model</span></span>
<span data-ttu-id="d7dfa-199">显著增强了提供程序与模型的交互方式，并简化了约定、注释和 Fluent API 用于不同提供程序的方法。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-199">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="d7dfa-200">EF Core 2.0 现将对所用的每个不同提供程序生成不同的 [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs)。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-200">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="d7dfa-201">这对应用程序而言通常是透明的。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-201">This is usually transparent to the application.</span></span> <span data-ttu-id="d7dfa-202">这有助于简化较低级别的元数据 API，从而始终通过调用 `.Relational`（而不是 `.SqlServer`、`.Sqlite` 等）来访问常见关系元数据概念  。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-202">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="d7dfa-203">增强的日志记录和诊断</span><span class="sxs-lookup"><span data-stu-id="d7dfa-203">Consolidated Logging and Diagnostics</span></span>

<span data-ttu-id="d7dfa-204">日志记录（基于 ILogger）和诊断（基于 DiagnosticSource）机制现可共享更多代码。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-204">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="d7dfa-205">发送给 ILogger 的消息的事件 ID 在 2.0 中已更改。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-205">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="d7dfa-206">现在，事件 ID 在 EF Core 代码内具有唯一性。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-206">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="d7dfa-207">这些消息现在还遵循 MVC 等所用的结构化日志记录的标准模式。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-207">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="d7dfa-208">记录器类别也已更改。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-208">Logger categories have also changed.</span></span> <span data-ttu-id="d7dfa-209">现提供通过 [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs) 访问的熟知类别集。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-209">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="d7dfa-210">DiagnosticSource 事件现使用与相应 `ILogger` 消息相同的事件 ID 名称。</span><span class="sxs-lookup"><span data-stu-id="d7dfa-210">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>

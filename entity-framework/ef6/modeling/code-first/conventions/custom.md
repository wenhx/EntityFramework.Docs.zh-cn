---
title: 自定义 Code First 约定-EF6
description: 实体框架6中的自定义 Code First 约定
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 5142f58d229dd7743fd4d5c7f0004c080e22c9e4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066455"
---
# <a name="custom-code-first-conventions"></a>自定义 Code First 约定
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。

使用时 Code First 模型将使用一组约定从类进行计算。 默认 [Code First 约定](xref:ef6/modeling/code-first/conventions/built-in) 确定哪些属性将成为实体的主键、实体映射到的表的名称，以及在默认情况下，小数列的精度和小数位数。

有时，这些默认约定并不适用于您的模型，并且您必须通过使用数据注释或流畅的 API 来配置多个单独的实体来解决它们。 自定义 Code First 约定允许您定义自己的约定，为模型提供配置默认值。 在本演练中，我们将探讨不同类型的自定义约定以及如何创建它们。


## <a name="model-based-conventions"></a>Model-Based 约定

本页介绍用于自定义约定的 DbModelBuilder API。 此 API 应该足以用于创作大多数自定义约定。 但是，还可以创作基于模型的约定-在创建最终模型后对其进行操作的约定-用于处理高级方案。 有关详细信息，请参阅 [基于模型的约定](xref:ef6/modeling/code-first/conventions/model)。

 

## <a name="our-model"></a>我们的模型

首先，我们定义一个可用于约定的简单模型。 将以下类添加到项目。

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a>自定义约定简介

我们来编写一种约定，将名为 Key 的任何属性配置为其实体类型的主键。

在模型生成器上启用约定，可以通过在上下文中重写 OnModelCreating 来访问这些约定。 更新 ProductContext 类，如下所示：

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

现在，模型中名为 Key 的任何属性都将配置为其一部分的任何实体的主键。

还可以通过筛选要配置的属性类型，使约定更加具体：

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

这会将名为 Key 的所有属性配置为其实体的主键，但前提是它们是整数。

IsKey 方法的一个有趣功能是它是附加的。 这意味着，如果在多个属性上调用 IsKey，它们都将成为复合键的一部分。 需要注意的一点是，如果为某个键指定了多个属性，还必须为这些属性指定顺序。 为此，可以调用 HasColumnOrder 方法，如下所示：

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

此代码将配置模型中的类型，使其包含 int 键列和字符串名称列组成的组合键。 如果在设计器中查看模型，它将如下所示：

![组合键](~/ef6/media/compositekey.png)

属性约定的另一个示例是将我的模型中的所有日期时间属性配置为映射到 SQL Server 中的 datetime2 类型，而不是 DateTime。 可以通过以下方式实现此目的：

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>约定类

定义约定的另一种方法是使用约定类封装约定。 使用约定类时，将创建一个从 ModelConfiguration 命名空间中的约定类继承的类型。

可以通过执行以下操作，创建一个具有前文约定的约定类：

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

若要告诉 EF 使用此约定，请将其添加到 OnModelCreating 中的约定集合，如果已执行以下操作，演练将如下所示：

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

如您所见，我们将约定的实例添加到约定集合。 从约定继承提供了一种在团队或项目之间进行分组和共享约定的便利方法。 例如，你可以有一个类库，其中包含所有组织项目使用的一组通用约定。

 

## <a name="custom-attributes"></a>自定义特性

约定的另一种很好的用途是启用在配置模型时使用的新属性。 为了说明这一点，让我们创建一个属性，用于将字符串属性标记为非 Unicode。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

现在，让我们创建一个约定，以将此属性应用于模型：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

通过此约定，我们可以将 NonUnicode 属性添加到字符串的任何属性，这意味着数据库中的列将存储为 varchar 而不是 nvarchar。

关于此约定，需要注意的一点是，如果将 NonUnicode 属性放在字符串属性以外的任何内容中，则会引发异常。 这是因为不能在字符串以外的任何类型上配置 IsUnicode。 如果发生这种情况，则可以使约定更为具体，使其筛选掉不是字符串的任何内容。

尽管上述约定适用于定义自定义属性，但还有另一个 API 可以更容易地使用，尤其是在你想要使用属性类中的属性时。

在此示例中，我们将更新属性，并将其更改为 IsUnicode 属性，如下所示：

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

完成此设置后，可以在属性上设置一个布尔值，以指示该属性是否应为 Unicode。 为此，我们可以通过访问配置类的 ClrProperty 来执行此操作，如下所示：

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

这非常简单，但通过使用约定 API 的 Having 方法，可以更简洁地实现此方法。 Having 方法具有类型为 Func &lt; PropertyInfo、T 的参数，该参数 &gt; 接受与 Where 方法相同的 PropertyInfo，但预期返回对象。 如果返回的对象为 null，则不会配置属性，这意味着，你可以像在中那样筛选 out 属性，但不同之处在于它还将捕获返回的对象并将其传递给 Configure 方法。 这类似于以下内容：

``` csharp
    modelBuilder.Properties()
                .Having(x => x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

自定义特性并不是使用 Having 方法的唯一理由，在配置类型或属性时，您需要对其进行筛选的任何位置都很有用。

 

## <a name="configuring-types"></a>配置类型

到目前为止，我们的所有约定都是关于属性的，但也有另一个用于配置模型中的类型的约定 API 区域。 经验类似于我们目前所见到的约定，但配置内的选项将位于实体而不是属性级别。

类型级别约定的一项功能对于更改表命名约定非常有用，它可以映射到与 EF 默认值不同的现有架构，也可以使用不同的命名约定来创建新的数据库。 为此，我们首先需要一个方法，该方法可以接受模型中类型的 TypeInfo，并返回该类型的表名称应为：

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

此方法采用类型并返回一个字符串，该字符串使用带下划线的小写形式而不是 CamelCase。 在我们的模型中，这意味着 ProductCategory 类将映射到称为 product \_ category 的表，而不是 ProductCategories。

获得该方法后，我们可以在此类约定中调用它：

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

此约定将模型中的每个类型配置为映射到 GetTableName 方法返回的表名。 此约定等效于使用熟知 API 为模型中的每个实体调用 ToTable 方法。

需要注意的一点是，当你调用 ToTable EF 时，将采用你提供的字符串作为确切的表名称，而不是在确定表名称时通常会执行的任何复数形式。 这就是我们的约定中的表名是产品 \_ 类别而不是产品类别的原因 \_ 。 我们可以通过调用复数形式服务自己的约定来解决这一问题。

在下面的代码中，我们将使用 EF6 中添加的 [依赖项解析](xref:ef6/fundamentals/configuring/dependency-resolution) 功能检索 EF 使用的复数形式服务并复数形式我们的表名。

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> GetService 的泛型版本是 DependencyResolution 命名空间中的扩展方法，需要将 using 语句添加到你的上下文中才能使用该语句。

### <a name="totable-and-inheritance"></a>ToTable 和继承

ToTable 的另一个重要方面是，如果您将某一类型显式映射到给定的表，则可以更改 EF 将使用的映射策略。 如果为继承层次结构中的每个类型调用 ToTable，并将类型名称作为表的名称传递，则会将默认的每个层次结构一个表 (TPH) 映射策略更改为每种类型一个表 (TPT) 。 描述这一点的最佳方式是 whith 一个具体的示例：

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

默认情况下，员工和经理都映射到数据库中 (员工) 的同一个表。 该表将包含具有鉴别器列的员工和经理，该列将告诉您每一行中存储的实例类型。 这是 TPH 映射，因为层次结构中有一个表。 但是，如果在这两个类上调用 ToTable，则每个类型都将映射到其自己的表，也称为 TPT，因为每个类型都有其自己的表。

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

上面的代码将映射到如下所示的表结构：

![tpt 示例](~/ef6/media/tptexample.jpg)

可以通过以下几种方式来避免这种情况并维护默认的 TPH 映射：

1.  为层次结构中的每个类型调用具有相同表名称的 ToTable。
2.  仅在层次结构的基类（在本示例中为 employee）上调用 ToTable。

 

## <a name="execution-order"></a>执行顺序

约定的运行方式与精通 API 相同。 这意味着，如果您编写两个约定来配置同一属性的相同选项，最后一个规则执行 wins。 例如，在下面的代码中，所有字符串的最大长度都设置为500，但然后，在模型中将名为 Name 的所有属性都配置为具有最大长度250。

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

由于将最大长度设置为250的约定是在将所有字符串设置为500后，因此模型中名为 "名称" 的所有属性的 MaxLength 都为250，而任何其他字符串（如 "说明"）都是500。 以这种方式使用约定意味着你可以为模型中的类型或属性提供一般约定，然后将其覆盖为不同的子集。

在特定情况下，还可以使用熟知的 API 和数据批注来替代约定。 在上面的示例中，如果我们使用了 "熟知 API" 设置属性的最大长度，则可以将其放在约定之前或之后，因为更具体的熟知 API 将通过更常规的配置约定。

 

## <a name="built-in-conventions"></a>内置约定

由于自定义约定可能受默认 Code First 约定的影响，因此添加约定以在其他约定之前或之后运行可能会很有用。 为此，可以在派生的 DbContext 上使用约定集合的 AddBefore 和 AddAfter 方法。 下面的代码将添加前面创建的约定类，使其在内置密钥发现约定之前运行。

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

添加需要在内置约定之前或之后运行的约定时，这将是最常使用的，可在以下位置找到内置约定列表： [ModelConfiguration 命名空间](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)。

还可以删除不希望应用于模型的约定。 若要删除约定，请使用 Remove 方法。 下面是删除 PluralizingTableNameConvention 的示例。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```

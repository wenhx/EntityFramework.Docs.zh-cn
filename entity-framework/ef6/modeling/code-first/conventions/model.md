---
title: 基于模型的约定-EF6
description: 实体框架6中基于模型的约定
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: 30a79f505939220b3d4040778397eab972e6a712
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617184"
---
# <a name="model-based-conventions"></a>基于模型的约定
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

基于模型的约定是基于约定的模型配置的高级方法。 大多数情况下，应使用 [DbModelBuilder 上的自定义 Code First 约定 API](xref:ef6/modeling/code-first/conventions/custom) 。 使用基于模型的约定之前，建议了解约定的 DbModelBuilder API。  

基于模型的约定允许创建影响不能通过标准约定进行配置的属性和表的约定。 例如，表中的鉴别器列按层次结构模型和独立关联列。  

## <a name="creating-a-convention"></a>创建约定   

创建基于模型的约定的第一步是在管道中选择约定需要应用到模型的时间。 有两种类型的模型约定：概念 (的 C-Space) 和存储 (的) 。 C-Space 约定应用于应用程序生成的模型，而 S 空间约定应用于表示数据库的模型的版本，并控制自动生成的列的命名方式。  

模型约定是从 IConceptualModelConvention 或 IStoreModelConvention 扩展的类。  这些接口都接受类型为 MetadataItem 的泛型类型，该类型用于筛选约定适用的数据类型。  

## <a name="adding-a-convention"></a>添加约定   

添加模型约定的方式与常规约定类相同。 在 **OnModelCreating** 方法中，将约定添加到模型的约定列表。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

还可以使用 AddBefore \<\> 或 AddAfter 方法相对于其他约定来添加约定。 \<\> 有关实体框架应用的约定的详细信息，请参阅 "备注" 部分。  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>示例：鉴别器模型约定  

重命名由 EF 生成的列是一个无法使用其他约定 Api 进行操作的示例。  这种情况下，使用模型约定是唯一的选择。  

有关如何使用基于模型的约定配置生成的列的示例，请自定义鉴别器列的命名方式。  下面是基于模型的简单约定的一个示例，它将名为 "鉴别器" 的模型中的每一列都重命名为 "EntityType"。  这包括开发人员简单命名为 "鉴别器" 的列。 由于 "鉴别器" 列是生成的列，因此需要在 S 空间中运行。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a>示例：常规 IA 重命名约定   

其他更复杂的基于模型的约定示例是配置独立关联 (IAs) 的命名方式。  这种情况下，模型约定适用，因为 IAs 由 EF 生成，并且不存在于 DbModelBuilder API 可以访问的模型中。  

当 EF 生成 IA 时，它将创建一个名为 EntityType_KeyName 的列。 例如，对于名为 Customer 且名为 CustomerId 的键列的关联，它会生成一个名为 Customer_CustomerId 的列。 下面的约定 \_ 从为 IA 生成的列名称中去除 "" 字符。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i < properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a>扩展现有约定   

如果需要编写一个约定，此约定与实体框架已应用于你的模型的约定之一类似，你始终可以扩展该约定，以避免必须从头开始重新编写该约定。  例如，将现有的 Id 匹配约定替换为自定义约定。   提高密钥约定的优点是：只有在未检测到或未显式配置任何键时才会调用重写的方法。 实体框架使用的约定列表如下所示： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

接下来，我们需要在现有的密钥约定之前添加新约定。 添加 CustomKeyDiscoveryConvention 后，我们可以删除 IdKeyDiscoveryConvention。  如果我们未删除现有的 IdKeyDiscoveryConvention，则此约定仍优先于 Id 发现约定，因为它首先运行，但在未找到 "密钥" 属性的情况下，将运行 "Id" 约定。  我们会看到此行为，因为每个约定都将模型视为由上一个约定更新 (，而不是单独操作，而是组合在一起) 这样，如果以前的约定更新了列名，使其与自定义约定相关的内容匹配 (，则在该) 名称之前，它将应用到该列。  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a>说明  

此处的 MSDN 文档提供了实体框架当前应用的约定列表： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。  此列表是直接从源代码中提取的。  [GitHub](https://github.com/aspnet/entityframework6/)上提供了实体框架6的源代码，而实体框架所使用的许多约定都是基于自定义模型的约定的良好起点。  

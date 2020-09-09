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
# <a name="model-based-conventions"></a><span data-ttu-id="731fa-103">基于模型的约定</span><span class="sxs-lookup"><span data-stu-id="731fa-103">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="731fa-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="731fa-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="731fa-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="731fa-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="731fa-106">基于模型的约定是基于约定的模型配置的高级方法。</span><span class="sxs-lookup"><span data-stu-id="731fa-106">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="731fa-107">大多数情况下，应使用 [DbModelBuilder 上的自定义 Code First 约定 API](xref:ef6/modeling/code-first/conventions/custom) 。</span><span class="sxs-lookup"><span data-stu-id="731fa-107">For most scenarios the [Custom Code First Convention API on DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) should be used.</span></span> <span data-ttu-id="731fa-108">使用基于模型的约定之前，建议了解约定的 DbModelBuilder API。</span><span class="sxs-lookup"><span data-stu-id="731fa-108">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="731fa-109">基于模型的约定允许创建影响不能通过标准约定进行配置的属性和表的约定。</span><span class="sxs-lookup"><span data-stu-id="731fa-109">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="731fa-110">例如，表中的鉴别器列按层次结构模型和独立关联列。</span><span class="sxs-lookup"><span data-stu-id="731fa-110">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="731fa-111">创建约定</span><span class="sxs-lookup"><span data-stu-id="731fa-111">Creating a Convention</span></span>   

<span data-ttu-id="731fa-112">创建基于模型的约定的第一步是在管道中选择约定需要应用到模型的时间。</span><span class="sxs-lookup"><span data-stu-id="731fa-112">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="731fa-113">有两种类型的模型约定：概念 (的 C-Space) 和存储 (的) 。</span><span class="sxs-lookup"><span data-stu-id="731fa-113">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="731fa-114">C-Space 约定应用于应用程序生成的模型，而 S 空间约定应用于表示数据库的模型的版本，并控制自动生成的列的命名方式。</span><span class="sxs-lookup"><span data-stu-id="731fa-114">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="731fa-115">模型约定是从 IConceptualModelConvention 或 IStoreModelConvention 扩展的类。</span><span class="sxs-lookup"><span data-stu-id="731fa-115">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="731fa-116">这些接口都接受类型为 MetadataItem 的泛型类型，该类型用于筛选约定适用的数据类型。</span><span class="sxs-lookup"><span data-stu-id="731fa-116">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="731fa-117">添加约定</span><span class="sxs-lookup"><span data-stu-id="731fa-117">Adding a Convention</span></span>   

<span data-ttu-id="731fa-118">添加模型约定的方式与常规约定类相同。</span><span class="sxs-lookup"><span data-stu-id="731fa-118">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="731fa-119">在 **OnModelCreating** 方法中，将约定添加到模型的约定列表。</span><span class="sxs-lookup"><span data-stu-id="731fa-119">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

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

<span data-ttu-id="731fa-120">还可以使用 AddBefore \<\> 或 AddAfter 方法相对于其他约定来添加约定。 \<\></span><span class="sxs-lookup"><span data-stu-id="731fa-120">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="731fa-121">有关实体框架应用的约定的详细信息，请参阅 "备注" 部分。</span><span class="sxs-lookup"><span data-stu-id="731fa-121">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="731fa-122">示例：鉴别器模型约定</span><span class="sxs-lookup"><span data-stu-id="731fa-122">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="731fa-123">重命名由 EF 生成的列是一个无法使用其他约定 Api 进行操作的示例。</span><span class="sxs-lookup"><span data-stu-id="731fa-123">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="731fa-124">这种情况下，使用模型约定是唯一的选择。</span><span class="sxs-lookup"><span data-stu-id="731fa-124">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="731fa-125">有关如何使用基于模型的约定配置生成的列的示例，请自定义鉴别器列的命名方式。</span><span class="sxs-lookup"><span data-stu-id="731fa-125">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="731fa-126">下面是基于模型的简单约定的一个示例，它将名为 "鉴别器" 的模型中的每一列都重命名为 "EntityType"。</span><span class="sxs-lookup"><span data-stu-id="731fa-126">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="731fa-127">这包括开发人员简单命名为 "鉴别器" 的列。</span><span class="sxs-lookup"><span data-stu-id="731fa-127">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="731fa-128">由于 "鉴别器" 列是生成的列，因此需要在 S 空间中运行。</span><span class="sxs-lookup"><span data-stu-id="731fa-128">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

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

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="731fa-129">示例：常规 IA 重命名约定</span><span class="sxs-lookup"><span data-stu-id="731fa-129">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="731fa-130">其他更复杂的基于模型的约定示例是配置独立关联 (IAs) 的命名方式。</span><span class="sxs-lookup"><span data-stu-id="731fa-130">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="731fa-131">这种情况下，模型约定适用，因为 IAs 由 EF 生成，并且不存在于 DbModelBuilder API 可以访问的模型中。</span><span class="sxs-lookup"><span data-stu-id="731fa-131">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="731fa-132">当 EF 生成 IA 时，它将创建一个名为 EntityType_KeyName 的列。</span><span class="sxs-lookup"><span data-stu-id="731fa-132">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="731fa-133">例如，对于名为 Customer 且名为 CustomerId 的键列的关联，它会生成一个名为 Customer_CustomerId 的列。</span><span class="sxs-lookup"><span data-stu-id="731fa-133">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="731fa-134">下面的约定 \_ 从为 IA 生成的列名称中去除 "" 字符。</span><span class="sxs-lookup"><span data-stu-id="731fa-134">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

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

## <a name="extending-existing-conventions"></a><span data-ttu-id="731fa-135">扩展现有约定</span><span class="sxs-lookup"><span data-stu-id="731fa-135">Extending Existing Conventions</span></span>   

<span data-ttu-id="731fa-136">如果需要编写一个约定，此约定与实体框架已应用于你的模型的约定之一类似，你始终可以扩展该约定，以避免必须从头开始重新编写该约定。</span><span class="sxs-lookup"><span data-stu-id="731fa-136">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="731fa-137">例如，将现有的 Id 匹配约定替换为自定义约定。</span><span class="sxs-lookup"><span data-stu-id="731fa-137">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="731fa-138">提高密钥约定的优点是：只有在未检测到或未显式配置任何键时才会调用重写的方法。</span><span class="sxs-lookup"><span data-stu-id="731fa-138">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="731fa-139">实体框架使用的约定列表如下所示： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="731fa-139">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

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

<span data-ttu-id="731fa-140">接下来，我们需要在现有的密钥约定之前添加新约定。</span><span class="sxs-lookup"><span data-stu-id="731fa-140">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="731fa-141">添加 CustomKeyDiscoveryConvention 后，我们可以删除 IdKeyDiscoveryConvention。</span><span class="sxs-lookup"><span data-stu-id="731fa-141">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="731fa-142">如果我们未删除现有的 IdKeyDiscoveryConvention，则此约定仍优先于 Id 发现约定，因为它首先运行，但在未找到 "密钥" 属性的情况下，将运行 "Id" 约定。</span><span class="sxs-lookup"><span data-stu-id="731fa-142">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="731fa-143">我们会看到此行为，因为每个约定都将模型视为由上一个约定更新 (，而不是单独操作，而是组合在一起) 这样，如果以前的约定更新了列名，使其与自定义约定相关的内容匹配 (，则在该) 名称之前，它将应用到该列。</span><span class="sxs-lookup"><span data-stu-id="731fa-143">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

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

## <a name="notes"></a><span data-ttu-id="731fa-144">说明</span><span class="sxs-lookup"><span data-stu-id="731fa-144">Notes</span></span>  

<span data-ttu-id="731fa-145">此处的 MSDN 文档提供了实体框架当前应用的约定列表： [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) 。</span><span class="sxs-lookup"><span data-stu-id="731fa-145">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="731fa-146">此列表是直接从源代码中提取的。</span><span class="sxs-lookup"><span data-stu-id="731fa-146">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="731fa-147">[GitHub](https://github.com/aspnet/entityframework6/)上提供了实体框架6的源代码，而实体框架所使用的许多约定都是基于自定义模型的约定的良好起点。</span><span class="sxs-lookup"><span data-stu-id="731fa-147">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  

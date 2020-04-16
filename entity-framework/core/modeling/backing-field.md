---
title: 支持字段 - EF 核心
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: d6faf7c0a07c7b855c1ce402ae29e19c80b9d3b4
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434170"
---
# <a name="backing-fields"></a><span data-ttu-id="09340-102">支持字段</span><span class="sxs-lookup"><span data-stu-id="09340-102">Backing Fields</span></span>

<span data-ttu-id="09340-103">备份字段允许 EF 读取和/或写入字段而不是属性。</span><span class="sxs-lookup"><span data-stu-id="09340-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="09340-104">当类中的封装用于限制和/或增强应用程序代码访问数据的语义时，这非常有用，但该值应从数据库读取和/或写入数据库，而无需使用这些限制/增强功能。</span><span class="sxs-lookup"><span data-stu-id="09340-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="09340-105">基本配置</span><span class="sxs-lookup"><span data-stu-id="09340-105">Basic configuration</span></span>

<span data-ttu-id="09340-106">按照惯例，以下字段将发现为给定属性的后备字段（按优先级顺序列出）。</span><span class="sxs-lookup"><span data-stu-id="09340-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="09340-107">在下面的示例中，`Url`属性配置为`_url`其备份字段：</span><span class="sxs-lookup"><span data-stu-id="09340-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="09340-108">请注意，仅对模型中包含的属性发现支持字段。</span><span class="sxs-lookup"><span data-stu-id="09340-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="09340-109">有关模型中包含哪些属性的详细信息，请参阅[包括&排除属性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="09340-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="09340-110">您还可以使用数据注释或 Fluent API 配置备份字段，例如，如果字段名称与上述约定不对应：</span><span class="sxs-lookup"><span data-stu-id="09340-110">You can also configure backing fields by using a Data Annotation or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="09340-111">数据注释</span><span class="sxs-lookup"><span data-stu-id="09340-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="09340-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="09340-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="09340-113">字段和属性访问</span><span class="sxs-lookup"><span data-stu-id="09340-113">Field and property access</span></span>

<span data-ttu-id="09340-114">默认情况下，EF 将始终读取和写入备份字段（假设一个字段已正确配置）并且永远不会使用该属性。</span><span class="sxs-lookup"><span data-stu-id="09340-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="09340-115">但是，EF 还支持其他访问模式。</span><span class="sxs-lookup"><span data-stu-id="09340-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="09340-116">例如，以下示例指示 EF 仅在具体化时写入备份字段，并在所有其他情况下使用 该属性：</span><span class="sxs-lookup"><span data-stu-id="09340-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="09340-117">有关支持的完整选项集，请参阅[属性访问模式枚举](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。</span><span class="sxs-lookup"><span data-stu-id="09340-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="09340-118">使用 EF Core 3.0 时，默认属性`PreferFieldDuringConstruction`访问`PreferField`模式从 更改为 。</span><span class="sxs-lookup"><span data-stu-id="09340-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="09340-119">仅字段属性</span><span class="sxs-lookup"><span data-stu-id="09340-119">Field-only properties</span></span>

<span data-ttu-id="09340-120">您还可以在模型中创建一个概念属性，该属性在实体类中没有相应的 CLR 属性，而是使用字段将数据存储在实体中。</span><span class="sxs-lookup"><span data-stu-id="09340-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="09340-121">这与[阴影属性](shadow-properties.md)不同，其中数据存储在更改跟踪器中，而不是存储在实体的 CLR 类型中。</span><span class="sxs-lookup"><span data-stu-id="09340-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="09340-122">当实体类使用方法而不是属性来获取/设置值时，或者在域模型中根本不不应公开字段（例如主键）的情况下，通常使用仅字段属性。</span><span class="sxs-lookup"><span data-stu-id="09340-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="09340-123">通过在`Property(...)`API 中提供名称来配置仅字段属性：</span><span class="sxs-lookup"><span data-stu-id="09340-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="09340-124">如果找不到属性，EF 将尝试查找具有给定名称的 CLR 属性，或者查找字段。</span><span class="sxs-lookup"><span data-stu-id="09340-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="09340-125">如果未找到属性，也找不到字段，则将改为设置阴影属性。</span><span class="sxs-lookup"><span data-stu-id="09340-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="09340-126">您可能需要从 LINQ 查询中引用仅字段属性，但此类字段通常是私有的。</span><span class="sxs-lookup"><span data-stu-id="09340-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="09340-127">可以使用 LINQ`EF.Property(...)`查询中的方法引用该字段：</span><span class="sxs-lookup"><span data-stu-id="09340-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

---
title: 支持字段-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: e015c4f3fca767d25bee179c027813bd9fcf4c07
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526753"
---
# <a name="backing-fields"></a><span data-ttu-id="7f853-102">支持字段</span><span class="sxs-lookup"><span data-stu-id="7f853-102">Backing Fields</span></span>

<span data-ttu-id="7f853-103">支持字段允许 EF 读取和/或写入字段，而不是属性。</span><span class="sxs-lookup"><span data-stu-id="7f853-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="7f853-104">当使用类中的封装来限制和/或通过应用程序代码对数据访问进行限制时，这可能很有用，但在不使用这些限制/增强功能的情况下，应从数据库中读取和/或写入值。</span><span class="sxs-lookup"><span data-stu-id="7f853-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="7f853-105">基本配置</span><span class="sxs-lookup"><span data-stu-id="7f853-105">Basic configuration</span></span>

<span data-ttu-id="7f853-106">按照约定，将发现以下字段作为给定属性的支持字段（按优先级顺序列出）。</span><span class="sxs-lookup"><span data-stu-id="7f853-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="7f853-107">在下面的示例中，将 `Url` 属性配置为具有 `_url` 其支持字段：</span><span class="sxs-lookup"><span data-stu-id="7f853-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="7f853-108">请注意，仅为模型中包含的属性发现支持字段。</span><span class="sxs-lookup"><span data-stu-id="7f853-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="7f853-109">有关模型中包含哪些属性的详细信息，请参阅[包括 & 排除属性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="7f853-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="7f853-110">你还可以使用数据批注（在 EFCore 5.0 中提供）或流畅的 API （例如，如果该字段名称与上述约定不对应）来配置支持字段：</span><span class="sxs-lookup"><span data-stu-id="7f853-110">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="7f853-111">数据批注</span><span class="sxs-lookup"><span data-stu-id="7f853-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="7f853-112">Fluent API</span><span class="sxs-lookup"><span data-stu-id="7f853-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="7f853-113">字段和属性访问</span><span class="sxs-lookup"><span data-stu-id="7f853-113">Field and property access</span></span>

<span data-ttu-id="7f853-114">默认情况下，EF 将始终读取并写入到支持字段-假设已正确配置了一个字段，并且永远不会使用属性。</span><span class="sxs-lookup"><span data-stu-id="7f853-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="7f853-115">但是，EF 还支持其他访问模式。</span><span class="sxs-lookup"><span data-stu-id="7f853-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="7f853-116">例如，下面的示例指示 EF 仅在具体化时写入支持字段，并在所有其他情况下使用属性：</span><span class="sxs-lookup"><span data-stu-id="7f853-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="7f853-117">有关完整的支持选项集，请参阅[PropertyAccessMode 枚举](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。</span><span class="sxs-lookup"><span data-stu-id="7f853-117">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="7f853-118">使用 EF Core 3.0，默认属性访问模式从更改 `PreferFieldDuringConstruction` 为 `PreferField` 。</span><span class="sxs-lookup"><span data-stu-id="7f853-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="7f853-119">仅限字段的属性</span><span class="sxs-lookup"><span data-stu-id="7f853-119">Field-only properties</span></span>

<span data-ttu-id="7f853-120">您还可以在您的模型中创建一个概念属性，该属性在实体类中不具有相应的 CLR 属性，而是使用字段来存储实体中的数据。</span><span class="sxs-lookup"><span data-stu-id="7f853-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="7f853-121">这不同于[阴影属性](shadow-properties.md)，其中的数据存储在更改跟踪器中，而不是存储在实体的 CLR 类型中。</span><span class="sxs-lookup"><span data-stu-id="7f853-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="7f853-122">仅字段属性在实体类使用方法而不是属性来获取/设置值时使用，或者在字段不应在域模型中公开（例如主键）的情况下使用。</span><span class="sxs-lookup"><span data-stu-id="7f853-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="7f853-123">可以通过在 API 中提供名称来配置仅限字段的属性 `Property(...)` ：</span><span class="sxs-lookup"><span data-stu-id="7f853-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="7f853-124">EF 将尝试查找具有给定名称的 CLR 属性，如果找不到属性，则尝试查找一个字段。</span><span class="sxs-lookup"><span data-stu-id="7f853-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="7f853-125">如果属性和字段均未找到，则将改为设置影子属性。</span><span class="sxs-lookup"><span data-stu-id="7f853-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="7f853-126">您可能需要从 LINQ 查询中引用仅限字段的属性，但此类字段通常是私有的。</span><span class="sxs-lookup"><span data-stu-id="7f853-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="7f853-127">可以 `EF.Property(...)` 在 LINQ 查询中使用方法来引用字段：</span><span class="sxs-lookup"><span data-stu-id="7f853-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```

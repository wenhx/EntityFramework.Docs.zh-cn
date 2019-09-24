---
title: 支持字段-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: c3ca8bb97992c192672e8c2f2040b0de029df68d
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197479"
---
# <a name="backing-fields"></a><span data-ttu-id="24ff0-102">支持字段</span><span class="sxs-lookup"><span data-stu-id="24ff0-102">Backing Fields</span></span>

> [!NOTE]  
> <span data-ttu-id="24ff0-103">此功能是 EF Core 1.1 中新增的功能。</span><span class="sxs-lookup"><span data-stu-id="24ff0-103">This feature is new in EF Core 1.1.</span></span>

<span data-ttu-id="24ff0-104">支持字段允许 EF 读取和/或写入字段，而不是属性。</span><span class="sxs-lookup"><span data-stu-id="24ff0-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="24ff0-105">当使用类中的封装来限制和/或通过应用程序代码对数据访问进行限制时，这可能很有用，但在不使用这些限制的情况下，应从数据库中读取和/或写入值。举措.</span><span class="sxs-lookup"><span data-stu-id="24ff0-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="conventions"></a><span data-ttu-id="24ff0-106">约定</span><span class="sxs-lookup"><span data-stu-id="24ff0-106">Conventions</span></span>

<span data-ttu-id="24ff0-107">按照约定，将发现以下字段作为给定属性的支持字段（按优先级顺序列出）。</span><span class="sxs-lookup"><span data-stu-id="24ff0-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> <span data-ttu-id="24ff0-108">仅为模型中包含的属性发现字段。</span><span class="sxs-lookup"><span data-stu-id="24ff0-108">Fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="24ff0-109">有关模型中包含哪些属性的详细信息，请参阅[包括 & 排除属性](included-properties.md)。</span><span class="sxs-lookup"><span data-stu-id="24ff0-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="24ff0-110">配置了支持字段后，当从数据库具体化实体实例（而不是使用属性资源库）时，EF 将直接写入该字段。</span><span class="sxs-lookup"><span data-stu-id="24ff0-110">When a backing field is configured, EF will write directly to that field when materializing entity instances from the database (rather than using the property setter).</span></span> <span data-ttu-id="24ff0-111">如果 EF 需要在其他时间读取或写入值，则它将使用属性（如果可能）。</span><span class="sxs-lookup"><span data-stu-id="24ff0-111">If EF needs to read or write the value at other times, it will use the property if possible.</span></span> <span data-ttu-id="24ff0-112">例如，如果 EF 需要更新某个属性的值，则它将使用属性 setter （如果已定义）。</span><span class="sxs-lookup"><span data-stu-id="24ff0-112">For example, if EF needs to update the value for a property, it will use the property setter if one is defined.</span></span> <span data-ttu-id="24ff0-113">如果该属性为只读，则它将写入字段。</span><span class="sxs-lookup"><span data-stu-id="24ff0-113">If the property is read-only, then it will write to the field.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="24ff0-114">数据注释</span><span class="sxs-lookup"><span data-stu-id="24ff0-114">Data Annotations</span></span>

<span data-ttu-id="24ff0-115">不能通过数据批注配置支持字段。</span><span class="sxs-lookup"><span data-stu-id="24ff0-115">Backing fields cannot be configured with data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="24ff0-116">Fluent API</span><span class="sxs-lookup"><span data-stu-id="24ff0-116">Fluent API</span></span>

<span data-ttu-id="24ff0-117">您可以使用熟知的 API 来配置属性的支持字段。</span><span class="sxs-lookup"><span data-stu-id="24ff0-117">You can use the Fluent API to configure a backing field for a property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs#Sample)]

### <a name="controlling-when-the-field-is-used"></a><span data-ttu-id="24ff0-118">控制何时使用字段</span><span class="sxs-lookup"><span data-stu-id="24ff0-118">Controlling when the field is used</span></span>

<span data-ttu-id="24ff0-119">可以配置 EF 何时使用字段或属性。</span><span class="sxs-lookup"><span data-stu-id="24ff0-119">You can configure when EF uses the field or property.</span></span> <span data-ttu-id="24ff0-120">有关支持的选项，请参阅[PropertyAccessMode 枚举](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode)。</span><span class="sxs-lookup"><span data-stu-id="24ff0-120">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the supported options.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs#Sample)]

### <a name="fields-without-a-property"></a><span data-ttu-id="24ff0-121">没有属性的字段</span><span class="sxs-lookup"><span data-stu-id="24ff0-121">Fields without a property</span></span>

<span data-ttu-id="24ff0-122">您还可以在您的模型中创建一个概念属性，该属性在实体类中不具有相应的 CLR 属性，而是使用字段来存储实体中的数据。</span><span class="sxs-lookup"><span data-stu-id="24ff0-122">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="24ff0-123">这不同于[阴影属性](shadow-properties.md)，其中的数据存储在更改跟踪器中。</span><span class="sxs-lookup"><span data-stu-id="24ff0-123">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker.</span></span> <span data-ttu-id="24ff0-124">如果实体类使用方法获取/设置值，通常会使用此方法。</span><span class="sxs-lookup"><span data-stu-id="24ff0-124">This would typically be used if the entity class uses methods to get/set values.</span></span>

<span data-ttu-id="24ff0-125">可以在`Property(...)` API 中为 EF 指定字段的名称。</span><span class="sxs-lookup"><span data-stu-id="24ff0-125">You can give EF the name of the field in the `Property(...)` API.</span></span> <span data-ttu-id="24ff0-126">如果没有具有给定名称的属性，则 EF 将查找字段。</span><span class="sxs-lookup"><span data-stu-id="24ff0-126">If there is no property with the given name, then EF will look for a field.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="24ff0-127">您还可以选择为属性指定名称，而不是字段名称。</span><span class="sxs-lookup"><span data-stu-id="24ff0-127">You can also choose to give the property a name, other than the field name.</span></span> <span data-ttu-id="24ff0-128">然后，在创建模型时使用此名称，最值得注意的是，该名称将用于在数据库中映射到的列名称。</span><span class="sxs-lookup"><span data-stu-id="24ff0-128">This name is then used when creating the model, most notably it will be used for the column name that is mapped to in the database.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldConceptualProperty.cs#Sample)]

<span data-ttu-id="24ff0-129">如果实体类中没有属性，则可以在 LINQ 查询中使用`EF.Property(...)`方法来引用概念上是模型的一部分的属性。</span><span class="sxs-lookup"><span data-stu-id="24ff0-129">When there is no property in the entity class, you can use the `EF.Property(...)` method in a LINQ query to refer to the property that is conceptually part of the model.</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "Url"));
```

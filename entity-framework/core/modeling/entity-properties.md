---
title: 实体属性-EF Core
description: 如何使用 Entity Framework Core 配置和映射实体属性
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: b67603fbffd1f1c8506bc21f8972c851eb8eef29
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414565"
---
# <a name="entity-properties"></a><span data-ttu-id="e4c65-103">实体属性</span><span class="sxs-lookup"><span data-stu-id="e4c65-103">Entity Properties</span></span>

<span data-ttu-id="e4c65-104">模型中的每个实体类型都具有一组属性，这些属性 EF Core 将从数据库中读取和写入数据。</span><span class="sxs-lookup"><span data-stu-id="e4c65-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="e4c65-105">如果使用关系数据库，实体属性将映射到表列。</span><span class="sxs-lookup"><span data-stu-id="e4c65-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="e4c65-106">包含和排除的属性</span><span class="sxs-lookup"><span data-stu-id="e4c65-106">Included and excluded properties</span></span>

<span data-ttu-id="e4c65-107">按照约定，具有 getter 和 setter 的所有公共属性都将包括在模型中。</span><span class="sxs-lookup"><span data-stu-id="e4c65-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="e4c65-108">可以按如下所述排除特定属性：</span><span class="sxs-lookup"><span data-stu-id="e4c65-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="e4c65-109">数据注释</span><span class="sxs-lookup"><span data-stu-id="e4c65-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="e4c65-110">熟知 API</span><span class="sxs-lookup"><span data-stu-id="e4c65-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="e4c65-111">列名</span><span class="sxs-lookup"><span data-stu-id="e4c65-111">Column names</span></span>

<span data-ttu-id="e4c65-112">按照约定，使用关系数据库时，实体属性映射到与属性同名的表列。</span><span class="sxs-lookup"><span data-stu-id="e4c65-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="e4c65-113">如果希望使用不同的名称配置列，可以执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e4c65-113">If you prefer to configure your columns with different names, you can do so as following:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="e4c65-114">数据注释</span><span class="sxs-lookup"><span data-stu-id="e4c65-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="e4c65-115">熟知 API</span><span class="sxs-lookup"><span data-stu-id="e4c65-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="e4c65-116">列数据类型</span><span class="sxs-lookup"><span data-stu-id="e4c65-116">Column data types</span></span>

<span data-ttu-id="e4c65-117">使用关系数据库时，数据库提供程序根据属性的 .NET 类型选择数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4c65-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="e4c65-118">它还会考虑其他元数据，如配置的[最大长度](#maximum-length)、属性是否是主键的一部分等。</span><span class="sxs-lookup"><span data-stu-id="e4c65-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="e4c65-119">例如，SQL Server 将 `DateTime` 属性映射到 `datetime2(7)` 列，并将 `string` 属性映射到 `nvarchar(max)` 列（或用于 `nvarchar(450)` 用作键的属性）。</span><span class="sxs-lookup"><span data-stu-id="e4c65-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="e4c65-120">您还可以配置列，以便为列指定精确的数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4c65-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="e4c65-121">例如，下面的代码将 `Url` 配置为具有最大长度的非 unicode 字符串 `200` 并将 `Rating` 为 decimal，精度为 `5`，小数位数为 `2`：</span><span class="sxs-lookup"><span data-stu-id="e4c65-121">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="e4c65-122">数据注释</span><span class="sxs-lookup"><span data-stu-id="e4c65-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="e4c65-123">熟知 API</span><span class="sxs-lookup"><span data-stu-id="e4c65-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="e4c65-124">最大长度</span><span class="sxs-lookup"><span data-stu-id="e4c65-124">Maximum length</span></span>

<span data-ttu-id="e4c65-125">配置最大长度会向数据库提供程序提供有关要为给定属性选择的相应列数据类型的提示。</span><span class="sxs-lookup"><span data-stu-id="e4c65-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="e4c65-126">最大长度仅适用于数组数据类型，如 `string` 和 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="e4c65-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="e4c65-127">将数据传递到提供程序之前，实体框架不会执行任何最大长度验证。</span><span class="sxs-lookup"><span data-stu-id="e4c65-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="e4c65-128">由提供程序或数据存储在适当时机进行验证。</span><span class="sxs-lookup"><span data-stu-id="e4c65-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="e4c65-129">例如，如果以 SQL Server 为目标，超过最大长度将引发异常，因为基础列的数据类型不允许存储过多数据。</span><span class="sxs-lookup"><span data-stu-id="e4c65-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="e4c65-130">在下面的示例中，配置最大长度为500将导致在 SQL Server 上创建 `nvarchar(500)` 类型的列：</span><span class="sxs-lookup"><span data-stu-id="e4c65-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="e4c65-131">数据注释</span><span class="sxs-lookup"><span data-stu-id="e4c65-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="e4c65-132">熟知 API</span><span class="sxs-lookup"><span data-stu-id="e4c65-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="e4c65-133">必需属性和可选属性</span><span class="sxs-lookup"><span data-stu-id="e4c65-133">Required and optional properties</span></span>

<span data-ttu-id="e4c65-134">如果属性对于包含 `null`是有效的，则认为该属性是可选的。</span><span class="sxs-lookup"><span data-stu-id="e4c65-134">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="e4c65-135">如果 `null` 不是要分配给属性的有效值，则将其视为必需属性。</span><span class="sxs-lookup"><span data-stu-id="e4c65-135">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="e4c65-136">映射到关系数据库架构时，必需的属性将创建为不可为 null 的列，而可选属性则创建为可以为 null 的列。</span><span class="sxs-lookup"><span data-stu-id="e4c65-136">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="e4c65-137">约定</span><span class="sxs-lookup"><span data-stu-id="e4c65-137">Conventions</span></span>

<span data-ttu-id="e4c65-138">按照约定，.NET 类型可以包含 null 的属性将配置为可选，而 .NET 类型不能包含 null 的属性将根据需要进行配置。</span><span class="sxs-lookup"><span data-stu-id="e4c65-138">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="e4c65-139">例如，具有 .NET 值类型（`int`、`decimal`、`bool`等）的所有属性都是必需的，并且具有可为 null 的 .NET 值类型（`int?`、`decimal?`、`bool?`等）的所有属性都配置为可选。</span><span class="sxs-lookup"><span data-stu-id="e4c65-139">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="e4c65-140">C#8引入了一个名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，该功能允许对引用类型进行批注，以指示它是否可用于包含空值。</span><span class="sxs-lookup"><span data-stu-id="e4c65-140">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="e4c65-141">此功能在默认情况下处于禁用状态，如果启用，它将按以下方式修改 EF Core 的行为：</span><span class="sxs-lookup"><span data-stu-id="e4c65-141">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="e4c65-142">如果禁用了可为 null 的引用类型（默认值），则所有具有 .NET 引用类型的属性都将按约定（例如 `string`）配置为可选。</span><span class="sxs-lookup"><span data-stu-id="e4c65-142">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="e4c65-143">如果启用了可以为 null 的引用类型，则将根据 .NET C#类型的为 null 性配置属性： `string?` 将配置为可选，而 `string` 将配置为 "必需"。</span><span class="sxs-lookup"><span data-stu-id="e4c65-143">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="e4c65-144">下面的示例显示了一个具有必需和可选属性的实体类型，禁用了可为 null 的引用功能（默认值），并启用了该功能：</span><span class="sxs-lookup"><span data-stu-id="e4c65-144">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="e4c65-145">没有可为 null 的引用类型（默认值）</span><span class="sxs-lookup"><span data-stu-id="e4c65-145">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="e4c65-146">具有可以为 null 的引用类型</span><span class="sxs-lookup"><span data-stu-id="e4c65-146">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="e4c65-147">建议使用可以为 null 的引用类型，因为它会将C#代码中表示的可为 null 性传递到 EF Core 的模型和数据库，并免去使用熟知 API 或数据批注来两次表示相同的概念。</span><span class="sxs-lookup"><span data-stu-id="e4c65-147">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="e4c65-148">在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。</span><span class="sxs-lookup"><span data-stu-id="e4c65-148">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="e4c65-149">管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。</span><span class="sxs-lookup"><span data-stu-id="e4c65-149">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="e4c65-150">有关可以为 null 的引用类型以及如何将其与 EF Core 一起使用的详细信息，[请参阅此功能的专用文档页](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="e4c65-150">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="e4c65-151">显式配置</span><span class="sxs-lookup"><span data-stu-id="e4c65-151">Explicit configuration</span></span>

<span data-ttu-id="e4c65-152">可以按如下所示将 "约定" 可以为 "可选" 的属性配置为 "必需"：</span><span class="sxs-lookup"><span data-stu-id="e4c65-152">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="e4c65-153">数据注释</span><span class="sxs-lookup"><span data-stu-id="e4c65-153">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="e4c65-154">熟知 API</span><span class="sxs-lookup"><span data-stu-id="e4c65-154">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

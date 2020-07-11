---
title: 实体属性-EF Core
description: 如何使用 Entity Framework Core 配置和映射实体属性
author: lajones
ms.date: 05/27/2020
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: d4e4c50d8c7febf5e42e9aa39352c0bb6a6bd409
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238211"
---
# <a name="entity-properties"></a><span data-ttu-id="8ce71-103">实体属性</span><span class="sxs-lookup"><span data-stu-id="8ce71-103">Entity Properties</span></span>

<span data-ttu-id="8ce71-104">模型中的每个实体类型都具有一组属性，这些属性 EF Core 将从数据库中读取和写入数据。</span><span class="sxs-lookup"><span data-stu-id="8ce71-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="8ce71-105">如果使用关系数据库，实体属性将映射到表列。</span><span class="sxs-lookup"><span data-stu-id="8ce71-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="8ce71-106">包含和排除的属性</span><span class="sxs-lookup"><span data-stu-id="8ce71-106">Included and excluded properties</span></span>

<span data-ttu-id="8ce71-107">按照约定，具有 getter 和 setter 的所有公共属性都将包括在模型中。</span><span class="sxs-lookup"><span data-stu-id="8ce71-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="8ce71-108">可以按如下所述排除特定属性：</span><span class="sxs-lookup"><span data-stu-id="8ce71-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ce71-109">数据批注</span><span class="sxs-lookup"><span data-stu-id="8ce71-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ce71-110">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ce71-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="8ce71-111">列名</span><span class="sxs-lookup"><span data-stu-id="8ce71-111">Column names</span></span>

<span data-ttu-id="8ce71-112">按照约定，使用关系数据库时，实体属性映射到与属性同名的表列。</span><span class="sxs-lookup"><span data-stu-id="8ce71-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="8ce71-113">如果希望使用不同的名称配置列，可以使用以下代码片段：</span><span class="sxs-lookup"><span data-stu-id="8ce71-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ce71-114">数据批注</span><span class="sxs-lookup"><span data-stu-id="8ce71-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ce71-115">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ce71-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="8ce71-116">列数据类型</span><span class="sxs-lookup"><span data-stu-id="8ce71-116">Column data types</span></span>

<span data-ttu-id="8ce71-117">使用关系数据库时，数据库提供程序根据属性的 .NET 类型选择数据类型。</span><span class="sxs-lookup"><span data-stu-id="8ce71-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="8ce71-118">它还会考虑其他元数据，如配置的[最大长度](#maximum-length)、属性是否是主键的一部分等。</span><span class="sxs-lookup"><span data-stu-id="8ce71-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="8ce71-119">例如，对于用作键) 的属性，SQL Server 将 `DateTime` 属性映射到 `datetime2(7)` 列，并将属性映射 `string` 到 `nvarchar(max)` 列 (或 `nvarchar(450)` 。</span><span class="sxs-lookup"><span data-stu-id="8ce71-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="8ce71-120">您还可以配置列，以便为列指定精确的数据类型。</span><span class="sxs-lookup"><span data-stu-id="8ce71-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="8ce71-121">例如，以下代码将配置 `Url` 为一个非 unicode 字符串，其最大长度为 `200` ， `Rating` 小数位数为，小数 `5` 位数为，小数位数为 `2` ：</span><span class="sxs-lookup"><span data-stu-id="8ce71-121">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ce71-122">数据批注</span><span class="sxs-lookup"><span data-stu-id="8ce71-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ce71-123">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ce71-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="8ce71-124">最大长度</span><span class="sxs-lookup"><span data-stu-id="8ce71-124">Maximum length</span></span>

<span data-ttu-id="8ce71-125">配置最大长度会向数据库提供程序提供有关要为给定属性选择的相应列数据类型的提示。</span><span class="sxs-lookup"><span data-stu-id="8ce71-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="8ce71-126">最大长度仅适用于数组数据类型，例如 `string` 和 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="8ce71-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="8ce71-127">在将数据传递给提供程序之前，实体框架不会验证最大长度。</span><span class="sxs-lookup"><span data-stu-id="8ce71-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="8ce71-128">提供程序或数据存储将根据需要进行验证。</span><span class="sxs-lookup"><span data-stu-id="8ce71-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="8ce71-129">例如，如果以 SQL Server 为目标，则超出最大长度将导致异常，因为基础列的数据类型将不允许存储过多的数据。</span><span class="sxs-lookup"><span data-stu-id="8ce71-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="8ce71-130">在下面的示例中，配置最大长度为500将导致 `nvarchar(500)` 在 SQL Server 上创建类型为的列：</span><span class="sxs-lookup"><span data-stu-id="8ce71-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="8ce71-131">数据批注</span><span class="sxs-lookup"><span data-stu-id="8ce71-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="8ce71-132">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ce71-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a><span data-ttu-id="8ce71-133">精度和小数位数</span><span class="sxs-lookup"><span data-stu-id="8ce71-133">Precision and Scale</span></span>

<span data-ttu-id="8ce71-134">从 EFCore 5.0 开始，可以使用 Fluent API 来配置精度和小数位数。</span><span class="sxs-lookup"><span data-stu-id="8ce71-134">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="8ce71-135">它告知数据库提供程序所需的存储空间量。</span><span class="sxs-lookup"><span data-stu-id="8ce71-135">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="8ce71-136">它仅适用于提供程序允许精度和小数位数变化的数据类型，通常为 `decimal` 和 `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="8ce71-136">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="8ce71-137">对于 `decimal` 属性，精度定义表示列将包含的任何值所需的最大位数，而 scale 定义所需的最大小数位数。</span><span class="sxs-lookup"><span data-stu-id="8ce71-137">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="8ce71-138">对于 `DateTime` 属性，精度定义表示秒的小数部分所需的最大位数，并且不使用小数位数。</span><span class="sxs-lookup"><span data-stu-id="8ce71-138">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="8ce71-139">在将数据传递给提供程序之前，实体框架不会进行任何精度验证或缩放。</span><span class="sxs-lookup"><span data-stu-id="8ce71-139">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="8ce71-140">根据需要验证提供程序或数据存储。</span><span class="sxs-lookup"><span data-stu-id="8ce71-140">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="8ce71-141">例如，如果以 SQL Server 为目标，则数据类型为的列 `datetime` 不允许设置精度，而一个列的 `datetime2` 精度介于0到7（含）之间。</span><span class="sxs-lookup"><span data-stu-id="8ce71-141">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="8ce71-142">在下面的示例中， `Score` 将属性配置为具有精度14和小数位数2将导致 `decimal(14,2)` 在 SQL Server 上创建类型为的列，并且 `LastUpdated` 将属性配置为具有精度3将导致类型为的列 `datetime2(3)` ：</span><span class="sxs-lookup"><span data-stu-id="8ce71-142">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="8ce71-143">数据批注</span><span class="sxs-lookup"><span data-stu-id="8ce71-143">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="8ce71-144">当前无法使用数据批注来进行配置。</span><span class="sxs-lookup"><span data-stu-id="8ce71-144">Currently not possible to use data annotations to configure.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="8ce71-145">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ce71-145">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="8ce71-146">永远不会定义规模，无需先定义精度，因此用于定义刻度的流畅 API 是 `HasPrecision(precision, scale)` 。</span><span class="sxs-lookup"><span data-stu-id="8ce71-146">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="8ce71-147">必需属性和可选属性</span><span class="sxs-lookup"><span data-stu-id="8ce71-147">Required and optional properties</span></span>

<span data-ttu-id="8ce71-148">如果属性对于包含有效，则将其视为可选 `null` 。</span><span class="sxs-lookup"><span data-stu-id="8ce71-148">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="8ce71-149">如果 `null` 不是要分配给属性的有效值，则将其视为必需属性。</span><span class="sxs-lookup"><span data-stu-id="8ce71-149">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="8ce71-150">映射到关系数据库架构时，必需的属性将创建为不可为 null 的列，而可选属性则创建为可以为 null 的列。</span><span class="sxs-lookup"><span data-stu-id="8ce71-150">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="8ce71-151">约定</span><span class="sxs-lookup"><span data-stu-id="8ce71-151">Conventions</span></span>

<span data-ttu-id="8ce71-152">按照约定，.NET 类型可以包含 null 的属性将配置为可选，而 .NET 类型不能包含 null 的属性将根据需要进行配置。</span><span class="sxs-lookup"><span data-stu-id="8ce71-152">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="8ce71-153">例如，所有具有 .net 值类型的属性 (`int` 、 `decimal` ) 、等 `bool` 都是必需的，并且具有可为 null 的 .net 值类型 (、) 、等）的所有属性 `int?` `decimal?` `bool?` 都配置为可选。</span><span class="sxs-lookup"><span data-stu-id="8ce71-153">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="8ce71-154">C # 8 引入了一个名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，该功能允许对引用类型进行批注，以指示它是否可用于包含空值。</span><span class="sxs-lookup"><span data-stu-id="8ce71-154">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="8ce71-155">此功能在默认情况下处于禁用状态，如果启用，它将按以下方式修改 EF Core 的行为：</span><span class="sxs-lookup"><span data-stu-id="8ce71-155">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="8ce71-156">如果 (默认的) 中禁用了可以为 null 的引用类型，则所有具有 .NET 引用类型的属性都将按约定配置为可选的 (例如， `string`) 。</span><span class="sxs-lookup"><span data-stu-id="8ce71-156">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="8ce71-157">如果启用了可以为 null 的引用类型，则将根据 .NET 类型的 c # 为空性配置属性： `string?` 将配置为可选，但 `string` 会根据需要进行配置。</span><span class="sxs-lookup"><span data-stu-id="8ce71-157">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="8ce71-158">下面的示例显示了一个具有 required 和 optional 属性的实体类型， (默认) 并启用了 "可为 null 的引用" 功能：</span><span class="sxs-lookup"><span data-stu-id="8ce71-158">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="8ce71-159">不能为 null 的引用类型 (默认值) </span><span class="sxs-lookup"><span data-stu-id="8ce71-159">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="8ce71-160">具有可以为 null 的引用类型</span><span class="sxs-lookup"><span data-stu-id="8ce71-160">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="8ce71-161">建议使用可以为 null 的引用类型，因为它会将 c # 代码中表示的可为 null 性传递到 EF Core 的模型和数据库，并免去使用熟知 API 或数据批注来两次表示相同的概念。</span><span class="sxs-lookup"><span data-stu-id="8ce71-161">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="8ce71-162">在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。</span><span class="sxs-lookup"><span data-stu-id="8ce71-162">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="8ce71-163">管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。</span><span class="sxs-lookup"><span data-stu-id="8ce71-163">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="8ce71-164">有关可以为 null 的引用类型以及如何将其与 EF Core 一起使用的详细信息，[请参阅此功能的专用文档页](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="8ce71-164">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="8ce71-165">显式配置</span><span class="sxs-lookup"><span data-stu-id="8ce71-165">Explicit configuration</span></span>

<span data-ttu-id="8ce71-166">可以按如下所示将 "约定" 可以为 "可选" 的属性配置为 "必需"：</span><span class="sxs-lookup"><span data-stu-id="8ce71-166">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="8ce71-167">数据批注</span><span class="sxs-lookup"><span data-stu-id="8ce71-167">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="8ce71-168">Fluent API</span><span class="sxs-lookup"><span data-stu-id="8ce71-168">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="8ce71-169">列排序规则</span><span class="sxs-lookup"><span data-stu-id="8ce71-169">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="8ce71-170">EF Core 5.0 中已引入了此功能。</span><span class="sxs-lookup"><span data-stu-id="8ce71-170">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="8ce71-171">可以在文本列上定义排序规则，以确定如何对它们进行比较和排序。</span><span class="sxs-lookup"><span data-stu-id="8ce71-171">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="8ce71-172">例如，以下代码段将 SQL Server 列配置为不区分大小写：</span><span class="sxs-lookup"><span data-stu-id="8ce71-172">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="8ce71-173">如果数据库中的所有列都需要使用特定的排序规则，请改为在数据库级别定义排序规则。</span><span class="sxs-lookup"><span data-stu-id="8ce71-173">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="8ce71-174">有关排序规则 EF Core 支持的常规信息，请参阅[排序规则文档页](xref:core/miscellaneous/collations-and-case-sensitivity)。</span><span class="sxs-lookup"><span data-stu-id="8ce71-174">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>

---
title: 必需属性和可选属性-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: ddaa0a54-9f43-4c34-aae3-f95c96c69842
uid: core/modeling/required-optional
ms.openlocfilehash: fd9e96e6f79965e63b07c21217edd004fd5c4d54
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197852"
---
# <a name="required-and-optional-properties"></a><span data-ttu-id="b8ff4-102">必需属性和可选属性</span><span class="sxs-lookup"><span data-stu-id="b8ff4-102">Required and Optional Properties</span></span>

<span data-ttu-id="b8ff4-103">如果属性对于包含`null`有效，则将其视为可选。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-103">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="b8ff4-104">如果`null`不是要分配给属性的有效值，则将其视为必需属性。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-104">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span>

<span data-ttu-id="b8ff4-105">映射到关系数据库架构时，必需的属性将创建为不可为 null 的列，而可选属性则创建为可以为 null 的列。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-105">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

## <a name="conventions"></a><span data-ttu-id="b8ff4-106">约定</span><span class="sxs-lookup"><span data-stu-id="b8ff4-106">Conventions</span></span>

<span data-ttu-id="b8ff4-107">按照约定，.NET 类型可以包含 null 的属性将配置为可选，而 .NET 类型不能包含 null 的属性将根据需要进行配置。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-107">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="b8ff4-108">例如，具有 .net 值类型（`int`、 `decimal`、 `bool`等）的所有属性都是必需的，并且具有可为 null 的 .net 值类型（`int?`、 `decimal?`、 `bool?`等）的所有属性都是配置为可选。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-108">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="b8ff4-109">C#8引入了一个名[为 null 的引用类型](/dotnet/csharp/tutorials/nullable-reference-types)的新功能，该功能允许对引用类型进行批注，以指示它是否可用于包含空值。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-109">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="b8ff4-110">此功能在默认情况下处于禁用状态，如果启用，它将按以下方式修改 EF Core 的行为：</span><span class="sxs-lookup"><span data-stu-id="b8ff4-110">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="b8ff4-111">如果禁用可为 null 的引用类型（默认值），则所有具有 .NET 引用类型的属性都将按约定配置`string`为可选（例如）。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-111">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="b8ff4-112">如果启用了可以为 null 的引用类型，则将根据 .net C#类型的为 null 性配置`string?`属性：将配置为可选， `string`而将配置为 "必需"。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-112">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="b8ff4-113">下面的示例显示了一个具有必需和可选属性的实体类型，禁用了可为 null 的引用功能（默认值），并启用了该功能：</span><span class="sxs-lookup"><span data-stu-id="b8ff4-113">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

# <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[<span data-ttu-id="b8ff4-114">没有可为 null 的引用类型（默认值）</span><span class="sxs-lookup"><span data-stu-id="b8ff4-114">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

# <a name="with-nullable-reference-typestabwith-nrt"></a>[<span data-ttu-id="b8ff4-115">具有可以为 null 的引用类型</span><span class="sxs-lookup"><span data-stu-id="b8ff4-115">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="b8ff4-116">建议使用可以为 null 的引用类型，因为它会将C#代码中表示的可为 null 性传递到 EF Core 的模型和数据库，并免去使用熟知 API 或数据批注来两次表示相同的概念。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-116">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="b8ff4-117">在现有项目上启用可以为 null 的引用类型时要格外小心：现在配置为可选的引用类型属性现在将配置为 "必需"，除非它们显式批注为可为 null。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-117">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="b8ff4-118">管理关系数据库架构时，这可能会导致生成更改数据库列的为 null 性的迁移。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-118">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="b8ff4-119">有关可以为 null 的引用类型以及如何将其与 EF Core 一起使用的详细信息，[请参阅此功能的专用文档页](xref:core/miscellaneous/nullable-reference-types)。</span><span class="sxs-lookup"><span data-stu-id="b8ff4-119">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

## <a name="configuration"></a><span data-ttu-id="b8ff4-120">配置</span><span class="sxs-lookup"><span data-stu-id="b8ff4-120">Configuration</span></span>

<span data-ttu-id="b8ff4-121">可以按如下所示将 "约定" 可以为 "可选" 的属性配置为 "必需"：</span><span class="sxs-lookup"><span data-stu-id="b8ff4-121">A property that would be optional by convention can be configured to be required as follows:</span></span>

# <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="b8ff4-122">数据注释</span><span class="sxs-lookup"><span data-stu-id="b8ff4-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=14)]

# <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="b8ff4-123">熟知 API</span><span class="sxs-lookup"><span data-stu-id="b8ff4-123">Fluent API</span></span>](#tab/fluent-api) 

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=11-13)]

***
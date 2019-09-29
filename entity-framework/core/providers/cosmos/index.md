---
title: Azure Cosmos DB Provider - EF Core
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 09/12/2019
ms.assetid: 28264681-4486-4891-888c-be5e4ade24f1
uid: core/providers/cosmos/index
ms.openlocfilehash: 683436aa485d2fef9aa8bf6c6ff02b00dfeb28cf
ms.sourcegitcommit: 2caec1e63f2ce1d9439ef6193df5a77da2fedd0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317565"
---
# <a name="ef-core-azure-cosmos-db-provider"></a><span data-ttu-id="ca341-102">EF Core Azure Cosmos DB Provider</span><span class="sxs-lookup"><span data-stu-id="ca341-102">EF Core Azure Cosmos DB Provider</span></span>

>[!NOTE]
> <span data-ttu-id="ca341-103">此提供程序是 EF Core 3.0 新增内容。</span><span class="sxs-lookup"><span data-stu-id="ca341-103">This provider is new in EF Core 3.0.</span></span>

<span data-ttu-id="ca341-104">此数据库提供程序允许将 Entity Framework Core 与 Azure Cosmos DB 一起使用。</span><span class="sxs-lookup"><span data-stu-id="ca341-104">This database provider allows Entity Framework Core to be used with Azure Cosmos DB.</span></span> <span data-ttu-id="ca341-105">该提供程序作为 [Entity Framework Core 项目](https://github.com/aspnet/EntityFrameworkCore)的组成部分进行维护。</span><span class="sxs-lookup"><span data-stu-id="ca341-105">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

<span data-ttu-id="ca341-106">在阅读本部分之前，强烈建议先熟悉 [Azure Cosmos DB 文档](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction)。</span><span class="sxs-lookup"><span data-stu-id="ca341-106">It is strongly recommended to familiarize yourself with the [Azure Cosmos DB documentation](https://docs.microsoft.com/en-us/azure/cosmos-db/introduction) before reading this section.</span></span>

## <a name="install"></a><span data-ttu-id="ca341-107">安装</span><span class="sxs-lookup"><span data-stu-id="ca341-107">Install</span></span>

<span data-ttu-id="ca341-108">安装 [Microsoft.EntityFrameworkCore.Cosmos NuGet 包](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/)。</span><span class="sxs-lookup"><span data-stu-id="ca341-108">Install the [Microsoft.EntityFrameworkCore.Cosmos NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/).</span></span>

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Cosmos
```

## <a name="get-started"></a><span data-ttu-id="ca341-109">开始操作</span><span class="sxs-lookup"><span data-stu-id="ca341-109">Get Started</span></span>

> [!TIP]  
> <span data-ttu-id="ca341-110">可在 [GitHub 示例](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos)中查看此文章的示例。</span><span class="sxs-lookup"><span data-stu-id="ca341-110">You can view this article's [sample on GitHub](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Cosmos).</span></span>

<span data-ttu-id="ca341-111">与其他提供程序一样，第一步是调用 `UseCosmos`：</span><span class="sxs-lookup"><span data-stu-id="ca341-111">Like for other providers the first step is to call `UseCosmos`:</span></span>

[!code-csharp[Configuration](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Configuration)]

> [!WARNING]
> <span data-ttu-id="ca341-112">为了简单起见，此处对终结点和密钥进行了硬编码，但在生产应用中，应[安全地存储](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)这些终结点和密钥</span><span class="sxs-lookup"><span data-stu-id="ca341-112">The endpoint and key are hardcoded here for simplicity, but in a production app these should be [stored securily](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)</span></span>

<span data-ttu-id="ca341-113">在此示例中，`Order` 是一个简单实体，其中包含对[从属类型](../../modeling/owned-entities.md) `StreetAddress` 的引用。</span><span class="sxs-lookup"><span data-stu-id="ca341-113">In this example `Order` is a simple entity with a reference to the [owned type](../../modeling/owned-entities.md) `StreetAddress`.</span></span>

[!code-csharp[Order](../../../../samples/core/Cosmos/ModelBuilding/Order.cs?name=Order)]

[!code-csharp[StreetAddress](../../../../samples/core/Cosmos/ModelBuilding/StreetAddress.cs?name=StreetAddress)]

<span data-ttu-id="ca341-114">保存和查询数据遵循常规 EF 模式：</span><span class="sxs-lookup"><span data-stu-id="ca341-114">Saving and quering data follows the normal EF pattern:</span></span>

[!code-csharp[HelloCosmos](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=HelloCosmos)]

> [!IMPORTANT]
> <span data-ttu-id="ca341-115">要创建所需的集合并插入[种子数据](../../modeling/data-seeding.md)（如果存在于模型中），则需要调用 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="ca341-115">Calling `EnsureCreated` is necessary to create the required collections and insert the [seed data](../../modeling/data-seeding.md) if present in the model.</span></span> <span data-ttu-id="ca341-116">但是只应在部署期间调用 `EnsureCreated`，而不应在正常操作中调用，否则可能会导致性能问题。</span><span class="sxs-lookup"><span data-stu-id="ca341-116">However `EnsureCreated` should only be called during deployment, not normal operation, as it may cause performance issues.</span></span>

## <a name="cosmos-specific-model-customization"></a><span data-ttu-id="ca341-117">特定于 Cosmos 的模型自定义</span><span class="sxs-lookup"><span data-stu-id="ca341-117">Cosmos-specific Model Customization</span></span>

<span data-ttu-id="ca341-118">默认情况下，所有实体类型都映射到同一个容器，该容器以派生的上下文命名（在本例中为 `"OrderContext"`）。</span><span class="sxs-lookup"><span data-stu-id="ca341-118">By default all entity types are mapped to the same container, named after the derived context (`"OrderContext"` in this case).</span></span> <span data-ttu-id="ca341-119">要更改默认容器名称，请使用 `HasDefaultContainer`：</span><span class="sxs-lookup"><span data-stu-id="ca341-119">To change the default container name use `HasDefaultContainer`:</span></span>

[!code-csharp[DefaultContainer](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=DefaultContainer)]

<span data-ttu-id="ca341-120">要将实体类型映射到其他容器，请使用 `ToContainer`：</span><span class="sxs-lookup"><span data-stu-id="ca341-120">To map an entity type to a different container use `ToContainer`:</span></span>

[!code-csharp[Container](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=Container)]

<span data-ttu-id="ca341-121">为了标识给定项表示的实体类型，EF Core 添加鉴别器值（即使没有派生实体类型）。</span><span class="sxs-lookup"><span data-stu-id="ca341-121">To identify the entity type that a given item represent EF Core adds a discriminator value even if there are no derived entity types.</span></span> <span data-ttu-id="ca341-122">[可以更改](../../modeling/inheritance.md)鉴别器的名称和值。</span><span class="sxs-lookup"><span data-stu-id="ca341-122">The name and value of the discriminator [can be changed](../../modeling/inheritance.md).</span></span>

## <a name="embedded-entities"></a><span data-ttu-id="ca341-123">嵌入的实体</span><span class="sxs-lookup"><span data-stu-id="ca341-123">Embedded Entities</span></span>

<span data-ttu-id="ca341-124">对于 Cosmos，从属实体嵌入到所有者所在的项中。</span><span class="sxs-lookup"><span data-stu-id="ca341-124">For Cosmos owned entities are embedded in the same item as the owner.</span></span> <span data-ttu-id="ca341-125">要更改属性名称，请使用 `ToJsonProperty`：</span><span class="sxs-lookup"><span data-stu-id="ca341-125">To change a property name use `ToJsonProperty`:</span></span>

[!code-csharp[PropertyNames](../../../../samples/core/Cosmos/ModelBuilding/OrderContext.cs?name=PropertyNames)]

<span data-ttu-id="ca341-126">对于此配置，以上示例中的顺序存储如下：</span><span class="sxs-lookup"><span data-stu-id="ca341-126">With this configuration the order from the example above is stored like this:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
        "ShipsToStreet": "221 B Baker St"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-692e763901d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163674
}
```

<span data-ttu-id="ca341-127">还嵌入了从属实体的集合。</span><span class="sxs-lookup"><span data-stu-id="ca341-127">Collections of owned entities are embedded as well.</span></span> <span data-ttu-id="ca341-128">对于下一个示例，我们将使用具有 `StreetAddress` 集合的 `Distributor` 类：</span><span class="sxs-lookup"><span data-stu-id="ca341-128">For the next example we'll use the `Distributor` class with a collection of `StreetAddress`:</span></span>

[!code-csharp[Distributor](../../../../samples/core/Cosmos/ModelBuilding/Distributor.cs?name=Distributor)]

<span data-ttu-id="ca341-129">从属实体不需要提供要存储的显式键值：</span><span class="sxs-lookup"><span data-stu-id="ca341-129">The owned entities don't need to provide explicit key values to be stored:</span></span>

[!code-csharp[OwnedCollection](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=OwnedCollection)]

<span data-ttu-id="ca341-130">它们将以这种方式持久保存：</span><span class="sxs-lookup"><span data-stu-id="ca341-130">They will be persisted in this way:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Distributor",
    "id": "Distributor|1",
    "ShippingCenters": [
        {
            "City": "Phoenix",
            "Discriminator": "StreetAddress",
            "Street": "500 S 48th Street"
        },
        {
            "City": "Anaheim",
            "Discriminator": "StreetAddress",
            "Street": "5650 Dolly Ave"
        }
    ],
    "_rid": "6QEKANzISj0BAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKANzISj0=/docs/6QEKANzISj0BAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-7b2b439701d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163705
}
```

<span data-ttu-id="ca341-131">在内部而言，EF Core 始终需要对所有被跟踪实体提供唯一键值。</span><span class="sxs-lookup"><span data-stu-id="ca341-131">Internally EF Core always needs to have unique key values for all tracked entities.</span></span> <span data-ttu-id="ca341-132">默认情况下，为从属类型集合创建的主键包含指向所有者的外键属性和与 JSON 数组中的索引对应的 `int` 属性。</span><span class="sxs-lookup"><span data-stu-id="ca341-132">The primary key created by default for collections of owned types consists of the foreign key properties pointing to the owner and an `int` property corresponding to the index in the JSON array.</span></span> <span data-ttu-id="ca341-133">要检索这些值，可使用以下条目 API：</span><span class="sxs-lookup"><span data-stu-id="ca341-133">To retrieve these values entry API could be used:</span></span>

[!code-csharp[ImpliedProperties](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?name=ImpliedProperties)]

> [!TIP]
> <span data-ttu-id="ca341-134">必要时，可更改从属实体类型的默认主键，但应显式提供键值。</span><span class="sxs-lookup"><span data-stu-id="ca341-134">When necessary the default primary key for the owned entity types can be changed, but then key values should be provided explicitly.</span></span>

## <a name="working-with-disconnected-entities"></a><span data-ttu-id="ca341-135">使用断开连接的实体</span><span class="sxs-lookup"><span data-stu-id="ca341-135">Working with Disconnected Entities</span></span>

<span data-ttu-id="ca341-136">每个项都需要具有一个对于给定分区键唯一的 `id` 值。</span><span class="sxs-lookup"><span data-stu-id="ca341-136">Every item needs to have an `id` value that is unique for the given partition key.</span></span> <span data-ttu-id="ca341-137">默认情况下 EF Core 通过使用 '|' 作为分隔符串联鉴别器和主键值来生成值。</span><span class="sxs-lookup"><span data-stu-id="ca341-137">By default EF Core generates the value by concatenating the discriminator and the primary key values, using '|' as a delimiter.</span></span> <span data-ttu-id="ca341-138">仅当实体进入 `Added` 状态时才生成键值。</span><span class="sxs-lookup"><span data-stu-id="ca341-138">The key values are only generated when an entity enters the `Added` state.</span></span> <span data-ttu-id="ca341-139">如果[附加实体](../../saving/disconnected-entities.md)在 .NET 类型上没有用于存储值的 `id` 属性，则这可能会导致问题。</span><span class="sxs-lookup"><span data-stu-id="ca341-139">This might pose a problem when [attaching entities](../../saving/disconnected-entities.md) if they don't have an `id` property on the .NET type to store the value.</span></span>

<span data-ttu-id="ca341-140">要解决此限制，可以手动创建并设置 `id` 值，或者先将实体标记为已添加，然后将其更改为所需状态：</span><span class="sxs-lookup"><span data-stu-id="ca341-140">To work around this limitation one could create and set the `id` value manually or mark the entity as added first, then changing it to the desired state:</span></span>

[!code-csharp[Attach](../../../../samples/core/Cosmos/ModelBuilding/Sample.cs?highlight=4&name=Attach)]

<span data-ttu-id="ca341-141">生成的 JSON 如下：</span><span class="sxs-lookup"><span data-stu-id="ca341-141">This is the resulting JSON:</span></span>

``` json
{
    "Id": 1,
    "Discriminator": "Order",
    "TrackingNumber": null,
    "id": "Order|1",
    "Address": {
        "ShipsToCity": "London",
        "Discriminator": "StreetAddress",
        "ShipsToStreet": "3 Abbey Road"
    },
    "_rid": "6QEKAM+BOOABAAAAAAAAAA==",
    "_self": "dbs/6QEKAA==/colls/6QEKAM+BOOA=/docs/6QEKAM+BOOABAAAAAAAAAA==/",
    "_etag": "\"00000000-0000-0000-683c-8f7ac48f01d5\"",
    "_attachments": "attachments/",
    "_ts": 1568163739
}
```

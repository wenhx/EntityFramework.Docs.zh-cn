---
title: 使用代理-EF6
description: 使用实体框架6中的代理
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
uid: ef6/fundamentals/proxies
ms.openlocfilehash: 26493ecf1a894a1cd421f574de38678661f324a0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618369"
---
# <a name="working-with-proxies"></a><span data-ttu-id="61c1c-103">使用代理</span><span class="sxs-lookup"><span data-stu-id="61c1c-103">Working with proxies</span></span>
<span data-ttu-id="61c1c-104">创建 POCO 实体类型实例时，实体框架通常会创建动态生成的派生类型的实例，该类型充当实体的代理。</span><span class="sxs-lookup"><span data-stu-id="61c1c-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="61c1c-105">此代理会重写实体的某些虚拟属性，以插入挂钩，以便在访问属性时自动执行操作。</span><span class="sxs-lookup"><span data-stu-id="61c1c-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="61c1c-106">例如，此机制用于支持关系的延迟加载。</span><span class="sxs-lookup"><span data-stu-id="61c1c-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="61c1c-107">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="61c1c-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="61c1c-108">禁用代理创建</span><span class="sxs-lookup"><span data-stu-id="61c1c-108">Disabling proxy creation</span></span>  

<span data-ttu-id="61c1c-109">有时，阻止实体框架创建代理实例会很有用。</span><span class="sxs-lookup"><span data-stu-id="61c1c-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="61c1c-110">例如，序列化非代理实例比序列化代理实例要容易得多。</span><span class="sxs-lookup"><span data-stu-id="61c1c-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="61c1c-111">可以通过清除 ProxyCreationEnabled 标志关闭代理创建。</span><span class="sxs-lookup"><span data-stu-id="61c1c-111">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="61c1c-112">您可以在上下文的构造函数中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="61c1c-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="61c1c-113">例如：</span><span class="sxs-lookup"><span data-stu-id="61c1c-113">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="61c1c-114">请注意，EF 不会为代理创建没有的类型的代理。</span><span class="sxs-lookup"><span data-stu-id="61c1c-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="61c1c-115">这意味着，你还可以通过使用密封的类型和/或没有虚拟属性来避免代理。</span><span class="sxs-lookup"><span data-stu-id="61c1c-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="61c1c-116">显式创建代理的实例</span><span class="sxs-lookup"><span data-stu-id="61c1c-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="61c1c-117">如果使用 new 运算符创建实体的实例，则不会创建代理实例。</span><span class="sxs-lookup"><span data-stu-id="61c1c-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="61c1c-118">这可能不是问题，但如果你需要创建代理实例 (例如，因此延迟加载或代理更改跟踪将起作用) 则可以使用 DbSet 的 Create 方法执行此操作。</span><span class="sxs-lookup"><span data-stu-id="61c1c-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="61c1c-119">例如：</span><span class="sxs-lookup"><span data-stu-id="61c1c-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="61c1c-120">如果要创建派生实体类型的实例，则可以使用 Create 的泛型版本。</span><span class="sxs-lookup"><span data-stu-id="61c1c-120">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="61c1c-121">例如：</span><span class="sxs-lookup"><span data-stu-id="61c1c-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="61c1c-122">请注意，Create 方法不会将创建的实体添加或附加到上下文。</span><span class="sxs-lookup"><span data-stu-id="61c1c-122">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="61c1c-123">请注意，如果为实体创建代理类型，Create 方法将仅创建实体类型本身的实例，因为它不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="61c1c-123">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="61c1c-124">例如，如果实体类型是密封的并且/或没有虚拟属性，则 Create 将只创建实体类型的实例。</span><span class="sxs-lookup"><span data-stu-id="61c1c-124">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="61c1c-125">从代理类型获取实际实体类型</span><span class="sxs-lookup"><span data-stu-id="61c1c-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="61c1c-126">代理类型的名称如下所示：</span><span class="sxs-lookup"><span data-stu-id="61c1c-126">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="61c1c-127">DynamicProxies.. Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="61c1c-127">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="61c1c-128">可以使用 ObjectContext 中的 GetObjectType 方法查找此代理类型的实体类型。</span><span class="sxs-lookup"><span data-stu-id="61c1c-128">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="61c1c-129">例如：</span><span class="sxs-lookup"><span data-stu-id="61c1c-129">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="61c1c-130">请注意，如果传递给 GetObjectType 的类型是不是代理类型的实体类型的实例，则仍将返回实体的类型。</span><span class="sxs-lookup"><span data-stu-id="61c1c-130">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="61c1c-131">这意味着，始终可以使用此方法获取实际的实体类型，而无需进行任何其他检查即可查看该类型是否为代理类型。</span><span class="sxs-lookup"><span data-stu-id="61c1c-131">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  

---
title: 使用代理-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415987"
---
# <a name="working-with-proxies"></a><span data-ttu-id="69c69-102">使用代理</span><span class="sxs-lookup"><span data-stu-id="69c69-102">Working with proxies</span></span>
<span data-ttu-id="69c69-103">创建 POCO 实体类型实例时，实体框架通常会创建动态生成的派生类型的实例，该类型充当实体的代理。</span><span class="sxs-lookup"><span data-stu-id="69c69-103">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="69c69-104">此代理会重写实体的某些虚拟属性，以插入挂钩，以便在访问属性时自动执行操作。</span><span class="sxs-lookup"><span data-stu-id="69c69-104">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="69c69-105">例如，此机制用于支持关系的延迟加载。</span><span class="sxs-lookup"><span data-stu-id="69c69-105">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="69c69-106">本主题所介绍的方法同样适用于查询使用 Code First 和 EF 设计器创建的模型。</span><span class="sxs-lookup"><span data-stu-id="69c69-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="69c69-107">禁用代理创建</span><span class="sxs-lookup"><span data-stu-id="69c69-107">Disabling proxy creation</span></span>  

<span data-ttu-id="69c69-108">有时，阻止实体框架创建代理实例会很有用。</span><span class="sxs-lookup"><span data-stu-id="69c69-108">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="69c69-109">例如，序列化非代理实例比序列化代理实例要容易得多。</span><span class="sxs-lookup"><span data-stu-id="69c69-109">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="69c69-110">可以通过清除 ProxyCreationEnabled 标志关闭代理创建。</span><span class="sxs-lookup"><span data-stu-id="69c69-110">Proxy creation can be turned off by clearing the ProxyCreationEnabled flag.</span></span> <span data-ttu-id="69c69-111">您可以在上下文的构造函数中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="69c69-111">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="69c69-112">例如：</span><span class="sxs-lookup"><span data-stu-id="69c69-112">For example:</span></span>  

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

<span data-ttu-id="69c69-113">请注意，EF 不会为代理创建没有的类型的代理。</span><span class="sxs-lookup"><span data-stu-id="69c69-113">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="69c69-114">这意味着，你还可以通过使用密封的类型和/或没有虚拟属性来避免代理。</span><span class="sxs-lookup"><span data-stu-id="69c69-114">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="69c69-115">显式创建代理的实例</span><span class="sxs-lookup"><span data-stu-id="69c69-115">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="69c69-116">如果使用 new 运算符创建实体的实例，则不会创建代理实例。</span><span class="sxs-lookup"><span data-stu-id="69c69-116">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="69c69-117">这可能不是问题，但如果需要创建代理实例（例如，使延迟加载或代理更改跟踪有效），则可以使用 DbSet 的 Create 方法来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="69c69-117">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the Create method of DbSet.</span></span> <span data-ttu-id="69c69-118">例如：</span><span class="sxs-lookup"><span data-stu-id="69c69-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="69c69-119">如果要创建派生实体类型的实例，则可以使用 Create 的泛型版本。</span><span class="sxs-lookup"><span data-stu-id="69c69-119">The generic version of Create can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="69c69-120">例如：</span><span class="sxs-lookup"><span data-stu-id="69c69-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="69c69-121">请注意，Create 方法不会将创建的实体添加或附加到上下文。</span><span class="sxs-lookup"><span data-stu-id="69c69-121">Note that the Create method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="69c69-122">请注意，如果为实体创建代理类型，Create 方法将仅创建实体类型本身的实例，因为它不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="69c69-122">Note that the Create method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="69c69-123">例如，如果实体类型是密封的并且/或没有虚拟属性，则 Create 将只创建实体类型的实例。</span><span class="sxs-lookup"><span data-stu-id="69c69-123">For example, if the entity type is sealed and/or has no virtual properties then Create will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="69c69-124">从代理类型获取实际实体类型</span><span class="sxs-lookup"><span data-stu-id="69c69-124">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="69c69-125">代理类型的名称如下所示：</span><span class="sxs-lookup"><span data-stu-id="69c69-125">Proxy types have names that look something like this:</span></span>  

<span data-ttu-id="69c69-126">DynamicProxies. Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span><span class="sxs-lookup"><span data-stu-id="69c69-126">System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6</span></span>  

<span data-ttu-id="69c69-127">可以使用 ObjectContext 中的 GetObjectType 方法查找此代理类型的实体类型。</span><span class="sxs-lookup"><span data-stu-id="69c69-127">You can find the entity type for this proxy type using the GetObjectType method from ObjectContext.</span></span> <span data-ttu-id="69c69-128">例如：</span><span class="sxs-lookup"><span data-stu-id="69c69-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="69c69-129">请注意，如果传递给 GetObjectType 的类型是不是代理类型的实体类型的实例，则仍将返回实体的类型。</span><span class="sxs-lookup"><span data-stu-id="69c69-129">Note that if the type passed to GetObjectType is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="69c69-130">这意味着，始终可以使用此方法获取实际的实体类型，而无需进行任何其他检查即可查看该类型是否为代理类型。</span><span class="sxs-lookup"><span data-stu-id="69c69-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  

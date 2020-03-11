---
title: 使用模拟框架进行测试-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 790e077c5b30c4a68a96b3c1a99b40893b2bbe55
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415993"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="e6b37-102">使用模拟框架进行测试</span><span class="sxs-lookup"><span data-stu-id="e6b37-102">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="e6b37-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="e6b37-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="e6b37-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="e6b37-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="e6b37-105">编写应用程序测试时，通常需要避免数据库的命中。</span><span class="sxs-lookup"><span data-stu-id="e6b37-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="e6b37-106">实体框架使你可以通过创建上下文来实现此目的，该上下文具有测试定义的行为，从而利用内存中数据。</span><span class="sxs-lookup"><span data-stu-id="e6b37-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="e6b37-107">用于创建测试双精度的选项</span><span class="sxs-lookup"><span data-stu-id="e6b37-107">Options for creating test doubles</span></span>  

<span data-ttu-id="e6b37-108">可使用两种不同的方法创建上下文的内存中版本。</span><span class="sxs-lookup"><span data-stu-id="e6b37-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="e6b37-109">**创建自己的测试双精度**型–此方法涉及编写您自己的上下文和 dbset 的内存中实现。</span><span class="sxs-lookup"><span data-stu-id="e6b37-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="e6b37-110">这使你可以很好地控制类的行为，但可能涉及到编写和拥有合理的代码量。</span><span class="sxs-lookup"><span data-stu-id="e6b37-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="e6b37-111">**使用模拟框架创建测试双精度**型–使用模拟框架（如 Moq），可以在运行时为你的上下文提供内存中实现并在运行时动态创建。</span><span class="sxs-lookup"><span data-stu-id="e6b37-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="e6b37-112">本文将介绍如何使用模拟框架。</span><span class="sxs-lookup"><span data-stu-id="e6b37-112">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="e6b37-113">若要创建自己的测试，请参阅[采用自己的测试进行测试双精度](writing-test-doubles.md)。</span><span class="sxs-lookup"><span data-stu-id="e6b37-113">For creating your own test doubles see [Testing with Your Own Test Doubles](writing-test-doubles.md).</span></span>  

<span data-ttu-id="e6b37-114">为了演示如何将 EF 与模拟框架结合使用，我们将使用 Moq。</span><span class="sxs-lookup"><span data-stu-id="e6b37-114">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="e6b37-115">获取 Moq 的最简单方法是[从 NuGet 安装 Moq 包](https://nuget.org/packages/Moq/)。</span><span class="sxs-lookup"><span data-stu-id="e6b37-115">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="e6b37-116">用预 EF6 版本测试</span><span class="sxs-lookup"><span data-stu-id="e6b37-116">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="e6b37-117">本文中所述的方案取决于我们对 EF6 中的 DbSet 进行的一些更改。</span><span class="sxs-lookup"><span data-stu-id="e6b37-117">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="e6b37-118">若要使用 EF5 和更早的版本进行测试，请参阅[使用虚设上下文进行测试](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="e6b37-118">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="e6b37-119">EF 内存中测试的限制双精度</span><span class="sxs-lookup"><span data-stu-id="e6b37-119">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="e6b37-120">内存中测试双精度型可以是一种提供使用 EF 的应用程序的单元测试级别的好方法。</span><span class="sxs-lookup"><span data-stu-id="e6b37-120">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="e6b37-121">但是，在执行此操作时，将使用 LINQ to Objects 对内存中数据执行查询。</span><span class="sxs-lookup"><span data-stu-id="e6b37-121">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="e6b37-122">这可能会导致不同的行为，而不是使用 EF 的 LINQ 提供程序（LINQ to Entities）将查询转换为对数据库运行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="e6b37-122">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="e6b37-123">这种差异的一个示例就是加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="e6b37-123">One example of such a difference is loading related data.</span></span> <span data-ttu-id="e6b37-124">如果创建一系列博客，其中每个博客都有相关的文章，则在使用内存中数据时，将始终为每个博客加载相关文章。</span><span class="sxs-lookup"><span data-stu-id="e6b37-124">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="e6b37-125">但是，在对数据库运行时，仅当使用 Include 方法时才会加载数据。</span><span class="sxs-lookup"><span data-stu-id="e6b37-125">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="e6b37-126">出于此原因，建议始终包括某个级别的端到端测试（除了单元测试），以确保应用程序能够对数据库正常运行。</span><span class="sxs-lookup"><span data-stu-id="e6b37-126">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="e6b37-127">与本文一起介绍</span><span class="sxs-lookup"><span data-stu-id="e6b37-127">Following along with this article</span></span>  

<span data-ttu-id="e6b37-128">本文提供了完整的代码清单，你可以将其复制到 Visual Studio 中，以根据你的需要进行。</span><span class="sxs-lookup"><span data-stu-id="e6b37-128">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="e6b37-129">最简单的方法是创建一个**单元测试项目**，并需要将 **.NET Framework 4.5**作为目标来完成使用 async 的部分。</span><span class="sxs-lookup"><span data-stu-id="e6b37-129">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="e6b37-130">EF 模型</span><span class="sxs-lookup"><span data-stu-id="e6b37-130">The EF model</span></span>  

<span data-ttu-id="e6b37-131">要测试的服务利用的是由 "Bloggingcontext" 和博客和 Post 类组成的 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="e6b37-131">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="e6b37-132">此代码可能是由 EF 设计器生成的，或是 Code First 模型中。</span><span class="sxs-lookup"><span data-stu-id="e6b37-132">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="e6b37-133">虚拟 DbSet 属性与 EF 设计器</span><span class="sxs-lookup"><span data-stu-id="e6b37-133">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="e6b37-134">请注意，上下文中的 DbSet 属性标记为虚拟。</span><span class="sxs-lookup"><span data-stu-id="e6b37-134">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="e6b37-135">这将允许模拟框架从我们的上下文派生，并使用模拟实现覆盖这些属性。</span><span class="sxs-lookup"><span data-stu-id="e6b37-135">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="e6b37-136">如果使用 Code First，则可以直接编辑类。</span><span class="sxs-lookup"><span data-stu-id="e6b37-136">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="e6b37-137">如果使用的是 EF 设计器，则需要编辑生成上下文的 T4 模板。</span><span class="sxs-lookup"><span data-stu-id="e6b37-137">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="e6b37-138">打开\>\<model_name。Context.tt 文件嵌套在 edmx 文件下，查找以下代码片段，并将其添加到 virtual 关键字中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="e6b37-138">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="e6b37-139">要测试的服务</span><span class="sxs-lookup"><span data-stu-id="e6b37-139">Service to be tested</span></span>  

<span data-ttu-id="e6b37-140">为了演示使用内存中测试的测试，我们将为 BlogService 编写一些测试。</span><span class="sxs-lookup"><span data-stu-id="e6b37-140">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="e6b37-141">该服务可以创建新的博客（AddBlog）并返回按名称排序的所有博客（GetAllBlogs）。</span><span class="sxs-lookup"><span data-stu-id="e6b37-141">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="e6b37-142">除了 GetAllBlogs 之外，我们还提供了一个方法，该方法将异步获取按名称（GetAllBlogsAsync）排序的所有博客。</span><span class="sxs-lookup"><span data-stu-id="e6b37-142">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```  

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="e6b37-143">测试非查询方案</span><span class="sxs-lookup"><span data-stu-id="e6b37-143">Testing non-query scenarios</span></span>  

<span data-ttu-id="e6b37-144">这就是开始测试非查询方法所需的所有操作。</span><span class="sxs-lookup"><span data-stu-id="e6b37-144">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="e6b37-145">以下测试使用 Moq 创建上下文。</span><span class="sxs-lookup"><span data-stu-id="e6b37-145">The following test uses Moq to create a context.</span></span> <span data-ttu-id="e6b37-146">然后，它将创建一个 DbSet\<博客\> 并向其线路，使其从上下文的博客属性返回。</span><span class="sxs-lookup"><span data-stu-id="e6b37-146">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="e6b37-147">接下来，使用上下文创建新的 BlogService，然后使用 AddBlog 方法创建新的博客。</span><span class="sxs-lookup"><span data-stu-id="e6b37-147">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="e6b37-148">最后，测试将验证该服务是否在上下文中添加了新的博客并调用了 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="e6b37-148">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a><span data-ttu-id="e6b37-149">测试查询方案</span><span class="sxs-lookup"><span data-stu-id="e6b37-149">Testing query scenarios</span></span>  

<span data-ttu-id="e6b37-150">为了能够对 DbSet 测试执行查询，我们需要设置 IQueryable 的实现。</span><span class="sxs-lookup"><span data-stu-id="e6b37-150">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="e6b37-151">第一步是创建一些内存中数据，我们使用的是博客\>\<列表。</span><span class="sxs-lookup"><span data-stu-id="e6b37-151">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="e6b37-152">接下来，我们将创建一个上下文和 DBSet\<博客\> 然后将 DbSet 的 IQueryable 实现连接在一起–它们只是委托给适用于 List\<T\>的 LINQ to Objects 提供程序。</span><span class="sxs-lookup"><span data-stu-id="e6b37-152">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="e6b37-153">然后，可以根据我们的测试来创建 BlogService，并确保从 GetAllBlogs 返回的数据按名称排序。</span><span class="sxs-lookup"><span data-stu-id="e6b37-153">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a><span data-ttu-id="e6b37-154">用异步查询进行测试</span><span class="sxs-lookup"><span data-stu-id="e6b37-154">Testing with async queries</span></span>

<span data-ttu-id="e6b37-155">实体框架6引入了一组扩展方法，这些方法可用于以异步方式执行查询。</span><span class="sxs-lookup"><span data-stu-id="e6b37-155">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="e6b37-156">这些方法的示例包括 ToListAsync、FirstAsync、ForEachAsync 等。</span><span class="sxs-lookup"><span data-stu-id="e6b37-156">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="e6b37-157">由于实体框架查询使用 LINQ，因此扩展方法是在 IQueryable 和 IEnumerable 上定义的。</span><span class="sxs-lookup"><span data-stu-id="e6b37-157">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="e6b37-158">但是，因为它们仅设计为与实体框架一起使用，所以如果尝试在不是实体框架查询的 LINQ 查询中使用它们，则可能会收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="e6b37-158">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="e6b37-159">源 IQueryable 不实现 IDbAsyncEnumerable{0}。</span><span class="sxs-lookup"><span data-stu-id="e6b37-159">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="e6b37-160">仅实现 IDbAsyncEnumerable 的源可用于实体框架异步操作。</span><span class="sxs-lookup"><span data-stu-id="e6b37-160">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="e6b37-161">有关详细信息，请参阅[http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068)。</span><span class="sxs-lookup"><span data-stu-id="e6b37-161">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="e6b37-162">尽管异步方法仅在针对 EF 查询运行时受支持，但在对 DbSet 的内存中测试双精度运行时，您可能需要在单元测试中使用它们。</span><span class="sxs-lookup"><span data-stu-id="e6b37-162">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="e6b37-163">若要使用异步方法，我们需要创建内存中的 DbAsyncQueryProvider 以处理异步查询。</span><span class="sxs-lookup"><span data-stu-id="e6b37-163">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="e6b37-164">尽管可以使用 Moq 设置查询提供程序，但使用代码创建测试双实现要容易得多。</span><span class="sxs-lookup"><span data-stu-id="e6b37-164">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="e6b37-165">此实现的代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="e6b37-165">The code for this implementation is as follows:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

<span data-ttu-id="e6b37-166">现在，我们有了一个异步查询提供程序，我们可以为新的 GetAllBlogsAsync 方法编写单元测试。</span><span class="sxs-lookup"><span data-stu-id="e6b37-166">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

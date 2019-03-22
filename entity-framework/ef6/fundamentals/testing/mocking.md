---
title: 使用模拟框架的 EF6 进行测试
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 3d39b41018beb70b72105dfb2fe4d61afc0b0525
ms.sourcegitcommit: eb8359b7ab3b0a1a08522faf67b703a00ecdcefd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319200"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="ed356-102">使用模拟框架进行测试</span><span class="sxs-lookup"><span data-stu-id="ed356-102">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="ed356-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="ed356-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="ed356-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="ed356-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="ed356-105">为你的应用程序编写测试是，通常需要避免与数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="ed356-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="ed356-106">实体框架，可通过创建一个上下文 – 替换为你的测试 – 定义行为实现此目的使用内存中的数据。</span><span class="sxs-lookup"><span data-stu-id="ed356-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="ed356-107">用于创建 test double 的选项</span><span class="sxs-lookup"><span data-stu-id="ed356-107">Options for creating test doubles</span></span>  

<span data-ttu-id="ed356-108">有两种可用于创建您的上下文的内存中版本的不同方法。</span><span class="sxs-lookup"><span data-stu-id="ed356-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="ed356-109">**创建你自己的 test double** – 这种方法涉及编写您自己的上下文和 Dbset 的内存中实现。</span><span class="sxs-lookup"><span data-stu-id="ed356-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="ed356-110">这使你可以控制如何类的行为，但可以写入和拥有合理的代码涉及很多。</span><span class="sxs-lookup"><span data-stu-id="ed356-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="ed356-111">**使用模拟框架来创建 test double** – 使用模拟框架 （如 Moq) 可以具有的内存中实现的上下文和动态创建在运行时为你的设置。</span><span class="sxs-lookup"><span data-stu-id="ed356-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="ed356-112">使用模拟框架将处理这篇文章。</span><span class="sxs-lookup"><span data-stu-id="ed356-112">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="ed356-113">有关创建自己的 test double 请参阅[测试与你自己的 Test Double](writing-test-doubles.md)。</span><span class="sxs-lookup"><span data-stu-id="ed356-113">For creating your own test doubles see [Testing with Your Own Test Doubles](writing-test-doubles.md).</span></span>  

<span data-ttu-id="ed356-114">若要演示如何使用模拟框架使用 EF 我们将使用 Moq。</span><span class="sxs-lookup"><span data-stu-id="ed356-114">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="ed356-115">若要获取 Moq 的最简单方法是安装[来自 NuGet 的 Moq 包](http://nuget.org/packages/Moq/)。</span><span class="sxs-lookup"><span data-stu-id="ed356-115">The easiest way to get Moq is to install the [Moq package from NuGet](http://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="ed356-116">使用预 EF6 版本进行测试</span><span class="sxs-lookup"><span data-stu-id="ed356-116">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="ed356-117">在本文中所示的方案是依赖于我们对 DbSet 在 EF6 中进行一些更改。</span><span class="sxs-lookup"><span data-stu-id="ed356-117">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="ed356-118">要使用 EF5 和早期版本进行测试，请参阅[测试使用虚设上下文](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="ed356-118">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="ed356-119">EF 内存中测试双精度数的限制</span><span class="sxs-lookup"><span data-stu-id="ed356-119">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="ed356-120">内存中测试双精度型值可以是提供单元级别的测试覆盖率的应用程序使用 EF 的位的好方法。</span><span class="sxs-lookup"><span data-stu-id="ed356-120">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="ed356-121">但是，执行此操作时将使用 LINQ to Objects 对内存中数据执行查询。</span><span class="sxs-lookup"><span data-stu-id="ed356-121">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="ed356-122">这可能导致比使用 EF 的 LINQ 提供程序 (LINQ to Entities) 为针对数据库运行的 SQL 查询转换为不同的行为。</span><span class="sxs-lookup"><span data-stu-id="ed356-122">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="ed356-123">此类差异的一个示例加载相关的数据。</span><span class="sxs-lookup"><span data-stu-id="ed356-123">One example of such a difference is loading related data.</span></span> <span data-ttu-id="ed356-124">如果创建一系列的博客，每个具有相关的文章，则在使用内存中数据相关的帖子将始终加载每个博客。</span><span class="sxs-lookup"><span data-stu-id="ed356-124">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="ed356-125">但是，针对数据库运行时，如果您使用 Include 方法只被加载数据。</span><span class="sxs-lookup"><span data-stu-id="ed356-125">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="ed356-126">出于此原因，建议始终包括一定程度的端到端测试 （除了单元测试），确保正确地对数据库应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="ed356-126">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="ed356-127">根据这篇文章</span><span class="sxs-lookup"><span data-stu-id="ed356-127">Following along with this article</span></span>  

<span data-ttu-id="ed356-128">本文提供了您可以复制到 Visual Studio，若要跟着介绍一起操作，如果您想的完整代码列表。</span><span class="sxs-lookup"><span data-stu-id="ed356-128">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="ed356-129">它是最简单的方法创建**单元测试项目**，你需要为目标 **.NET Framework 4.5**来完成使用异步的部分。</span><span class="sxs-lookup"><span data-stu-id="ed356-129">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="ed356-130">EF 模型</span><span class="sxs-lookup"><span data-stu-id="ed356-130">The EF model</span></span>  

<span data-ttu-id="ed356-131">我们要测试的服务利用了 EF 模型 bloggingcontext 和在博客和文章类组成。</span><span class="sxs-lookup"><span data-stu-id="ed356-131">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="ed356-132">此代码可能由 EF 设计器生成也是 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="ed356-132">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="ed356-133">使用 EF 设计器的虚拟 DbSet 属性</span><span class="sxs-lookup"><span data-stu-id="ed356-133">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="ed356-134">请注意在上下文的 DbSet 属性标记为虚拟的。</span><span class="sxs-lookup"><span data-stu-id="ed356-134">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="ed356-135">这将允许模拟框架，用于从我们的上下文和重写的模拟实现这些属性派生。</span><span class="sxs-lookup"><span data-stu-id="ed356-135">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="ed356-136">如果使用 Code First 然后可以直接编辑您的类。</span><span class="sxs-lookup"><span data-stu-id="ed356-136">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="ed356-137">如果您使用的 EF 设计器将需要编辑 T4 模板生成您的上下文。</span><span class="sxs-lookup"><span data-stu-id="ed356-137">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="ed356-138">打开\<model_name\>。Context.tt 文件下您 edmx 文件，嵌套的查找以下代码段，并添加 virtual 关键字中所示。</span><span class="sxs-lookup"><span data-stu-id="ed356-138">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

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

## <a name="service-to-be-tested"></a><span data-ttu-id="ed356-139">要测试的服务</span><span class="sxs-lookup"><span data-stu-id="ed356-139">Service to be tested</span></span>  

<span data-ttu-id="ed356-140">若要演示如何使用内存中 test double 测试我们将会为 BlogService 编写一些测试。</span><span class="sxs-lookup"><span data-stu-id="ed356-140">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="ed356-141">该服务是能够创建新的博客 (AddBlog)，返回所有博客按名称 (GetAllBlogs) 排序。</span><span class="sxs-lookup"><span data-stu-id="ed356-141">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="ed356-142">除了 GetAllBlogs，我们还提供了将以异步方式获取按名称 (GetAllBlogsAsync) 排序的所有博客的方法。</span><span class="sxs-lookup"><span data-stu-id="ed356-142">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="ed356-143">测试非查询方案</span><span class="sxs-lookup"><span data-stu-id="ed356-143">Testing non-query scenarios</span></span>  

<span data-ttu-id="ed356-144">这是我们需要执行的操作开始测试非查询方法。</span><span class="sxs-lookup"><span data-stu-id="ed356-144">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="ed356-145">以下测试使用 Moq 创建上下文。</span><span class="sxs-lookup"><span data-stu-id="ed356-145">The following test uses Moq to create a context.</span></span> <span data-ttu-id="ed356-146">然后，它创建 DbSet\<博客\>，并向上连接要从上下文的博客属性中返回。</span><span class="sxs-lookup"><span data-stu-id="ed356-146">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="ed356-147">接下来，上下文用于创建新 BlogService 然后用于创建新的博客-使用 AddBlog 方法。</span><span class="sxs-lookup"><span data-stu-id="ed356-147">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="ed356-148">最后，此测试将验证该服务添加新的博客，并在上下文中调用 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="ed356-148">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

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

## <a name="testing-query-scenarios"></a><span data-ttu-id="ed356-149">测试查询方案</span><span class="sxs-lookup"><span data-stu-id="ed356-149">Testing query scenarios</span></span>  

<span data-ttu-id="ed356-150">若要执行针对我们的 DbSet 测试双精度的查询将我们需要设置的 IQueryable 实现。</span><span class="sxs-lookup"><span data-stu-id="ed356-150">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="ed356-151">第一步是创建一些内存中数据 – 我们将使用列表\<博客\>。</span><span class="sxs-lookup"><span data-stu-id="ed356-151">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="ed356-152">接下来，我们创建一个上下文和 DBSet\<博客\>然后接通 DbSet – IQueryable 实现它们只需将委派给 LINQ to Objects 提供程序的列表的合作\<T\>。</span><span class="sxs-lookup"><span data-stu-id="ed356-152">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="ed356-153">然后，我们可以创建基于我们的 test double BlogService 并确保我们得到从 GetAllBlogs 的数据按名称排序。</span><span class="sxs-lookup"><span data-stu-id="ed356-153">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

### <a name="testing-with-async-queries"></a><span data-ttu-id="ed356-154">使用异步查询测试</span><span class="sxs-lookup"><span data-stu-id="ed356-154">Testing with async queries</span></span>

<span data-ttu-id="ed356-155">Entity Framework 6 引入了一组可用于以异步方式执行查询的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="ed356-155">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="ed356-156">这些方法的示例包括 ToListAsync、 FirstAsync、 ForEachAsync 等。</span><span class="sxs-lookup"><span data-stu-id="ed356-156">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="ed356-157">因为实体框架查询使使用 LINQ，扩展方法定义 IQueryable 和 IEnumerable。</span><span class="sxs-lookup"><span data-stu-id="ed356-157">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="ed356-158">但是，因为它们仅旨在与实体框架一起使用可能会收到以下错误，如果你尝试将其用于不是实体框架查询的 LINQ 查询：</span><span class="sxs-lookup"><span data-stu-id="ed356-158">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="ed356-159">源 IQueryable 没有实现 IDbAsyncEnumerable{0}。</span><span class="sxs-lookup"><span data-stu-id="ed356-159">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="ed356-160">实现 IDbAsyncEnumerable 的源可以用于实体框架的异步操作。</span><span class="sxs-lookup"><span data-stu-id="ed356-160">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="ed356-161">有关更多详细信息，请参阅[ http://go.microsoft.com/fwlink/?LinkId=287068 ](https://go.microsoft.com/fwlink/?LinkId=287068)。</span><span class="sxs-lookup"><span data-stu-id="ed356-161">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="ed356-162">同时对 EF 查询运行时，才支持异步方法，可能想要针对内存中运行测试的 DbSet 的双时，在单元测试中使用它们。</span><span class="sxs-lookup"><span data-stu-id="ed356-162">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="ed356-163">若要使用异步方法，我们需要创建内存中 DbAsyncQueryProvider 来处理异步查询。</span><span class="sxs-lookup"><span data-stu-id="ed356-163">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="ed356-164">很可能设置使用 Moq 向查询提供程序，同时是更易于在代码中创建测试 double 实现。</span><span class="sxs-lookup"><span data-stu-id="ed356-164">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="ed356-165">此实现的代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="ed356-165">The code for this implementation is as follows:</span></span>  

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

<span data-ttu-id="ed356-166">现在，我们有一个异步查询提供商我们可以为我们新 GetAllBlogsAsync 方法编写单元测试。</span><span class="sxs-lookup"><span data-stu-id="ed356-166">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

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

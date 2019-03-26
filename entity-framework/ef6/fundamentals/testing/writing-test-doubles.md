---
title: 使用你自己的 test double 的 EF6 进行测试
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
ms.openlocfilehash: 9db56e28cd89084fece36c3e5a2c1b4495991d01
ms.sourcegitcommit: 645785187ae23ddf7d7b0642c7a4da5ffb0c7f30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419726"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="afe92-102">使用你自己的 test double 进行测试</span><span class="sxs-lookup"><span data-stu-id="afe92-102">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="afe92-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="afe92-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="afe92-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="afe92-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="afe92-105">为你的应用程序编写测试是，通常需要避免与数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="afe92-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="afe92-106">实体框架，可通过创建一个上下文 – 替换为你的测试 – 定义行为实现此目的使用内存中的数据。</span><span class="sxs-lookup"><span data-stu-id="afe92-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="afe92-107">用于创建 test double 的选项</span><span class="sxs-lookup"><span data-stu-id="afe92-107">Options for creating test doubles</span></span>  

<span data-ttu-id="afe92-108">有两种可用于创建您的上下文的内存中版本的不同方法。</span><span class="sxs-lookup"><span data-stu-id="afe92-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="afe92-109">**创建你自己的 test double** – 这种方法涉及编写您自己的上下文和 Dbset 的内存中实现。</span><span class="sxs-lookup"><span data-stu-id="afe92-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="afe92-110">这使你可以控制如何类的行为，但可以写入和拥有合理的代码涉及很多。</span><span class="sxs-lookup"><span data-stu-id="afe92-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="afe92-111">**使用模拟框架来创建 test double** – 使用模拟框架 （如 Moq)，可以有您的内存中实现上下文和动态创建在运行时为你的设置。</span><span class="sxs-lookup"><span data-stu-id="afe92-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="afe92-112">本文将处理创建你自己的测试双精度。</span><span class="sxs-lookup"><span data-stu-id="afe92-112">This article will deal with creating your own test double.</span></span> <span data-ttu-id="afe92-113">使用模拟框架的信息，请参阅[测试提供了模拟框架](mocking.md)。</span><span class="sxs-lookup"><span data-stu-id="afe92-113">For information on using a mocking framework see [Testing with a Mocking Framework](mocking.md).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="afe92-114">使用预 EF6 版本进行测试</span><span class="sxs-lookup"><span data-stu-id="afe92-114">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="afe92-115">在本文中所示的代码适用于 EF6。</span><span class="sxs-lookup"><span data-stu-id="afe92-115">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="afe92-116">要使用 EF5 和早期版本进行测试，请参阅[测试使用虚设上下文](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="afe92-116">For testing with EF5 and earlier version see [Testing with a Fake Context](http://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="afe92-117">EF 内存中测试双精度数的限制</span><span class="sxs-lookup"><span data-stu-id="afe92-117">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="afe92-118">内存中测试双精度型值可以是提供单元级别的测试覆盖率的应用程序使用 EF 的位的好方法。</span><span class="sxs-lookup"><span data-stu-id="afe92-118">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="afe92-119">但是，执行此操作时将使用 LINQ to Objects 对内存中数据执行查询。</span><span class="sxs-lookup"><span data-stu-id="afe92-119">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="afe92-120">这可能导致比使用 EF 的 LINQ 提供程序 (LINQ to Entities) 为针对数据库运行的 SQL 查询转换为不同的行为。</span><span class="sxs-lookup"><span data-stu-id="afe92-120">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="afe92-121">此类差异的一个示例加载相关的数据。</span><span class="sxs-lookup"><span data-stu-id="afe92-121">One example of such a difference is loading related data.</span></span> <span data-ttu-id="afe92-122">如果创建一系列的博客，每个具有相关的文章，则在使用内存中数据相关的帖子将始终加载每个博客。</span><span class="sxs-lookup"><span data-stu-id="afe92-122">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="afe92-123">但是，针对数据库运行时，如果您使用 Include 方法只被加载数据。</span><span class="sxs-lookup"><span data-stu-id="afe92-123">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="afe92-124">出于此原因，建议始终包括一定程度的端到端测试 （除了单元测试），确保正确地对数据库应用程序正常工作。</span><span class="sxs-lookup"><span data-stu-id="afe92-124">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="afe92-125">根据这篇文章</span><span class="sxs-lookup"><span data-stu-id="afe92-125">Following along with this article</span></span>  

<span data-ttu-id="afe92-126">本文提供了您可以复制到 Visual Studio，若要跟着介绍一起操作，如果您想的完整代码列表。</span><span class="sxs-lookup"><span data-stu-id="afe92-126">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="afe92-127">它是最简单的方法创建**单元测试项目**，你需要为目标 **.NET Framework 4.5**来完成使用异步的部分。</span><span class="sxs-lookup"><span data-stu-id="afe92-127">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="afe92-128">创建上下文接口</span><span class="sxs-lookup"><span data-stu-id="afe92-128">Creating a context interface</span></span>  

<span data-ttu-id="afe92-129">我们要看一下测试利用了 EF 的服务模型。</span><span class="sxs-lookup"><span data-stu-id="afe92-129">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="afe92-130">为了能够使用内存中版本进行测试替换我们的 EF 上下文，我们将定义我们的 EF 上下文 （和内存中双） 将实现的接口。</span><span class="sxs-lookup"><span data-stu-id="afe92-130">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="afe92-131">我们要测试该服务将查询和修改使用我们的上下文的 DbSet 属性的数据，调用 SaveChanges 以将更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="afe92-131">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="afe92-132">因此我们要包括在接口上的这些成员。</span><span class="sxs-lookup"><span data-stu-id="afe92-132">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="afe92-133">EF 模型</span><span class="sxs-lookup"><span data-stu-id="afe92-133">The EF model</span></span>  

<span data-ttu-id="afe92-134">我们要测试的服务利用了 EF 模型 bloggingcontext 和在博客和文章类组成。</span><span class="sxs-lookup"><span data-stu-id="afe92-134">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="afe92-135">此代码可能由 EF 设计器生成也是 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="afe92-135">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="afe92-136">实现与 EF 设计器上下文接口</span><span class="sxs-lookup"><span data-stu-id="afe92-136">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="afe92-137">请注意，我们的上下文实现 IBloggingContext 接口。</span><span class="sxs-lookup"><span data-stu-id="afe92-137">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="afe92-138">如果使用 Code First 然后可以编辑您的上下文直接以实现该接口。</span><span class="sxs-lookup"><span data-stu-id="afe92-138">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="afe92-139">如果您使用的 EF 设计器将需要编辑 T4 模板生成您的上下文。</span><span class="sxs-lookup"><span data-stu-id="afe92-139">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="afe92-140">打开\<model_name\>。Context.tt 文件下您 edmx 文件，嵌套的查找以下代码段，并添加在界面中所示。</span><span class="sxs-lookup"><span data-stu-id="afe92-140">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="afe92-141">要测试的服务</span><span class="sxs-lookup"><span data-stu-id="afe92-141">Service to be tested</span></span>  

<span data-ttu-id="afe92-142">若要演示如何使用内存中 test double 测试我们将会为 BlogService 编写一些测试。</span><span class="sxs-lookup"><span data-stu-id="afe92-142">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="afe92-143">该服务是能够创建新的博客 (AddBlog)，返回所有博客按名称 (GetAllBlogs) 排序。</span><span class="sxs-lookup"><span data-stu-id="afe92-143">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="afe92-144">除了 GetAllBlogs，我们还提供了将以异步方式获取按名称 (GetAllBlogsAsync) 排序的所有博客的方法。</span><span class="sxs-lookup"><span data-stu-id="afe92-144">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
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

<span data-ttu-id="afe92-145"><a name="creating-the-in-memory-test-doubles"/> # # 创建内存中测试两倍，</span><span class="sxs-lookup"><span data-stu-id="afe92-145"><a name="creating-the-in-memory-test-doubles"/> ## Creating the in-memory test doubles</span></span>  

<span data-ttu-id="afe92-146">现在，我们已实际 EF 模型和服务可使用它，就可以创建内存中测试 double，我们可将用于测试。</span><span class="sxs-lookup"><span data-stu-id="afe92-146">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="afe92-147">我们已经为我们的上下文来创建 TestContext 测试双精度。</span><span class="sxs-lookup"><span data-stu-id="afe92-147">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="afe92-148">在 test double 选择我们想要以支持测试的行为，我们将我们将运行。</span><span class="sxs-lookup"><span data-stu-id="afe92-148">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="afe92-149">在此示例中我们只捕获的次数调用 SaveChanges 时，但可以包含验证正在测试的方案所需的任何逻辑。</span><span class="sxs-lookup"><span data-stu-id="afe92-149">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="afe92-150">我们还创建了提供内存中实现的 DbSet TestDbSet。</span><span class="sxs-lookup"><span data-stu-id="afe92-150">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="afe92-151">我们提供完整实现的所有方法，以在 DbSet （除外找到），但只需实现你的测试方案将使用的成员。</span><span class="sxs-lookup"><span data-stu-id="afe92-151">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="afe92-152">TestDbSet 使用我们提供了以确保可以处理异步查询的一些其他基础结构类。</span><span class="sxs-lookup"><span data-stu-id="afe92-152">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

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

### <a name="implementing-find"></a><span data-ttu-id="afe92-153">实现查找</span><span class="sxs-lookup"><span data-stu-id="afe92-153">Implementing Find</span></span>  

<span data-ttu-id="afe92-154">Find 方法很难采用一般形式实现。</span><span class="sxs-lookup"><span data-stu-id="afe92-154">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="afe92-155">如果您需要测试进行的代码使用 Find 方法是最简单的方法创建的每种需要支持实体类型的 DbSet 查找的测试。</span><span class="sxs-lookup"><span data-stu-id="afe92-155">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="afe92-156">然后可以编写逻辑来查找实体，该特定类型，如下所示。</span><span class="sxs-lookup"><span data-stu-id="afe92-156">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="afe92-157">编写测试</span><span class="sxs-lookup"><span data-stu-id="afe92-157">Writing some tests</span></span>  

<span data-ttu-id="afe92-158">这是我们需要执行的操作开始测试。</span><span class="sxs-lookup"><span data-stu-id="afe92-158">That’s all we need to do to start testing.</span></span> <span data-ttu-id="afe92-159">以下测试创建 TestContext，然后基于此上下文的服务。</span><span class="sxs-lookup"><span data-stu-id="afe92-159">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="afe92-160">然后使用该服务以创建新的博客-使用 AddBlog 方法。</span><span class="sxs-lookup"><span data-stu-id="afe92-160">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="afe92-161">最后，此测试将验证该服务添加到上下文的博客属性的新博客，并在上下文中调用 SaveChanges。</span><span class="sxs-lookup"><span data-stu-id="afe92-161">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="afe92-162">这是只是一个可以测试与双精度的内存中测试的内容类型的示例，您可以调整的 test double 和以满足您的要求的验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="afe92-162">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="afe92-163">下面是测试的这一次一个的方式执行查询的另一个示例。</span><span class="sxs-lookup"><span data-stu-id="afe92-163">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="afe92-164">测试开始通过使用其网络日志属性-请注意，数据不是按字母顺序中的某些数据创建测试上下文。</span><span class="sxs-lookup"><span data-stu-id="afe92-164">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="afe92-165">我们然后可以创建基于我们的测试上下文 BlogService 并确保我们得到从 GetAllBlogs 的数据按名称排序。</span><span class="sxs-lookup"><span data-stu-id="afe92-165">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="afe92-166">最后，我们将编写一个测试，以使用我们的异步方法以确保 async 基础结构我们纳入[TestDbSet](#creating-the-in-memory-test-doubles)是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="afe92-166">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
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
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

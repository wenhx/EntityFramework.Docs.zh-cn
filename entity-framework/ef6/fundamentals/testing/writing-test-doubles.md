---
title: 用自己的测试进行测试双 EF6
description: 在实体框架6中，用自己的测试进行测试翻倍
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 575007c5568049e6508d1bd6435597a1acd843ab
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070427"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="d71fd-103">用自己的测试进行测试双精度</span><span class="sxs-lookup"><span data-stu-id="d71fd-103">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="d71fd-104">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="d71fd-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="d71fd-105">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="d71fd-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="d71fd-106">编写应用程序测试时，通常需要避免数据库的命中。</span><span class="sxs-lookup"><span data-stu-id="d71fd-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="d71fd-107">实体框架使你可以通过创建上下文来实现此目的，该上下文具有测试定义的行为，从而利用内存中数据。</span><span class="sxs-lookup"><span data-stu-id="d71fd-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="d71fd-108">用于创建测试双精度的选项</span><span class="sxs-lookup"><span data-stu-id="d71fd-108">Options for creating test doubles</span></span>  

<span data-ttu-id="d71fd-109">可使用两种不同的方法创建上下文的内存中版本。</span><span class="sxs-lookup"><span data-stu-id="d71fd-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="d71fd-110">**创建自己的测试双精度** 型–此方法涉及编写您自己的上下文和 dbset 的内存中实现。</span><span class="sxs-lookup"><span data-stu-id="d71fd-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="d71fd-111">这使你可以很好地控制类的行为，但可能涉及到编写和拥有合理的代码量。</span><span class="sxs-lookup"><span data-stu-id="d71fd-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="d71fd-112">**使用模拟框架创建测试双精度** 数–使用模拟 framework (例如 Moq) 你可以在运行时为你提供上下文的内存中实现并在运行时动态创建。</span><span class="sxs-lookup"><span data-stu-id="d71fd-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="d71fd-113">本文将介绍如何创建自己的测试 double。</span><span class="sxs-lookup"><span data-stu-id="d71fd-113">This article will deal with creating your own test double.</span></span> <span data-ttu-id="d71fd-114">有关使用模拟框架的信息，请参阅使用 [模拟框架进行测试](xref:ef6/fundamentals/testing/mocking)。</span><span class="sxs-lookup"><span data-stu-id="d71fd-114">For information on using a mocking framework see [Testing with a Mocking Framework](xref:ef6/fundamentals/testing/mocking).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="d71fd-115">用预 EF6 版本测试</span><span class="sxs-lookup"><span data-stu-id="d71fd-115">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="d71fd-116">本文中所示的代码与 EF6 兼容。</span><span class="sxs-lookup"><span data-stu-id="d71fd-116">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="d71fd-117">若要使用 EF5 和更早的版本进行测试，请参阅 [使用虚设上下文进行测试](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。</span><span class="sxs-lookup"><span data-stu-id="d71fd-117">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="d71fd-118">EF 内存中测试的限制双精度</span><span class="sxs-lookup"><span data-stu-id="d71fd-118">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="d71fd-119">内存中测试双精度型可以是一种提供使用 EF 的应用程序的单元测试级别的好方法。</span><span class="sxs-lookup"><span data-stu-id="d71fd-119">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="d71fd-120">但是，在执行此操作时，将使用 LINQ to Objects 对内存中数据执行查询。</span><span class="sxs-lookup"><span data-stu-id="d71fd-120">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="d71fd-121">这可能会导致不同的行为，而不是使用 EF 的 LINQ 提供程序 (LINQ to Entities) 将查询转换为对数据库运行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="d71fd-121">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="d71fd-122">这种差异的一个示例就是加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="d71fd-122">One example of such a difference is loading related data.</span></span> <span data-ttu-id="d71fd-123">如果创建一系列博客，其中每个博客都有相关的文章，则在使用内存中数据时，将始终为每个博客加载相关文章。</span><span class="sxs-lookup"><span data-stu-id="d71fd-123">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="d71fd-124">但是，在对数据库运行时，仅当使用 Include 方法时才会加载数据。</span><span class="sxs-lookup"><span data-stu-id="d71fd-124">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="d71fd-125">出于此原因，建议始终包含某些级别的端到端测试 (除了单元测试) ，以确保应用程序可针对数据库正常运行。</span><span class="sxs-lookup"><span data-stu-id="d71fd-125">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="d71fd-126">与本文一起介绍</span><span class="sxs-lookup"><span data-stu-id="d71fd-126">Following along with this article</span></span>  

<span data-ttu-id="d71fd-127">本文提供了完整的代码清单，你可以将其复制到 Visual Studio 中，以根据你的需要进行。</span><span class="sxs-lookup"><span data-stu-id="d71fd-127">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="d71fd-128">最简单的方法是创建一个 **单元测试项目** ，并需要将 **.NET Framework 4.5** 作为目标来完成使用 async 的部分。</span><span class="sxs-lookup"><span data-stu-id="d71fd-128">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="d71fd-129">创建上下文接口</span><span class="sxs-lookup"><span data-stu-id="d71fd-129">Creating a context interface</span></span>  

<span data-ttu-id="d71fd-130">我们将介绍如何测试使用 EF 模型的服务。</span><span class="sxs-lookup"><span data-stu-id="d71fd-130">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="d71fd-131">为了能够将 EF 上下文替换为内存中用于测试的版本，我们将定义 EF 上下文 (的接口，并且它的内存中双精度) 将实现。</span><span class="sxs-lookup"><span data-stu-id="d71fd-131">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="d71fd-132">我们将要测试的服务将使用上下文的 DbSet 属性来查询和修改数据，还会调用 SaveChanges 将更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="d71fd-132">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="d71fd-133">我们将这些成员包括在接口上。</span><span class="sxs-lookup"><span data-stu-id="d71fd-133">So we're including these members on the interface.</span></span>  

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

## <a name="the-ef-model"></a><span data-ttu-id="d71fd-134">EF 模型</span><span class="sxs-lookup"><span data-stu-id="d71fd-134">The EF model</span></span>  

<span data-ttu-id="d71fd-135">要测试的服务利用的是由 "Bloggingcontext" 和博客和 Post 类组成的 EF 模型。</span><span class="sxs-lookup"><span data-stu-id="d71fd-135">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="d71fd-136">此代码可能是由 EF 设计器生成的，或是 Code First 模型中。</span><span class="sxs-lookup"><span data-stu-id="d71fd-136">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="d71fd-137">通过 EF 设计器实现上下文接口</span><span class="sxs-lookup"><span data-stu-id="d71fd-137">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="d71fd-138">请注意，我们的上下文实现了 IBloggingContext 接口。</span><span class="sxs-lookup"><span data-stu-id="d71fd-138">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="d71fd-139">如果使用 Code First，则可以直接编辑上下文来实现接口。</span><span class="sxs-lookup"><span data-stu-id="d71fd-139">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="d71fd-140">如果使用的是 EF 设计器，则需要编辑生成上下文的 T4 模板。</span><span class="sxs-lookup"><span data-stu-id="d71fd-140">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="d71fd-141">打开 \<model_name\> 。Context.tt 文件嵌套在 edmx 文件下，查找以下代码片段，并将其添加到接口中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="d71fd-141">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="d71fd-142">要测试的服务</span><span class="sxs-lookup"><span data-stu-id="d71fd-142">Service to be tested</span></span>  

<span data-ttu-id="d71fd-143">为了演示使用内存中测试的测试，我们将为 BlogService 编写一些测试。</span><span class="sxs-lookup"><span data-stu-id="d71fd-143">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="d71fd-144">该服务可以创建新的博客 (AddBlog) 并返回按名称 (GetAllBlogs) 排序的所有博客。</span><span class="sxs-lookup"><span data-stu-id="d71fd-144">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="d71fd-145">除了 GetAllBlogs 之外，我们还提供了一个方法，该方法以异步方式获取按名称 (GetAllBlogsAsync) 排序的所有博客。</span><span class="sxs-lookup"><span data-stu-id="d71fd-145">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

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

## <a name="creating-the-in-memory-test-doubles"></a><span data-ttu-id="d71fd-146">创建内存中测试双精度</span><span class="sxs-lookup"><span data-stu-id="d71fd-146">Creating the in-memory test doubles</span></span>  

<span data-ttu-id="d71fd-147">现在，我们已经有了真正的 EF 模型和可使用该模型的服务，接下来可以创建可用于测试的内存中测试双精度。</span><span class="sxs-lookup"><span data-stu-id="d71fd-147">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="d71fd-148">我们为上下文创建了 TestContext 测试 double。</span><span class="sxs-lookup"><span data-stu-id="d71fd-148">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="d71fd-149">在测试中，我们会选择所需的行为，以便支持我们将要运行的测试。</span><span class="sxs-lookup"><span data-stu-id="d71fd-149">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="d71fd-150">在此示例中，我们只是捕获调用 SaveChanges 的次数，但你可以包括验证所测试方案所需的任何逻辑。</span><span class="sxs-lookup"><span data-stu-id="d71fd-150">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="d71fd-151">我们还创建了 TestDbSet 来提供 DbSet 的内存中实现。</span><span class="sxs-lookup"><span data-stu-id="d71fd-151">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="d71fd-152">我们为 DbSet (上的所有方法提供了一个完整的实现，但 Find) 除外，但你只需实现测试方案将使用的成员。</span><span class="sxs-lookup"><span data-stu-id="d71fd-152">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="d71fd-153">TestDbSet 利用我们提供的其他一些基础结构类，以确保可以处理异步查询。</span><span class="sxs-lookup"><span data-stu-id="d71fd-153">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

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

### <a name="implementing-find"></a><span data-ttu-id="d71fd-154">实现查找</span><span class="sxs-lookup"><span data-stu-id="d71fd-154">Implementing Find</span></span>  

<span data-ttu-id="d71fd-155">Find 方法难以以一般方式实现。</span><span class="sxs-lookup"><span data-stu-id="d71fd-155">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="d71fd-156">如果需要测试使用 Find 方法的代码，最简单的方法是为需要支持 find 的每个实体类型创建测试 DbSet。</span><span class="sxs-lookup"><span data-stu-id="d71fd-156">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="d71fd-157">然后，可以编写逻辑来查找特定类型的实体，如下所示。</span><span class="sxs-lookup"><span data-stu-id="d71fd-157">You can then write logic to find that particular type of entity, as shown below.</span></span>  

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

## <a name="writing-some-tests"></a><span data-ttu-id="d71fd-158">编写一些测试</span><span class="sxs-lookup"><span data-stu-id="d71fd-158">Writing some tests</span></span>  

<span data-ttu-id="d71fd-159">这就是开始测试时需要执行的所有操作。</span><span class="sxs-lookup"><span data-stu-id="d71fd-159">That’s all we need to do to start testing.</span></span> <span data-ttu-id="d71fd-160">以下测试将创建 TestContext，然后基于此上下文创建一个服务。</span><span class="sxs-lookup"><span data-stu-id="d71fd-160">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="d71fd-161">然后，使用 AddBlog 方法创建新的博客。</span><span class="sxs-lookup"><span data-stu-id="d71fd-161">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="d71fd-162">最后，此测试将验证服务是否已将新的博客添加到上下文的 "博客" 属性，并在上下文中调用 "SaveChanges"。</span><span class="sxs-lookup"><span data-stu-id="d71fd-162">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="d71fd-163">这只是您可以使用内存中测试双精度测试的类型的示例，您可以根据您的要求调整测试的逻辑加倍和验证。</span><span class="sxs-lookup"><span data-stu-id="d71fd-163">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

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

<span data-ttu-id="d71fd-164">下面是测试的另一个示例-这是执行查询的另一个示例。</span><span class="sxs-lookup"><span data-stu-id="d71fd-164">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="d71fd-165">首先，通过在其 "博客" 属性中创建包含某些数据的测试上下文来开始测试，请注意，数据不按字母顺序排序。</span><span class="sxs-lookup"><span data-stu-id="d71fd-165">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="d71fd-166">然后，可以基于测试上下文创建 BlogService，并确保从 GetAllBlogs 返回的数据按名称排序。</span><span class="sxs-lookup"><span data-stu-id="d71fd-166">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

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

<span data-ttu-id="d71fd-167">最后，我们将编写一个使用异步方法的测试，以确保 [TestDbSet](#creating-the-in-memory-test-doubles) 中包含的异步基础结构工作正常。</span><span class="sxs-lookup"><span data-stu-id="d71fd-167">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

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

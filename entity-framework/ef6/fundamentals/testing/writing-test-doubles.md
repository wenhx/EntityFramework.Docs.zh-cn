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
# <a name="testing-with-your-own-test-doubles"></a>用自己的测试进行测试双精度
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

编写应用程序测试时，通常需要避免数据库的命中。  实体框架使你可以通过创建上下文来实现此目的，该上下文具有测试定义的行为，从而利用内存中数据。  

## <a name="options-for-creating-test-doubles"></a>用于创建测试双精度的选项  

可使用两种不同的方法创建上下文的内存中版本。  

- **创建自己的测试双精度** 型–此方法涉及编写您自己的上下文和 dbset 的内存中实现。 这使你可以很好地控制类的行为，但可能涉及到编写和拥有合理的代码量。  
- **使用模拟框架创建测试双精度** 数–使用模拟 framework (例如 Moq) 你可以在运行时为你提供上下文的内存中实现并在运行时动态创建。  

本文将介绍如何创建自己的测试 double。 有关使用模拟框架的信息，请参阅使用 [模拟框架进行测试](xref:ef6/fundamentals/testing/mocking)。  

## <a name="testing-with-pre-ef6-versions"></a>用预 EF6 版本测试  

本文中所示的代码与 EF6 兼容。 若要使用 EF5 和更早的版本进行测试，请参阅 [使用虚设上下文进行测试](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/)。  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>EF 内存中测试的限制双精度  

内存中测试双精度型可以是一种提供使用 EF 的应用程序的单元测试级别的好方法。 但是，在执行此操作时，将使用 LINQ to Objects 对内存中数据执行查询。 这可能会导致不同的行为，而不是使用 EF 的 LINQ 提供程序 (LINQ to Entities) 将查询转换为对数据库运行的 SQL。  

这种差异的一个示例就是加载相关数据。 如果创建一系列博客，其中每个博客都有相关的文章，则在使用内存中数据时，将始终为每个博客加载相关文章。 但是，在对数据库运行时，仅当使用 Include 方法时才会加载数据。  

出于此原因，建议始终包含某些级别的端到端测试 (除了单元测试) ，以确保应用程序可针对数据库正常运行。  

## <a name="following-along-with-this-article"></a>与本文一起介绍  

本文提供了完整的代码清单，你可以将其复制到 Visual Studio 中，以根据你的需要进行。 最简单的方法是创建一个 **单元测试项目** ，并需要将 **.NET Framework 4.5** 作为目标来完成使用 async 的部分。  

## <a name="creating-a-context-interface"></a>创建上下文接口  

我们将介绍如何测试使用 EF 模型的服务。 为了能够将 EF 上下文替换为内存中用于测试的版本，我们将定义 EF 上下文 (的接口，并且它的内存中双精度) 将实现。

我们将要测试的服务将使用上下文的 DbSet 属性来查询和修改数据，还会调用 SaveChanges 将更改推送到数据库。 我们将这些成员包括在接口上。  

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

## <a name="the-ef-model"></a>EF 模型  

要测试的服务利用的是由 "Bloggingcontext" 和博客和 Post 类组成的 EF 模型。 此代码可能是由 EF 设计器生成的，或是 Code First 模型中。  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>通过 EF 设计器实现上下文接口  

请注意，我们的上下文实现了 IBloggingContext 接口。  

如果使用 Code First，则可以直接编辑上下文来实现接口。 如果使用的是 EF 设计器，则需要编辑生成上下文的 T4 模板。 打开 \<model_name\> 。Context.tt 文件嵌套在 edmx 文件下，查找以下代码片段，并将其添加到接口中，如下所示。  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a>要测试的服务  

为了演示使用内存中测试的测试，我们将为 BlogService 编写一些测试。 该服务可以创建新的博客 (AddBlog) 并返回按名称 (GetAllBlogs) 排序的所有博客。 除了 GetAllBlogs 之外，我们还提供了一个方法，该方法以异步方式获取按名称 (GetAllBlogsAsync) 排序的所有博客。  

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

## <a name="creating-the-in-memory-test-doubles"></a>创建内存中测试双精度  

现在，我们已经有了真正的 EF 模型和可使用该模型的服务，接下来可以创建可用于测试的内存中测试双精度。 我们为上下文创建了 TestContext 测试 double。 在测试中，我们会选择所需的行为，以便支持我们将要运行的测试。 在此示例中，我们只是捕获调用 SaveChanges 的次数，但你可以包括验证所测试方案所需的任何逻辑。  

我们还创建了 TestDbSet 来提供 DbSet 的内存中实现。 我们为 DbSet (上的所有方法提供了一个完整的实现，但 Find) 除外，但你只需实现测试方案将使用的成员。  

TestDbSet 利用我们提供的其他一些基础结构类，以确保可以处理异步查询。  

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

### <a name="implementing-find"></a>实现查找  

Find 方法难以以一般方式实现。 如果需要测试使用 Find 方法的代码，最简单的方法是为需要支持 find 的每个实体类型创建测试 DbSet。 然后，可以编写逻辑来查找特定类型的实体，如下所示。  

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

## <a name="writing-some-tests"></a>编写一些测试  

这就是开始测试时需要执行的所有操作。 以下测试将创建 TestContext，然后基于此上下文创建一个服务。 然后，使用 AddBlog 方法创建新的博客。 最后，此测试将验证服务是否已将新的博客添加到上下文的 "博客" 属性，并在上下文中调用 "SaveChanges"。  

这只是您可以使用内存中测试双精度测试的类型的示例，您可以根据您的要求调整测试的逻辑加倍和验证。  

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

下面是测试的另一个示例-这是执行查询的另一个示例。 首先，通过在其 "博客" 属性中创建包含某些数据的测试上下文来开始测试，请注意，数据不按字母顺序排序。 然后，可以基于测试上下文创建 BlogService，并确保从 GetAllBlogs 返回的数据按名称排序。  

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

最后，我们将编写一个使用异步方法的测试，以确保 [TestDbSet](#creating-the-in-memory-test-doubles) 中包含的异步基础结构工作正常。  

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

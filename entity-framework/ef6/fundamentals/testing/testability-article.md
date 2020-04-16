---
title: 可测试性和实体框架 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434308"
---
# <a name="testability-and-entity-framework-40"></a>可测试性和实体框架 4.0
斯科特·艾伦

发布时间：2010 年 5 月

## <a name="introduction"></a>简介

本白皮书介绍并演示了如何使用 ADO.NET 实体框架 4.0 和 Visual Studio 2010 编写可测试代码。 本文不尝试专注于特定的测试方法，如测试驱动设计 （TDD） 或行为驱动设计 （BDD）。 相反，本文将重点介绍如何使用ADO.NET实体框架编写代码，但仍然易于以自动化方式隔离和测试。 我们将了解在数据访问方案中简化测试的常见设计模式，并了解如何在使用框架时应用这些模式。 我们还将查看框架的特定功能，了解这些功能如何在可测试代码中工作。

## <a name="what-is-testable-code"></a>什么是可测试代码？

使用自动单元测试验证软件的能力提供了许多理想的好处。 每个人都知道，好的测试将减少应用程序中的软件缺陷数量，并提高应用程序的质量，但单元测试的到位远不止发现错误。

一个好的单元测试套件允许开发团队节省时间并保持对所创建软件的控制。 团队可以对现有代码进行更改、重构、重新设计和重组软件以满足新要求，并在知道测试套件可以验证应用程序的行为的同时，将新组件添加到应用程序中。 单元测试是快速反馈周期的一部分，用于随着复杂性的增加，促进更改并保留软件的可维护性。

然而，单元测试是有代价的。 团队必须投入时间创建和维护单元测试。 创建这些测试所需的工作量与基础软件的**可测试性**直接相关。 软件测试有多容易？ 设计具有可测试性的软件的团队将比使用不可测试软件的团队更快地创建有效的测试。

Microsoft 设计了ADO.NET实体框架 4.0 （EF4）， 并考虑到可测试性。 这并不意味着开发人员将针对框架代码本身编写单元测试。 相反，EF4 的可测试性目标便于创建基于框架的可测试代码。 在查看特定示例之前，了解可测试代码的质量是值得的。

### <a name="the-qualities-of-testable-code"></a>可测试代码的质量

易于测试的代码始终表现出至少两个特征。 首先，可测试代码易于**观察**。 给定一组输入，应很容易观察代码的输出。 例如，测试以下方法非常简单，因为该方法直接返回计算结果。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

如果方法将计算的值写入网络套接字、数据库表或文件（如以下代码）中，则测试方法非常困难。 测试必须执行其他工作才能检索该值。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

其次，可测试代码易于**隔离**。 让我们使用以下伪代码作为可测试代码的坏示例。

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

该方法易于观察 – 我们可以在保险单中传递并验证返回价值与预期结果匹配。 但是，要测试该方法，我们需要使用正确的架构安装数据库，并配置 SMTP 服务器，以防该方法尝试发送电子邮件。

单元测试只想验证方法内的计算逻辑，但测试可能会失败，因为电子邮件服务器处于脱机状态，或者因为数据库服务器移动。 这两个失败都与测试要验证的行为无关。 该行为难以隔离。

努力编写可测试代码的软件开发人员通常努力在编写的代码中保持关注点的分离。 上述方法应侧重于业务计算，并将数据库和电子邮件实现详细信息委派给其他组件。 罗伯特·马丁称之为单一责任原则。 对象应封装单个窄责任，例如计算策略的值。 所有其他数据库和通知工作应由其他对象负责。 以这种方式编写的代码更易于隔离，因为它侧重于单个任务。

在 .NET 中，我们有遵循单一责任原则并实现隔离所需的抽象。 我们可以使用接口定义，并强制代码使用接口抽象而不是具体类型。 在本文的后面部分，我们将了解类似上面介绍的坏示例这样的方法如何使用*看起来*他们将与数据库对话的接口。 但是，在测试时，我们可以替换不与数据库对话但将数据存储在内存中的虚拟实现。 此虚拟实现将隔离代码与数据访问代码或数据库配置中的不相关问题。

隔离还有其他好处。 最后一种方法中的业务计算只需几毫秒即可执行，但测试本身可能会运行几秒钟，因为代码在网络中跳跃并与各种服务器进行对话。 单元测试应快速运行，以方便进行小更改。 单元测试也应是可重复的，而不是失败的，因为与测试无关的组件有问题。 编写易于观察和隔离的代码意味着开发人员将更容易地为代码编写测试，花更少的时间等待测试执行，更重要的是，花费更少的时间来跟踪不存在的 Bug。

希望您能够理解测试的好处，并了解可测试代码所展示的品质。 我们将讨论如何编写与 EF4 配合使用的代码，将数据保存到数据库中，同时保持可观察且易于隔离，但首先，我们将缩小重点，以讨论数据访问的可测试设计。

## <a name="design-patterns-for-data-persistence"></a>数据持久性的设计模式

前面提出的两个不好的例子都有太多的责任。 第一个坏示例必须执行计算*并*写入文件。 第二个坏例子必须从数据库中读取数据 *，并*执行业务计算*并*发送电子邮件。 通过设计更小的方法，将关注点分离并委派给其他组件，您将在编写可测试代码方面取得重大进展。 目标是通过从小型抽象和重点抽象中组合操作来构建功能。

在数据持久性方面，我们正在寻找的小型且重点突出的抽象非常普遍，已记录为设计模式。 Martin Fowler 的著作《企业应用程序体系结构模式》是描述这些模式的第一部著作。 我们将在以下各节中简要介绍这些模式，然后介绍这些ADO.NET实体框架如何实现和使用这些模式。

### <a name="the-repository-pattern"></a>存储库模式

福勒说，一个存储库"使用类似集合的接口访问域对象，在域和数据映射层之间进行中介"。 存储库模式的目标是将代码与数据访问的细枝末节隔离开来，正如我们之前看到的，隔离是可测试性的必要特征。

隔离的关键是存储库如何使用类似集合的接口公开对象。 您编写用于使用存储库的逻辑不知道存储库将如何实现请求的对象。 存储库可能与数据库对话，或者可能只是从内存中集合返回对象。 所有代码需要知道的是，存储库似乎维护集合，并且可以从集合中检索、添加和删除对象。

在现有 .NET 应用程序中，具体存储库通常从通用接口继承，如下所示：

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

在为 EF4 提供实现时，我们将对接口定义进行一些更改，但基本概念保持不变。 代码可以使用实现此接口的具体存储库按其主键值检索实体，基于谓词的评估检索实体集合，或者仅检索所有可用实体。 代码还可以通过存储库接口添加和删除实体。

给定"员工"对象的 IRepository，代码可以执行以下操作。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

由于代码使用接口（员工 IRepository），我们可以为代码提供接口的不同实现。 一个实现可能是由 EF4 支持的实现，并将对象持久化到 Microsoft SQL Server 数据库中。 不同的实现（我们在测试期间使用的实现）可能由内存中的员工对象列表支持。 该接口将有助于在代码中实现隔离。

请注意，IRepository&lt;&gt; T 接口不会公开"保存"操作。 我们如何更新现有对象？ 您可能会遇到包含 Save 操作的 IRepository 定义，这些存储库的实现需要立即将对象保存到数据库中。 但是，在许多应用程序中，我们不想单独保留对象。 相反，我们希望将对象（可能来自不同的存储库）带入生活，将这些对象修改为业务活动的一部分，然后将所有对象保留为单个原子操作的一部分。 幸运的是，有一个模式允许这种类型的行为。

### <a name="the-unit-of-work-pattern"></a>工作单位模式

福勒说，一个工作单元将"维护受业务交易影响的对象列表，并协调更改的写入和并发问题的解决"。 工作单位有责任跟踪我们从存储库中引入的对象的更改，并在告诉工作单位提交更改时保留我们对对象所做的任何更改。 工作单元也有责任将我们添加到所有存储库的新对象并插入数据库，以及进行删除。

如果您曾经做过ADO.NET数据集的任何工作，那么你已经熟悉工作模式的单位。 ADO.NET DataSet 能够跟踪 DataRow 对象的更新、删除和插入，并且可以（在表适配器的帮助下）协调我们对数据库的所有更改。 但是，DataSet 对象对基础数据库的断开连接子集建模。 工作模式单元表现出相同的行为，但适用于与数据访问代码隔离且不知道数据库的业务对象和域对象。

对 .NET 代码中工作单元建模的抽象可能如下所示：

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

通过公开来自工作单元的存储库引用，我们可以确保单个工作单元的对象能够跟踪业务事务期间实现的所有实体。 实际工作单元的 Commit 方法的实现是所有魔术都恰好在内存中更改与数据库协调的地方。 

给定 IUnitOfWork 引用，代码可以更改从一个或多个存储库检索的业务对象，并使用原子提交操作保存所有更改。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>延迟加载模式

Fowler 使用名称延迟负载来描述"一个不包含所需所有数据但知道如何获取数据的对象"。 透明延迟加载是编写可测试业务代码和使用关系数据库时需要具备的重要功能。 例如，请考虑以下代码。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

如何填充时间卡集合？ 有两个可能的答案。 一个答案是，当员工存储库被要求获取员工时，会发出查询以检索员工以及员工关联的时卡信息。 在关系数据库中，这通常需要使用 JOIN 子句的查询，并且可能导致检索比应用程序需要的信息更多。 如果应用程序不需要触摸 TimeCard 属性，该怎么办？

第二个答案是"按需"加载 TimeCard 属性。 此延迟加载对业务逻辑是隐式的和透明的，因为代码不会调用特殊的 API 来检索时卡信息。 代码假定时间卡信息在需要时存在。 延迟加载涉及一些魔术，通常涉及方法调用的运行时拦截。 拦截代码负责与数据库交谈和检索时卡信息，同时使业务逻辑自由成为业务逻辑。 这种惰性加载魔力允许业务代码将自己与数据检索操作隔离开来，并生成更可测试的代码。

延迟加载的缺点是，当应用程序*确实需要*时卡信息时，代码将执行其他查询。 这不是许多应用程序的问题，但对于性能敏感的应用程序或应用程序循环访问多个员工对象和执行查询以在循环的每次迭代期间检索时间卡（通常称为 N+1 查询问题），延迟加载是一种阻力。 在这些情况下，应用程序可能希望以最有效的方式紧急加载时间卡信息。

幸运的是，我们将了解 EF4 如何支持隐式惰性负载和高效的热要负载，同时进入下一节并实现这些模式。

## <a name="implementing-patterns-with-the-entity-framework"></a>使用实体框架实现模式

好消息是，我们在最后一节中介绍的所有设计模式都非常简单地使用 EF4 实现。 为了演示我们将使用简单的ASP.NET MVC 应用程序来编辑和显示员工及其关联的时卡信息。 我们将首先使用以下"普通旧 CLR 对象"（POCO）。 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

当我们探索 EF4 的不同方法和功能时，这些类定义将略有变化，但目的是尽可能将这些类保留为持久性无知 （PI）。 PI*对象不知道如何，**甚至即使它*保持的状态生活在数据库中。 PI 和 POCO 与可测试软件齐头并进。 使用 POCO 方法的对象约束较小、更灵活且更易于测试，因为它们可以在没有数据库存在的情况下运行。

在 POCO 到位后，我们可以在可视化工作室中创建实体数据模型 （EDM），参见图 1）。 我们不会使用 EDM 为我们的实体生成代码。 相反，我们希望使用我们精心打造的实体。 我们只会使用 EDM 生成数据库架构并提供将对象映射到数据库所需的元数据 EF4。

![埃夫·test_01](~/ef6/media/eftest-01.jpg)

**图 1**

注意：如果要首先开发 EDM 模型，则可以从 EDM 生成干净的 POCO 代码。 您可以使用数据可编程团队提供的 Visual Studio 2010 扩展来执行此操作。 要下载扩展，请从 Visual Studio 中的"工具"菜单启动扩展管理器，并搜索"POCO"模板的在线库（见图 2）。 有几种 POCO 模板可用于 EF。 有关使用模板的详细信息，请参阅"[演练：实体框架的 POCO 模板](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)"。

![埃夫test_02](~/ef6/media/eftest-02.png)

**图 2**

从这个 POCO 起点，我们将探讨两种不同的可测试代码方法。 我称之为 EF 方法的第一种方法是因为它利用实体框架 API 中的抽象来实现工作单元和存储库。 在第二种方法中，我们将创建自己的自定义存储库抽象，然后了解每种方法的优缺点。 我们将首先探索 EF 方法。  

### <a name="an-ef-centric-implementation"></a>以 EF 为中心的实施

请考虑 ASP.NET MVC 项目中的以下控制器操作。 该操作检索"员工"对象并返回结果以显示员工的详细视图。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

代码是可测试的吗？ 我们至少需要测试两个来验证操作的行为。 首先，我们要验证操作返回正确的视图 - 一个简单的测试。 我们还希望编写一个测试来验证操作检索正确的员工，并且我们希望在不执行代码来查询数据库的情况下执行此操作。 请记住，我们要隔离被测的代码。 隔离将确保测试不会因为数据访问代码或数据库配置中的 Bug 而失败。 如果测试失败，我们将知道控制器逻辑中存在 Bug，而不是某些较低级别的系统组件中的错误。

为了实现隔离，我们需要一些抽象，例如我们前面为存储库和工作单元提供的接口。 请记住，存储库模式旨在在域对象和数据映射层之间进行中介。 在这种情况下，EF4*是*数据映射层，并且已经提供了一个名为 IObjectSet&lt;T（&gt;来自 System.Data.object 命名空间）的类似存储库的抽象。 接口定义如下所示。

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet&lt;&gt; T 满足存储库的要求，因为它类似于对象集合（通过&lt;&gt;IE55t），并提供从模拟集合中添加和删除对象的方法。 附加和分离方法公开 EF4 API 的其他功能。 要使用&lt;IObjectSet&gt; T 作为存储库的接口，我们需要一个工作抽象单元来将存储库绑定在一起。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

此接口的一个具体实现将与 SQL Server 对话，并且使用 EF4 中的 ObjectContext 类轻松创建。 ObjectContext 类是 EF4 API 中的实际工作单元。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

将 IObjectSet&lt;&gt; T 变为生命就像调用对象上下文对象的 CreateObjectSet 方法一样简单。 在幕后，框架将使用我们在 EDM 中提供的元数据来生成具体的 ObjectSet&lt;T&gt;。 我们将坚持返回 IObjectSet&lt;T&gt;接口，因为它将有助于在客户端代码中保留可测试性。

此具体实现在生产中非常有用，但我们需要关注如何使用 IUnitOfWork 抽象来简化测试。

### <a name="the-test-doubles"></a>测试双精度值

要隔离控制器操作，我们需要能够在实际工作单元（由 ObjectContext 支持）和测试双精度或"假"工作单元（执行内存中操作）之间切换。 执行此类切换的常见方法是不要让 MVC 控制器实例化工作单元，而是将工作单元作为构造函数参数传递到控制器中。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上述代码是依赖项注入的示例。 我们不允许控制器创建其依赖项（工作单位），但将依赖项注入控制器。 在 MVC 项目中，通常使用自定义控制器工厂与控制反转 （IoC） 容器一起使用，以自动执行依赖项注入。 这些主题超出了本文的范围，但您可以通过关注本文末尾的引用来阅读更多内容。

我们可以用于测试的假工作实现单元可能如下所示。

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

请注意，假工作单元会公开已提交的属性。 有时将功能添加到便于测试的假类中非常有用。 在这种情况下，通过检查"提交"属性，很容易观察代码是否提交工作单元。

我们还需要一个假的 IObjectSet&lt;&gt; T 来将员工和 TimeCard 对象保留在内存中。 我们可以使用泛型提供单个实现。

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

此测试将其大部分工作双重委托给基础哈希集&lt;T&gt;对象。 请注意，IObjectSet&lt;&gt; T 需要一个泛型约束，强制 T 作为类（引用类型），并且还强制我们&lt;实现&gt;IQuery T 。 使用标准 LINQ 运算符 AsQuery 使内存中集合显示为&lt;&gt; IQuery T 是很容易的。

### <a name="the-tests"></a>测试

传统的单元测试将使用单个测试类来保存单个 MVC 控制器中所有操作的所有测试。 我们可以使用我们构建的内存中的假，编写这些测试或任何类型的单元测试。 但是，在本文中，我们将避免采用单片式测试类方法，而是将测试分组以专注于特定的功能。例如，"创建新员工"可能是我们想要测试的功能，因此我们将使用单个测试类来验证负责创建新员工的单个控制器操作。

对于所有这些细粒度测试类，我们需要一些常见的设置代码。 例如，我们总是需要创建内存中存储库和假工作单元。 我们还需要一个员工控制器的实例，并注入假的工作单元。 我们将使用基类跨测试类共享此公共设置代码。

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

我们在基类中使用的"对象母体"是创建测试数据的一种常见模式。 对象母体包含用于实例化测试实体的工厂方法，用于跨多个测试夹具使用。

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

我们可以将员工控制器测试Base作为许多测试夹具的基础类（见图 3）。 每个测试夹具将测试特定的控制器操作。 例如，一个测试夹具将侧重于测试 HTTP GET 请求期间使用的"创建"操作（以显示创建员工的视图），其他固件将侧重于 HTTP POST 请求中使用的"创建"操作（以获取用户提交的信息以创建员工）。 每个派生类只负责其特定上下文中所需的设置，并提供验证其特定测试上下文结果所需的断言。

![埃夫·test_03](~/ef6/media/eftest-03.png)

**图 3**

此处介绍的命名约定和测试样式对于可测试代码来说并不是必需的，这只是一种方法。 图 4 显示了 Visual Studio 2010 的 Jet Brains Resharper 测试运行插件中运行的测试。

![埃夫test_04](~/ef6/media/eftest-04.png)

**图 4**

使用处理共享设置代码的基类时，每个控制器操作的单位测试都很小且易于编写。 测试将快速执行（因为我们正在执行内存中操作），并且不应因为不相关的基础结构或环境问题而失败（因为我们隔离了被测单元）。

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

在这些测试中，基类执行大多数设置工作。 请记住，基类构造函数创建内存中存储库、假工作单元和 EmployController 类的实例。 测试类派生自此基类，并侧重于测试 Create 方法的具体细节。 在这种情况下，具体情况可归结为您将在任何单元测试过程中看到的"安排、操作和断言"步骤：

-   创建新的"员工"对象以模拟传入数据。
-   调用"创建员工控制程序"的操作并传递给新员工。
-   验证"创建"操作生成预期结果（员工显示在存储库中）。

我们构建的内容允许我们测试任何员工控制器操作。 例如，当我们为"员工"控制器的 Index 操作编写测试时，我们可以从测试基类继承，为测试建立相同的基本设置。 再次，基类将创建内存中存储库、假工作单元和员工控制器的实例。 索引操作的测试只需要专注于调用 Index 操作并测试操作返回的模型的质量。

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

我们使用内存中假体创建测试面向测试软件*的状态*。 例如，在测试"创建"操作时，我们希望在创建操作执行后检查存储库的状态 - 存储库是否留住新员工？

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

稍后我们将介绍基于交互的测试。 基于交互的测试将询问被测代码是否在我们的对象上调用了正确的方法并传递了正确的参数。 现在，我们将在封面上移动另一种设计模式 - 延迟负载。

## <a name="eager-loading-and-lazy-loading"></a>热装和延迟加载

在 mVC Web 应用程序ASP.NET的某个时刻，我们可能希望显示员工的信息，并包括员工的相关时间卡。 例如，我们可能有一个时间卡摘要显示，显示员工的姓名和系统中的考勤卡总数。 我们可以采取几种方法来实现此功能。

### <a name="projection"></a>投影

创建摘要的一种简单方法是构建一个模型，专门用于要在视图中显示的信息。 在这种情况下，模型可能如下所示。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

请注意，员工摘要视图模型不是实体 ，换句话说，它不是我们想要保留在数据库中的内容。 我们只会使用此类以强类型方式将数据随机排列到视图中。 视图模型类似于数据传输对象 （DTO），因为它不包含任何行为（没有方法） - 仅包含属性。 属性将保存我们需要移动的数据。 使用 LINQ 的标准投影运算符 - Select 运算符，可以轻松实例化此视图模型。

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

上述代码有两个值得注意的功能。 首先 ， 代码很容易测试， 因为它仍然易于观察和隔离. Select 运算符与针对实际工作单元的内存假一样出色。

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

第二个值得注意的特征是，代码如何允许 EF4 生成单个高效的查询，以组合员工和时卡信息。 我们将员工信息和工时卡信息加载到同一对象中，而无需使用任何特殊的 API。 代码仅表示它使用标准 LINQ 运算符对内存中数据源和远程数据源执行所需的信息。 EF4 能够将 LINQ 查询和 C\#编译器生成的表达式树转换为单个高效的 T-SQL 查询。

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

有时，我们不想使用视图模型或 DTO 对象，而是使用实际实体。 当我们知道我们需要员工*和员工*的时间卡时，我们可以以不显眼和高效的方式急切地加载相关数据。

### <a name="explicit-eager-loading"></a>显式渴望加载

当我们想要急切地加载相关的实体信息时，我们需要一些业务逻辑机制（或在这种情况下，控制器操作逻辑）来表达对存储库的需求。 EF4 对象查询&lt;&gt; T 类定义一个 Include 方法，以指定在查询期间要检索的相关对象。 请记住 EF4 对象上下文通过&lt;从对象查询&gt;&lt;T&gt;继承的具体 ObjectSet T 类公开实体。如果我们在控制器操作中使用&lt;ObjectSet T&gt;引用，我们可以编写以下代码来为每个员工指定热切的时卡信息负载。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

但是，由于我们试图保持代码的可测试性，因此我们不会在工作类的实际单元&lt;之外&gt;公开 ObjectSet T。 相反，我们&lt;依赖于 IObjectSet T&gt;接口，该接口更易于伪造，但 IObjectSet&lt;T&gt;不定义 Include 方法。 LINQ 的优点是我们可以创建自己的"包括"运算符。

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

请注意，此包含运算符定义为&lt;I可查询 T&gt;而不是 IObjectSet&lt;T&gt;的扩展方法。 这使我们能够将该方法用于更广泛的可能类型，包括 IQuery&lt;T、IObjectSet&gt;&lt;T、ObjectQuery&gt;&lt;T&gt;和 Objectset&lt;T&gt;。 如果基础序列不是真正的 EF4 对象查询&lt;T，&gt;则不会造成任何伤害，并且 Include 运算符是 no-op。 如果基础序列*是*&lt;ObjectQuery T（&gt;或从 ObjectQuery&lt;&gt;T 派生），则 EF4 将看到我们对其他数据的要求，并制定正确的 SQL 查询。

有了这个新的运算符，我们可以显式要求从存储库中加载时间卡信息。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

当对实际对象上下文运行时，代码将生成以下单个查询。 查询在一次行程中从数据库中收集足够的信息，以实现员工对象并完全填充其 TimeCard 属性。

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

好消息是操作方法内的代码仍然完全可测试。 我们不需要为我们的假货提供任何其他功能来支持 Include 运算符。 坏消息是，我们必须在代码内使用 Include 运算符，以保持持久性无知。 这是构建可测试代码时需要评估的权衡类型的示例。 有时，您需要让持久性问题泄漏到存储库抽象之外，以满足性能目标。

热装的替代是延迟加载。 延迟加载意味着我们不需要业务*not*代码来显式声明关联数据的要求。 相反，我们在应用程序中使用我们的实体，如果需要其他数据，实体框架将按需加载数据。

### <a name="lazy-loading"></a>延迟加载

很容易想象一个场景，即我们不知道业务逻辑需要哪些数据。 我们可能知道逻辑需要员工对象，但我们可能分支到不同的执行路径，其中某些路径需要员工提供时卡信息，而有些路径不需要。 像这样的方案非常适合隐式延迟加载，因为数据会根据需要神奇地显示。

延迟加载（也称为延迟加载）确实对我们的实体对象提出了一些要求。 具有真正持久性无知的 POCO 不会面临来自持久性层的任何要求，但真正的持久性无知几乎不可能实现。相反，我们测量相对程度的持久性无知。 如果我们需要从面向持久性的基类继承或使用专用集合在 POCO 中实现延迟加载，那将是不幸的。 幸运的是，EF4 具有侵入性较低的解决方案。

### <a name="virtually-undetectable"></a>几乎检测不到

使用 POCO 对象时，EF4 可以动态生成实体的运行时代理。 这些代理无形地包装了具体化的 POCO，并通过拦截每个属性获取和设置操作来执行其他工作来提供其他服务。 其中一项服务是我们要查找的延迟加载功能。 另一个服务是一种有效的更改跟踪机制，它可以记录程序何时更改实体的属性值。 在保存更改方法期间，ObjectContext 使用更改列表来保留使用 UPDATE 命令的任何已修改实体。

但是，要使这些代理正常工作，他们需要一种方法来挂钩到实体上获取和设置操作，而代理通过重写虚拟成员来实现此目标。 因此，如果我们想要隐式延迟加载和高效的更改跟踪，我们需要回到 POCO 类定义并将属性标记为虚拟。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

我们仍然可以说，员工实体大多是坚持无知。 唯一的要求是使用虚拟成员，这不会影响代码的可测试性。 我们不需要从任何特殊的基类派生，甚至不需要使用专用于延迟加载的特殊集合。 如代码所示，任何实现 ICollection&lt;T&gt;的类都可以保留相关实体。

在我们的工作单元内，我们还需要进行一些小的变化。 默认情况下，当直接使用 ObjectContext 对象时，延迟加载*处于关闭状态*。 我们可以在 ContextOptions 属性上设置一个属性，以启用延迟加载，如果我们想要在任何地方启用延迟加载，我们可以在实际工作单元中设置此属性。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

启用隐式延迟加载后，应用程序代码可以使用员工和员工关联的时间卡，同时对 EF 加载额外数据所需的工作一无所知。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

延迟加载使应用程序代码更易于编写，并且使用代理魔法，代码保持完全可测试。 工作单元的内存中伪造可以简单地在测试期间在需要时预加载带有相关数据的假实体。

此时，我们将从使用 IObjectSet&lt;T&gt;构建存储库转向我们的注意力，并查看抽象来隐藏持久性框架的所有迹象。

## <a name="custom-repositories"></a>自定义存储库

当我们在本文中首次介绍工作设计单元模式时，我们提供了一些示例代码，说明工作单元的外观。 让我们使用我们一直在处理的员工和员工时卡方案重新呈现这一原始想法。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

此工作单元与我们在上一节中创建的工作单元之间的主要区别是此工作单元不使用 EF4 框架中的任何抽象（没有 IObjectSet&lt;T）。&gt; IObjectSet&lt;&gt; T 作为存储库接口工作良好，但它公开的 API 可能不能完全与我们的应用程序需求一致。 在即将采用的方法中，我们将使用自定义 IRepository&lt;T&gt;抽象表示存储库。

许多遵循测试驱动设计、行为驱动设计和域驱动方法设计的开发人员都更喜欢 IRepository&lt;T&gt;方法，原因有多种。 首先，IRepository&lt;T&gt;接口表示"反腐败"层。 正如 Eric Evans 在他的域驱动设计书中描述的那样，反腐败层使域代码远离基础结构 API，如持久性 API。 其次，开发人员可以将方法构建到存储库中，以满足应用程序的确切需求（如编写测试时发现的那样）。 例如，我们可能需要使用 ID 值查找单个实体，以便我们可以将 FindById 方法添加到存储库接口。我们的&lt;IRepository&gt; T 定义如下所示。

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

请注意，我们将回到使用 IQuery&lt;T&gt;接口来公开实体集合。 IQueryT&lt;&gt;允许 LINQ 表达式树流入 EF4 提供程序，并赋予提供程序查询的整体视图。 第二个选项是返回 IE55t&lt;&gt;T ，这意味着 EF4 LINQ 提供程序将只看到存储库内生成的表达式。 在存储库之外完成的任何分组、排序和投影将不会组成发送到数据库的 SQL 命令，这可能会损害性能。 另一方面，仅返回 IE550t&lt;&gt;结果的存储库永远不会让您使用新的 SQL 命令感到惊讶。 这两种方法都工作，并且两种方法都是可测试的。

使用泛型和 EF4 ObjectContext API&lt;提供&gt;IRepository T 接口的单个实现非常简单。

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

IRepository&lt;T&gt;方法为我们提供了对查询的一些附加控制，因为客户端必须调用方法才能访问实体。 在该方法内，我们可以提供额外的检查和LINQ运算符来强制执行应用程序约束。 请注意，接口对泛型类型参数有两个约束。 第一个约束是 ObjectSet&lt;T&gt;所需的类缺点，第二个约束强制我们的实体实现 IEntity - 为应用程序创建的抽象。 IEntity 接口强制实体具有可读 Id 属性，然后我们可以在 FindById 方法中使用此属性。 IEntity 使用以下代码定义。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity 可被视为对持久性无知的轻微违反，因为我们的实体需要实现此接口。 请记住，持久性无知是关于权衡的，对于许多 FindById 功能来说，它将超过接口施加的约束。 该接口对可测试性没有影响。

实例化实时 IRepository&lt;T&gt;需要 EF4 对象上下文，因此具体的工作实现单元应管理实例化。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>使用自定义存储库

使用我们的自定义存储库与使用基于 IObjectSet&lt;T&gt;的存储库没有显著差异。 我们首先需要调用存储库的方法来获取 IQuery T&lt;&gt;引用，而不是将 LINQ 运算符直接应用于属性。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

请注意，我们以前实现的自定义"包括"运算符将工作而不进行更改。 存储库的 FindById 方法从尝试检索单个实体的操作中删除重复的逻辑。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

我们研究过的两种方法的可测试性没有显著差异。 我们可以通过&lt;构建由 HashSet&gt;&lt;员工&gt;支持的混凝土类来提供 IRepository T 的虚假实现-就像我们在上一节中所做的那样。 但是，一些开发人员更喜欢使用模拟对象和模拟对象框架，而不是构建假物。 我们将在下一节中介绍使用模拟来测试我们的实现，并讨论模拟和假之间的差异。

### <a name="testing-with-mocks"></a>使用模拟测试

构建马丁·福勒所谓的"双测试双"的方法不同。 测试双精度（如电影特技双）是您在测试期间为实际生产对象构建为"站立"的对象。 我们创建的内存内存储库是与 SQL Server 对话的存储库的测试双精度值。 我们已经看到如何在单元测试期间使用这些测试双精度值来隔离代码并保持测试快速运行。

我们构建的测试双精度值具有实际的工作实现。 在后台，每个对象都存储一个具体的对象集合，并且在测试期间操作存储库时，它们将从此集合中添加和删除对象。 一些开发人员喜欢用实际代码和工作实现来构建测试双精度值。这些测试双号是我们称之为*假货*的。 它们具有工作实现，但它们不够真实，可用于生产。 假存储库实际上不会写入数据库。 假 SMTP 服务器实际上不会通过网络发送电子邮件。

### <a name="mocks-versus-fakes"></a>模拟与假象

还有另一种类型的测试双称为*模拟*。 虽然假有工作实现，但模拟没有实现。 在模拟对象框架的帮助下，我们在运行时构造这些模拟对象，并将其用作测试双精度值。 在本节中，我们将使用开源模拟框架 Moq。 下面是使用 Moq 动态为员工存储库创建测试双精度值的简单示例。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

我们要求 Moq 提供&lt;IRepository 员工&gt;实现，并动态构建一个。 通过&lt;访问 Mock&gt; &lt;T&gt;对象的 Object 属性，我们可以访问实现 IRepository 员工的对象。 它是这个内部对象，我们可以传递到我们的控制器，他们不知道这是一个测试双或真正的存储库。 我们可以调用对象上的方法，就像在具有实际实现的对象上调用方法一样。

您必须想知道模拟存储库在调用 Add 方法时将执行什么操作。 由于模拟对象后面没有实现，因此 Add 不执行任何操作。 幕后没有像我们写的假货那样的具体收藏，所以员工被丢弃了。 FindById 的返回值如何？ 在这种情况下，模拟对象执行它唯一能做的，即返回默认值。 由于我们要返回引用类型（员工），因此返回值为 null 值。

模拟听起来可能毫无价值;然而，还有两个模拟功能，我们还没有谈到。 首先，Moq 框架记录对模拟对象进行的所有调用。 在代码的后面部分，我们可以问 Moq 是否有人调用了 Add 方法，或者是否有人调用了 FindById 方法。 稍后我们将了解如何在测试中使用此"黑匣子"录制功能。

第二个伟大的功能是我们如何使用Moq编程一个模拟对象*与期望*。 期望值告诉模拟对象如何响应任何给定的交互。 例如，我们可以将期望值编程到模拟中，并告诉它当有人调用 FindById 时返回员工对象。 Moq 框架使用安装程序 API 和 lambda 表达式来对这些期望进行编程。

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

在此示例中，我们要求 Moq 动态构建存储库，然后我们对存储库进行编程。 当某人调用传递值为 5 的 FindById 方法时，期望告诉模拟对象返回 Id 值为 5 的新员工对象。 此测试通过，我们不需要构建一个完整的实现假 IRepository&lt;T&gt;。

让我们重温我们之前编写的测试，并重新编写它们以使用模拟而不是假测试。 与以前一样，我们将使用基类来设置所有控制器测试所需的通用基础结构部分。

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

设置代码大致相同。 我们将使用 Moq 来构造模拟对象，而不是使用假物。 基类安排模拟工作单元在代码调用"员工"属性时返回模拟存储库。 模拟设置的其余部分将发生在专用于每个特定场景的测试夹具内。 例如，当操作调用模拟存储库的 FindAll 方法时，Index 操作的测试固件将设置模拟存储库以返回员工列表。

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

除了期望值之外，我们的测试看起来与之前的测试相似。 然而，随着模拟框架的录制能力，我们可以从不同的角度进行测试。 我们将在下一节中介绍这一新观点。

### <a name="state-versus-interaction-testing"></a>状态与交互测试

您可以使用不同的技术来测试使用模拟对象的软件。 一种方法是使用基于状态的测试，这就是我们在本文中到目前为止所做的。 基于状态的测试对软件的状态进行断言。 在上一次测试中，我们在控制器上调用了一个操作方法，并断言它应该构建的模型。 下面是测试状态的其他一些示例：

-   在执行"创建"后验证存储库包含新员工对象。
-   在索引执行后验证模型包含所有员工的列表。
-   在执行"删除"后，验证存储库不包含给定员工。

使用模拟对象将看到的另一种方法是验证*交互*。 虽然基于状态的测试对对象的状态进行断言，但基于交互的测试会断言对象如何交互。 例如：

-   在执行"创建"时验证控制器调用存储库的 Add 方法。
-   当索引执行时，验证控制器调用存储库的 FindAll 方法。
-   验证控制器调用工作单元的提交方法，以在"编辑"执行时保存更改。

交互测试通常需要较少的测试数据，因为我们不是在集合内部进行戳造和验证计数。 例如，如果我们知道"详细信息"操作调用具有正确值的存储库的 FindById 方法，则操作可能运行正常。 我们可以验证此行为，而无需设置任何测试数据从 FindById 返回。

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

上述测试夹具中唯一需要的设置是基类提供的设置。 当我们调用控制器操作时，Moq 将记录与模拟存储库的交互。 使用 Moq 的验证 API，我们可以询问 Moq 控制器是否使用正确的 ID 值调用 FindById。 如果控制器未调用方法，或者使用意外参数值调用该方法，则 Verify 方法将引发异常，并且测试将失败。

下面是另一个示例，用于验证"创建操作在当前工作单元上调用提交"。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

交互测试的一个危险是过度指定交互的倾向。 模拟对象记录和验证与模拟对象的每次交互的能力并不意味着测试应尝试验证每个交互。 某些交互是实现详细信息，您只应验证满足当前测试*所需的*交互。

在模拟或假货之间进行选择很大程度上取决于您测试的系统以及您的个人（或团队）偏好。 Mock 对象可以大大减少实现测试双精度值所需的代码量，但并不是每个人都会舒适地实现编程期望和验证交互。

## <a name="conclusions"></a>结论

在本文中，我们演示了几种在使用ADO.NET实体框架进行数据持久性时创建可测试代码的方法。 我们可以利用内置的抽象&lt;，如IObjectSet T，&gt;或者创建我们自己的抽象，如IRepositoryT。&gt;&lt;在这两种情况下，ADO.NET实体框架 4.0 中的 POCO 支持允许这些抽象的使用者保持持久无知和高度可测试。 隐式延迟加载等其他 EF4 功能允许业务和应用程序服务代码工作，而不必担心关系数据存储的详细信息。 最后，我们创建的抽象在单元测试中很容易模拟或伪造，我们可以使用这些测试双精度值来实现快速运行、高度隔离和可靠的测试。

### <a name="additional-resources"></a>其他资源

-   罗伯特·马丁，"[单一责任原则](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   马丁·福勒[Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html)，*企业应用程序体系结构模式模式目录*
-   格里芬·卡普里奥，"[依赖注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   数据可编程性博客，"[演练：使用实体框架 4.0 的测试驱动开发](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)"。
-   数据可编程性博客，"[使用具有实体框架 4.0 的存储库和工作单元模式](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   亚伦·詹森，"[介绍机器规格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   埃里克·李[，"BDD与MSTest"](https://saintgimp.org/2009/01/20/bdd-with-mstest/)
-   埃里克·埃文斯，"[领域驱动设计](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   马丁·福勒，"[模仿不是斯图布斯](https://martinfowler.com/articles/mocksArentStubs.html)"
-   马丁·福勒，"[测试双](https://martinfowler.com/bliki/TestDouble.html)"
-   [莫克](https://code.google.com/p/moq/)

### <a name="biography"></a>传记

Scott Allen 是多元视的技术人员，也是 OdeToCode.com的创始人。 在 15 年的商业软件开发中，Scott 一直致力于从 8 位嵌入式设备到高度可扩展ASP.NET Web 应用程序等所有解决方案。 您可以在 OdeToCode 的博客上或在推特上[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)联系斯科特。

---
title: 可测试性和实体框架 4.0-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 28ec5446ce9faf98fb8fff141832236d70b29daf
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181576"
---
# <a name="testability-and-entity-framework-40"></a>可测试性和实体框架4。0
Scott Allen

发布2010 年 5 月

## <a name="introduction"></a>介绍

本白皮书介绍并演示了如何通过 ADO.NET 实体框架4.0 和 Visual Studio 2010 编写可测试代码。 本文不会尝试专注于特定的测试方法，如测试驱动设计（TDD）或行为驱动设计（BDD）。 本文将重点介绍如何编写使用 ADO.NET 实体框架的代码，但仍可以轻松地进行隔离和测试。 我们将探讨有助于在数据访问方案中进行测试的常见设计模式，并了解在使用 framework 时如何应用这些模式。 我们还将探讨框架的特定功能，以了解这些功能在可测试代码中的工作方式。

## <a name="what-is-testable-code"></a>什么是可测试代码？

使用自动单元测试来验证软件的功能提供了许多理想的好处。 大家都知道，良好的测试会减少应用程序中的软件缺陷数量并提高应用程序的质量，但目前没有找到 bug。

优秀的单元测试套件使开发团队能够节省时间，并仍可控制其创建的软件。 团队可以对现有代码进行更改、重构、重新设计和重构软件以满足新要求，并将新组件添加到应用程序中，同时知道测试套件可以验证应用程序的行为。 单元测试是快速反馈周期的一部分，用于在复杂性增加的情况中方便更改和保留软件的可维护性。

不过，单元测试有价格。 团队必须投入时间来创建和维护单元测试。 创建这些测试所需的工作量直接与底层软件的可**测试**性相关。 要测试的软件有多容易？ 设计具有可测试性的软件的团队将比使用无测试软件的团队更快地创建有效的测试。

Microsoft 设计了 ADO.NET 实体框架4.0 （EF4），并考虑了可测试性。 这并不意味着开发人员将针对框架代码本身编写单元测试。 相反，EF4 的可测试性目标使创建在框架之上构建的可测试代码变得简单。 在查看具体的示例之前，有必要了解可测试代码的质量。

### <a name="the-qualities-of-testable-code"></a>可测试代码的质量

易于测试的代码将始终至少显示两个特性。 首先，可测试代码易于理解 **。** 给定一组输入，应该很容易观察代码的输出。 例如，测试以下方法非常简单，因为方法直接返回计算的结果。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

如果方法将计算的值写入网络套接字、数据库表或文件（如以下代码），则很难测试方法。 测试必须执行其他工作来检索值。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

其次，可测试代码易于**隔离**。 让我们使用以下伪代码作为可测试代码的错误示例。

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

此方法很容易观察–我们可以传入保险政策，并验证返回值是否与预期结果匹配。 但是，若要测试方法，我们需要使用正确的架构安装一个数据库，并在该方法尝试发送电子邮件时配置 SMTP 服务器。

单元测试只需验证方法中的计算逻辑，但测试可能会失败，因为电子邮件服务器处于脱机状态，或数据库服务器已移动。 这两个故障与测试要验证的行为无关。 此行为难以隔离。

努力编写可测试代码的软件开发人员通常会在其编写的代码中保持关注点的分离。 上述方法应侧重于业务计算，并将数据库和电子邮件实现细节委托给其他组件。 Robert 将按单一责任原则调用此项。 对象应封装单个、窄的责任，如计算策略的值。 所有其他数据库和通知工作都应负责其他某个对象。 以这种方式编写的代码更易于隔离，因为它侧重于单个任务。

在 .NET 中，我们有了需要遵循单一责任原则并实现隔离的抽象。 我们可以使用接口定义，并强制代码使用接口抽象，而不是具体类型。 在本文的后面部分，我们将了解如何使用类似于上面所示的错误示例的方法来处理与它们将与数据库进行*通信的接口*。 但是在测试时，我们可以用不与数据库通信的虚拟实现替代，而是将数据保存在内存中。 此虚拟实现将代码与数据访问代码或数据库配置中不相关的问题隔离开来。

隔离还有其他优点。 最后一种方法中的业务计算应该只需几毫秒时间，但测试本身可能会运行几秒钟，作为网络的代码跃点，并与各种服务器通信。 单元测试应快速运行以简化小的更改。 单元测试也应该是可重复的且不会失败，因为与测试无关的组件有问题。 编写易于观察和隔离的代码意味着开发人员可以更轻松地编写代码测试，花费更少的时间等待测试执行，更重要的是，花费更少的时间来跟踪不存在的错误。

希望您可以感激测试的好处，并了解可测试代码展示的质量。 我们将介绍如何编写适用于 EF4 的代码，以便将数据保存到数据库中，并将数据保存到数据库中，并使其易于隔离，但首先我们将重点放在讨论数据访问的可测试设计上。

## <a name="design-patterns-for-data-persistence"></a>数据持久性的设计模式

前面介绍的两个错误示例都具有太多的责任。 第一个错误示例必须对文件执行计算*和*写入。 第二个错误示例是从数据库中读取数据*并*执行业务计算*并*发送电子邮件。 通过设计分隔关注点并将责任委派给其他组件的较小方法，你将在编写可测试代码时做出很大努力。 其目标是通过编写小型和重点抽象的操作来生成功能。

当涉及数据持久性时，我们所要寻找的小型重点抽象非常常见。 圣马丁 Fowler 企业应用程序体系结构的书籍模式是第一次在打印中描述这些模式。 在以下部分中，我们将简要介绍这些模式，然后显示这些 ADO.NET 实体框架如何实现和使用这些模式。

### <a name="the-repository-pattern"></a>存储库模式

Fowler 显示了一个存储库 "使用类似于集合的接口来访问域对象" 的域和数据映射层。 存储库模式的目标是将代码与数据访问的 minutiae 隔离，因此，我们看到的早期隔离是可测试性的必需特性。

隔离的关键是存储库如何使用类似集合的接口公开对象。 你编写的使用存储库的逻辑并不知道存储库将如何具体化你请求的对象。 存储库可能会与数据库进行通信，也可能只是从内存中集合返回对象。 您的所有代码都需要知道存储库似乎维护集合，并且您可以从集合中检索、添加和删除对象。

在现有的 .NET 应用程序中，具体的存储库通常继承自如下所示的泛型接口：

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

当我们为 EF4 提供实现时，我们将对接口定义进行一些更改，但基本概念仍保持不变。 代码可以使用实现此接口的具体存储库，根据谓词的计算结果检索实体集合，或者只检索所有可用的实体。 代码还可以通过存储库接口添加和删除实体。

对于员工对象的 IRepository，代码可以执行以下操作。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

由于代码使用的是接口（IRepository），因此我们可以为代码提供不同的接口实现。 一个实现可能是 EF4 支持的实现，并将对象保存到 Microsoft SQL Server 数据库中。 不同的实现（我们在测试过程中使用的）可能由一个内存中员工对象列表来支持。 接口将帮助实现代码中的隔离。

请注意，IRepository @ no__t-0T @ no__t 接口不会公开保存操作。 如何更新现有对象？ 你可能会遇到包含 Save 操作的 IRepository 定义，而这些存储库的实现需要立即将对象保存到数据库中。 但是，在许多应用程序中，我们不希望单独保存对象。 相反，我们想要将对象从不同的存储库中移到现实中，并将这些对象修改为业务活动的一部分，然后将所有对象作为单个原子操作的一部分进行保存。 幸运的是，有一种模式可用于实现此类型的行为。

### <a name="the-unit-of-work-pattern"></a>工作单元模式

Fowler 表示工作单元将 "维护受业务事务影响的对象列表，并协调发生的更改和并发问题的解决方法"。 工作单元的责任是跟踪对存储库所做的对象所做的更改，并在告知工作单元提交更改时保留对对象所做的任何更改。 还需要负责将已添加的新对象添加到所有存储库中的工作单元，并将对象插入到数据库中，还会管理删除。

如果曾经使用过 ADO.NET 数据集完成任何工作，则已熟悉工作单元模式。 ADO.NET 数据集能够跟踪对 DataRow 对象的更新、删除和插入操作，并且可以（使用 TableAdapter 的帮助）协调对数据库进行的所有更改。 但是，DataSet 对象会为底层数据库的断开连接的子集建模。 工作单元模式的行为相同，但它适用于与数据访问代码隔离并不知道数据库的业务对象和域对象。

对 .NET 代码中的工作单元进行建模的抽象可能如下所示：

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

通过从工作单元公开存储库引用，可以确保单个工作单元对象能够跟踪在业务事务中具体化的所有实体。 在实际工作单元中实现 Commit 方法是指所有神奇的情况都是为了协调与数据库的内存中更改。 

给定 IUnitOfWork 引用后，代码可以对从一个或多个存储库检索的业务对象进行更改，并使用原子提交操作保存所有更改。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>延迟负载模式

Fowler 使用名称懒惰加载来描述 "对象，该对象不包含你需要的所有数据，但知道如何获取它"。 在编写可测试的业务代码和使用关系数据库时，透明延迟加载是一项重要功能。 作为示例，请考虑以下代码。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

如何填充时间卡片集合？ 有两个可能的答案。 一项答案是，如果员工需要提取员工，则会发出一个查询，同时检索员工和员工的关联的时间卡信息。 在关系数据库中，这通常需要带有 JOIN 子句的查询，并且可能会导致检索超过应用程序需求的信息。 如果应用程序永远不需要接触时间卡片属性，该怎么办？

第二个答案是按需加载时间卡片属性。 此延迟加载对于业务逻辑是隐式且透明的，因为代码不会调用特殊的 Api 来检索时间卡信息。 此代码假定需要时提供卡信息。 延迟加载涉及一些神奇的延迟，通常涉及方法调用的运行时侦听。 拦截代码负责与数据库进行通信并检索时间卡信息，同时使业务逻辑成为业务逻辑。 这种延迟加载幻数允许业务代码将自身与数据检索操作隔离开来，并导致代码更具可测试性。

延迟加载的缺点是，*当应用程序*需要时间卡信息时，代码将执行其他查询。 对于许多应用程序而言，这并不是问题，但对于性能敏感的应用程序或应用程序在循环的每次迭代期间循环使用查询来检索时间卡（通常称为 N + 1查询问题），延迟加载是一个拖动。 在这些情况下，应用程序可能需要以最有效的方式积极加载时间卡信息。

幸运的是，在转到下一节并实现这些模式时，我们将了解 EF4 如何支持隐式延迟加载和高效的预先加载。

## <a name="implementing-patterns-with-the-entity-framework"></a>用实体框架实现模式

好消息是，我们在上一节中介绍的所有设计模式都是通过 EF4 实现的简单方式。 为了演示，我们将使用一个简单的 ASP.NET MVC 应用程序来编辑和显示员工及其相关的时间卡信息。 首先，我们将使用以下 "纯旧 CLR 对象（Poco）"。 

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

当我们探索 EF4 的不同方法和功能时，这些类定义将略有不同，但其目的是将这些类视为持久性未知（PI）。 PI 对象并不知道它持有的状态在数据库*内的状态*。 PI 和 Poco 有可测试软件。 使用 POCO 方法的对象不受约束，更灵活，更易于测试，因为它们可以在没有数据库的情况下运行。

通过 Poco，我们可以在 Visual Studio 中创建实体数据模型（EDM）（参见图1）。 我们不会使用 EDM 为实体生成代码。 相反，我们想要使用我们 lovingly 的实体。 我们将仅使用 EDM 生成数据库架构，并提供元数据 EF4 需要将对象映射到数据库。

![ef test_01](~/ef6/media/eftest-01.jpg)

**图1**

注意：如果要首先开发 EDM 模型，可以从 EDM 生成干净的 POCO 代码。 你可以使用数据可编程性团队提供的 Visual Studio 2010 扩展来执行此操作。 若要下载该扩展，请从 Visual Studio 中的 "工具" 菜单启动 "扩展管理器"，然后在 "POCO" 模板的联机库中搜索（参见图2）。 有多种可用于 EF 的 POCO 模板。 有关使用模板的详细信息，请参阅 "[Walkthrough：实体框架 @ no__t 的 POCO 模板-0 "。

![ef test_02](~/ef6/media/eftest-02.png)

**图2**

从此 POCO 开始，我们将探讨可测试代码的两种不同方法。 第一种方法是调用 EF 方法，因为它利用实体框架 API 中的抽象来实现工作单元和存储库。 在第二种方法中，我们将创建自己的自定义存储库抽象，并了解每种方法的优点和缺点。 首先，我们将探讨 EF 方法。  

### <a name="an-ef-centric-implementation"></a>EF 中心实现

请考虑 ASP.NET MVC 项目中的以下控制器操作。 操作将检索 Employee 对象，并返回一个结果以显示雇员的详细视图。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

代码是否可测试？ 至少需要两个测试来验证操作的行为。 首先，我们想要验证操作是否返回正确的视图–一个简单的测试。 我们还希望编写一个测试来验证该操作是否可检索正确的雇员，我们想要执行此操作，而不执行代码查询数据库。 请记住，我们想要隔离受测代码。 隔离将确保测试不会因数据访问代码或数据库配置中的错误而失败。 如果测试失败，我们将了解控制器逻辑中的 bug，而不是在某个较低级别的系统组件中。

若要实现隔离，我们需要一些抽象，如我们之前为存储库和工作单元提供的接口。 请记住，存储库模式旨在协调域对象和数据映射层。 在此方案中，EF4*是*数据映射层，并且已经提供名为 IObjectSet @ No__t-1T @ no__t-2 （来自 system.object 命名空间）的类似于存储库的抽象。 接口定义如下所示。

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

IObjectSet @ no__t-0T @ no__t 满足了存储库的要求，因为它类似于对象的集合（通过 IEnumerable @ no__t-2T @ no__t），并提供了在模拟的集合中添加和删除对象的方法。 附加和分离方法公开了 EF4 API 的其他功能。 若要将 IObjectSet @ no__t-0T @ no__t 用作存储库的接口，我们需要一个工作单元来将存储库绑定在一起。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

此接口的一个具体实现将与 SQL Server 通信，并使用 EF4 中的 ObjectContext 类轻松创建。 ObjectContext 类是 EF4 API 中的实际工作单元。

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

将 IObjectSet @ no__t-0T @ no__t 引入到生活中，就像调用 ObjectContext 对象的 Createobjectset<tentity> 方法一样简单。 在后台，框架将使用我们在 EDM 中提供的元数据生成具体的 ObjectSet @ no__t-0T @ no__t。 我们会坚持返回 IObjectSet @ no__t-0T @ no__t 接口，因为它有助于在客户端代码中保留可测试性。

这种具体的实现在生产中很有用，但我们需要重点介绍我们如何使用我们的 IUnitOfWork 抽象来简化测试。

### <a name="the-test-doubles"></a>测试双精度

若要隔离控制器操作，我们需要能够在实际工作单元（由 ObjectContext 支持）和测试 double 或 "假" 工作单元（执行内存中操作）之间进行切换。 执行这种类型的切换的常见方法是不让 MVC 控制器实例化工作单元，而是将工作单元作为构造函数参数传递到控制器。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上面的代码是依赖关系注入的示例。 我们不允许控制器创建依赖项（工作单元），但会将依赖项注入到控制器中。 在 MVC 项目中，通常将自定义控制器工厂与控制反转（IoC）容器结合使用来自动执行依赖关系注入。 这些主题超出了本文的范围，但你可以通过遵循本文末尾的参考来了解详细信息。

可用于测试的伪工作单元实现可能如下所示。

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

请注意，伪工作单元公开了提交的属性。 有时将功能添加到可简化测试的虚设类中。 在这种情况下，如果代码通过检查确认属性来提交工作单元，则很容易观察。

还需要一个虚设的 IObjectSet @ no__t-0T @ no__t，以便在内存中保存员工和考勤卡对象。 我们可以使用泛型提供单个实现。

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

此测试将其大部分工作委托给基础 HashSet @ no__t-0T @ no__t 对象。 请注意，IObjectSet @ no__t-0T @ no__t 需要将 T 作为类（引用类型）强制执行的泛型约束，并强制我们实现 IQueryable @ no__t-2T @ no__t。 使用标准 LINQ 运算符 AsQueryable，可以轻松地将内存中集合显示为 IQueryable @ no__t-0T @ no__t。

### <a name="the-tests"></a>测试

传统单元测试将使用一个测试类来保存单个 MVC 控制器中所有操作的所有测试。 我们可以使用生成的内存 fakes 中的来编写这些测试或任何类型的单元测试。 但在本文中，我们将避免使用整体测试类方法，而是将测试分组，以重点关注特定功能。  例如，"创建新员工" 可能是我们要测试的功能，因此，我们将使用一个测试类来验证负责创建新员工的单个控制器操作。

所有这些细化测试类都需要一些常见的安装代码。 例如，我们始终需要创建内存中存储库和伪工作单元。 还需要一个员工控制器实例，其中包含虚假的工作单元。 我们将使用基类在测试类之间共享此常见安装代码。

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

在基类中使用的 "对象母亲" 是创建测试数据的一种常见模式。 对象母亲包含用于实例化测试实体的工厂方法，以便在多个测试装置中使用。

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

我们可以将 EmployeeControllerTestBase 用作多个测试装置的基类（请参阅图3）。 每个测试装置将测试特定控制器操作。 例如，一个测试装置将重点放在测试 HTTP GET 请求期间使用的 "创建" 操作（用于显示用于创建员工的视图），另一个装置将重点放在 HTTP POST 请求中使用的 "创建" 操作（以获取由用户创建员工）。 每个派生类只负责特定上下文中所需的设置，并提供验证其特定测试上下文的结果所需的断言。

![ef test_03](~/ef6/media/eftest-03.png)

**图3**

此处提供的命名约定和测试样式不是可测试代码所必需的–它只是一种方法。 图4显示了 Visual Studio 2010 的 Jet 大脑 Resharper 测试运行程序插件中运行的测试。

![ef test_04](~/ef6/media/eftest-04.png)

**图4**

使用基类来处理共享的安装代码，每个控制器操作的单元测试都很小，并且易于编写。 测试将快速执行（因为我们在执行内存中的操作），不应因为不相关的基础结构或环境问题而失败（因为我们已将所测试的单元隔离）。

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

在这些测试中，基类执行大部分的设置工作。 请记住，基类构造函数会创建内存中存储库、伪工作单元和 EmployeeController 类的实例。 该测试类从此基类派生，重点介绍了测试 Create 方法的具体信息。 在这种情况下，将在任何单元测试过程中看到的 "排列、操作和断言" 步骤可归结：

-   创建用于模拟传入数据的 newEmployee 对象。
-   调用 EmployeeController 的 Create 操作并传入 newEmployee。
-   验证 "创建" 操作是否生成预期的结果（员工出现在存储库中）。

我们生成的内容使我们可以测试任何 EmployeeController 操作。 例如，当我们为 Employee 控制器的索引操作编写测试时，可以从测试基类继承，为测试建立相同的基本设置。 同样，基类会创建内存中存储库、伪工作单元和 EmployeeController 的实例。 针对索引操作的测试只需重点介绍如何调用索引操作并测试操作返回的模型的质量。

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

用内存中 fakes 创建的测试面向测试软件的*状态*。 例如，在测试创建操作时，我们想要在执行 "创建" 操作后检查存储库的状态–存储库是否持有新员工？

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

稍后我们将探讨基于交互的测试。 基于交互的测试会询问所测试的代码是否在对象上调用了正确的方法，并传递了正确的参数。 现在，我们将继续介绍另一种设计模式–延迟负载。

## <a name="eager-loading-and-lazy-loading"></a>预先加载和延迟加载

在 ASP.NET MVC web 应用程序的某个时候，我们可能希望显示雇员的信息，并包括员工的相关时间表。 例如，我们可能会显示一个 "时间表摘要" 显示，其中显示了雇员的姓名和系统中的总时间。 可以采用几种方法实现此功能。

### <a name="projection"></a>投影

创建摘要的一种简单方法是构造专用于要在视图中显示的信息的模型。 在这种情况下，该模型可能如下所示。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

请注意，EmployeeSummaryViewModel 不是一个实体–换言之，在数据库中不会保留它。 我们只会使用此类以强类型方式将数据无序播放到视图中。 视图模型类似于数据传输对象（DTO），因为它不包含 "仅行为" 属性。 这些属性将保存需要移动的数据。 使用 LINQ 的标准投影运算符（Select 运算符），可以轻松地实例化此视图模型。

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

上面的代码有两个值得注意的功能。 首先–代码易于测试，因为它仍易于观察和隔离。 除了实际工作单元，Select 运算符还能与内存中的 fakes 一起工作。

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

第二个值得注意的功能是代码如何允许 EF4 生成单个有效的查询，以将员工和时间卡信息组合在一起。 我们已将员工信息和时间卡信息加载到同一个对象中，而无需使用任何特殊的 Api。 此代码仅使用标准 LINQ 运算符（适用于内存中数据源以及远程数据源）来表示所需的信息。 EF4 能够将 LINQ 查询和 C @ no__t-0 编译器生成的表达式树转换为单个高效的 T-sql 查询。

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

在其他一些情况下，我们不想使用视图模型或 DTO 对象，而是使用实际实体。 当我们知道我们需要员工*和*员工的时间卡时，可以积极以不引人注目和高效的方式加载相关数据。

### <a name="explicit-eager-loading"></a>显式预先加载

当我们想要积极加载相关实体信息时，我们需要某种机制来实现业务逻辑（或在此方案中为控制器操作逻辑），以表达其对存储库的期望。 EF4 ObjectQuery @ no__t-0T @ no__t 类定义了包含方法，以指定在查询过程中要检索的相关对象。 请记住，EF4 ObjectContext 通过从 No__t @ ObjectQuery-No__t @ 2T 继承的具体 ObjectSet @ no__t-0T @ no__t 类公开实体。  如果我们在控制器操作中使用 ObjectSet @ no__t-0T @ no__t 引用，则可以编写以下代码来为每位员工指定预先加载的时间卡信息。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

不过，由于我们要尝试让代码可测试，因此，我们不会从真实的工作单元外公开 ObjectSet @ no__t-0T @ no__t。 相反，我们依赖于 IObjectSet @ no__t-0T @ no__t 接口，该接口更易于假冒，但 IObjectSet @ no__t-2T @ no__t 不定义 Include 方法。 LINQ 的优点是，我们可以创建自己的 Include 运算符。

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

请注意，此 Include 运算符定义为 IQueryable @ no__t-0T @ no__t （而不是 IObjectSet @ no__t-2T @ no__t）的扩展方法。 这使我们能够将方法用于范围更广的可能类型，包括 IQueryable @ no__t-0T @ no__t、IObjectSet @ no__t-2T @ no__t、ObjectQuery @ no__t-4T @ no__t 和 ObjectSet @ no__t-6T @ no__t。 如果基础序列不是正版 EF4 ObjectQuery @ no__t-0T @ no__t-1，则不会有任何损害，而且 Include 运算符是一个无操作。 如果基础序列*是*ObjectQuery @ No__t-1T @ no__t （或派生自 ObjectQuery @ No__t-3T @ no__t-4），则 EF4 将看到对其他数据的要求，并制定正确的 SQL 查询。

使用这个新的运算符，我们可以从存储库显式请求预先加载的时间卡信息。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

针对实际 ObjectContext 运行时，代码将生成以下单个查询。 查询在一次中从数据库收集足够多的信息，以具体化 employee 对象并完全填充其时间卡片属性。

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

好消息是，操作方法中的代码保持完全可测试性。 我们不需要为 fakes 提供任何其他功能来支持 Include 运算符。 糟糕的是，我们必须在要保留持久性未知的代码内使用 Include 运算符。 这是生成可测试代码时需要评估的折衷类型的一个典型示例。 有时，您需要使持久性问题在存储库抽象之外泄露以满足性能目标。

预先加载的替代方法是延迟加载。 延迟加载意味着我们*不*需要业务代码来显式公告关联数据的要求。 相反，我们在应用程序中使用实体，如果需要其他数据实体框架将按需加载数据。

### <a name="lazy-loading"></a>延迟加载

这种情况很容易想象到，这种情况下，我们并不知道有业务逻辑需要哪些数据。 我们可能知道逻辑需要一个 employee 对象，但我们可能会将其分支到不同的执行路径，其中某些路径需要员工的时间卡信息，而另一些则不需要。 这种情况非常适合隐式延迟加载，因为数据神奇地出现在需要的基础上。

延迟加载（也称为延迟加载）对实体对象有一些要求。 具有 true 持久性无感知的 Poco 不会面对持久性层的任何要求，但真正的持久性无感知根本无法实现。  相反，我们会以相对度数度量持久性无感知。 如果需要从面向持久性的基类继承，或使用专用集合在 Poco 中实现延迟加载，则会很遗憾。 幸运的是，EF4 具有更不具侵入性的解决方案。

### <a name="virtually-undetectable"></a>几乎无法检测

使用 POCO 对象时，EF4 可以为实体动态生成运行时代理。 这些代理以不可见的方式包装具体化的 Poco，并通过截取每个属性 get 和 set 操作来执行其他工作，从而提供其他服务。 其中一项服务是我们要查找的延迟加载功能。 另一服务是一种有效的更改跟踪机制，可以在程序更改实体的属性值时进行记录。 在 SaveChanges 方法期间 ObjectContext 使用更改列表，以使用更新命令来持久保存所有已修改的实体。

但是，若要使这些代理正常工作，它们需要有一种方法来挂钩实体上的属性 get 和 set 操作，并且代理通过重写虚拟成员来实现此目标。 因此，如果需要隐式延迟加载和高效的更改跟踪，则需要返回到 POCO 类定义并将属性标记为虚拟。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

我们仍然可以说，Employee 实体主要是持久性未知的。 唯一的要求是使用虚拟成员，这不会影响代码的可测试性。 不需要从任何特殊基类派生，甚至可以使用专用于延迟加载的特殊集合。 如代码所示，实现 ICollection @ no__t-0T @ no__t-1 的任何类都可用于保存相关实体。

还需要在我们的工作单元内进行一个小的更改。 当直接使用 ObjectContext 对象时，延迟加载默认情况下处于*关闭状态*。 可以在 ContextOptions 属性上设置一个属性来启用延迟加载，如果我们想要在任何位置启用延迟加载，则可以在实际工作单元内设置此属性。

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

启用隐式延迟加载后，应用程序代码可以使用员工和员工的相关时间表，同时丝毫不知道 EF 加载额外数据所需的工作。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

延迟加载使得应用程序代码更易于编写，而对于代理的神奇，代码会保持完全可测试性。 工作单元的内存中 fakes 只需在测试过程中需要时使用关联数据预加载虚设实体。

此时，我们将使用 IObjectSet @ no__t-0T @ no__t 构建存储库，并查看抽象以隐藏持久性框架的所有标志。

## <a name="custom-repositories"></a>自定义存储库

当我们在本文中第一次介绍工作单元设计模式时，我们提供了一些示例代码，说明工作单元的外观。 让我们使用我们一直在使用的员工和员工时间表方案来重新展示这一思路。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

此工作单元和我们在上一节中创建的工作单元之间的主要区别在于，此工作单元不使用 EF4 框架中的任何抽象（没有 IObjectSet @ no__t-0T @ no__t-1）。 IObjectSet @ no__t-0T @ no__t 是一个存储库接口，但它所公开的 API 可能并不完全符合应用程序的需求。 在即将推出的方法中，我们将使用自定义 IRepository @ no__t-0T @ no__t 抽象来表示存储库。

许多遵循测试驱动设计、行为驱动设计和域驱动方法设计的开发人员出于多种原因而倾向于使用 IRepository @ no__t-0T @ no__t 方法。 首先，IRepository @ no__t-0T @ no__t 接口表示 "反损坏" 层。 正如 Eric Evans 在他的域驱动的设计书中所述，抗损坏层会使你的域代码远离基础结构 Api，如永久性 API。 其次，开发人员可以在存储库中构建满足应用程序确切需求的方法（在编写测试时发现）。 例如，我们可能经常需要使用 ID 值查找单个实体，因此可以将 FindById 方法添加到存储库接口。  IRepository @ no__t-0T @ no__t 定义将如下所示。

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

请注意，我们将返回使用 IQueryable @ no__t-0T @ no__t 接口来公开实体集合。 IQueryable @ no__t-0T @ no__t 允许 LINQ 表达式树流入 EF4 提供程序，并为提供程序提供查询的整体视图。 第二个选项是返回 IEnumerable @ no__t-0T @ no__t，这意味着 EF4 LINQ 提供程序将只能看到在存储库内生成的表达式。 不会将存储库外部完成的任何分组、排序和投影组合到发送到数据库的 SQL 命令中，这可能会影响性能。 另一方面，只返回 IEnumerable @ no__t-0T @ no__t 结果的存储库永远不会让你使用新的 SQL 命令。 这两种方法都适用，并且这两种方法保持可测试性。

使用泛型和 EF4 ObjectContext API，可以简单地提供 IRepository @ no__t-0T @ no__t 接口的单个实现。

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

IRepository @ no__t-0T @ no__t 方法为我们提供了对查询的一些额外控制，因为客户端必须调用方法才能获取实体。 在方法中，我们可以提供额外的检查和 LINQ 运算符来强制应用程序约束。 请注意，接口对泛型类型参数具有两个约束。 第一个约束是 ObjectSet @ no__t-0T @ no__t-1 所需的类缺点破坏，第二个约束强制我们的实体实现 IEntity –为应用程序创建的抽象。 IEntity 接口强制实体具有可读 Id 属性，然后可以在 FindById 方法中使用此属性。 IEntity 是用下面的代码定义的。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity 可能被视为一小部分持久性无感知，因为需要实体才能实现此接口。 请记住持久性无感知是关于权衡的，许多 FindById 功能将超过接口施加的约束。 接口对可测试性没有影响。

实例化 live IRepository @ no__t-0T @ no__t 需要一个 EF4 ObjectContext，因此具体的工作单元实现应管理实例化。

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

使用自定义存储库并不大不同于使用基于 IObjectSet @ no__t-0T @ no__t-1 的存储库。 我们首先需要调用一个存储库方法来获取 IQueryable @ no__t-0T @ no__t 引用，而不是直接对属性应用 LINQ 运算符。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

请注意，我们先前实现的自定义 Include 运算符无需更改即可运行。 存储库的 FindById 方法从尝试检索单个实体的操作中删除重复逻辑。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

我们所检查的两种方法的可测试性并没有显著的差异。 我们可以通过生成由 HashSet @ no__t-2Employee @ no__t-3 支持的具体类（就像我们在上一节中所做的那样），提供 IRepository @ no__t-0T @ no__t 的伪实现。 但是，某些开发人员更喜欢使用 mock 对象和模拟对象框架，而不是生成 fakes。 我们将在下一节中介绍如何使用模拟来测试实现并讨论模拟和 fakes 之间的差异。

### <a name="testing-with-mocks"></a>用模拟进行测试

可以使用不同的方法来构建 Fowler 调用 "测试双精度" 的情况。 测试双精度型（如电影 stunt 双精度型）是在测试期间生成的、用于实际生产对象的对象。 我们创建的内存中存储库是与 SQL Server 通信的存储库的测试。 我们已了解如何在单元测试过程中使用这些测试双线来隔离代码并使测试运行速度更快。

我们生成的测试翻倍，实现了真实的工作实现。 在幕后，每个对象都存储一个具体的对象集合，并将在测试过程中操作存储库时，在此集合中添加和移除对象。 一些开发人员喜欢以这种方式生成测试，这种方法包括真实的代码和工作实现。  这些测试是*fakes*的。 它们具有有效的实现，但并不真正足以用于生产。 伪存储库实际上不会写入数据库。 伪 SMTP 服务器实际上不会通过网络发送电子邮件。

### <a name="mocks-versus-fakes"></a>模拟与 Fakes

还有另一种类型的测试 double 称为*模拟*。 尽管 fakes 具有有效的实现，但模拟没有实现。 使用 mock 对象框架的帮助，我们在运行时构造这些模拟对象并将其用作测试双精度型。 在本部分中，我们将使用开源模拟 framework Moq。 下面是一个简单的示例，说明如何使用 Moq 为员工存储库动态创建测试 double。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

我们要求 Moq IRepository @ no__t-0Employee @ no__t 实现，并且它会动态生成一个。 通过访问 Mock @ no__t-2T @ no__t 对象的对象属性，我们可以访问实现 IRepository @ no__t-0Employee @ no__t 的对象。 我们可以将此内部对象传递到我们的控制器中，并不知道这是测试 double 还是真正的存储库。 可以调用对象上的方法，就像我们使用实际实现调用对象上的方法一样。

当我们调用 Add 方法时，你必须知道 mock 存储库将执行的操作。 由于 mock 对象尚无实现，因此不执行任何操作。 幕后没有具体的集合，就像我们编写的 fakes 一样，因此放弃了该员工。 FindById 的返回值怎么样？ 在这种情况下，mock 对象执行的唯一操作就是返回默认值。 由于我们返回引用类型（雇员），因此返回值为 null 值。

模拟可能会毫无意义。但是，我们还没有谈到模拟的两个功能。 首先，Moq 框架记录模拟对象上发出的所有调用。 稍后在代码中，我们可以询问 Moq 是否有人调用了 Add 方法，或者有人调用了 FindById 方法。 稍后我们将介绍如何在测试中使用此 "黑色框" 录制功能。

第二大功能*是，我们*可以使用 Moq 来为模拟对象编程。 预期会告诉 mock 对象如何响应任何给定的交互。 例如，我们可以将预期计划为模拟，并告诉它在用户调用 FindById 时返回 employee 对象。 Moq 框架使用安装 API 和 lambda 表达式来对这些预期进行编程。

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

在此示例中，我们要求 Moq 动态构建一个存储库，然后对存储库进行计划。 当有人调用 FindById 方法传递值5时，预期会告知 mock 对象返回 Id 值为5的新 employee 对象。 此测试通过，我们不需要生成完整的实现来实现虚设 IRepository @ no__t-0T @ no__t。

让我们重新访问之前编写的测试，并对其进行返工，使用模拟而不是 fakes。 就像以前一样，我们将使用基类来设置控制器所有测试所需的基础结构的公共部分。

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

安装程序代码的基本保持不变。 我们将使用 Moq 来构造 mock 对象，而不是使用 fakes。 当代码调用 Employees 属性时，该基类将排列模拟工作单元以返回 mock 存储库。 模拟设置的其余部分将在专用于每个特定方案的测试装置内进行。 例如，当操作调用 mock 存储库的 FindAll 方法时，用于索引操作的测试装置将设置 mock 存储库以返回员工列表。

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

除了预期之外，我们的测试看起来类似于以前的测试。 然而，使用模拟框架的录制功能，我们可以从不同角度进行测试。 在下一部分中，我们将介绍这个新的透视。

### <a name="state-versus-interaction-testing"></a>状态与交互测试

你可以使用不同的技术来测试具有 mock 对象的软件。 一种方法是使用基于状态的测试，这是我们目前在本文中所执行的操作。 基于状态的测试对软件的状态进行断言。 在最后一次测试中，我们在控制器上调用了操作方法，并对它应生成的模型作出了一个断言。 下面是测试状态的一些其他示例：

-   创建 "创建" 后，验证存储库是否包含新的 employee 对象。
-   验证该模型在执行 Index 后是否包含所有员工的列表。
-   验证 "删除" 执行后存储库不包含给定的员工。

模拟对象的另一种方法是验证*交互*。 虽然基于状态的测试对对象的状态进行断言，但基于交互的测试会使断言与对象交互的方式有关。 例如：

-   验证在创建执行时控制器是否调用存储库的 Add 方法。
-   当执行索引时，验证控制器是否调用存储库的 FindAll 方法。
-   验证控制器是否调用工作单元的 Commit 方法，以便在执行编辑时保存更改。

交互测试通常需要较少的测试数据，因为我们不会在集合中闲逛和验证计数。 例如，如果我们知道详细信息操作将使用正确的值调用存储库的 FindById 方法，则操作可能会正常运行。 我们可以验证此行为，而无需设置任何要从 FindById 返回的测试数据。

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

上述测试装置中唯一需要的设置是由基类提供的设置。 当我们调用控制器操作时，Moq 将记录与 mock 存储库的交互。 使用 Moq 的 Verify API，如果控制器使用正确的 ID 值调用 FindById，则可以询问 Moq。 如果控制器未调用方法，或调用方法时出现意外的参数值，则 Verify 方法将引发异常，并且测试将失败。

下面是另一个示例，用于验证创建操作对当前工作单元调用 Commit。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

交互测试有一个危险，就是指定交互。 Mock 对象记录和验证每个与 mock 对象的交互的能力并不意味着测试应尝试验证每个交互。 某些交互是实现详细信息，只应验证满足当前测试*所需*的交互。

模拟或 fakes 之间的选择主要取决于你要测试的系统和个人（或团队）首选项。 Mock 对象可以极大地减少实现测试双精度值所需的代码量，但并非每个人都能得心应手地进行编程和验证交互。

## <a name="conclusions"></a>结论

在本文中，我们演示了几种创建可测试代码的方法，同时使用 ADO.NET 实体框架来实现数据持久性。 我们可以利用内置抽象，如 IObjectSet @ no__t-0T @ no__t-1，或创建自己的抽象，如 IRepository @ no__t-2T @ no__t。  在这两种情况下，ADO.NET 中的 POCO 支持实体框架4.0，使这些抽象的使用者能够维持永久性未知和高度可测试性。 隐式延迟加载等附加 EF4 功能允许业务和应用程序服务代码工作，而无需担心关系数据存储的详细信息。 最后，我们创建的抽象可以轻松地在单元测试中模拟或伪造，而且我们可以使用这些测试来实现快速运行、高度隔离和可靠的测试。

### <a name="additional-resources"></a>其他资源

-   Robert，"[单责任原则](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   圣马丁 Fowler，从*企业应用程序体系结构模式*的模式[目录](https://www.martinfowler.com/eaaCatalog/index.html)
-   Griffin Caprio，"[依赖关系注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   数据可编程性博客，"@no__t 0Walkthrough：测试驱动开发与实体框架 4.0 @ no__t-0 "。
-   数据可编程性博客，"[使用存储库和实体框架4.0 的工作单元模式](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)"
-   Aaron Jensen，"[机器规格简介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric 先生，" [BDD With MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"
-   Eric Evans，"[域驱动设计](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   圣马丁 Fowler，"[模拟不是存根](https://martinfowler.com/articles/mocksArentStubs.html)"
-   圣马丁 Fowler，" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>事迹

Scott Allen 是技术人员在 Pluralsight 和创始人 OdeToCode.com 的成员。 在15年的商业软件开发中，Scott 已经处理了从8位嵌入式设备到高度可扩展的 ASP.NET web 应用程序的各种解决方案。 你可以在 OdeToCode 上或通过[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)在 Twitter 上联系 Scott。

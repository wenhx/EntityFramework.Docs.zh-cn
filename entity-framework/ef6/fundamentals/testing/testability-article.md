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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="5658a-102">可测试性和实体框架4。0</span><span class="sxs-lookup"><span data-stu-id="5658a-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="5658a-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="5658a-103">Scott Allen</span></span>

<span data-ttu-id="5658a-104">发布日期：5月2010</span><span class="sxs-lookup"><span data-stu-id="5658a-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="5658a-105">介绍</span><span class="sxs-lookup"><span data-stu-id="5658a-105">Introduction</span></span>

<span data-ttu-id="5658a-106">本白皮书介绍并演示了如何通过 ADO.NET 实体框架4.0 和 Visual Studio 2010 编写可测试代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="5658a-107">本文不会尝试专注于特定的测试方法，如测试驱动设计（TDD）或行为驱动设计（BDD）。</span><span class="sxs-lookup"><span data-stu-id="5658a-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="5658a-108">本文将重点介绍如何编写使用 ADO.NET 实体框架的代码，但仍可以轻松地进行隔离和测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="5658a-109">我们将探讨有助于在数据访问方案中进行测试的常见设计模式，并了解在使用 framework 时如何应用这些模式。</span><span class="sxs-lookup"><span data-stu-id="5658a-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="5658a-110">我们还将探讨框架的特定功能，以了解这些功能在可测试代码中的工作方式。</span><span class="sxs-lookup"><span data-stu-id="5658a-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="5658a-111">什么是可测试代码？</span><span class="sxs-lookup"><span data-stu-id="5658a-111">What Is Testable Code?</span></span>

<span data-ttu-id="5658a-112">使用自动单元测试来验证软件的功能提供了许多理想的好处。</span><span class="sxs-lookup"><span data-stu-id="5658a-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="5658a-113">大家都知道，良好的测试会减少应用程序中的软件缺陷数量并提高应用程序的质量，但目前没有找到 bug。</span><span class="sxs-lookup"><span data-stu-id="5658a-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="5658a-114">优秀的单元测试套件使开发团队能够节省时间，并仍可控制其创建的软件。</span><span class="sxs-lookup"><span data-stu-id="5658a-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="5658a-115">团队可以对现有代码进行更改、重构、重新设计和重构软件以满足新要求，并将新组件添加到应用程序中，同时知道测试套件可以验证应用程序的行为。</span><span class="sxs-lookup"><span data-stu-id="5658a-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="5658a-116">单元测试是快速反馈周期的一部分，用于在复杂性增加的情况中方便更改和保留软件的可维护性。</span><span class="sxs-lookup"><span data-stu-id="5658a-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="5658a-117">不过，单元测试有价格。</span><span class="sxs-lookup"><span data-stu-id="5658a-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="5658a-118">团队必须投入时间来创建和维护单元测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="5658a-119">创建这些测试所需的工作量直接与底层软件的可**测试**性相关。</span><span class="sxs-lookup"><span data-stu-id="5658a-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="5658a-120">要测试的软件有多容易？</span><span class="sxs-lookup"><span data-stu-id="5658a-120">How easy is the software to test?</span></span> <span data-ttu-id="5658a-121">设计具有可测试性的软件的团队将比使用无测试软件的团队更快地创建有效的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="5658a-122">Microsoft 设计了 ADO.NET 实体框架4.0 （EF4），并考虑了可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="5658a-123">这并不意味着开发人员将针对框架代码本身编写单元测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="5658a-124">相反，EF4 的可测试性目标使创建在框架之上构建的可测试代码变得简单。</span><span class="sxs-lookup"><span data-stu-id="5658a-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="5658a-125">在查看具体的示例之前，有必要了解可测试代码的质量。</span><span class="sxs-lookup"><span data-stu-id="5658a-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="5658a-126">可测试代码的质量</span><span class="sxs-lookup"><span data-stu-id="5658a-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="5658a-127">易于测试的代码将始终至少显示两个特性。</span><span class="sxs-lookup"><span data-stu-id="5658a-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="5658a-128">首先，可测试代码易于理解 **。**</span><span class="sxs-lookup"><span data-stu-id="5658a-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="5658a-129">给定一组输入，应该很容易观察代码的输出。</span><span class="sxs-lookup"><span data-stu-id="5658a-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="5658a-130">例如，测试以下方法非常简单，因为方法直接返回计算的结果。</span><span class="sxs-lookup"><span data-stu-id="5658a-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="5658a-131">如果方法将计算的值写入网络套接字、数据库表或文件（如以下代码），则很难测试方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="5658a-132">测试必须执行其他工作来检索值。</span><span class="sxs-lookup"><span data-stu-id="5658a-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="5658a-133">其次，可测试代码易于**隔离**。</span><span class="sxs-lookup"><span data-stu-id="5658a-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="5658a-134">让我们使用以下伪代码作为可测试代码的错误示例。</span><span class="sxs-lookup"><span data-stu-id="5658a-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="5658a-135">此方法很容易观察–我们可以传入保险政策，并验证返回值是否与预期结果匹配。</span><span class="sxs-lookup"><span data-stu-id="5658a-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="5658a-136">但是，若要测试方法，我们需要使用正确的架构安装一个数据库，并在该方法尝试发送电子邮件时配置 SMTP 服务器。</span><span class="sxs-lookup"><span data-stu-id="5658a-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="5658a-137">单元测试只需验证方法中的计算逻辑，但测试可能会失败，因为电子邮件服务器处于脱机状态，或数据库服务器已移动。</span><span class="sxs-lookup"><span data-stu-id="5658a-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="5658a-138">这两个故障与测试要验证的行为无关。</span><span class="sxs-lookup"><span data-stu-id="5658a-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="5658a-139">此行为难以隔离。</span><span class="sxs-lookup"><span data-stu-id="5658a-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="5658a-140">努力编写可测试代码的软件开发人员通常会在其编写的代码中保持关注点的分离。</span><span class="sxs-lookup"><span data-stu-id="5658a-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="5658a-141">上述方法应侧重于业务计算，并将数据库和电子邮件实现细节委托给其他组件。</span><span class="sxs-lookup"><span data-stu-id="5658a-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="5658a-142">Robert 将按单一责任原则调用此项。</span><span class="sxs-lookup"><span data-stu-id="5658a-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="5658a-143">对象应封装单个、窄的责任，如计算策略的值。</span><span class="sxs-lookup"><span data-stu-id="5658a-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="5658a-144">所有其他数据库和通知工作都应负责其他某个对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="5658a-145">以这种方式编写的代码更易于隔离，因为它侧重于单个任务。</span><span class="sxs-lookup"><span data-stu-id="5658a-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="5658a-146">在 .NET 中，我们有了需要遵循单一责任原则并实现隔离的抽象。</span><span class="sxs-lookup"><span data-stu-id="5658a-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="5658a-147">我们可以使用接口定义，并强制代码使用接口抽象，而不是具体类型。</span><span class="sxs-lookup"><span data-stu-id="5658a-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="5658a-148">在本文的后面部分，我们将了解如何使用类似于上面所示的错误示例的方法来处理与它们将与数据库进行*通信的接口*。</span><span class="sxs-lookup"><span data-stu-id="5658a-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="5658a-149">但是在测试时，我们可以用不与数据库通信的虚拟实现替代，而是将数据保存在内存中。</span><span class="sxs-lookup"><span data-stu-id="5658a-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="5658a-150">此虚拟实现将代码与数据访问代码或数据库配置中不相关的问题隔离开来。</span><span class="sxs-lookup"><span data-stu-id="5658a-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="5658a-151">隔离还有其他优点。</span><span class="sxs-lookup"><span data-stu-id="5658a-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="5658a-152">最后一种方法中的业务计算应该只需几毫秒时间，但测试本身可能会运行几秒钟，作为网络的代码跃点，并与各种服务器通信。</span><span class="sxs-lookup"><span data-stu-id="5658a-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="5658a-153">单元测试应快速运行以简化小的更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="5658a-154">单元测试也应该是可重复的且不会失败，因为与测试无关的组件有问题。</span><span class="sxs-lookup"><span data-stu-id="5658a-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="5658a-155">编写易于观察和隔离的代码意味着开发人员可以更轻松地编写代码测试，花费更少的时间等待测试执行，更重要的是，花费更少的时间来跟踪不存在的错误。</span><span class="sxs-lookup"><span data-stu-id="5658a-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="5658a-156">希望您可以感激测试的好处，并了解可测试代码展示的质量。</span><span class="sxs-lookup"><span data-stu-id="5658a-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="5658a-157">我们将介绍如何编写适用于 EF4 的代码，以便将数据保存到数据库中，并将数据保存到数据库中，并使其易于隔离，但首先我们将重点放在讨论数据访问的可测试设计上。</span><span class="sxs-lookup"><span data-stu-id="5658a-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="5658a-158">数据持久性的设计模式</span><span class="sxs-lookup"><span data-stu-id="5658a-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="5658a-159">前面介绍的两个错误示例都具有太多的责任。</span><span class="sxs-lookup"><span data-stu-id="5658a-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="5658a-160">第一个错误示例必须对文件执行计算*和*写入。</span><span class="sxs-lookup"><span data-stu-id="5658a-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="5658a-161">第二个错误示例是从数据库中读取数据*并*执行业务计算*并*发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="5658a-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="5658a-162">通过设计分隔关注点并将责任委派给其他组件的较小方法，你将在编写可测试代码时做出很大努力。</span><span class="sxs-lookup"><span data-stu-id="5658a-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="5658a-163">其目标是通过编写小型和重点抽象的操作来生成功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="5658a-164">当涉及数据持久性时，我们所要寻找的小型重点抽象非常常见。</span><span class="sxs-lookup"><span data-stu-id="5658a-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="5658a-165">圣马丁 Fowler 企业应用程序体系结构的书籍模式是第一次在打印中描述这些模式。</span><span class="sxs-lookup"><span data-stu-id="5658a-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="5658a-166">在以下部分中，我们将简要介绍这些模式，然后显示这些 ADO.NET 实体框架如何实现和使用这些模式。</span><span class="sxs-lookup"><span data-stu-id="5658a-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="5658a-167">存储库模式</span><span class="sxs-lookup"><span data-stu-id="5658a-167">The Repository Pattern</span></span>

<span data-ttu-id="5658a-168">Fowler 显示了一个存储库 "使用类似于集合的接口来访问域对象" 的域和数据映射层。</span><span class="sxs-lookup"><span data-stu-id="5658a-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="5658a-169">存储库模式的目标是将代码与数据访问的 minutiae 隔离，因此，我们看到的早期隔离是可测试性的必需特性。</span><span class="sxs-lookup"><span data-stu-id="5658a-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="5658a-170">隔离的关键是存储库如何使用类似集合的接口公开对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="5658a-171">你编写的使用存储库的逻辑并不知道存储库将如何具体化你请求的对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="5658a-172">存储库可能会与数据库进行通信，也可能只是从内存中集合返回对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="5658a-173">您的所有代码都需要知道存储库似乎维护集合，并且您可以从集合中检索、添加和删除对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="5658a-174">在现有的 .NET 应用程序中，具体的存储库通常继承自如下所示的泛型接口：</span><span class="sxs-lookup"><span data-stu-id="5658a-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="5658a-175">当我们为 EF4 提供实现时，我们将对接口定义进行一些更改，但基本概念仍保持不变。</span><span class="sxs-lookup"><span data-stu-id="5658a-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="5658a-176">代码可以使用实现此接口的具体存储库，根据谓词的计算结果检索实体集合，或者只检索所有可用的实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="5658a-177">代码还可以通过存储库接口添加和删除实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="5658a-178">对于员工对象的 IRepository，代码可以执行以下操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="5658a-179">由于代码使用的是接口（IRepository），因此我们可以为代码提供不同的接口实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="5658a-180">一个实现可能是 EF4 支持的实现，并将对象保存到 Microsoft SQL Server 数据库中。</span><span class="sxs-lookup"><span data-stu-id="5658a-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="5658a-181">不同的实现（我们在测试过程中使用的）可能由一个内存中员工对象列表来支持。</span><span class="sxs-lookup"><span data-stu-id="5658a-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="5658a-182">接口将帮助实现代码中的隔离。</span><span class="sxs-lookup"><span data-stu-id="5658a-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="5658a-183">请注意，IRepository&lt;T&gt; 接口不会公开保存操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="5658a-184">如何更新现有对象？</span><span class="sxs-lookup"><span data-stu-id="5658a-184">How do we update existing objects?</span></span> <span data-ttu-id="5658a-185">你可能会遇到包含 Save 操作的 IRepository 定义，而这些存储库的实现需要立即将对象保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="5658a-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="5658a-186">但是，在许多应用程序中，我们不希望单独保存对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="5658a-187">相反，我们想要将对象从不同的存储库中移到现实中，并将这些对象修改为业务活动的一部分，然后将所有对象作为单个原子操作的一部分进行保存。</span><span class="sxs-lookup"><span data-stu-id="5658a-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="5658a-188">幸运的是，有一种模式可用于实现此类型的行为。</span><span class="sxs-lookup"><span data-stu-id="5658a-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="5658a-189">工作单元模式</span><span class="sxs-lookup"><span data-stu-id="5658a-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="5658a-190">Fowler 表示工作单元将 "维护受业务事务影响的对象列表，并协调发生的更改和并发问题的解决方法"。</span><span class="sxs-lookup"><span data-stu-id="5658a-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="5658a-191">工作单元的责任是跟踪对存储库所做的对象所做的更改，并在告知工作单元提交更改时保留对对象所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="5658a-192">还需要负责将已添加的新对象添加到所有存储库中的工作单元，并将对象插入到数据库中，还会管理删除。</span><span class="sxs-lookup"><span data-stu-id="5658a-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="5658a-193">如果曾经使用过 ADO.NET 数据集完成任何工作，则已熟悉工作单元模式。</span><span class="sxs-lookup"><span data-stu-id="5658a-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="5658a-194">ADO.NET 数据集能够跟踪对 DataRow 对象的更新、删除和插入操作，并且可以（使用 TableAdapter 的帮助）协调对数据库进行的所有更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="5658a-195">但是，DataSet 对象会为底层数据库的断开连接的子集建模。</span><span class="sxs-lookup"><span data-stu-id="5658a-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="5658a-196">工作单元模式的行为相同，但它适用于与数据访问代码隔离并不知道数据库的业务对象和域对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="5658a-197">对 .NET 代码中的工作单元进行建模的抽象可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="5658a-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="5658a-198">通过从工作单元公开存储库引用，可以确保单个工作单元对象能够跟踪在业务事务中具体化的所有实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="5658a-199">在实际工作单元中实现 Commit 方法是指所有神奇的情况都是为了协调与数据库的内存中更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="5658a-200">给定 IUnitOfWork 引用后，代码可以对从一个或多个存储库检索的业务对象进行更改，并使用原子提交操作保存所有更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="5658a-201">延迟负载模式</span><span class="sxs-lookup"><span data-stu-id="5658a-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="5658a-202">Fowler 使用名称懒惰加载来描述 "对象，该对象不包含你需要的所有数据，但知道如何获取它"。</span><span class="sxs-lookup"><span data-stu-id="5658a-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="5658a-203">在编写可测试的业务代码和使用关系数据库时，透明延迟加载是一项重要功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="5658a-204">作为示例，请考虑以下代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="5658a-205">如何填充时间卡片集合？</span><span class="sxs-lookup"><span data-stu-id="5658a-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="5658a-206">有两个可能的答案。</span><span class="sxs-lookup"><span data-stu-id="5658a-206">There are two possible answers.</span></span> <span data-ttu-id="5658a-207">一项答案是，如果员工需要提取员工，则会发出一个查询，同时检索员工和员工的关联的时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="5658a-208">在关系数据库中，这通常需要带有 JOIN 子句的查询，并且可能会导致检索超过应用程序需求的信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="5658a-209">如果应用程序永远不需要接触时间卡片属性，该怎么办？</span><span class="sxs-lookup"><span data-stu-id="5658a-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="5658a-210">第二个答案是按需加载时间卡片属性。</span><span class="sxs-lookup"><span data-stu-id="5658a-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="5658a-211">此延迟加载对于业务逻辑是隐式且透明的，因为代码不会调用特殊的 Api 来检索时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="5658a-212">此代码假定需要时提供卡信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="5658a-213">延迟加载涉及一些神奇的延迟，通常涉及方法调用的运行时侦听。</span><span class="sxs-lookup"><span data-stu-id="5658a-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="5658a-214">拦截代码负责与数据库进行通信并检索时间卡信息，同时使业务逻辑成为业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="5658a-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="5658a-215">这种延迟加载幻数允许业务代码将自身与数据检索操作隔离开来，并导致代码更具可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="5658a-216">延迟加载的缺点是，*当应用程序*需要时间卡信息时，代码将执行其他查询。</span><span class="sxs-lookup"><span data-stu-id="5658a-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="5658a-217">对于许多应用程序而言，这并不是问题，但对于性能敏感的应用程序或应用程序在循环的每次迭代期间循环使用查询来检索时间卡（通常称为 N + 1查询问题），延迟加载是一个拖动。</span><span class="sxs-lookup"><span data-stu-id="5658a-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="5658a-218">在这些情况下，应用程序可能需要以最有效的方式积极加载时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="5658a-219">幸运的是，在转到下一节并实现这些模式时，我们将了解 EF4 如何支持隐式延迟加载和高效的预先加载。</span><span class="sxs-lookup"><span data-stu-id="5658a-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="5658a-220">用实体框架实现模式</span><span class="sxs-lookup"><span data-stu-id="5658a-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="5658a-221">好消息是，我们在上一节中介绍的所有设计模式都是通过 EF4 实现的简单方式。</span><span class="sxs-lookup"><span data-stu-id="5658a-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="5658a-222">为了演示，我们将使用一个简单的 ASP.NET MVC 应用程序来编辑和显示员工及其相关的时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="5658a-223">首先，我们将使用以下 "纯旧 CLR 对象（Poco）"。</span><span class="sxs-lookup"><span data-stu-id="5658a-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="5658a-224">当我们探索 EF4 的不同方法和功能时，这些类定义将略有不同，但其目的是将这些类视为持久性未知（PI）。</span><span class="sxs-lookup"><span data-stu-id="5658a-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="5658a-225">PI 对象并不知道它持有的状态在数据库*内的状态*。</span><span class="sxs-lookup"><span data-stu-id="5658a-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="5658a-226">PI 和 Poco 有可测试软件。</span><span class="sxs-lookup"><span data-stu-id="5658a-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="5658a-227">使用 POCO 方法的对象不受约束，更灵活，更易于测试，因为它们可以在没有数据库的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="5658a-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="5658a-228">通过 Poco，我们可以在 Visual Studio 中创建实体数据模型（EDM）（参见图1）。</span><span class="sxs-lookup"><span data-stu-id="5658a-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="5658a-229">我们不会使用 EDM 为实体生成代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="5658a-230">相反，我们想要使用我们 lovingly 的实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="5658a-231">我们将仅使用 EDM 生成数据库架构，并提供元数据 EF4 需要将对象映射到数据库。</span><span class="sxs-lookup"><span data-stu-id="5658a-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="5658a-233">**图1**</span><span class="sxs-lookup"><span data-stu-id="5658a-233">**Figure 1**</span></span>

<span data-ttu-id="5658a-234">注意：如果要首先开发 EDM 模型，可以从 EDM 生成干净的 POCO 代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="5658a-235">你可以使用数据可编程性团队提供的 Visual Studio 2010 扩展来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="5658a-236">若要下载该扩展，请从 Visual Studio 中的 "工具" 菜单启动 "扩展管理器"，然后在 "POCO" 模板的联机库中搜索（参见图2）。</span><span class="sxs-lookup"><span data-stu-id="5658a-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="5658a-237">有多种可用于 EF 的 POCO 模板。</span><span class="sxs-lookup"><span data-stu-id="5658a-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="5658a-238">有关使用模板的详细信息，请参阅 "[演练：用于实体框架的 POCO 模板](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)"。</span><span class="sxs-lookup"><span data-stu-id="5658a-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="5658a-240">**图2**</span><span class="sxs-lookup"><span data-stu-id="5658a-240">**Figure 2**</span></span>

<span data-ttu-id="5658a-241">从此 POCO 开始，我们将探讨可测试代码的两种不同方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="5658a-242">第一种方法是调用 EF 方法，因为它利用实体框架 API 中的抽象来实现工作单元和存储库。</span><span class="sxs-lookup"><span data-stu-id="5658a-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="5658a-243">在第二种方法中，我们将创建自己的自定义存储库抽象，并了解每种方法的优点和缺点。</span><span class="sxs-lookup"><span data-stu-id="5658a-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="5658a-244">首先，我们将探讨 EF 方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="5658a-245">EF 中心实现</span><span class="sxs-lookup"><span data-stu-id="5658a-245">An EF Centric Implementation</span></span>

<span data-ttu-id="5658a-246">请考虑 ASP.NET MVC 项目中的以下控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="5658a-247">操作将检索 Employee 对象，并返回一个结果以显示雇员的详细视图。</span><span class="sxs-lookup"><span data-stu-id="5658a-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="5658a-248">代码是否可测试？</span><span class="sxs-lookup"><span data-stu-id="5658a-248">Is the code testable?</span></span> <span data-ttu-id="5658a-249">至少需要两个测试来验证操作的行为。</span><span class="sxs-lookup"><span data-stu-id="5658a-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="5658a-250">首先，我们想要验证操作是否返回正确的视图–一个简单的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="5658a-251">我们还希望编写一个测试来验证该操作是否可检索正确的雇员，我们想要执行此操作，而不执行代码查询数据库。</span><span class="sxs-lookup"><span data-stu-id="5658a-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="5658a-252">请记住，我们想要隔离受测代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="5658a-253">隔离将确保测试不会因数据访问代码或数据库配置中的错误而失败。</span><span class="sxs-lookup"><span data-stu-id="5658a-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="5658a-254">如果测试失败，我们将了解控制器逻辑中的 bug，而不是在某个较低级别的系统组件中。</span><span class="sxs-lookup"><span data-stu-id="5658a-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="5658a-255">若要实现隔离，我们需要一些抽象，如我们之前为存储库和工作单元提供的接口。</span><span class="sxs-lookup"><span data-stu-id="5658a-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="5658a-256">请记住，存储库模式旨在协调域对象和数据映射层。</span><span class="sxs-lookup"><span data-stu-id="5658a-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="5658a-257">在此方案中，EF4*是*数据映射层，并且已经提供名为 IObjectSet 的存储库抽象抽象&lt;t&gt; （来自 system.object 命名空间）。</span><span class="sxs-lookup"><span data-stu-id="5658a-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="5658a-258">接口定义如下所示。</span><span class="sxs-lookup"><span data-stu-id="5658a-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="5658a-259">IObjectSet&lt;T&gt; 满足存储库的要求，因为它类似于对象的集合（通过 IEnumerable&lt;&gt;），并提供了在模拟的集合中添加和删除对象的方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="5658a-260">附加和分离方法公开了 EF4 API 的其他功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="5658a-261">若要使用 IObjectSet&lt;T&gt; 作为存储库的接口，我们需要一个工作单元来将存储库绑定在一起。</span><span class="sxs-lookup"><span data-stu-id="5658a-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="5658a-262">此接口的一个具体实现将与 SQL Server 通信，并使用 EF4 中的 ObjectContext 类轻松创建。</span><span class="sxs-lookup"><span data-stu-id="5658a-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="5658a-263">ObjectContext 类是 EF4 API 中的实际工作单元。</span><span class="sxs-lookup"><span data-stu-id="5658a-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="5658a-264">将 IObjectSet&lt;T&gt; 的生活与调用 ObjectContext 对象的 Createobjectset<tentity> 方法一样简单。</span><span class="sxs-lookup"><span data-stu-id="5658a-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="5658a-265">在后台，框架将使用我们在 EDM 中提供的元数据生成具体的 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5658a-266">我们会坚持返回 IObjectSet&lt;T&gt; 接口，因为它有助于在客户端代码中保留可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="5658a-267">这种具体的实现在生产中很有用，但我们需要重点介绍我们如何使用我们的 IUnitOfWork 抽象来简化测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="5658a-268">测试双精度</span><span class="sxs-lookup"><span data-stu-id="5658a-268">The Test Doubles</span></span>

<span data-ttu-id="5658a-269">若要隔离控制器操作，我们需要能够在实际工作单元（由 ObjectContext 支持）和测试 double 或 "假" 工作单元（执行内存中操作）之间进行切换。</span><span class="sxs-lookup"><span data-stu-id="5658a-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="5658a-270">执行这种类型的切换的常见方法是不让 MVC 控制器实例化工作单元，而是将工作单元作为构造函数参数传递到控制器。</span><span class="sxs-lookup"><span data-stu-id="5658a-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="5658a-271">上面的代码是依赖关系注入的示例。</span><span class="sxs-lookup"><span data-stu-id="5658a-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="5658a-272">我们不允许控制器创建依赖项（工作单元），但会将依赖项注入到控制器中。</span><span class="sxs-lookup"><span data-stu-id="5658a-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="5658a-273">在 MVC 项目中，通常将自定义控制器工厂与控制反转（IoC）容器结合使用来自动执行依赖关系注入。</span><span class="sxs-lookup"><span data-stu-id="5658a-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="5658a-274">这些主题超出了本文的范围，但你可以通过遵循本文末尾的参考来了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="5658a-275">可用于测试的伪工作单元实现可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="5658a-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="5658a-276">请注意，伪工作单元公开了提交的属性。</span><span class="sxs-lookup"><span data-stu-id="5658a-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="5658a-277">有时将功能添加到可简化测试的虚设类中。</span><span class="sxs-lookup"><span data-stu-id="5658a-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="5658a-278">在这种情况下，如果代码通过检查确认属性来提交工作单元，则很容易观察。</span><span class="sxs-lookup"><span data-stu-id="5658a-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="5658a-279">还需要一个虚设的 IObjectSet&lt;T&gt; 将员工和卡上的对象保存在内存中。</span><span class="sxs-lookup"><span data-stu-id="5658a-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="5658a-280">我们可以使用泛型提供单个实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="5658a-281">此测试将其大部分工作委托给基础 HashSet&lt;T&gt; 对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="5658a-282">请注意，IObjectSet&lt;T&gt; 需要将 T 强制为类（引用类型）的泛型约束，并强制我们实现 IQueryable&lt;&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="5658a-283">使用标准 LINQ 运算符 AsQueryable，可以轻松地将内存中集合显示为 IQueryable&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="5658a-284">测试</span><span class="sxs-lookup"><span data-stu-id="5658a-284">The Tests</span></span>

<span data-ttu-id="5658a-285">传统单元测试将使用一个测试类来保存单个 MVC 控制器中所有操作的所有测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="5658a-286">我们可以使用生成的内存 fakes 中的来编写这些测试或任何类型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="5658a-287">但在本文中，我们将避免使用整体测试类方法，而是将测试分组，以重点关注特定功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="5658a-288">  例如，"创建新员工" 可能是我们要测试的功能，因此，我们将使用一个测试类来验证负责创建新员工的单个控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="5658a-289">所有这些细化测试类都需要一些常见的安装代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="5658a-290">例如，我们始终需要创建内存中存储库和伪工作单元。</span><span class="sxs-lookup"><span data-stu-id="5658a-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="5658a-291">还需要一个员工控制器实例，其中包含虚假的工作单元。</span><span class="sxs-lookup"><span data-stu-id="5658a-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="5658a-292">我们将使用基类在测试类之间共享此常见安装代码。</span><span class="sxs-lookup"><span data-stu-id="5658a-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="5658a-293">在基类中使用的 "对象母亲" 是创建测试数据的一种常见模式。</span><span class="sxs-lookup"><span data-stu-id="5658a-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="5658a-294">对象母亲包含用于实例化测试实体的工厂方法，以便在多个测试装置中使用。</span><span class="sxs-lookup"><span data-stu-id="5658a-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="5658a-295">我们可以将 EmployeeControllerTestBase 用作多个测试装置的基类（请参阅图3）。</span><span class="sxs-lookup"><span data-stu-id="5658a-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="5658a-296">每个测试装置将测试特定控制器操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="5658a-297">例如，一个测试装置将重点放在测试 HTTP GET 请求期间使用的 "创建" 操作（用于显示用于创建员工的视图），另一个装置将重点放在 HTTP POST 请求中使用的 "创建" 操作（以获取由用户创建员工）。</span><span class="sxs-lookup"><span data-stu-id="5658a-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="5658a-298">每个派生类只负责特定上下文中所需的设置，并提供验证其特定测试上下文的结果所需的断言。</span><span class="sxs-lookup"><span data-stu-id="5658a-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="5658a-300">**图3**</span><span class="sxs-lookup"><span data-stu-id="5658a-300">**Figure 3**</span></span>

<span data-ttu-id="5658a-301">此处提供的命名约定和测试样式不是可测试代码所必需的–它只是一种方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="5658a-302">图4显示了 Visual Studio 2010 的 Jet 大脑 Resharper 测试运行程序插件中运行的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="5658a-304">**图4**</span><span class="sxs-lookup"><span data-stu-id="5658a-304">**Figure 4**</span></span>

<span data-ttu-id="5658a-305">使用基类来处理共享的安装代码，每个控制器操作的单元测试都很小，并且易于编写。</span><span class="sxs-lookup"><span data-stu-id="5658a-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="5658a-306">测试将快速执行（因为我们在执行内存中的操作），不应因为不相关的基础结构或环境问题而失败（因为我们已将所测试的单元隔离）。</span><span class="sxs-lookup"><span data-stu-id="5658a-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="5658a-307">在这些测试中，基类执行大部分的设置工作。</span><span class="sxs-lookup"><span data-stu-id="5658a-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="5658a-308">请记住，基类构造函数会创建内存中存储库、伪工作单元和 EmployeeController 类的实例。</span><span class="sxs-lookup"><span data-stu-id="5658a-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="5658a-309">该测试类从此基类派生，重点介绍了测试 Create 方法的具体信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="5658a-310">在这种情况下，将在任何单元测试过程中看到的 "排列、操作和断言" 步骤可归结：</span><span class="sxs-lookup"><span data-stu-id="5658a-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="5658a-311">创建用于模拟传入数据的 newEmployee 对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="5658a-312">调用 EmployeeController 的 Create 操作并传入 newEmployee。</span><span class="sxs-lookup"><span data-stu-id="5658a-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="5658a-313">验证 "创建" 操作是否生成预期的结果（员工出现在存储库中）。</span><span class="sxs-lookup"><span data-stu-id="5658a-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="5658a-314">我们生成的内容使我们可以测试任何 EmployeeController 操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="5658a-315">例如，当我们为 Employee 控制器的索引操作编写测试时，可以从测试基类继承，为测试建立相同的基本设置。</span><span class="sxs-lookup"><span data-stu-id="5658a-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="5658a-316">同样，基类会创建内存中存储库、伪工作单元和 EmployeeController 的实例。</span><span class="sxs-lookup"><span data-stu-id="5658a-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="5658a-317">针对索引操作的测试只需重点介绍如何调用索引操作并测试操作返回的模型的质量。</span><span class="sxs-lookup"><span data-stu-id="5658a-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="5658a-318">用内存中 fakes 创建的测试面向测试软件的*状态*。</span><span class="sxs-lookup"><span data-stu-id="5658a-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="5658a-319">例如，在测试创建操作时，我们想要在执行 "创建" 操作后检查存储库的状态–存储库是否持有新员工？</span><span class="sxs-lookup"><span data-stu-id="5658a-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="5658a-320">稍后我们将探讨基于交互的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="5658a-321">基于交互的测试会询问所测试的代码是否在对象上调用了正确的方法，并传递了正确的参数。</span><span class="sxs-lookup"><span data-stu-id="5658a-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="5658a-322">现在，我们将继续介绍另一种设计模式–延迟负载。</span><span class="sxs-lookup"><span data-stu-id="5658a-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="5658a-323">预先加载和延迟加载</span><span class="sxs-lookup"><span data-stu-id="5658a-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="5658a-324">在 ASP.NET MVC web 应用程序的某个时候，我们可能希望显示雇员的信息，并包括员工的相关时间表。</span><span class="sxs-lookup"><span data-stu-id="5658a-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="5658a-325">例如，我们可能会显示一个 "时间表摘要" 显示，其中显示了雇员的姓名和系统中的总时间。</span><span class="sxs-lookup"><span data-stu-id="5658a-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="5658a-326">可以采用几种方法实现此功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="5658a-327">投影</span><span class="sxs-lookup"><span data-stu-id="5658a-327">Projection</span></span>

<span data-ttu-id="5658a-328">创建摘要的一种简单方法是构造专用于要在视图中显示的信息的模型。</span><span class="sxs-lookup"><span data-stu-id="5658a-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="5658a-329">在这种情况下，该模型可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="5658a-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="5658a-330">请注意，EmployeeSummaryViewModel 不是一个实体–换言之，在数据库中不会保留它。</span><span class="sxs-lookup"><span data-stu-id="5658a-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="5658a-331">我们只会使用此类以强类型方式将数据无序播放到视图中。</span><span class="sxs-lookup"><span data-stu-id="5658a-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="5658a-332">视图模型类似于数据传输对象（DTO），因为它不包含 "仅行为" 属性。</span><span class="sxs-lookup"><span data-stu-id="5658a-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="5658a-333">这些属性将保存需要移动的数据。</span><span class="sxs-lookup"><span data-stu-id="5658a-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="5658a-334">使用 LINQ 的标准投影运算符（Select 运算符），可以轻松地实例化此视图模型。</span><span class="sxs-lookup"><span data-stu-id="5658a-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="5658a-335">上面的代码有两个值得注意的功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-335">There are two notable features to the above code.</span></span> <span data-ttu-id="5658a-336">首先–代码易于测试，因为它仍易于观察和隔离。</span><span class="sxs-lookup"><span data-stu-id="5658a-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="5658a-337">除了实际工作单元，Select 运算符还能与内存中的 fakes 一起工作。</span><span class="sxs-lookup"><span data-stu-id="5658a-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="5658a-338">第二个值得注意的功能是代码如何允许 EF4 生成单个有效的查询，以将员工和时间卡信息组合在一起。</span><span class="sxs-lookup"><span data-stu-id="5658a-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="5658a-339">我们已将员工信息和时间卡信息加载到同一个对象中，而无需使用任何特殊的 Api。</span><span class="sxs-lookup"><span data-stu-id="5658a-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="5658a-340">此代码仅使用标准 LINQ 运算符（适用于内存中数据源以及远程数据源）来表示所需的信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="5658a-341">EF4 能够将 LINQ 查询和 C\# 编译器生成的表达式树转换为单个高效的 T-sql 查询。</span><span class="sxs-lookup"><span data-stu-id="5658a-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="5658a-342">在其他一些情况下，我们不想使用视图模型或 DTO 对象，而是使用实际实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="5658a-343">当我们知道我们需要员工*和*员工的时间卡时，可以积极以不引人注目和高效的方式加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="5658a-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="5658a-344">显式预先加载</span><span class="sxs-lookup"><span data-stu-id="5658a-344">Explicit Eager Loading</span></span>

<span data-ttu-id="5658a-345">当我们想要积极加载相关实体信息时，我们需要某种机制来实现业务逻辑（或在此方案中为控制器操作逻辑），以表达其对存储库的期望。</span><span class="sxs-lookup"><span data-stu-id="5658a-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="5658a-346">EF4 ObjectQuery&lt;T&gt; 类定义了包含方法，以指定在查询过程中要检索的相关对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="5658a-347">请记住，EF4 ObjectContext 通过&gt; 类的具体 ObjectSet&lt;类公开实体，后者继承自 ObjectQuery&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="5658a-348">  如果使用的是 ObjectSet&lt;T&gt; 控制器操作中的引用，我们可以编写以下代码来指定每个员工的时间卡信息的预先加载。</span><span class="sxs-lookup"><span data-stu-id="5658a-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="5658a-349">不过，由于我们要尝试让代码可测试，因此，我们不会公开 ObjectSet&lt;T&gt; 从真实的工作单元的外部。</span><span class="sxs-lookup"><span data-stu-id="5658a-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="5658a-350">相反，我们依赖于 IObjectSet&lt;T&gt; 接口，该接口更易于假冒，但 IObjectSet&lt;T&gt; 未定义 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="5658a-351">LINQ 的优点是，我们可以创建自己的 Include 运算符。</span><span class="sxs-lookup"><span data-stu-id="5658a-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="5658a-352">请注意，此 Include 运算符定义为 IQueryable&lt;T&gt; 的扩展方法，而不是&gt;的 IObjectSet&lt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5658a-353">这使我们能够将方法用于范围更广的可能类型，包括 IQueryable&lt;T&gt;、IObjectSet&lt;T&gt;、ObjectQuery&lt;T&gt;和 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5658a-354">如果基础序列不是真正的 EF4 ObjectQuery&lt;T&gt;，则不会有任何损害，而且 Include 运算符是一个无操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="5658a-355">如果基础序列*是*ObjectQuery&lt;t&gt; （或派生自 ObjectQuery&lt;&gt;），则 EF4 将看到对其他数据的要求，并制定正确的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="5658a-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="5658a-356">使用这个新的运算符，我们可以从存储库显式请求预先加载的时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="5658a-357">针对实际 ObjectContext 运行时，代码将生成以下单个查询。</span><span class="sxs-lookup"><span data-stu-id="5658a-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="5658a-358">查询在一次中从数据库收集足够多的信息，以具体化 employee 对象并完全填充其时间卡片属性。</span><span class="sxs-lookup"><span data-stu-id="5658a-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="5658a-359">好消息是，操作方法中的代码保持完全可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="5658a-360">我们不需要为 fakes 提供任何其他功能来支持 Include 运算符。</span><span class="sxs-lookup"><span data-stu-id="5658a-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="5658a-361">糟糕的是，我们必须在要保留持久性未知的代码内使用 Include 运算符。</span><span class="sxs-lookup"><span data-stu-id="5658a-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="5658a-362">这是生成可测试代码时需要评估的折衷类型的一个典型示例。</span><span class="sxs-lookup"><span data-stu-id="5658a-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="5658a-363">有时，您需要使持久性问题在存储库抽象之外泄露以满足性能目标。</span><span class="sxs-lookup"><span data-stu-id="5658a-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="5658a-364">预先加载的替代方法是延迟加载。</span><span class="sxs-lookup"><span data-stu-id="5658a-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="5658a-365">延迟加载意味着我们*不*需要业务代码来显式公告关联数据的要求。</span><span class="sxs-lookup"><span data-stu-id="5658a-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="5658a-366">相反，我们在应用程序中使用实体，如果需要其他数据实体框架将按需加载数据。</span><span class="sxs-lookup"><span data-stu-id="5658a-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="5658a-367">延迟加载</span><span class="sxs-lookup"><span data-stu-id="5658a-367">Lazy Loading</span></span>

<span data-ttu-id="5658a-368">这种情况很容易想象到，这种情况下，我们并不知道有业务逻辑需要哪些数据。</span><span class="sxs-lookup"><span data-stu-id="5658a-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="5658a-369">我们可能知道逻辑需要一个 employee 对象，但我们可能会将其分支到不同的执行路径，其中某些路径需要员工的时间卡信息，而另一些则不需要。</span><span class="sxs-lookup"><span data-stu-id="5658a-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="5658a-370">这种情况非常适合隐式延迟加载，因为数据神奇地出现在需要的基础上。</span><span class="sxs-lookup"><span data-stu-id="5658a-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="5658a-371">延迟加载（也称为延迟加载）对实体对象有一些要求。</span><span class="sxs-lookup"><span data-stu-id="5658a-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="5658a-372">具有 true 持久性无感知的 Poco 不会面对持久性层的任何要求，但真正的持久性无感知根本无法实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="5658a-373">  相反，我们会以相对度数度量持久性无感知。</span><span class="sxs-lookup"><span data-stu-id="5658a-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="5658a-374">如果需要从面向持久性的基类继承，或使用专用集合在 Poco 中实现延迟加载，则会很遗憾。</span><span class="sxs-lookup"><span data-stu-id="5658a-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="5658a-375">幸运的是，EF4 具有更不具侵入性的解决方案。</span><span class="sxs-lookup"><span data-stu-id="5658a-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="5658a-376">几乎无法检测</span><span class="sxs-lookup"><span data-stu-id="5658a-376">Virtually Undetectable</span></span>

<span data-ttu-id="5658a-377">使用 POCO 对象时，EF4 可以为实体动态生成运行时代理。</span><span class="sxs-lookup"><span data-stu-id="5658a-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="5658a-378">这些代理以不可见的方式包装具体化的 Poco，并通过截取每个属性 get 和 set 操作来执行其他工作，从而提供其他服务。</span><span class="sxs-lookup"><span data-stu-id="5658a-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="5658a-379">其中一项服务是我们要查找的延迟加载功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="5658a-380">另一服务是一种有效的更改跟踪机制，可以在程序更改实体的属性值时进行记录。</span><span class="sxs-lookup"><span data-stu-id="5658a-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="5658a-381">在 SaveChanges 方法期间 ObjectContext 使用更改列表，以使用更新命令来持久保存所有已修改的实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="5658a-382">但是，若要使这些代理正常工作，它们需要有一种方法来挂钩实体上的属性 get 和 set 操作，并且代理通过重写虚拟成员来实现此目标。</span><span class="sxs-lookup"><span data-stu-id="5658a-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="5658a-383">因此，如果需要隐式延迟加载和高效的更改跟踪，则需要返回到 POCO 类定义并将属性标记为虚拟。</span><span class="sxs-lookup"><span data-stu-id="5658a-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="5658a-384">我们仍然可以说，Employee 实体主要是持久性未知的。</span><span class="sxs-lookup"><span data-stu-id="5658a-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="5658a-385">唯一的要求是使用虚拟成员，这不会影响代码的可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="5658a-386">不需要从任何特殊基类派生，甚至可以使用专用于延迟加载的特殊集合。</span><span class="sxs-lookup"><span data-stu-id="5658a-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="5658a-387">如代码所示，任何实现 ICollection&lt;T&gt; 的类都可用于保存相关实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="5658a-388">还需要在我们的工作单元内进行一个小的更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="5658a-389">当直接使用 ObjectContext 对象时，延迟加载默认情况下处于*关闭状态*。</span><span class="sxs-lookup"><span data-stu-id="5658a-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="5658a-390">可以在 ContextOptions 属性上设置一个属性来启用延迟加载，如果我们想要在任何位置启用延迟加载，则可以在实际工作单元内设置此属性。</span><span class="sxs-lookup"><span data-stu-id="5658a-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="5658a-391">启用隐式延迟加载后，应用程序代码可以使用员工和员工的相关时间表，同时丝毫不知道 EF 加载额外数据所需的工作。</span><span class="sxs-lookup"><span data-stu-id="5658a-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="5658a-392">延迟加载使得应用程序代码更易于编写，而对于代理的神奇，代码会保持完全可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="5658a-393">工作单元的内存中 fakes 只需在测试过程中需要时使用关联数据预加载虚设实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="5658a-394">此时，我们将使用 IObjectSet&lt;T&gt; 来找出生成存储库的注意力，并查看抽象以隐藏持久性框架的所有标志。</span><span class="sxs-lookup"><span data-stu-id="5658a-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="5658a-395">自定义存储库</span><span class="sxs-lookup"><span data-stu-id="5658a-395">Custom Repositories</span></span>

<span data-ttu-id="5658a-396">当我们在本文中第一次介绍工作单元设计模式时，我们提供了一些示例代码，说明工作单元的外观。</span><span class="sxs-lookup"><span data-stu-id="5658a-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="5658a-397">让我们使用我们一直在使用的员工和员工时间表方案来重新展示这一思路。</span><span class="sxs-lookup"><span data-stu-id="5658a-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="5658a-398">此工作单元和我们在上一节中创建的工作单元之间的主要区别在于，此工作单元不使用 EF4 框架中的任何抽象（&gt;没有任何 IObjectSet&lt;）。</span><span class="sxs-lookup"><span data-stu-id="5658a-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="5658a-399">IObjectSet&lt;T&gt; 可用作存储库接口，但它公开的 API 可能并不完全符合应用程序的需求。</span><span class="sxs-lookup"><span data-stu-id="5658a-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="5658a-400">在即将推出的方法中，我们将使用自定义 IRepository&lt;T&gt; 抽象来表示存储库。</span><span class="sxs-lookup"><span data-stu-id="5658a-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="5658a-401">许多遵循测试驱动设计、行为驱动设计和域驱动方法设计的开发人员出于多种原因而 IRepository&lt;T&gt; 方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="5658a-402">首先，IRepository&lt;T&gt; 接口表示 "反损坏" 层。</span><span class="sxs-lookup"><span data-stu-id="5658a-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="5658a-403">正如 Eric Evans 在他的域驱动的设计书中所述，抗损坏层会使你的域代码远离基础结构 Api，如永久性 API。</span><span class="sxs-lookup"><span data-stu-id="5658a-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="5658a-404">其次，开发人员可以在存储库中构建满足应用程序确切需求的方法（在编写测试时发现）。</span><span class="sxs-lookup"><span data-stu-id="5658a-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="5658a-405">例如，我们可能经常需要使用 ID 值查找单个实体，因此可以将 FindById 方法添加到存储库接口。</span><span class="sxs-lookup"><span data-stu-id="5658a-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="5658a-406">  IRepository&lt;T&gt; 定义将如下所示。</span><span class="sxs-lookup"><span data-stu-id="5658a-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="5658a-407">请注意，我们将返回使用 IQueryable&lt;T&gt; 接口来公开实体集合。</span><span class="sxs-lookup"><span data-stu-id="5658a-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="5658a-408">IQueryable&lt;T&gt; 允许 LINQ 表达式树流入 EF4 提供程序，并为提供程序提供查询的整体视图。</span><span class="sxs-lookup"><span data-stu-id="5658a-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="5658a-409">第二个选项是返回 IEnumerable&lt;T&gt;，这意味着 EF4 LINQ 提供程序将仅查看在存储库内生成的表达式。</span><span class="sxs-lookup"><span data-stu-id="5658a-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="5658a-410">不会将存储库外部完成的任何分组、排序和投影组合到发送到数据库的 SQL 命令中，这可能会影响性能。</span><span class="sxs-lookup"><span data-stu-id="5658a-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="5658a-411">另一方面，只返回 IEnumerable&lt;T&gt; 结果的存储库永远不会让你使用新的 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="5658a-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="5658a-412">这两种方法都适用，并且这两种方法保持可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="5658a-413">简单的方法是使用泛型和 EF4 ObjectContext API 提供 IRepository&lt;T&gt; 接口的单个实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="5658a-414">IRepository&lt;T&gt; 方法为我们提供对查询的更多控制，因为客户端必须调用方法才能访问实体。</span><span class="sxs-lookup"><span data-stu-id="5658a-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="5658a-415">在方法中，我们可以提供额外的检查和 LINQ 运算符来强制应用程序约束。</span><span class="sxs-lookup"><span data-stu-id="5658a-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="5658a-416">请注意，接口对泛型类型参数具有两个约束。</span><span class="sxs-lookup"><span data-stu-id="5658a-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="5658a-417">第一个约束是 ObjectSet&lt;T&gt;所需的类缺点破坏，第二个约束强制我们的实体实现 IEntity –为应用程序创建的抽象。</span><span class="sxs-lookup"><span data-stu-id="5658a-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="5658a-418">IEntity 接口强制实体具有可读 Id 属性，然后可以在 FindById 方法中使用此属性。</span><span class="sxs-lookup"><span data-stu-id="5658a-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="5658a-419">IEntity 是用下面的代码定义的。</span><span class="sxs-lookup"><span data-stu-id="5658a-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="5658a-420">IEntity 可能被视为一小部分持久性无感知，因为需要实体才能实现此接口。</span><span class="sxs-lookup"><span data-stu-id="5658a-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="5658a-421">请记住持久性无感知是关于权衡的，许多 FindById 功能将超过接口施加的约束。</span><span class="sxs-lookup"><span data-stu-id="5658a-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="5658a-422">接口对可测试性没有影响。</span><span class="sxs-lookup"><span data-stu-id="5658a-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="5658a-423">实例化 live IRepository&lt;T&gt; 需要 EF4 ObjectContext，因此具体的工作单元实现应管理实例化。</span><span class="sxs-lookup"><span data-stu-id="5658a-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="5658a-424">使用自定义存储库</span><span class="sxs-lookup"><span data-stu-id="5658a-424">Using the Custom Repository</span></span>

<span data-ttu-id="5658a-425">使用自定义存储库并不大不同于使用基于 IObjectSet&lt;T&gt;的存储库。</span><span class="sxs-lookup"><span data-stu-id="5658a-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="5658a-426">我们首先需要调用一个存储库方法来获取 IQueryable&lt;T&gt; 引用，而不是直接对属性应用 LINQ 运算符。</span><span class="sxs-lookup"><span data-stu-id="5658a-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="5658a-427">请注意，我们先前实现的自定义 Include 运算符无需更改即可运行。</span><span class="sxs-lookup"><span data-stu-id="5658a-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="5658a-428">存储库的 FindById 方法从尝试检索单个实体的操作中删除重复逻辑。</span><span class="sxs-lookup"><span data-stu-id="5658a-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="5658a-429">我们所检查的两种方法的可测试性并没有显著的差异。</span><span class="sxs-lookup"><span data-stu-id="5658a-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="5658a-430">我们可以通过构建由 HashSet&lt;&gt; 员工支持的具体类来提供 IRepository&lt;T&gt; 的伪实现，就像我们在上一节中所做的那样。</span><span class="sxs-lookup"><span data-stu-id="5658a-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="5658a-431">但是，某些开发人员更喜欢使用 mock 对象和模拟对象框架，而不是生成 fakes。</span><span class="sxs-lookup"><span data-stu-id="5658a-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="5658a-432">我们将在下一节中介绍如何使用模拟来测试实现并讨论模拟和 fakes 之间的差异。</span><span class="sxs-lookup"><span data-stu-id="5658a-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="5658a-433">用模拟进行测试</span><span class="sxs-lookup"><span data-stu-id="5658a-433">Testing with Mocks</span></span>

<span data-ttu-id="5658a-434">可以使用不同的方法来构建 Fowler 调用 "测试双精度" 的情况。</span><span class="sxs-lookup"><span data-stu-id="5658a-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="5658a-435">测试双精度型（如电影 stunt 双精度型）是在测试期间生成的、用于实际生产对象的对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="5658a-436">我们创建的内存中存储库是与 SQL Server 通信的存储库的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="5658a-437">我们已了解如何在单元测试过程中使用这些测试双线来隔离代码并使测试运行速度更快。</span><span class="sxs-lookup"><span data-stu-id="5658a-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="5658a-438">我们生成的测试翻倍，实现了真实的工作实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="5658a-439">在幕后，每个对象都存储一个具体的对象集合，并将在测试过程中操作存储库时，在此集合中添加和移除对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="5658a-440">一些开发人员喜欢以这种方式生成测试，这种方法包括真实的代码和工作实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="5658a-441">  这些测试是*fakes*的。</span><span class="sxs-lookup"><span data-stu-id="5658a-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="5658a-442">它们具有有效的实现，但并不真正足以用于生产。</span><span class="sxs-lookup"><span data-stu-id="5658a-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="5658a-443">伪存储库实际上不会写入数据库。</span><span class="sxs-lookup"><span data-stu-id="5658a-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="5658a-444">伪 SMTP 服务器实际上不会通过网络发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="5658a-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="5658a-445">模拟与 Fakes</span><span class="sxs-lookup"><span data-stu-id="5658a-445">Mocks versus Fakes</span></span>

<span data-ttu-id="5658a-446">还有另一种类型的测试 double 称为*模拟*。</span><span class="sxs-lookup"><span data-stu-id="5658a-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="5658a-447">尽管 fakes 具有有效的实现，但模拟没有实现。</span><span class="sxs-lookup"><span data-stu-id="5658a-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="5658a-448">使用 mock 对象框架的帮助，我们在运行时构造这些模拟对象并将其用作测试双精度型。</span><span class="sxs-lookup"><span data-stu-id="5658a-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="5658a-449">在本部分中，我们将使用开源模拟 framework Moq。</span><span class="sxs-lookup"><span data-stu-id="5658a-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="5658a-450">下面是一个简单的示例，说明如何使用 Moq 为员工存储库动态创建测试 double。</span><span class="sxs-lookup"><span data-stu-id="5658a-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="5658a-451">我们要求 Moq IRepository&lt;员工&gt; 实现，并且它会动态生成一个。</span><span class="sxs-lookup"><span data-stu-id="5658a-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="5658a-452">通过访问 Mock&lt;T&gt; 对象的对象属性，我们可以访问实现 IRepository 的对象&lt;员工&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="5658a-453">我们可以将此内部对象传递到我们的控制器中，并不知道这是测试 double 还是真正的存储库。</span><span class="sxs-lookup"><span data-stu-id="5658a-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="5658a-454">可以调用对象上的方法，就像我们使用实际实现调用对象上的方法一样。</span><span class="sxs-lookup"><span data-stu-id="5658a-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="5658a-455">当我们调用 Add 方法时，你必须知道 mock 存储库将执行的操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="5658a-456">由于 mock 对象尚无实现，因此不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="5658a-457">幕后没有具体的集合，就像我们编写的 fakes 一样，因此放弃了该员工。</span><span class="sxs-lookup"><span data-stu-id="5658a-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="5658a-458">FindById 的返回值怎么样？</span><span class="sxs-lookup"><span data-stu-id="5658a-458">What about the return value of FindById?</span></span> <span data-ttu-id="5658a-459">在这种情况下，mock 对象执行的唯一操作就是返回默认值。</span><span class="sxs-lookup"><span data-stu-id="5658a-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="5658a-460">由于我们返回引用类型（雇员），因此返回值为 null 值。</span><span class="sxs-lookup"><span data-stu-id="5658a-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="5658a-461">模拟可能会毫无意义。但是，我们还没有谈到模拟的两个功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="5658a-462">首先，Moq 框架记录模拟对象上发出的所有调用。</span><span class="sxs-lookup"><span data-stu-id="5658a-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="5658a-463">稍后在代码中，我们可以询问 Moq 是否有人调用了 Add 方法，或者有人调用了 FindById 方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="5658a-464">稍后我们将介绍如何在测试中使用此 "黑色框" 录制功能。</span><span class="sxs-lookup"><span data-stu-id="5658a-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="5658a-465">第二大功能*是，我们*可以使用 Moq 来为模拟对象编程。</span><span class="sxs-lookup"><span data-stu-id="5658a-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="5658a-466">预期会告诉 mock 对象如何响应任何给定的交互。</span><span class="sxs-lookup"><span data-stu-id="5658a-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="5658a-467">例如，我们可以将预期计划为模拟，并告诉它在用户调用 FindById 时返回 employee 对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="5658a-468">Moq 框架使用安装 API 和 lambda 表达式来对这些预期进行编程。</span><span class="sxs-lookup"><span data-stu-id="5658a-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="5658a-469">在此示例中，我们要求 Moq 动态构建一个存储库，然后对存储库进行计划。</span><span class="sxs-lookup"><span data-stu-id="5658a-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="5658a-470">当有人调用 FindById 方法传递值5时，预期会告知 mock 对象返回 Id 值为5的新 employee 对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="5658a-471">此测试通过，我们不需要生成完整的实现来实现&lt;T&gt;的虚假 IRepository。</span><span class="sxs-lookup"><span data-stu-id="5658a-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="5658a-472">让我们重新访问之前编写的测试，并对其进行返工，使用模拟而不是 fakes。</span><span class="sxs-lookup"><span data-stu-id="5658a-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="5658a-473">就像以前一样，我们将使用基类来设置控制器所有测试所需的基础结构的公共部分。</span><span class="sxs-lookup"><span data-stu-id="5658a-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="5658a-474">安装程序代码的基本保持不变。</span><span class="sxs-lookup"><span data-stu-id="5658a-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="5658a-475">我们将使用 Moq 来构造 mock 对象，而不是使用 fakes。</span><span class="sxs-lookup"><span data-stu-id="5658a-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="5658a-476">当代码调用 Employees 属性时，该基类将排列模拟工作单元以返回 mock 存储库。</span><span class="sxs-lookup"><span data-stu-id="5658a-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="5658a-477">模拟设置的其余部分将在专用于每个特定方案的测试装置内进行。</span><span class="sxs-lookup"><span data-stu-id="5658a-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="5658a-478">例如，当操作调用 mock 存储库的 FindAll 方法时，用于索引操作的测试装置将设置 mock 存储库以返回员工列表。</span><span class="sxs-lookup"><span data-stu-id="5658a-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="5658a-479">除了预期之外，我们的测试看起来类似于以前的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="5658a-480">然而，使用模拟框架的录制功能，我们可以从不同角度进行测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="5658a-481">在下一部分中，我们将介绍这个新的透视。</span><span class="sxs-lookup"><span data-stu-id="5658a-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="5658a-482">状态与交互测试</span><span class="sxs-lookup"><span data-stu-id="5658a-482">State versus Interaction Testing</span></span>

<span data-ttu-id="5658a-483">你可以使用不同的技术来测试具有 mock 对象的软件。</span><span class="sxs-lookup"><span data-stu-id="5658a-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="5658a-484">一种方法是使用基于状态的测试，这是我们目前在本文中所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="5658a-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="5658a-485">基于状态的测试对软件的状态进行断言。</span><span class="sxs-lookup"><span data-stu-id="5658a-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="5658a-486">在最后一次测试中，我们在控制器上调用了操作方法，并对它应生成的模型作出了一个断言。</span><span class="sxs-lookup"><span data-stu-id="5658a-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="5658a-487">下面是测试状态的一些其他示例：</span><span class="sxs-lookup"><span data-stu-id="5658a-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="5658a-488">创建 "创建" 后，验证存储库是否包含新的 employee 对象。</span><span class="sxs-lookup"><span data-stu-id="5658a-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="5658a-489">验证该模型在执行 Index 后是否包含所有员工的列表。</span><span class="sxs-lookup"><span data-stu-id="5658a-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="5658a-490">验证 "删除" 执行后存储库不包含给定的员工。</span><span class="sxs-lookup"><span data-stu-id="5658a-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="5658a-491">模拟对象的另一种方法是验证*交互*。</span><span class="sxs-lookup"><span data-stu-id="5658a-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="5658a-492">虽然基于状态的测试对对象的状态进行断言，但基于交互的测试会使断言与对象交互的方式有关。</span><span class="sxs-lookup"><span data-stu-id="5658a-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="5658a-493">例如：</span><span class="sxs-lookup"><span data-stu-id="5658a-493">For example:</span></span>

-   <span data-ttu-id="5658a-494">验证在创建执行时控制器是否调用存储库的 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="5658a-495">当执行索引时，验证控制器是否调用存储库的 FindAll 方法。</span><span class="sxs-lookup"><span data-stu-id="5658a-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="5658a-496">验证控制器是否调用工作单元的 Commit 方法，以便在执行编辑时保存更改。</span><span class="sxs-lookup"><span data-stu-id="5658a-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="5658a-497">交互测试通常需要较少的测试数据，因为我们不会在集合中闲逛和验证计数。</span><span class="sxs-lookup"><span data-stu-id="5658a-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="5658a-498">例如，如果我们知道详细信息操作将使用正确的值调用存储库的 FindById 方法，则操作可能会正常运行。</span><span class="sxs-lookup"><span data-stu-id="5658a-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="5658a-499">我们可以验证此行为，而无需设置任何要从 FindById 返回的测试数据。</span><span class="sxs-lookup"><span data-stu-id="5658a-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="5658a-500">上述测试装置中唯一需要的设置是由基类提供的设置。</span><span class="sxs-lookup"><span data-stu-id="5658a-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="5658a-501">当我们调用控制器操作时，Moq 将记录与 mock 存储库的交互。</span><span class="sxs-lookup"><span data-stu-id="5658a-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="5658a-502">使用 Moq 的 Verify API，如果控制器使用正确的 ID 值调用 FindById，则可以询问 Moq。</span><span class="sxs-lookup"><span data-stu-id="5658a-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="5658a-503">如果控制器未调用方法，或调用方法时出现意外的参数值，则 Verify 方法将引发异常，并且测试将失败。</span><span class="sxs-lookup"><span data-stu-id="5658a-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="5658a-504">下面是另一个示例，用于验证创建操作对当前工作单元调用 Commit。</span><span class="sxs-lookup"><span data-stu-id="5658a-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="5658a-505">交互测试有一个危险，就是指定交互。</span><span class="sxs-lookup"><span data-stu-id="5658a-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="5658a-506">Mock 对象记录和验证每个与 mock 对象的交互的能力并不意味着测试应尝试验证每个交互。</span><span class="sxs-lookup"><span data-stu-id="5658a-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="5658a-507">某些交互是实现详细信息，只应验证满足当前测试*所需*的交互。</span><span class="sxs-lookup"><span data-stu-id="5658a-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="5658a-508">模拟或 fakes 之间的选择主要取决于你要测试的系统和个人（或团队）首选项。</span><span class="sxs-lookup"><span data-stu-id="5658a-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="5658a-509">Mock 对象可以极大地减少实现测试双精度值所需的代码量，但并非每个人都能得心应手地进行编程和验证交互。</span><span class="sxs-lookup"><span data-stu-id="5658a-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="5658a-510">结论</span><span class="sxs-lookup"><span data-stu-id="5658a-510">Conclusions</span></span>

<span data-ttu-id="5658a-511">在本文中，我们演示了几种创建可测试代码的方法，同时使用 ADO.NET 实体框架来实现数据持久性。</span><span class="sxs-lookup"><span data-stu-id="5658a-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="5658a-512">我们可以利用内置抽象，例如 IObjectSet&lt;T&gt;，或创建自己的抽象，例如 IRepository&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="5658a-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="5658a-513">  在这两种情况下，ADO.NET 中的 POCO 支持实体框架4.0，使这些抽象的使用者能够维持永久性未知和高度可测试性。</span><span class="sxs-lookup"><span data-stu-id="5658a-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="5658a-514">隐式延迟加载等附加 EF4 功能允许业务和应用程序服务代码工作，而无需担心关系数据存储的详细信息。</span><span class="sxs-lookup"><span data-stu-id="5658a-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="5658a-515">最后，我们创建的抽象可以轻松地在单元测试中模拟或伪造，而且我们可以使用这些测试来实现快速运行、高度隔离和可靠的测试。</span><span class="sxs-lookup"><span data-stu-id="5658a-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="5658a-516">其他资源</span><span class="sxs-lookup"><span data-stu-id="5658a-516">Additional Resources</span></span>

-   <span data-ttu-id="5658a-517">Robert，"[单责任原则](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="5658a-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="5658a-518">圣马丁 Fowler，从*企业应用程序体系结构模式*的模式[目录](https://www.martinfowler.com/eaaCatalog/index.html)</span><span class="sxs-lookup"><span data-stu-id="5658a-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="5658a-519">Griffin Caprio，"[依赖关系注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="5658a-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="5658a-520">数据可编程性博客，"[演练：测试驱动开发与实体框架 4.0](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)"。</span><span class="sxs-lookup"><span data-stu-id="5658a-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)”.</span></span>
-   <span data-ttu-id="5658a-521">数据可编程性博客，"[使用存储库和实体框架4.0 的工作单元模式](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)"</span><span class="sxs-lookup"><span data-stu-id="5658a-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)”</span></span>
-   <span data-ttu-id="5658a-522">Aaron Jensen，"[机器规格简介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="5658a-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="5658a-523">Eric 先生，" [BDD With MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"</span><span class="sxs-lookup"><span data-stu-id="5658a-523">Eric Lee, “ [BDD with MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)”</span></span>
-   <span data-ttu-id="5658a-524">Eric Evans，"[域驱动设计](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="5658a-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="5658a-525">圣马丁 Fowler，"[模拟不是存根](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="5658a-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="5658a-526">圣马丁 Fowler，" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="5658a-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="5658a-527">Moq</span><span class="sxs-lookup"><span data-stu-id="5658a-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="5658a-528">事迹</span><span class="sxs-lookup"><span data-stu-id="5658a-528">Biography</span></span>

<span data-ttu-id="5658a-529">Scott Allen 是技术人员在 Pluralsight 和创始人 OdeToCode.com 的成员。</span><span class="sxs-lookup"><span data-stu-id="5658a-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="5658a-530">在15年的商业软件开发中，Scott 已经处理了从8位嵌入式设备到高度可扩展的 ASP.NET web 应用程序的各种解决方案。</span><span class="sxs-lookup"><span data-stu-id="5658a-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="5658a-531">你可以在 OdeToCode 上或在 Twitter 的[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)上联系 Scott。</span><span class="sxs-lookup"><span data-stu-id="5658a-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>

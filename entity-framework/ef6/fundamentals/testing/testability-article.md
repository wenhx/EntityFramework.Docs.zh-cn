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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="ff0e2-102">可测试性和实体框架 4.0</span><span class="sxs-lookup"><span data-stu-id="ff0e2-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="ff0e2-103">斯科特·艾伦</span><span class="sxs-lookup"><span data-stu-id="ff0e2-103">Scott Allen</span></span>

<span data-ttu-id="ff0e2-104">发布时间：2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="ff0e2-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="ff0e2-105">简介</span><span class="sxs-lookup"><span data-stu-id="ff0e2-105">Introduction</span></span>

<span data-ttu-id="ff0e2-106">本白皮书介绍并演示了如何使用 ADO.NET 实体框架 4.0 和 Visual Studio 2010 编写可测试代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="ff0e2-107">本文不尝试专注于特定的测试方法，如测试驱动设计 （TDD） 或行为驱动设计 （BDD）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="ff0e2-108">相反，本文将重点介绍如何使用ADO.NET实体框架编写代码，但仍然易于以自动化方式隔离和测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="ff0e2-109">我们将了解在数据访问方案中简化测试的常见设计模式，并了解如何在使用框架时应用这些模式。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="ff0e2-110">我们还将查看框架的特定功能，了解这些功能如何在可测试代码中工作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="ff0e2-111">什么是可测试代码？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-111">What Is Testable Code?</span></span>

<span data-ttu-id="ff0e2-112">使用自动单元测试验证软件的能力提供了许多理想的好处。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="ff0e2-113">每个人都知道，好的测试将减少应用程序中的软件缺陷数量，并提高应用程序的质量，但单元测试的到位远不止发现错误。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="ff0e2-114">一个好的单元测试套件允许开发团队节省时间并保持对所创建软件的控制。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="ff0e2-115">团队可以对现有代码进行更改、重构、重新设计和重组软件以满足新要求，并在知道测试套件可以验证应用程序的行为的同时，将新组件添加到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="ff0e2-116">单元测试是快速反馈周期的一部分，用于随着复杂性的增加，促进更改并保留软件的可维护性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="ff0e2-117">然而，单元测试是有代价的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="ff0e2-118">团队必须投入时间创建和维护单元测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="ff0e2-119">创建这些测试所需的工作量与基础软件的**可测试性**直接相关。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="ff0e2-120">软件测试有多容易？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-120">How easy is the software to test?</span></span> <span data-ttu-id="ff0e2-121">设计具有可测试性的软件的团队将比使用不可测试软件的团队更快地创建有效的测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="ff0e2-122">Microsoft 设计了ADO.NET实体框架 4.0 （EF4）， 并考虑到可测试性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="ff0e2-123">这并不意味着开发人员将针对框架代码本身编写单元测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="ff0e2-124">相反，EF4 的可测试性目标便于创建基于框架的可测试代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="ff0e2-125">在查看特定示例之前，了解可测试代码的质量是值得的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="ff0e2-126">可测试代码的质量</span><span class="sxs-lookup"><span data-stu-id="ff0e2-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="ff0e2-127">易于测试的代码始终表现出至少两个特征。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="ff0e2-128">首先，可测试代码易于**观察**。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="ff0e2-129">给定一组输入，应很容易观察代码的输出。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="ff0e2-130">例如，测试以下方法非常简单，因为该方法直接返回计算结果。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="ff0e2-131">如果方法将计算的值写入网络套接字、数据库表或文件（如以下代码）中，则测试方法非常困难。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="ff0e2-132">测试必须执行其他工作才能检索该值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="ff0e2-133">其次，可测试代码易于**隔离**。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="ff0e2-134">让我们使用以下伪代码作为可测试代码的坏示例。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="ff0e2-135">该方法易于观察 – 我们可以在保险单中传递并验证返回价值与预期结果匹配。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="ff0e2-136">但是，要测试该方法，我们需要使用正确的架构安装数据库，并配置 SMTP 服务器，以防该方法尝试发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="ff0e2-137">单元测试只想验证方法内的计算逻辑，但测试可能会失败，因为电子邮件服务器处于脱机状态，或者因为数据库服务器移动。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="ff0e2-138">这两个失败都与测试要验证的行为无关。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="ff0e2-139">该行为难以隔离。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="ff0e2-140">努力编写可测试代码的软件开发人员通常努力在编写的代码中保持关注点的分离。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="ff0e2-141">上述方法应侧重于业务计算，并将数据库和电子邮件实现详细信息委派给其他组件。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="ff0e2-142">罗伯特·马丁称之为单一责任原则。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="ff0e2-143">对象应封装单个窄责任，例如计算策略的值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="ff0e2-144">所有其他数据库和通知工作应由其他对象负责。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="ff0e2-145">以这种方式编写的代码更易于隔离，因为它侧重于单个任务。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="ff0e2-146">在 .NET 中，我们有遵循单一责任原则并实现隔离所需的抽象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="ff0e2-147">我们可以使用接口定义，并强制代码使用接口抽象而不是具体类型。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="ff0e2-148">在本文的后面部分，我们将了解类似上面介绍的坏示例这样的方法如何使用*看起来*他们将与数据库对话的接口。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="ff0e2-149">但是，在测试时，我们可以替换不与数据库对话但将数据存储在内存中的虚拟实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="ff0e2-150">此虚拟实现将隔离代码与数据访问代码或数据库配置中的不相关问题。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="ff0e2-151">隔离还有其他好处。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="ff0e2-152">最后一种方法中的业务计算只需几毫秒即可执行，但测试本身可能会运行几秒钟，因为代码在网络中跳跃并与各种服务器进行对话。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="ff0e2-153">单元测试应快速运行，以方便进行小更改。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="ff0e2-154">单元测试也应是可重复的，而不是失败的，因为与测试无关的组件有问题。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="ff0e2-155">编写易于观察和隔离的代码意味着开发人员将更容易地为代码编写测试，花更少的时间等待测试执行，更重要的是，花费更少的时间来跟踪不存在的 Bug。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="ff0e2-156">希望您能够理解测试的好处，并了解可测试代码所展示的品质。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="ff0e2-157">我们将讨论如何编写与 EF4 配合使用的代码，将数据保存到数据库中，同时保持可观察且易于隔离，但首先，我们将缩小重点，以讨论数据访问的可测试设计。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="ff0e2-158">数据持久性的设计模式</span><span class="sxs-lookup"><span data-stu-id="ff0e2-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="ff0e2-159">前面提出的两个不好的例子都有太多的责任。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="ff0e2-160">第一个坏示例必须执行计算*并*写入文件。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="ff0e2-161">第二个坏例子必须从数据库中读取数据 *，并*执行业务计算*并*发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="ff0e2-162">通过设计更小的方法，将关注点分离并委派给其他组件，您将在编写可测试代码方面取得重大进展。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="ff0e2-163">目标是通过从小型抽象和重点抽象中组合操作来构建功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="ff0e2-164">在数据持久性方面，我们正在寻找的小型且重点突出的抽象非常普遍，已记录为设计模式。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="ff0e2-165">Martin Fowler 的著作《企业应用程序体系结构模式》是描述这些模式的第一部著作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="ff0e2-166">我们将在以下各节中简要介绍这些模式，然后介绍这些ADO.NET实体框架如何实现和使用这些模式。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="ff0e2-167">存储库模式</span><span class="sxs-lookup"><span data-stu-id="ff0e2-167">The Repository Pattern</span></span>

<span data-ttu-id="ff0e2-168">福勒说，一个存储库"使用类似集合的接口访问域对象，在域和数据映射层之间进行中介"。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="ff0e2-169">存储库模式的目标是将代码与数据访问的细枝末节隔离开来，正如我们之前看到的，隔离是可测试性的必要特征。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="ff0e2-170">隔离的关键是存储库如何使用类似集合的接口公开对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="ff0e2-171">您编写用于使用存储库的逻辑不知道存储库将如何实现请求的对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="ff0e2-172">存储库可能与数据库对话，或者可能只是从内存中集合返回对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="ff0e2-173">所有代码需要知道的是，存储库似乎维护集合，并且可以从集合中检索、添加和删除对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="ff0e2-174">在现有 .NET 应用程序中，具体存储库通常从通用接口继承，如下所示：</span><span class="sxs-lookup"><span data-stu-id="ff0e2-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="ff0e2-175">在为 EF4 提供实现时，我们将对接口定义进行一些更改，但基本概念保持不变。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="ff0e2-176">代码可以使用实现此接口的具体存储库按其主键值检索实体，基于谓词的评估检索实体集合，或者仅检索所有可用实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="ff0e2-177">代码还可以通过存储库接口添加和删除实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="ff0e2-178">给定"员工"对象的 IRepository，代码可以执行以下操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="ff0e2-179">由于代码使用接口（员工 IRepository），我们可以为代码提供接口的不同实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="ff0e2-180">一个实现可能是由 EF4 支持的实现，并将对象持久化到 Microsoft SQL Server 数据库中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="ff0e2-181">不同的实现（我们在测试期间使用的实现）可能由内存中的员工对象列表支持。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="ff0e2-182">该接口将有助于在代码中实现隔离。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="ff0e2-183">请注意，IRepository&lt;&gt; T 接口不会公开"保存"操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="ff0e2-184">我们如何更新现有对象？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-184">How do we update existing objects?</span></span> <span data-ttu-id="ff0e2-185">您可能会遇到包含 Save 操作的 IRepository 定义，这些存储库的实现需要立即将对象保存到数据库中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="ff0e2-186">但是，在许多应用程序中，我们不想单独保留对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="ff0e2-187">相反，我们希望将对象（可能来自不同的存储库）带入生活，将这些对象修改为业务活动的一部分，然后将所有对象保留为单个原子操作的一部分。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="ff0e2-188">幸运的是，有一个模式允许这种类型的行为。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="ff0e2-189">工作单位模式</span><span class="sxs-lookup"><span data-stu-id="ff0e2-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="ff0e2-190">福勒说，一个工作单元将"维护受业务交易影响的对象列表，并协调更改的写入和并发问题的解决"。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="ff0e2-191">工作单位有责任跟踪我们从存储库中引入的对象的更改，并在告诉工作单位提交更改时保留我们对对象所做的任何更改。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="ff0e2-192">工作单元也有责任将我们添加到所有存储库的新对象并插入数据库，以及进行删除。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="ff0e2-193">如果您曾经做过ADO.NET数据集的任何工作，那么你已经熟悉工作模式的单位。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="ff0e2-194">ADO.NET DataSet 能够跟踪 DataRow 对象的更新、删除和插入，并且可以（在表适配器的帮助下）协调我们对数据库的所有更改。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="ff0e2-195">但是，DataSet 对象对基础数据库的断开连接子集建模。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="ff0e2-196">工作模式单元表现出相同的行为，但适用于与数据访问代码隔离且不知道数据库的业务对象和域对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="ff0e2-197">对 .NET 代码中工作单元建模的抽象可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="ff0e2-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="ff0e2-198">通过公开来自工作单元的存储库引用，我们可以确保单个工作单元的对象能够跟踪业务事务期间实现的所有实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="ff0e2-199">实际工作单元的 Commit 方法的实现是所有魔术都恰好在内存中更改与数据库协调的地方。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="ff0e2-200">给定 IUnitOfWork 引用，代码可以更改从一个或多个存储库检索的业务对象，并使用原子提交操作保存所有更改。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="ff0e2-201">延迟加载模式</span><span class="sxs-lookup"><span data-stu-id="ff0e2-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="ff0e2-202">Fowler 使用名称延迟负载来描述"一个不包含所需所有数据但知道如何获取数据的对象"。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="ff0e2-203">透明延迟加载是编写可测试业务代码和使用关系数据库时需要具备的重要功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="ff0e2-204">例如，请考虑以下代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="ff0e2-205">如何填充时间卡集合？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="ff0e2-206">有两个可能的答案。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-206">There are two possible answers.</span></span> <span data-ttu-id="ff0e2-207">一个答案是，当员工存储库被要求获取员工时，会发出查询以检索员工以及员工关联的时卡信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="ff0e2-208">在关系数据库中，这通常需要使用 JOIN 子句的查询，并且可能导致检索比应用程序需要的信息更多。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="ff0e2-209">如果应用程序不需要触摸 TimeCard 属性，该怎么办？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="ff0e2-210">第二个答案是"按需"加载 TimeCard 属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="ff0e2-211">此延迟加载对业务逻辑是隐式的和透明的，因为代码不会调用特殊的 API 来检索时卡信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="ff0e2-212">代码假定时间卡信息在需要时存在。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="ff0e2-213">延迟加载涉及一些魔术，通常涉及方法调用的运行时拦截。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="ff0e2-214">拦截代码负责与数据库交谈和检索时卡信息，同时使业务逻辑自由成为业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="ff0e2-215">这种惰性加载魔力允许业务代码将自己与数据检索操作隔离开来，并生成更可测试的代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="ff0e2-216">延迟加载的缺点是，当应用程序*确实需要*时卡信息时，代码将执行其他查询。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="ff0e2-217">这不是许多应用程序的问题，但对于性能敏感的应用程序或应用程序循环访问多个员工对象和执行查询以在循环的每次迭代期间检索时间卡（通常称为 N+1 查询问题），延迟加载是一种阻力。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="ff0e2-218">在这些情况下，应用程序可能希望以最有效的方式紧急加载时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="ff0e2-219">幸运的是，我们将了解 EF4 如何支持隐式惰性负载和高效的热要负载，同时进入下一节并实现这些模式。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="ff0e2-220">使用实体框架实现模式</span><span class="sxs-lookup"><span data-stu-id="ff0e2-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="ff0e2-221">好消息是，我们在最后一节中介绍的所有设计模式都非常简单地使用 EF4 实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="ff0e2-222">为了演示我们将使用简单的ASP.NET MVC 应用程序来编辑和显示员工及其关联的时卡信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="ff0e2-223">我们将首先使用以下"普通旧 CLR 对象"（POCO）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="ff0e2-224">当我们探索 EF4 的不同方法和功能时，这些类定义将略有变化，但目的是尽可能将这些类保留为持久性无知 （PI）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="ff0e2-225">PI*对象不知道如何，\*\*甚至即使它*保持的状态生活在数据库中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="ff0e2-226">PI 和 POCO 与可测试软件齐头并进。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="ff0e2-227">使用 POCO 方法的对象约束较小、更灵活且更易于测试，因为它们可以在没有数据库存在的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="ff0e2-228">在 POCO 到位后，我们可以在可视化工作室中创建实体数据模型 （EDM），参见图 1）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="ff0e2-229">我们不会使用 EDM 为我们的实体生成代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="ff0e2-230">相反，我们希望使用我们精心打造的实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="ff0e2-231">我们只会使用 EDM 生成数据库架构并提供将对象映射到数据库所需的元数据 EF4。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![埃夫·test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="ff0e2-233">**图 1**</span><span class="sxs-lookup"><span data-stu-id="ff0e2-233">**Figure 1**</span></span>

<span data-ttu-id="ff0e2-234">注意：如果要首先开发 EDM 模型，则可以从 EDM 生成干净的 POCO 代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="ff0e2-235">您可以使用数据可编程团队提供的 Visual Studio 2010 扩展来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="ff0e2-236">要下载扩展，请从 Visual Studio 中的"工具"菜单启动扩展管理器，并搜索"POCO"模板的在线库（见图 2）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="ff0e2-237">有几种 POCO 模板可用于 EF。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="ff0e2-238">有关使用模板的详细信息，请参阅"[演练：实体框架的 POCO 模板](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)"。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![埃夫test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="ff0e2-240">**图 2**</span><span class="sxs-lookup"><span data-stu-id="ff0e2-240">**Figure 2**</span></span>

<span data-ttu-id="ff0e2-241">从这个 POCO 起点，我们将探讨两种不同的可测试代码方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="ff0e2-242">我称之为 EF 方法的第一种方法是因为它利用实体框架 API 中的抽象来实现工作单元和存储库。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="ff0e2-243">在第二种方法中，我们将创建自己的自定义存储库抽象，然后了解每种方法的优缺点。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="ff0e2-244">我们将首先探索 EF 方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="ff0e2-245">以 EF 为中心的实施</span><span class="sxs-lookup"><span data-stu-id="ff0e2-245">An EF Centric Implementation</span></span>

<span data-ttu-id="ff0e2-246">请考虑 ASP.NET MVC 项目中的以下控制器操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="ff0e2-247">该操作检索"员工"对象并返回结果以显示员工的详细视图。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="ff0e2-248">代码是可测试的吗？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-248">Is the code testable?</span></span> <span data-ttu-id="ff0e2-249">我们至少需要测试两个来验证操作的行为。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="ff0e2-250">首先，我们要验证操作返回正确的视图 - 一个简单的测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="ff0e2-251">我们还希望编写一个测试来验证操作检索正确的员工，并且我们希望在不执行代码来查询数据库的情况下执行此操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="ff0e2-252">请记住，我们要隔离被测的代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="ff0e2-253">隔离将确保测试不会因为数据访问代码或数据库配置中的 Bug 而失败。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="ff0e2-254">如果测试失败，我们将知道控制器逻辑中存在 Bug，而不是某些较低级别的系统组件中的错误。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="ff0e2-255">为了实现隔离，我们需要一些抽象，例如我们前面为存储库和工作单元提供的接口。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="ff0e2-256">请记住，存储库模式旨在在域对象和数据映射层之间进行中介。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="ff0e2-257">在这种情况下，EF4*是*数据映射层，并且已经提供了一个名为 IObjectSet&lt;T（&gt;来自 System.Data.object 命名空间）的类似存储库的抽象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="ff0e2-258">接口定义如下所示。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="ff0e2-259">IObjectSet&lt;&gt; T 满足存储库的要求，因为它类似于对象集合（通过&lt;&gt;IE55t），并提供从模拟集合中添加和删除对象的方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="ff0e2-260">附加和分离方法公开 EF4 API 的其他功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="ff0e2-261">要使用&lt;IObjectSet&gt; T 作为存储库的接口，我们需要一个工作抽象单元来将存储库绑定在一起。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="ff0e2-262">此接口的一个具体实现将与 SQL Server 对话，并且使用 EF4 中的 ObjectContext 类轻松创建。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="ff0e2-263">ObjectContext 类是 EF4 API 中的实际工作单元。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="ff0e2-264">将 IObjectSet&lt;&gt; T 变为生命就像调用对象上下文对象的 CreateObjectSet 方法一样简单。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="ff0e2-265">在幕后，框架将使用我们在 EDM 中提供的元数据来生成具体的 ObjectSet&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="ff0e2-266">我们将坚持返回 IObjectSet&lt;T&gt;接口，因为它将有助于在客户端代码中保留可测试性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="ff0e2-267">此具体实现在生产中非常有用，但我们需要关注如何使用 IUnitOfWork 抽象来简化测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="ff0e2-268">测试双精度值</span><span class="sxs-lookup"><span data-stu-id="ff0e2-268">The Test Doubles</span></span>

<span data-ttu-id="ff0e2-269">要隔离控制器操作，我们需要能够在实际工作单元（由 ObjectContext 支持）和测试双精度或"假"工作单元（执行内存中操作）之间切换。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="ff0e2-270">执行此类切换的常见方法是不要让 MVC 控制器实例化工作单元，而是将工作单元作为构造函数参数传递到控制器中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="ff0e2-271">上述代码是依赖项注入的示例。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="ff0e2-272">我们不允许控制器创建其依赖项（工作单位），但将依赖项注入控制器。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="ff0e2-273">在 MVC 项目中，通常使用自定义控制器工厂与控制反转 （IoC） 容器一起使用，以自动执行依赖项注入。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="ff0e2-274">这些主题超出了本文的范围，但您可以通过关注本文末尾的引用来阅读更多内容。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="ff0e2-275">我们可以用于测试的假工作实现单元可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="ff0e2-276">请注意，假工作单元会公开已提交的属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="ff0e2-277">有时将功能添加到便于测试的假类中非常有用。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="ff0e2-278">在这种情况下，通过检查"提交"属性，很容易观察代码是否提交工作单元。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="ff0e2-279">我们还需要一个假的 IObjectSet&lt;&gt; T 来将员工和 TimeCard 对象保留在内存中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="ff0e2-280">我们可以使用泛型提供单个实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="ff0e2-281">此测试将其大部分工作双重委托给基础哈希集&lt;T&gt;对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="ff0e2-282">请注意，IObjectSet&lt;&gt; T 需要一个泛型约束，强制 T 作为类（引用类型），并且还强制我们&lt;实现&gt;IQuery T 。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="ff0e2-283">使用标准 LINQ 运算符 AsQuery 使内存中集合显示为&lt;&gt; IQuery T 是很容易的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="ff0e2-284">测试</span><span class="sxs-lookup"><span data-stu-id="ff0e2-284">The Tests</span></span>

<span data-ttu-id="ff0e2-285">传统的单元测试将使用单个测试类来保存单个 MVC 控制器中所有操作的所有测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="ff0e2-286">我们可以使用我们构建的内存中的假，编写这些测试或任何类型的单元测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="ff0e2-287">但是，在本文中，我们将避免采用单片式测试类方法，而是将测试分组以专注于特定的功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="ff0e2-288">例如，"创建新员工"可能是我们想要测试的功能，因此我们将使用单个测试类来验证负责创建新员工的单个控制器操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="ff0e2-289">对于所有这些细粒度测试类，我们需要一些常见的设置代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="ff0e2-290">例如，我们总是需要创建内存中存储库和假工作单元。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="ff0e2-291">我们还需要一个员工控制器的实例，并注入假的工作单元。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="ff0e2-292">我们将使用基类跨测试类共享此公共设置代码。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="ff0e2-293">我们在基类中使用的"对象母体"是创建测试数据的一种常见模式。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="ff0e2-294">对象母体包含用于实例化测试实体的工厂方法，用于跨多个测试夹具使用。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="ff0e2-295">我们可以将员工控制器测试Base作为许多测试夹具的基础类（见图 3）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="ff0e2-296">每个测试夹具将测试特定的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="ff0e2-297">例如，一个测试夹具将侧重于测试 HTTP GET 请求期间使用的"创建"操作（以显示创建员工的视图），其他固件将侧重于 HTTP POST 请求中使用的"创建"操作（以获取用户提交的信息以创建员工）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="ff0e2-298">每个派生类只负责其特定上下文中所需的设置，并提供验证其特定测试上下文结果所需的断言。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![埃夫·test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="ff0e2-300">**图 3**</span><span class="sxs-lookup"><span data-stu-id="ff0e2-300">**Figure 3**</span></span>

<span data-ttu-id="ff0e2-301">此处介绍的命名约定和测试样式对于可测试代码来说并不是必需的，这只是一种方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="ff0e2-302">图 4 显示了 Visual Studio 2010 的 Jet Brains Resharper 测试运行插件中运行的测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![埃夫test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="ff0e2-304">**图 4**</span><span class="sxs-lookup"><span data-stu-id="ff0e2-304">**Figure 4**</span></span>

<span data-ttu-id="ff0e2-305">使用处理共享设置代码的基类时，每个控制器操作的单位测试都很小且易于编写。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="ff0e2-306">测试将快速执行（因为我们正在执行内存中操作），并且不应因为不相关的基础结构或环境问题而失败（因为我们隔离了被测单元）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="ff0e2-307">在这些测试中，基类执行大多数设置工作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="ff0e2-308">请记住，基类构造函数创建内存中存储库、假工作单元和 EmployController 类的实例。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="ff0e2-309">测试类派生自此基类，并侧重于测试 Create 方法的具体细节。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="ff0e2-310">在这种情况下，具体情况可归结为您将在任何单元测试过程中看到的"安排、操作和断言"步骤：</span><span class="sxs-lookup"><span data-stu-id="ff0e2-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="ff0e2-311">创建新的"员工"对象以模拟传入数据。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="ff0e2-312">调用"创建员工控制程序"的操作并传递给新员工。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="ff0e2-313">验证"创建"操作生成预期结果（员工显示在存储库中）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="ff0e2-314">我们构建的内容允许我们测试任何员工控制器操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="ff0e2-315">例如，当我们为"员工"控制器的 Index 操作编写测试时，我们可以从测试基类继承，为测试建立相同的基本设置。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="ff0e2-316">再次，基类将创建内存中存储库、假工作单元和员工控制器的实例。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="ff0e2-317">索引操作的测试只需要专注于调用 Index 操作并测试操作返回的模型的质量。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="ff0e2-318">我们使用内存中假体创建测试面向测试软件*的状态*。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="ff0e2-319">例如，在测试"创建"操作时，我们希望在创建操作执行后检查存储库的状态 - 存储库是否留住新员工？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="ff0e2-320">稍后我们将介绍基于交互的测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="ff0e2-321">基于交互的测试将询问被测代码是否在我们的对象上调用了正确的方法并传递了正确的参数。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="ff0e2-322">现在，我们将在封面上移动另一种设计模式 - 延迟负载。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="ff0e2-323">热装和延迟加载</span><span class="sxs-lookup"><span data-stu-id="ff0e2-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="ff0e2-324">在 mVC Web 应用程序ASP.NET的某个时刻，我们可能希望显示员工的信息，并包括员工的相关时间卡。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="ff0e2-325">例如，我们可能有一个时间卡摘要显示，显示员工的姓名和系统中的考勤卡总数。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="ff0e2-326">我们可以采取几种方法来实现此功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="ff0e2-327">投影</span><span class="sxs-lookup"><span data-stu-id="ff0e2-327">Projection</span></span>

<span data-ttu-id="ff0e2-328">创建摘要的一种简单方法是构建一个模型，专门用于要在视图中显示的信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="ff0e2-329">在这种情况下，模型可能如下所示。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="ff0e2-330">请注意，员工摘要视图模型不是实体 ，换句话说，它不是我们想要保留在数据库中的内容。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="ff0e2-331">我们只会使用此类以强类型方式将数据随机排列到视图中。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="ff0e2-332">视图模型类似于数据传输对象 （DTO），因为它不包含任何行为（没有方法） - 仅包含属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="ff0e2-333">属性将保存我们需要移动的数据。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="ff0e2-334">使用 LINQ 的标准投影运算符 - Select 运算符，可以轻松实例化此视图模型。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="ff0e2-335">上述代码有两个值得注意的功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-335">There are two notable features to the above code.</span></span> <span data-ttu-id="ff0e2-336">首先 ， 代码很容易测试， 因为它仍然易于观察和隔离.</span><span class="sxs-lookup"><span data-stu-id="ff0e2-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="ff0e2-337">Select 运算符与针对实际工作单元的内存假一样出色。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="ff0e2-338">第二个值得注意的特征是，代码如何允许 EF4 生成单个高效的查询，以组合员工和时卡信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="ff0e2-339">我们将员工信息和工时卡信息加载到同一对象中，而无需使用任何特殊的 API。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="ff0e2-340">代码仅表示它使用标准 LINQ 运算符对内存中数据源和远程数据源执行所需的信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="ff0e2-341">EF4 能够将 LINQ 查询和 C\#编译器生成的表达式树转换为单个高效的 T-SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="ff0e2-342">有时，我们不想使用视图模型或 DTO 对象，而是使用实际实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="ff0e2-343">当我们知道我们需要员工*和员工*的时间卡时，我们可以以不显眼和高效的方式急切地加载相关数据。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="ff0e2-344">显式渴望加载</span><span class="sxs-lookup"><span data-stu-id="ff0e2-344">Explicit Eager Loading</span></span>

<span data-ttu-id="ff0e2-345">当我们想要急切地加载相关的实体信息时，我们需要一些业务逻辑机制（或在这种情况下，控制器操作逻辑）来表达对存储库的需求。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="ff0e2-346">EF4 对象查询&lt;&gt; T 类定义一个 Include 方法，以指定在查询期间要检索的相关对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="ff0e2-347">请记住 EF4 对象上下文通过&lt;从对象查询&gt;&lt;T&gt;继承的具体 ObjectSet T 类公开实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="ff0e2-348">如果我们在控制器操作中使用&lt;ObjectSet T&gt;引用，我们可以编写以下代码来为每个员工指定热切的时卡信息负载。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="ff0e2-349">但是，由于我们试图保持代码的可测试性，因此我们不会在工作类的实际单元&lt;之外&gt;公开 ObjectSet T。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="ff0e2-350">相反，我们&lt;依赖于 IObjectSet T&gt;接口，该接口更易于伪造，但 IObjectSet&lt;T&gt;不定义 Include 方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="ff0e2-351">LINQ 的优点是我们可以创建自己的"包括"运算符。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="ff0e2-352">请注意，此包含运算符定义为&lt;I可查询 T&gt;而不是 IObjectSet&lt;T&gt;的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="ff0e2-353">这使我们能够将该方法用于更广泛的可能类型，包括 IQuery&lt;T、IObjectSet&gt;&lt;T、ObjectQuery&gt;&lt;T&gt;和 Objectset&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="ff0e2-354">如果基础序列不是真正的 EF4 对象查询&lt;T，&gt;则不会造成任何伤害，并且 Include 运算符是 no-op。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="ff0e2-355">如果基础序列*是*&lt;ObjectQuery T（&gt;或从 ObjectQuery&lt;&gt;T 派生），则 EF4 将看到我们对其他数据的要求，并制定正确的 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="ff0e2-356">有了这个新的运算符，我们可以显式要求从存储库中加载时间卡信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="ff0e2-357">当对实际对象上下文运行时，代码将生成以下单个查询。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="ff0e2-358">查询在一次行程中从数据库中收集足够的信息，以实现员工对象并完全填充其 TimeCard 属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="ff0e2-359">好消息是操作方法内的代码仍然完全可测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="ff0e2-360">我们不需要为我们的假货提供任何其他功能来支持 Include 运算符。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="ff0e2-361">坏消息是，我们必须在代码内使用 Include 运算符，以保持持久性无知。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="ff0e2-362">这是构建可测试代码时需要评估的权衡类型的示例。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="ff0e2-363">有时，您需要让持久性问题泄漏到存储库抽象之外，以满足性能目标。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="ff0e2-364">热装的替代是延迟加载。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="ff0e2-365">延迟加载意味着我们不需要业务*not*代码来显式声明关联数据的要求。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="ff0e2-366">相反，我们在应用程序中使用我们的实体，如果需要其他数据，实体框架将按需加载数据。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="ff0e2-367">延迟加载</span><span class="sxs-lookup"><span data-stu-id="ff0e2-367">Lazy Loading</span></span>

<span data-ttu-id="ff0e2-368">很容易想象一个场景，即我们不知道业务逻辑需要哪些数据。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="ff0e2-369">我们可能知道逻辑需要员工对象，但我们可能分支到不同的执行路径，其中某些路径需要员工提供时卡信息，而有些路径不需要。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="ff0e2-370">像这样的方案非常适合隐式延迟加载，因为数据会根据需要神奇地显示。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="ff0e2-371">延迟加载（也称为延迟加载）确实对我们的实体对象提出了一些要求。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="ff0e2-372">具有真正持久性无知的 POCO 不会面临来自持久性层的任何要求，但真正的持久性无知几乎不可能实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="ff0e2-373">相反，我们测量相对程度的持久性无知。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="ff0e2-374">如果我们需要从面向持久性的基类继承或使用专用集合在 POCO 中实现延迟加载，那将是不幸的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="ff0e2-375">幸运的是，EF4 具有侵入性较低的解决方案。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="ff0e2-376">几乎检测不到</span><span class="sxs-lookup"><span data-stu-id="ff0e2-376">Virtually Undetectable</span></span>

<span data-ttu-id="ff0e2-377">使用 POCO 对象时，EF4 可以动态生成实体的运行时代理。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="ff0e2-378">这些代理无形地包装了具体化的 POCO，并通过拦截每个属性获取和设置操作来执行其他工作来提供其他服务。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="ff0e2-379">其中一项服务是我们要查找的延迟加载功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="ff0e2-380">另一个服务是一种有效的更改跟踪机制，它可以记录程序何时更改实体的属性值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="ff0e2-381">在保存更改方法期间，ObjectContext 使用更改列表来保留使用 UPDATE 命令的任何已修改实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="ff0e2-382">但是，要使这些代理正常工作，他们需要一种方法来挂钩到实体上获取和设置操作，而代理通过重写虚拟成员来实现此目标。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="ff0e2-383">因此，如果我们想要隐式延迟加载和高效的更改跟踪，我们需要回到 POCO 类定义并将属性标记为虚拟。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="ff0e2-384">我们仍然可以说，员工实体大多是坚持无知。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="ff0e2-385">唯一的要求是使用虚拟成员，这不会影响代码的可测试性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="ff0e2-386">我们不需要从任何特殊的基类派生，甚至不需要使用专用于延迟加载的特殊集合。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="ff0e2-387">如代码所示，任何实现 ICollection&lt;T&gt;的类都可以保留相关实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="ff0e2-388">在我们的工作单元内，我们还需要进行一些小的变化。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="ff0e2-389">默认情况下，当直接使用 ObjectContext 对象时，延迟加载*处于关闭状态*。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="ff0e2-390">我们可以在 ContextOptions 属性上设置一个属性，以启用延迟加载，如果我们想要在任何地方启用延迟加载，我们可以在实际工作单元中设置此属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="ff0e2-391">启用隐式延迟加载后，应用程序代码可以使用员工和员工关联的时间卡，同时对 EF 加载额外数据所需的工作一无所知。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="ff0e2-392">延迟加载使应用程序代码更易于编写，并且使用代理魔法，代码保持完全可测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="ff0e2-393">工作单元的内存中伪造可以简单地在测试期间在需要时预加载带有相关数据的假实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="ff0e2-394">此时，我们将从使用 IObjectSet&lt;T&gt;构建存储库转向我们的注意力，并查看抽象来隐藏持久性框架的所有迹象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="ff0e2-395">自定义存储库</span><span class="sxs-lookup"><span data-stu-id="ff0e2-395">Custom Repositories</span></span>

<span data-ttu-id="ff0e2-396">当我们在本文中首次介绍工作设计单元模式时，我们提供了一些示例代码，说明工作单元的外观。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="ff0e2-397">让我们使用我们一直在处理的员工和员工时卡方案重新呈现这一原始想法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="ff0e2-398">此工作单元与我们在上一节中创建的工作单元之间的主要区别是此工作单元不使用 EF4 框架中的任何抽象（没有 IObjectSet&lt;T）。&gt;</span><span class="sxs-lookup"><span data-stu-id="ff0e2-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="ff0e2-399">IObjectSet&lt;&gt; T 作为存储库接口工作良好，但它公开的 API 可能不能完全与我们的应用程序需求一致。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="ff0e2-400">在即将采用的方法中，我们将使用自定义 IRepository&lt;T&gt;抽象表示存储库。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="ff0e2-401">许多遵循测试驱动设计、行为驱动设计和域驱动方法设计的开发人员都更喜欢 IRepository&lt;T&gt;方法，原因有多种。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="ff0e2-402">首先，IRepository&lt;T&gt;接口表示"反腐败"层。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="ff0e2-403">正如 Eric Evans 在他的域驱动设计书中描述的那样，反腐败层使域代码远离基础结构 API，如持久性 API。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="ff0e2-404">其次，开发人员可以将方法构建到存储库中，以满足应用程序的确切需求（如编写测试时发现的那样）。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="ff0e2-405">例如，我们可能需要使用 ID 值查找单个实体，以便我们可以将 FindById 方法添加到存储库接口。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="ff0e2-406">我们的&lt;IRepository&gt; T 定义如下所示。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="ff0e2-407">请注意，我们将回到使用 IQuery&lt;T&gt;接口来公开实体集合。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="ff0e2-408">IQueryT&lt;&gt;允许 LINQ 表达式树流入 EF4 提供程序，并赋予提供程序查询的整体视图。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="ff0e2-409">第二个选项是返回 IE55t&lt;&gt;T ，这意味着 EF4 LINQ 提供程序将只看到存储库内生成的表达式。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="ff0e2-410">在存储库之外完成的任何分组、排序和投影将不会组成发送到数据库的 SQL 命令，这可能会损害性能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="ff0e2-411">另一方面，仅返回 IE550t&lt;&gt;结果的存储库永远不会让您使用新的 SQL 命令感到惊讶。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="ff0e2-412">这两种方法都工作，并且两种方法都是可测试的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="ff0e2-413">使用泛型和 EF4 ObjectContext API&lt;提供&gt;IRepository T 接口的单个实现非常简单。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="ff0e2-414">IRepository&lt;T&gt;方法为我们提供了对查询的一些附加控制，因为客户端必须调用方法才能访问实体。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="ff0e2-415">在该方法内，我们可以提供额外的检查和LINQ运算符来强制执行应用程序约束。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="ff0e2-416">请注意，接口对泛型类型参数有两个约束。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="ff0e2-417">第一个约束是 ObjectSet&lt;T&gt;所需的类缺点，第二个约束强制我们的实体实现 IEntity - 为应用程序创建的抽象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="ff0e2-418">IEntity 接口强制实体具有可读 Id 属性，然后我们可以在 FindById 方法中使用此属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="ff0e2-419">IEntity 使用以下代码定义。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="ff0e2-420">IEntity 可被视为对持久性无知的轻微违反，因为我们的实体需要实现此接口。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="ff0e2-421">请记住，持久性无知是关于权衡的，对于许多 FindById 功能来说，它将超过接口施加的约束。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="ff0e2-422">该接口对可测试性没有影响。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="ff0e2-423">实例化实时 IRepository&lt;T&gt;需要 EF4 对象上下文，因此具体的工作实现单元应管理实例化。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="ff0e2-424">使用自定义存储库</span><span class="sxs-lookup"><span data-stu-id="ff0e2-424">Using the Custom Repository</span></span>

<span data-ttu-id="ff0e2-425">使用我们的自定义存储库与使用基于 IObjectSet&lt;T&gt;的存储库没有显著差异。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="ff0e2-426">我们首先需要调用存储库的方法来获取 IQuery T&lt;&gt;引用，而不是将 LINQ 运算符直接应用于属性。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="ff0e2-427">请注意，我们以前实现的自定义"包括"运算符将工作而不进行更改。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="ff0e2-428">存储库的 FindById 方法从尝试检索单个实体的操作中删除重复的逻辑。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="ff0e2-429">我们研究过的两种方法的可测试性没有显著差异。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="ff0e2-430">我们可以通过&lt;构建由 HashSet&gt;&lt;员工&gt;支持的混凝土类来提供 IRepository T 的虚假实现-就像我们在上一节中所做的那样。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="ff0e2-431">但是，一些开发人员更喜欢使用模拟对象和模拟对象框架，而不是构建假物。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="ff0e2-432">我们将在下一节中介绍使用模拟来测试我们的实现，并讨论模拟和假之间的差异。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="ff0e2-433">使用模拟测试</span><span class="sxs-lookup"><span data-stu-id="ff0e2-433">Testing with Mocks</span></span>

<span data-ttu-id="ff0e2-434">构建马丁·福勒所谓的"双测试双"的方法不同。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="ff0e2-435">测试双精度（如电影特技双）是您在测试期间为实际生产对象构建为"站立"的对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="ff0e2-436">我们创建的内存内存储库是与 SQL Server 对话的存储库的测试双精度值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="ff0e2-437">我们已经看到如何在单元测试期间使用这些测试双精度值来隔离代码并保持测试快速运行。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="ff0e2-438">我们构建的测试双精度值具有实际的工作实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="ff0e2-439">在后台，每个对象都存储一个具体的对象集合，并且在测试期间操作存储库时，它们将从此集合中添加和删除对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="ff0e2-440">一些开发人员喜欢用实际代码和工作实现来构建测试双精度值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="ff0e2-441">这些测试双号是我们称之为*假货*的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="ff0e2-442">它们具有工作实现，但它们不够真实，可用于生产。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="ff0e2-443">假存储库实际上不会写入数据库。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="ff0e2-444">假 SMTP 服务器实际上不会通过网络发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="ff0e2-445">模拟与假象</span><span class="sxs-lookup"><span data-stu-id="ff0e2-445">Mocks versus Fakes</span></span>

<span data-ttu-id="ff0e2-446">还有另一种类型的测试双称为*模拟*。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="ff0e2-447">虽然假有工作实现，但模拟没有实现。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="ff0e2-448">在模拟对象框架的帮助下，我们在运行时构造这些模拟对象，并将其用作测试双精度值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="ff0e2-449">在本节中，我们将使用开源模拟框架 Moq。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="ff0e2-450">下面是使用 Moq 动态为员工存储库创建测试双精度值的简单示例。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="ff0e2-451">我们要求 Moq 提供&lt;IRepository 员工&gt;实现，并动态构建一个。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="ff0e2-452">通过&lt;访问 Mock&gt; &lt;T&gt;对象的 Object 属性，我们可以访问实现 IRepository 员工的对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="ff0e2-453">它是这个内部对象，我们可以传递到我们的控制器，他们不知道这是一个测试双或真正的存储库。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="ff0e2-454">我们可以调用对象上的方法，就像在具有实际实现的对象上调用方法一样。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="ff0e2-455">您必须想知道模拟存储库在调用 Add 方法时将执行什么操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="ff0e2-456">由于模拟对象后面没有实现，因此 Add 不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="ff0e2-457">幕后没有像我们写的假货那样的具体收藏，所以员工被丢弃了。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="ff0e2-458">FindById 的返回值如何？</span><span class="sxs-lookup"><span data-stu-id="ff0e2-458">What about the return value of FindById?</span></span> <span data-ttu-id="ff0e2-459">在这种情况下，模拟对象执行它唯一能做的，即返回默认值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="ff0e2-460">由于我们要返回引用类型（员工），因此返回值为 null 值。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="ff0e2-461">模拟听起来可能毫无价值;然而，还有两个模拟功能，我们还没有谈到。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="ff0e2-462">首先，Moq 框架记录对模拟对象进行的所有调用。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="ff0e2-463">在代码的后面部分，我们可以问 Moq 是否有人调用了 Add 方法，或者是否有人调用了 FindById 方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="ff0e2-464">稍后我们将了解如何在测试中使用此"黑匣子"录制功能。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="ff0e2-465">第二个伟大的功能是我们如何使用Moq编程一个模拟对象*与期望*。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="ff0e2-466">期望值告诉模拟对象如何响应任何给定的交互。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="ff0e2-467">例如，我们可以将期望值编程到模拟中，并告诉它当有人调用 FindById 时返回员工对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="ff0e2-468">Moq 框架使用安装程序 API 和 lambda 表达式来对这些期望进行编程。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="ff0e2-469">在此示例中，我们要求 Moq 动态构建存储库，然后我们对存储库进行编程。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="ff0e2-470">当某人调用传递值为 5 的 FindById 方法时，期望告诉模拟对象返回 Id 值为 5 的新员工对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="ff0e2-471">此测试通过，我们不需要构建一个完整的实现假 IRepository&lt;T&gt;。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="ff0e2-472">让我们重温我们之前编写的测试，并重新编写它们以使用模拟而不是假测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="ff0e2-473">与以前一样，我们将使用基类来设置所有控制器测试所需的通用基础结构部分。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="ff0e2-474">设置代码大致相同。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="ff0e2-475">我们将使用 Moq 来构造模拟对象，而不是使用假物。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="ff0e2-476">基类安排模拟工作单元在代码调用"员工"属性时返回模拟存储库。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="ff0e2-477">模拟设置的其余部分将发生在专用于每个特定场景的测试夹具内。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="ff0e2-478">例如，当操作调用模拟存储库的 FindAll 方法时，Index 操作的测试固件将设置模拟存储库以返回员工列表。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="ff0e2-479">除了期望值之外，我们的测试看起来与之前的测试相似。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="ff0e2-480">然而，随着模拟框架的录制能力，我们可以从不同的角度进行测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="ff0e2-481">我们将在下一节中介绍这一新观点。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="ff0e2-482">状态与交互测试</span><span class="sxs-lookup"><span data-stu-id="ff0e2-482">State versus Interaction Testing</span></span>

<span data-ttu-id="ff0e2-483">您可以使用不同的技术来测试使用模拟对象的软件。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="ff0e2-484">一种方法是使用基于状态的测试，这就是我们在本文中到目前为止所做的。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="ff0e2-485">基于状态的测试对软件的状态进行断言。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="ff0e2-486">在上一次测试中，我们在控制器上调用了一个操作方法，并断言它应该构建的模型。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="ff0e2-487">下面是测试状态的其他一些示例：</span><span class="sxs-lookup"><span data-stu-id="ff0e2-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="ff0e2-488">在执行"创建"后验证存储库包含新员工对象。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="ff0e2-489">在索引执行后验证模型包含所有员工的列表。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="ff0e2-490">在执行"删除"后，验证存储库不包含给定员工。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="ff0e2-491">使用模拟对象将看到的另一种方法是验证*交互*。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="ff0e2-492">虽然基于状态的测试对对象的状态进行断言，但基于交互的测试会断言对象如何交互。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="ff0e2-493">例如：</span><span class="sxs-lookup"><span data-stu-id="ff0e2-493">For example:</span></span>

-   <span data-ttu-id="ff0e2-494">在执行"创建"时验证控制器调用存储库的 Add 方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="ff0e2-495">当索引执行时，验证控制器调用存储库的 FindAll 方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="ff0e2-496">验证控制器调用工作单元的提交方法，以在"编辑"执行时保存更改。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="ff0e2-497">交互测试通常需要较少的测试数据，因为我们不是在集合内部进行戳造和验证计数。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="ff0e2-498">例如，如果我们知道"详细信息"操作调用具有正确值的存储库的 FindById 方法，则操作可能运行正常。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="ff0e2-499">我们可以验证此行为，而无需设置任何测试数据从 FindById 返回。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="ff0e2-500">上述测试夹具中唯一需要的设置是基类提供的设置。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="ff0e2-501">当我们调用控制器操作时，Moq 将记录与模拟存储库的交互。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="ff0e2-502">使用 Moq 的验证 API，我们可以询问 Moq 控制器是否使用正确的 ID 值调用 FindById。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="ff0e2-503">如果控制器未调用方法，或者使用意外参数值调用该方法，则 Verify 方法将引发异常，并且测试将失败。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="ff0e2-504">下面是另一个示例，用于验证"创建操作在当前工作单元上调用提交"。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="ff0e2-505">交互测试的一个危险是过度指定交互的倾向。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="ff0e2-506">模拟对象记录和验证与模拟对象的每次交互的能力并不意味着测试应尝试验证每个交互。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="ff0e2-507">某些交互是实现详细信息，您只应验证满足当前测试*所需的*交互。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="ff0e2-508">在模拟或假货之间进行选择很大程度上取决于您测试的系统以及您的个人（或团队）偏好。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="ff0e2-509">Mock 对象可以大大减少实现测试双精度值所需的代码量，但并不是每个人都会舒适地实现编程期望和验证交互。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="ff0e2-510">结论</span><span class="sxs-lookup"><span data-stu-id="ff0e2-510">Conclusions</span></span>

<span data-ttu-id="ff0e2-511">在本文中，我们演示了几种在使用ADO.NET实体框架进行数据持久性时创建可测试代码的方法。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="ff0e2-512">我们可以利用内置的抽象&lt;，如IObjectSet T，&gt;或者创建我们自己的抽象，如IRepositoryT。&gt;&lt;</span><span class="sxs-lookup"><span data-stu-id="ff0e2-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="ff0e2-513">在这两种情况下，ADO.NET实体框架 4.0 中的 POCO 支持允许这些抽象的使用者保持持久无知和高度可测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="ff0e2-514">隐式延迟加载等其他 EF4 功能允许业务和应用程序服务代码工作，而不必担心关系数据存储的详细信息。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="ff0e2-515">最后，我们创建的抽象在单元测试中很容易模拟或伪造，我们可以使用这些测试双精度值来实现快速运行、高度隔离和可靠的测试。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="ff0e2-516">其他资源</span><span class="sxs-lookup"><span data-stu-id="ff0e2-516">Additional Resources</span></span>

-   <span data-ttu-id="ff0e2-517">罗伯特·马丁，"[单一责任原则](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="ff0e2-518">马丁·福勒[Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html)，*企业应用程序体系结构模式模式目录*</span><span class="sxs-lookup"><span data-stu-id="ff0e2-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="ff0e2-519">格里芬·卡普里奥，"[依赖注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="ff0e2-520">数据可编程性博客，"[演练：使用实体框架 4.0 的测试驱动开发](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)"。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="ff0e2-521">数据可编程性博客，"[使用具有实体框架 4.0 的存储库和工作单元模式](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="ff0e2-522">亚伦·詹森，"[介绍机器规格](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="ff0e2-523">埃里克·李[，"BDD与MSTest"](https://saintgimp.org/2009/01/20/bdd-with-mstest/)</span><span class="sxs-lookup"><span data-stu-id="ff0e2-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="ff0e2-524">埃里克·埃文斯，"[领域驱动设计](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="ff0e2-525">马丁·福勒，"[模仿不是斯图布斯](https://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="ff0e2-526">马丁·福勒，"[测试双](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="ff0e2-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="ff0e2-527">莫克</span><span class="sxs-lookup"><span data-stu-id="ff0e2-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="ff0e2-528">传记</span><span class="sxs-lookup"><span data-stu-id="ff0e2-528">Biography</span></span>

<span data-ttu-id="ff0e2-529">Scott Allen 是多元视的技术人员，也是 OdeToCode.com的创始人。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="ff0e2-530">在 15 年的商业软件开发中，Scott 一直致力于从 8 位嵌入式设备到高度可扩展ASP.NET Web 应用程序等所有解决方案。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="ff0e2-531">您可以在 OdeToCode 的博客上或在推特上[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)联系斯科特。</span><span class="sxs-lookup"><span data-stu-id="ff0e2-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>

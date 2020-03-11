---
title: 基于代码的配置-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: 079a4ab30af74eac8b1f51ece5801ff40a867a29
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414799"
---
# <a name="code-based-configuration"></a><span data-ttu-id="eafc5-102">基于代码的配置</span><span class="sxs-lookup"><span data-stu-id="eafc5-102">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="eafc5-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="eafc5-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="eafc5-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="eafc5-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="eafc5-105">可以在配置文件（app.config/web.config）或代码中指定实体框架应用程序的配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-105">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="eafc5-106">后者称为基于代码的配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-106">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="eafc5-107">在[单独的文章](config-file.md)中介绍了配置文件中的配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-107">Configuration in a config file is described in a [separate article](config-file.md).</span></span> <span data-ttu-id="eafc5-108">配置文件优先于基于代码的配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-108">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="eafc5-109">换句话说，如果在代码和配置文件中都设置了配置选项，则使用配置文件中的设置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-109">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="eafc5-110">使用 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="eafc5-110">Using DbConfiguration</span></span>  

<span data-ttu-id="eafc5-111">EF6 和更高版本中基于代码的配置是通过创建 DbConfiguration 的子类来实现的。</span><span class="sxs-lookup"><span data-stu-id="eafc5-111">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="eafc5-112">在对 DbConfiguration 进行子类化时，应遵循以下准则：</span><span class="sxs-lookup"><span data-stu-id="eafc5-112">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="eafc5-113">只为应用程序创建一个 DbConfiguration 类。</span><span class="sxs-lookup"><span data-stu-id="eafc5-113">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="eafc5-114">此类指定应用域范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-114">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="eafc5-115">将 DbConfiguration 类放在与 DbContext 类相同的程序集中。</span><span class="sxs-lookup"><span data-stu-id="eafc5-115">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="eafc5-116">（若要更改此内容，请参阅*移动 DbConfiguration*部分。）</span><span class="sxs-lookup"><span data-stu-id="eafc5-116">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="eafc5-117">为 DbConfiguration 类指定一个公共的无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="eafc5-117">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="eafc5-118">通过在此构造函数中调用受保护的 DbConfiguration 方法来设置配置选项。</span><span class="sxs-lookup"><span data-stu-id="eafc5-118">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="eafc5-119">遵循这些指导原则后，EF 可以通过需要访问模型和应用程序运行时的两个工具自动发现和使用配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-119">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="eafc5-120">示例</span><span class="sxs-lookup"><span data-stu-id="eafc5-120">Example</span></span>  

<span data-ttu-id="eafc5-121">派生自 DbConfiguration 的类可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="eafc5-121">A class derived from DbConfiguration might look like this:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

<span data-ttu-id="eafc5-122">此类设置 EF 以使用 SQL Azure 执行策略-自动重试失败的数据库操作，并使用由 Code First 中的约定创建的数据库的本地数据库。</span><span class="sxs-lookup"><span data-stu-id="eafc5-122">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="eafc5-123">移动 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="eafc5-123">Moving DbConfiguration</span></span>  

<span data-ttu-id="eafc5-124">有些情况下，不能将 DbConfiguration 类放在与 DbContext 类相同的程序集中。</span><span class="sxs-lookup"><span data-stu-id="eafc5-124">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="eafc5-125">例如，你可能在不同的程序集中有两个 DbContext 类。</span><span class="sxs-lookup"><span data-stu-id="eafc5-125">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="eafc5-126">有两个选项可用于处理此操作。</span><span class="sxs-lookup"><span data-stu-id="eafc5-126">There are two options for handling this.</span></span>  

<span data-ttu-id="eafc5-127">第一种方法是使用配置文件指定要使用的 DbConfiguration 实例。</span><span class="sxs-lookup"><span data-stu-id="eafc5-127">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="eafc5-128">为此，请设置 entityFramework 部分的 codeConfigurationType 属性。</span><span class="sxs-lookup"><span data-stu-id="eafc5-128">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="eafc5-129">例如：</span><span class="sxs-lookup"><span data-stu-id="eafc5-129">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="eafc5-130">CodeConfigurationType 的值必须是 DbConfiguration 类的程序集和命名空间限定名称。</span><span class="sxs-lookup"><span data-stu-id="eafc5-130">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="eafc5-131">第二种方法是在上下文类上放置 DbConfigurationTypeAttribute。</span><span class="sxs-lookup"><span data-stu-id="eafc5-131">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="eafc5-132">例如：</span><span class="sxs-lookup"><span data-stu-id="eafc5-132">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="eafc5-133">传递给特性的值可以是 DbConfiguration 类型（如上所示），也可以是程序集和命名空间限定的类型名称字符串。</span><span class="sxs-lookup"><span data-stu-id="eafc5-133">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="eafc5-134">例如：</span><span class="sxs-lookup"><span data-stu-id="eafc5-134">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="eafc5-135">显式设置 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="eafc5-135">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="eafc5-136">在某些情况下，在使用任何 DbContext 类型之前可能需要进行配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-136">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="eafc5-137">这种情况的示例包括：</span><span class="sxs-lookup"><span data-stu-id="eafc5-137">Examples of this include:</span></span>  

- <span data-ttu-id="eafc5-138">在不使用上下文的情况下使用 DbModelBuilder 生成模型</span><span class="sxs-lookup"><span data-stu-id="eafc5-138">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="eafc5-139">在使用应用程序上下文之前，使用一些其他使用 DbContext 的框架/实用工具代码，其中使用了该上下文</span><span class="sxs-lookup"><span data-stu-id="eafc5-139">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="eafc5-140">在这种情况下，EF 无法自动发现配置，而必须执行以下操作之一：</span><span class="sxs-lookup"><span data-stu-id="eafc5-140">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="eafc5-141">设置配置文件中的 DbConfiguration 类型，如上面的*移动 DbConfiguration*部分中所述</span><span class="sxs-lookup"><span data-stu-id="eafc5-141">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="eafc5-142">在应用程序启动过程中调用静态 DbConfiguration SetConfiguration 方法</span><span class="sxs-lookup"><span data-stu-id="eafc5-142">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="eafc5-143">重写 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="eafc5-143">Overriding DbConfiguration</span></span>  

<span data-ttu-id="eafc5-144">在某些情况下，需要重写 DbConfiguration 中的配置集。</span><span class="sxs-lookup"><span data-stu-id="eafc5-144">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="eafc5-145">这通常不是由应用程序开发人员完成，而是由不能使用派生的 DbConfiguration 类的第三方提供程序和插件来完成。</span><span class="sxs-lookup"><span data-stu-id="eafc5-145">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="eafc5-146">为此，EntityFramework 允许注册事件处理程序，该事件处理程序可以在锁定之前修改现有配置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-146">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="eafc5-147">它还提供专门用于替换 EF 服务定位器返回的任何服务的一个糖方法。</span><span class="sxs-lookup"><span data-stu-id="eafc5-147">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="eafc5-148">这就是如何使用它：</span><span class="sxs-lookup"><span data-stu-id="eafc5-148">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="eafc5-149">在应用启动时（使用 EF 之前）插件或提供程序应为此事件注册事件处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="eafc5-149">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="eafc5-150">（请注意，此操作必须在应用程序使用 EF 之前发生。）</span><span class="sxs-lookup"><span data-stu-id="eafc5-150">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="eafc5-151">对于需要替换的每个服务，事件处理程序都会调用 ReplaceService。</span><span class="sxs-lookup"><span data-stu-id="eafc5-151">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="eafc5-152">例如，若要替换 IDbConnectionFactory 和 DbProviderService，需注册一个类似于以下内容的处理程序：</span><span class="sxs-lookup"><span data-stu-id="eafc5-152">For example, to replace IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="eafc5-153">在上面的代码中，MyProviderServices 和 MyConnectionFactory 表示服务的实现。</span><span class="sxs-lookup"><span data-stu-id="eafc5-153">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="eafc5-154">你还可以添加其他依赖关系处理程序以获得相同的效果。</span><span class="sxs-lookup"><span data-stu-id="eafc5-154">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="eafc5-155">请注意，你也可以通过这种方式来包装 DbProviderFactory，但这样做只会影响 EF，而不会在 EF 之外使用 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="eafc5-155">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="eafc5-156">出于此原因，你可能需要继续像以前一样来包装 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="eafc5-156">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="eafc5-157">你还应记住你在应用程序外部运行的服务（例如，从包管理器控制台运行迁移时）。</span><span class="sxs-lookup"><span data-stu-id="eafc5-157">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="eafc5-158">当你从控制台运行迁移时，它将尝试查找你的 DbConfiguration。</span><span class="sxs-lookup"><span data-stu-id="eafc5-158">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="eafc5-159">但是，无论是否获取包装服务，都取决于它所注册的事件处理程序的位置。</span><span class="sxs-lookup"><span data-stu-id="eafc5-159">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="eafc5-160">如果已将其注册为 DbConfiguration 构造的一部分，则应执行代码，并将服务打包。</span><span class="sxs-lookup"><span data-stu-id="eafc5-160">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="eafc5-161">通常不会出现这种情况，这意味着工具不会获得包装服务。</span><span class="sxs-lookup"><span data-stu-id="eafc5-161">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  

---
title: 基于代码的配置-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
ms.openlocfilehash: c317f112f713612f7b9aef3764a0bd004fef5424
ms.sourcegitcommit: 735715f10cc8a231c213e4f055d79f0effd86570
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2019
ms.locfileid: "56325348"
---
# <a name="code-based-configuration"></a><span data-ttu-id="1c1e2-102">基于代码的配置</span><span class="sxs-lookup"><span data-stu-id="1c1e2-102">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="1c1e2-103">**仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1c1e2-104">如果使用的是早期版本，则部分或全部信息不适用。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1c1e2-105">在配置文件 (app.config/web.config) 或通过代码，可以指定的实体框架应用程序配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-105">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="1c1e2-106">后一种称为基于代码的配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-106">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="1c1e2-107">配置配置文件中的所述[单独的文章](config-file.md)。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-107">Configuration in a config file is described in a [separate article](config-file.md).</span></span> <span data-ttu-id="1c1e2-108">配置文件将优先于基于代码的配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-108">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="1c1e2-109">换而言之，如果在这两个代码和配置文件中设置的配置选项，则使用配置文件中的设置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-109">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="1c1e2-110">使用 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="1c1e2-110">Using DbConfiguration</span></span>  

<span data-ttu-id="1c1e2-111">基于代码的配置在 EF6 及更高版本是通过创建 System.Data.Entity.Config.DbConfiguration 的子类来实现的。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-111">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="1c1e2-112">子类化 DbConfiguration 时，应遵循以下准则：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-112">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="1c1e2-113">创建应用程序只有一个 DbConfiguration 类。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-113">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="1c1e2-114">此类指定应用程序域范围的设置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-114">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="1c1e2-115">将 DbConfiguration 类放在 DbContext 类相同的程序集中。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-115">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="1c1e2-116">(请参阅*移动 DbConfiguration*部分如果想要更改此设置。)</span><span class="sxs-lookup"><span data-stu-id="1c1e2-116">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="1c1e2-117">为 DbConfiguration 类提供公共的无参数构造函数。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-117">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="1c1e2-118">通过调用此构造函数中的受保护的 DbConfiguration 方法从设置配置选项。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-118">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="1c1e2-119">遵循以下准则使得 EF 可发现和需要访问您的模型和运行你的应用程序时这两个工具通过自动使用你的配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-119">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="1c1e2-120">示例</span><span class="sxs-lookup"><span data-stu-id="1c1e2-120">Example</span></span>  

<span data-ttu-id="1c1e2-121">从 DbConfiguration 派生的类可能如下所示：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-121">A class derived from DbConfiguration might look like this:</span></span>  

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

<span data-ttu-id="1c1e2-122">此类设置 EF，若要使用 SQL Azure 执行策略的自动重试失败的数据库操作-和 Local DB 用于通过从代码优先约定创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-122">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="1c1e2-123">移动 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="1c1e2-123">Moving DbConfiguration</span></span>  

<span data-ttu-id="1c1e2-124">有些情况下，不能将 DbConfiguration 类放在 DbContext 类相同的程序集中。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-124">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="1c1e2-125">例如，你可能有两个 DbContext 类每个不同程序集中。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-125">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="1c1e2-126">有两个选项用于实现此操作。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-126">There are two options for handling this.</span></span>  

<span data-ttu-id="1c1e2-127">第一个选项是使用配置文件来指定要使用的 DbConfiguration 实例。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-127">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="1c1e2-128">若要执行此操作，设置 entityFramework 部分的 codeConfigurationType 属性。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-128">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="1c1e2-129">例如：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-129">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="1c1e2-130">CodeConfigurationType 的值必须是程序集和 DbConfiguration 类的命名空间限定的名称。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-130">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="1c1e2-131">第二个选项是 DbConfigurationTypeAttribute 置于您的上下文类。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-131">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="1c1e2-132">例如：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-132">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="1c1e2-133">值传递给该属性可以是你的 DbConfiguration 类型--如上所示，或程序集和命名空间限定类型名称字符串。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-133">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="1c1e2-134">例如：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-134">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="1c1e2-135">显式设置 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="1c1e2-135">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="1c1e2-136">有某些情况下，已使用任何 DbContext 类型之前可能需要配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-136">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="1c1e2-137">这样的示例包括：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-137">Examples of this include:</span></span>  

- <span data-ttu-id="1c1e2-138">使用 DbModelBuilder 生成没有上下文的模型</span><span class="sxs-lookup"><span data-stu-id="1c1e2-138">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="1c1e2-139">使用使用的 DbContext 之前使用应用程序上下文则使用该上下文的其他一些框架/实用程序代码</span><span class="sxs-lookup"><span data-stu-id="1c1e2-139">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="1c1e2-140">在这种情况下 EF 是无法自动发现配置，而是必须执行以下项之一：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-140">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="1c1e2-141">DbConfiguration 中的类型设置配置文件中，如中所述*移动 DbConfiguration*上面部分</span><span class="sxs-lookup"><span data-stu-id="1c1e2-141">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="1c1e2-142">在应用程序启动过程中调用静态 DbConfiguration.SetConfiguration 方法</span><span class="sxs-lookup"><span data-stu-id="1c1e2-142">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="1c1e2-143">重写 DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="1c1e2-143">Overriding DbConfiguration</span></span>  

<span data-ttu-id="1c1e2-144">有某些情况下，您需要重写中 DbConfiguration 设置的配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-144">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="1c1e2-145">这不通常是由应用程序开发人员，而是由第三方提供程序和不能使用一个 DbConfiguration 类，派生的插件。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-145">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="1c1e2-146">为此，EntityFramework 允许事件处理程序注册的向下锁定之前可以修改现有配置。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-146">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="1c1e2-147">它还提供专用于替换任何服务返回了由 EF 服务定位器的 sugar 方法。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-147">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="1c1e2-148">这是如何它旨在使用：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-148">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="1c1e2-149">在应用启动时 （之前使用 EF） 的插件或提供程序应注册此事件的事件处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-149">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="1c1e2-150">（请注意这必须发生之前应用程序使用 EF）。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-150">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="1c1e2-151">事件处理程序调用到 ReplaceService 需要替换每个服务。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-151">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="1c1e2-152">例如下 IDbConnectionFactory, 和 DbProviderService 会注册一个处理程序如下所示：</span><span class="sxs-lookup"><span data-stu-id="1c1e2-152">For example, to repalce IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="1c1e2-153">在代码中更高版本的 MyProviderServices MyConnectionFactory 表示你的服务的实现。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-153">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="1c1e2-154">此外可以添加其他依赖项的处理程序来获取相同的效果。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-154">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="1c1e2-155">请注意，您还可以封装 DbProviderFactory 在这样一来，但这样做因此只会影响 EF 而不使用在 EF 外部 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-155">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="1c1e2-156">出于此原因您可能需要继续为之前已包装 DbProviderFactory。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-156">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="1c1e2-157">您应该还请记住您运行外部到你的应用程序-例如，从包管理器控制台运行迁移时的服务。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-157">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="1c1e2-158">在运行时将从它将尝试查找你 DbConfiguration 控制台迁移。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-158">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="1c1e2-159">但是，无论有无它将获取已包装的服务依赖于 where 它注册的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-159">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="1c1e2-160">如果你 DbConfiguration 的构造过程中注册然后应执行的代码和服务应获取包装。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-160">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="1c1e2-161">通常这不会出现这种情况，这意味着工具不会获取已包装的服务。</span><span class="sxs-lookup"><span data-stu-id="1c1e2-161">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  

---
title: 基于代码的配置-EF6
description: 实体框架6中基于代码的配置
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 67bb7ebd620c90ebe80983cc5baa6cab032907f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070726"
---
# <a name="code-based-configuration"></a>基于代码的配置
> [!NOTE]
> **仅限 EF6 及更高版本** - 此页面中讨论的功能、API 等已引入实体框架 6。 如果使用的是早期版本，则部分或全部信息不适用。  

实体框架应用程序的配置可以在配置文件中指定 ( # A0/web.config) 或代码。 后者称为基于代码的配置。  

在 [单独的文章](xref:ef6/fundamentals/configuring/config-file)中介绍了配置文件中的配置。 配置文件优先于基于代码的配置。 换句话说，如果在代码和配置文件中都设置了配置选项，则使用配置文件中的设置。  

## <a name="using-dbconfiguration"></a>使用 DbConfiguration  

EF6 和更高版本中基于代码的配置是通过创建 System.Data.Entity.Config 的子类来实现的。DbConfiguration. 在对 DbConfiguration 进行子类化时，应遵循以下准则：  

- 只为应用程序创建一个 DbConfiguration 类。 此类指定应用域范围内的设置。  
- 将 DbConfiguration 类放在与 DbContext 类相同的程序集中。  (如果要更改此内容，请参阅 *移动 DbConfiguration* 部分。 )   
- 为 DbConfiguration 类指定一个公共的无参数构造函数。  
- 通过在此构造函数中调用受保护的 DbConfiguration 方法来设置配置选项。  

遵循这些指导原则后，EF 可以通过需要访问模型和应用程序运行时的两个工具自动发现和使用配置。  

## <a name="example"></a>示例  

派生自 DbConfiguration 的类可能如下所示：  

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

此类设置 EF 以使用 SQL Azure 执行策略-自动重试失败的数据库操作，并使用由 Code First 中的约定创建的数据库的本地数据库。  

## <a name="moving-dbconfiguration"></a>移动 DbConfiguration  

有些情况下，不能将 DbConfiguration 类放在与 DbContext 类相同的程序集中。 例如，你可能在不同的程序集中有两个 DbContext 类。 有两个选项可用于处理此操作。  

第一种方法是使用配置文件指定要使用的 DbConfiguration 实例。 为此，请设置 entityFramework 部分的 codeConfigurationType 属性。 例如：  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

CodeConfigurationType 的值必须是 DbConfiguration 类的程序集和命名空间限定名称。  

第二种方法是在上下文类上放置 DbConfigurationTypeAttribute。 例如：  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

传递给特性的值可以是 DbConfiguration 类型（如上所示），也可以是程序集和命名空间限定的类型名称字符串。 例如：  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>显式设置 DbConfiguration  

在某些情况下，在使用任何 DbContext 类型之前可能需要进行配置。 这种情况的示例包括：  

- 在不使用上下文的情况下使用 DbModelBuilder 生成模型  
- 在使用应用程序上下文之前，使用一些其他使用 DbContext 的框架/实用工具代码，其中使用了该上下文  

在这种情况下，EF 无法自动发现配置，而必须执行以下操作之一：  

- 设置配置文件中的 DbConfiguration 类型，如上面的 *移动 DbConfiguration* 部分中所述
- 在应用程序启动过程中调用静态 DbConfiguration SetConfiguration 方法  

## <a name="overriding-dbconfiguration"></a>重写 DbConfiguration  

在某些情况下，需要重写 DbConfiguration 中的配置集。 这通常不是由应用程序开发人员完成，而是由不能使用派生的 DbConfiguration 类的第三方提供程序和插件来完成。  

为此，EntityFramework 允许注册事件处理程序，该事件处理程序可以在锁定之前修改现有配置。  它还提供专门用于替换 EF 服务定位器返回的任何服务的一个糖方法。 这就是如何使用它：  

- 在应用启动 (在使用 EF 之前) 插件或提供程序应为此事件注册事件处理程序方法。  (注意，此操作必须在应用程序使用 EF 之前发生。 )   
- 对于需要替换的每个服务，事件处理程序都会调用 ReplaceService。  

例如，若要替换 IDbConnectionFactory 和 DbProviderService，需注册一个类似于以下内容的处理程序：  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

在上面的代码中，MyProviderServices 和 MyConnectionFactory 表示服务的实现。  

你还可以添加其他依赖关系处理程序以获得相同的效果。  

请注意，你也可以通过这种方式来包装 DbProviderFactory，但这样做只会影响 EF，而不会在 EF 之外使用 DbProviderFactory。 出于此原因，你可能需要继续像以前一样来包装 DbProviderFactory。  

你还应记住你在应用程序外部运行的服务（例如，从包管理器控制台运行迁移时）。 当你从控制台运行迁移时，它将尝试查找你的 DbConfiguration。 但是，无论是否获取包装服务，都取决于它所注册的事件处理程序的位置。 如果已将其注册为 DbConfiguration 构造的一部分，则应执行代码，并将服务打包。 通常不会出现这种情况，这意味着工具不会获得包装服务。  

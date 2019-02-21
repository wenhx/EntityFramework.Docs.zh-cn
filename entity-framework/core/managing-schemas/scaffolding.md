---
title: 反向工程-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 6e61d2ebcf5ada365dcdb264bc371199574e12fa
ms.sourcegitcommit: 33b2e84dae96040f60a613186a24ff3c7b00b6db
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2019
ms.locfileid: "56459180"
---
# <a name="reverse-engineering"></a><span data-ttu-id="5b528-102">反向工程</span><span class="sxs-lookup"><span data-stu-id="5b528-102">Reverse Engineering</span></span>

<span data-ttu-id="5b528-103">反向工程是实体类型类和一个基于数据库架构的 DbContext 类的基架的过程。</span><span class="sxs-lookup"><span data-stu-id="5b528-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="5b528-104">可以执行使用`Scaffold-DbContext`EF Core 包管理器控制台 (PMC) 工具的命令或`dotnet ef dbcontext scaffold`.NET 命令行接口 (CLI) 工具的命令。</span><span class="sxs-lookup"><span data-stu-id="5b528-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="5b528-105">安装</span><span class="sxs-lookup"><span data-stu-id="5b528-105">Installing</span></span>

<span data-ttu-id="5b528-106">反向工程之前你将需要安装[PMC 工具](xref:core/miscellaneous/cli/powershell)(仅限 Visual Studio) 或[CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="5b528-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="5b528-107">请参阅有关详细信息的链接。</span><span class="sxs-lookup"><span data-stu-id="5b528-107">See links for details.</span></span>

<span data-ttu-id="5b528-108">您还需要安装相应[数据库提供程序](xref:core/providers/index)你想要实施反向工程的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5b528-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="5b528-109">连接字符串</span><span class="sxs-lookup"><span data-stu-id="5b528-109">Connection string</span></span>

<span data-ttu-id="5b528-110">该命令的第一个参数是数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5b528-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="5b528-111">工具将使用此连接字符串来读取数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5b528-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="5b528-112">如何用引号括起来并转义的连接字符串取决于哪个 shell 您正在使用执行命令。</span><span class="sxs-lookup"><span data-stu-id="5b528-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="5b528-113">请参阅 shell 的文档了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="5b528-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="5b528-114">例如，PowerShell 要求你进行转义`$`字符，但不是`\`。</span><span class="sxs-lookup"><span data-stu-id="5b528-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

``` Console
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="5b528-115">配置和用户机密</span><span class="sxs-lookup"><span data-stu-id="5b528-115">Configuration and User Secrets</span></span>

<span data-ttu-id="5b528-116">如果您有一个 ASP.NET Core 项目，则可以使用`Name=<connection-string>`语法来从配置中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5b528-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="5b528-117">这适用于[机密管理器工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)以便将自己的数据库密码与你的基本代码分开。</span><span class="sxs-lookup"><span data-stu-id="5b528-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

``` Console
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="5b528-118">提供程序名称</span><span class="sxs-lookup"><span data-stu-id="5b528-118">Provider name</span></span>

<span data-ttu-id="5b528-119">第二个参数是提供程序名称。</span><span class="sxs-lookup"><span data-stu-id="5b528-119">The second argument is the provider name.</span></span> <span data-ttu-id="5b528-120">提供程序名称通常是与提供程序的 NuGet 包名称相同。</span><span class="sxs-lookup"><span data-stu-id="5b528-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="5b528-121">指定表</span><span class="sxs-lookup"><span data-stu-id="5b528-121">Specifying tables</span></span>

<span data-ttu-id="5b528-122">数据库架构中的所有表都被反向工程到实体类型，默认情况下。</span><span class="sxs-lookup"><span data-stu-id="5b528-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="5b528-123">您可以限制哪些表是反向工程处理通过指定架构和表。</span><span class="sxs-lookup"><span data-stu-id="5b528-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="5b528-124">`-Schemas`在 PMC 中的参数和`--schema`CLI 中的选项可用于包含在架构中的每个表。</span><span class="sxs-lookup"><span data-stu-id="5b528-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="5b528-125">`-Tables` (PMC) 和`--table`(CLI) 可用于包括特定的表。</span><span class="sxs-lookup"><span data-stu-id="5b528-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="5b528-126">若要在 PMC 中包含多个表，使用一个数组。</span><span class="sxs-lookup"><span data-stu-id="5b528-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="5b528-127">若要在 CLI 中包含多个表，请多次指定选项。</span><span class="sxs-lookup"><span data-stu-id="5b528-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

``` Console
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="5b528-128">保留名称</span><span class="sxs-lookup"><span data-stu-id="5b528-128">Preserving names</span></span>

<span data-ttu-id="5b528-129">表和列的名称被固定的以更好地匹配的类型和属性的.NET 命名约定默认情况下。</span><span class="sxs-lookup"><span data-stu-id="5b528-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="5b528-130">指定`-UseDatabaseNames`切换在 PMC 中或`--use-database-names`CLI 中的选项将禁用此行为保留尽可能多地原始数据库名称。</span><span class="sxs-lookup"><span data-stu-id="5b528-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="5b528-131">仍将修复无效的.NET 标识符和合成的名称，如导航属性仍将遵循.NET 命名约定。</span><span class="sxs-lookup"><span data-stu-id="5b528-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="5b528-132">Fluent API 或数据注释</span><span class="sxs-lookup"><span data-stu-id="5b528-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="5b528-133">实体类型是使用 Fluent API 默认配置。</span><span class="sxs-lookup"><span data-stu-id="5b528-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="5b528-134">指定`-DataAnnotations`(PMC) 或`--data-annotations`(CLI) 要改为使用数据注释在可能的情况。</span><span class="sxs-lookup"><span data-stu-id="5b528-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="5b528-135">例如，使用 Fluent API 将基架生成此：</span><span class="sxs-lookup"><span data-stu-id="5b528-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="5b528-136">使用数据注释时将创建基架这：</span><span class="sxs-lookup"><span data-stu-id="5b528-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="5b528-137">DbContext 名称</span><span class="sxs-lookup"><span data-stu-id="5b528-137">DbContext name</span></span>

<span data-ttu-id="5b528-138">已搭建基架的 DbContext 类名称将用作后缀的数据库的名称*上下文*默认情况下。</span><span class="sxs-lookup"><span data-stu-id="5b528-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="5b528-139">若要指定一个不同，使用`-Context`在 PMC 中和`--context`CLI 中。</span><span class="sxs-lookup"><span data-stu-id="5b528-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="5b528-140">目录和命名空间</span><span class="sxs-lookup"><span data-stu-id="5b528-140">Directories and namespaces</span></span>

<span data-ttu-id="5b528-141">实体类和 DbContext 类到项目的根目录的基架，并使用项目的默认命名空间。</span><span class="sxs-lookup"><span data-stu-id="5b528-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="5b528-142">可以指定的目录的类使用基架`-OutputDir`(PMC) 或`--output-dir`(CLI)。</span><span class="sxs-lookup"><span data-stu-id="5b528-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="5b528-143">命名空间将为根命名空间加上任何项目的根目录下的子目录的名称。</span><span class="sxs-lookup"><span data-stu-id="5b528-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="5b528-144">此外可以使用`-ContextDir`(PMC) 和`--context-dir`(CLI) 来创建到一个单独的目录从实体类型类的基架的 DbContext 类。</span><span class="sxs-lookup"><span data-stu-id="5b528-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

``` Console
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="5b528-145">其工作原理</span><span class="sxs-lookup"><span data-stu-id="5b528-145">How it works</span></span>

<span data-ttu-id="5b528-146">反向工程开始时读取数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5b528-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="5b528-147">它将读取有关表、 列、 约束和索引的信息。</span><span class="sxs-lookup"><span data-stu-id="5b528-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="5b528-148">接下来，它使用的架构信息创建 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="5b528-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="5b528-149">使用表来创建实体类型;使用列来创建属性;和外键用于创建关系。</span><span class="sxs-lookup"><span data-stu-id="5b528-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="5b528-150">最后，该模型用于生成代码。</span><span class="sxs-lookup"><span data-stu-id="5b528-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="5b528-151">相应的实体类型的类、 Fluent API 和数据批注已搭建基架以重新创建相同的模型从您的应用程序中。</span><span class="sxs-lookup"><span data-stu-id="5b528-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="what-doesnt-work"></a><span data-ttu-id="5b528-152">什么不起作用</span><span class="sxs-lookup"><span data-stu-id="5b528-152">What doesn't work</span></span>

<span data-ttu-id="5b528-153">并非所有有关模型的内容都可以表示使用数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5b528-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="5b528-154">例如，有关的信息**继承层次结构**，**固有类型**，并**表拆分**数据库架构中不存在。</span><span class="sxs-lookup"><span data-stu-id="5b528-154">For example, information about **inheritance hierarchies**, **owned types**, and **table splitting** are not present in the database schema.</span></span> <span data-ttu-id="5b528-155">因此，这些构造将永远不能反向工程处理。</span><span class="sxs-lookup"><span data-stu-id="5b528-155">Because of this, these constructs will never be reverse engineered.</span></span>

<span data-ttu-id="5b528-156">此外，**某些列类型**可能不支持通过 EF Core 提供程序。</span><span class="sxs-lookup"><span data-stu-id="5b528-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="5b528-157">这些列不包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="5b528-157">These columns won't be included in the model.</span></span>

<span data-ttu-id="5b528-158">EF Core，需要每个实体类型的键。</span><span class="sxs-lookup"><span data-stu-id="5b528-158">EF Core requires every entity type to have a key.</span></span> <span data-ttu-id="5b528-159">表，但是，无需指定为主键。</span><span class="sxs-lookup"><span data-stu-id="5b528-159">Tables, however, aren't required to specify a primary key.</span></span> <span data-ttu-id="5b528-160">**表没有主键**是当前不实施反向工程。</span><span class="sxs-lookup"><span data-stu-id="5b528-160">**Tables without a primary key** are currently not reverse engineered.</span></span>

<span data-ttu-id="5b528-161">您可以定义**并发标记**EF Core 模型以防止两个用户在同一时间更新同一实体中。</span><span class="sxs-lookup"><span data-stu-id="5b528-161">You can define **concurrency tokens** in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="5b528-162">有些数据库具有一种特殊类型来表示这种情况下，我们可以反向设计此信息; 在这种类型的列 （例如，SQL Server 中的行版本）但是，其他并发令牌将不能反向工程处理。</span><span class="sxs-lookup"><span data-stu-id="5b528-162">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="5b528-163">自定义模型</span><span class="sxs-lookup"><span data-stu-id="5b528-163">Customizing the model</span></span>

<span data-ttu-id="5b528-164">EF Core 生成的代码是你的代码。</span><span class="sxs-lookup"><span data-stu-id="5b528-164">The code generated by EF Core is your code.</span></span> <span data-ttu-id="5b528-165">随意对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="5b528-165">Feel free to change it.</span></span> <span data-ttu-id="5b528-166">它将仅重新生成如果再次反向工程，相同的模型。</span><span class="sxs-lookup"><span data-stu-id="5b528-166">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="5b528-167">基架的代码表示*一个*模型，可用于访问数据库，但它当然不是*仅*可以使用的模型。</span><span class="sxs-lookup"><span data-stu-id="5b528-167">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="5b528-168">自定义实体类型类和 DbContext 类，以满足你的需求。</span><span class="sxs-lookup"><span data-stu-id="5b528-168">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="5b528-169">例如，你可以选择重命名类型和属性，引入了继承层次结构或拆分到多个实体的表。</span><span class="sxs-lookup"><span data-stu-id="5b528-169">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="5b528-170">此外可以从模型中删除非唯一索引、 未使用的序列和导航属性、 可选的标量属性和约束名称。</span><span class="sxs-lookup"><span data-stu-id="5b528-170">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="5b528-171">此外可以添加其他构造函数、 方法、 属性等。</span><span class="sxs-lookup"><span data-stu-id="5b528-171">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="5b528-172">在一个单独的文件中使用另一个分部类。</span><span class="sxs-lookup"><span data-stu-id="5b528-172">using another partial class in a separate file.</span></span> <span data-ttu-id="5b528-173">此方法适用于即使你想要再次反向工程的模型。</span><span class="sxs-lookup"><span data-stu-id="5b528-173">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="5b528-174">更新模型</span><span class="sxs-lookup"><span data-stu-id="5b528-174">Updating the model</span></span>

<span data-ttu-id="5b528-175">之后对数据库进行更改，可能需要更新您的 EF Core 模型以反映这些更改。</span><span class="sxs-lookup"><span data-stu-id="5b528-175">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="5b528-176">如果数据库更改很简单，它可能是最简单的方法手动对 EF Core 模型进行更改。</span><span class="sxs-lookup"><span data-stu-id="5b528-176">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="5b528-177">例如，重命名表或列、 删除列，或更新列的类型是无关紧要的更改以使在代码中。</span><span class="sxs-lookup"><span data-stu-id="5b528-177">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="5b528-178">更显著的更改，但是，不是为轻松进行手动。</span><span class="sxs-lookup"><span data-stu-id="5b528-178">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="5b528-179">一种常见的工作流是反向设计模型从再次使用的数据库`-Force`(PMC) 或`--force`(CLI) 使用已更新覆盖现有模型。</span><span class="sxs-lookup"><span data-stu-id="5b528-179">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="5b528-180">另一个经常请求的功能是能够从数据库更新模型同时还能保留自定义项，例如重命名、 类型层次结构，等等。使用问题[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)来跟踪此功能的进度。</span><span class="sxs-lookup"><span data-stu-id="5b528-180">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="5b528-181">如果进行反向工程的模型从数据库再次，您对文件所做的任何更改将丢失。</span><span class="sxs-lookup"><span data-stu-id="5b528-181">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>

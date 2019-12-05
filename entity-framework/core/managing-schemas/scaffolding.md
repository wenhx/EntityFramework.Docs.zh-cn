---
title: 反向工程-EF Core
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 1ba9352d261f1c131b0d70f8cdad2128d9afaefe
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824465"
---
# <a name="reverse-engineering"></a><span data-ttu-id="273b3-102">反向工程</span><span class="sxs-lookup"><span data-stu-id="273b3-102">Reverse Engineering</span></span>

<span data-ttu-id="273b3-103">反向工程是基架实体类型类的过程，以及基于数据库架构的 DbContext 类。</span><span class="sxs-lookup"><span data-stu-id="273b3-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="273b3-104">可以使用 EF Core 程序包管理器控制台（PMC）工具的 `Scaffold-DbContext` 命令或 .NET 命令行接口（CLI）工具的 `dotnet ef dbcontext scaffold` 命令来执行该命令。</span><span class="sxs-lookup"><span data-stu-id="273b3-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="273b3-105">安装</span><span class="sxs-lookup"><span data-stu-id="273b3-105">Installing</span></span>

<span data-ttu-id="273b3-106">在进行反向工程之前，你需要安装[PMC 工具](xref:core/miscellaneous/cli/powershell)（仅适用于 Visual Studio）或[CLI 工具](xref:core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="273b3-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="273b3-107">有关详细信息，请参阅链接。</span><span class="sxs-lookup"><span data-stu-id="273b3-107">See links for details.</span></span>

<span data-ttu-id="273b3-108">还需要为要进行反向工程的数据库架构安装适当的[数据库提供程序](xref:core/providers/index)。</span><span class="sxs-lookup"><span data-stu-id="273b3-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="273b3-109">连接字符串</span><span class="sxs-lookup"><span data-stu-id="273b3-109">Connection string</span></span>

<span data-ttu-id="273b3-110">该命令的第一个参数是指向数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="273b3-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="273b3-111">工具将使用此连接字符串来读取数据库架构。</span><span class="sxs-lookup"><span data-stu-id="273b3-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="273b3-112">引号和转义连接字符串的方式取决于您使用哪个 shell 执行命令。</span><span class="sxs-lookup"><span data-stu-id="273b3-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="273b3-113">有关详细信息，请参阅 shell 的文档。</span><span class="sxs-lookup"><span data-stu-id="273b3-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="273b3-114">例如，PowerShell 需要转义 `$` 字符，但不能 `\`。</span><span class="sxs-lookup"><span data-stu-id="273b3-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="273b3-115">配置和用户机密</span><span class="sxs-lookup"><span data-stu-id="273b3-115">Configuration and User Secrets</span></span>

<span data-ttu-id="273b3-116">如果有 ASP.NET Core 项目，则可以使用 `Name=<connection-string>` 语法从配置中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="273b3-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="273b3-117">这很适合用于[机密管理器工具](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager)，使数据库密码与代码库保持分离。</span><span class="sxs-lookup"><span data-stu-id="273b3-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="273b3-118">提供程序名称</span><span class="sxs-lookup"><span data-stu-id="273b3-118">Provider name</span></span>

<span data-ttu-id="273b3-119">第二个参数是提供程序名称。</span><span class="sxs-lookup"><span data-stu-id="273b3-119">The second argument is the provider name.</span></span> <span data-ttu-id="273b3-120">提供程序名称通常与提供程序的 NuGet 包名称相同。</span><span class="sxs-lookup"><span data-stu-id="273b3-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="273b3-121">指定表</span><span class="sxs-lookup"><span data-stu-id="273b3-121">Specifying tables</span></span>

<span data-ttu-id="273b3-122">默认情况下，将数据库架构中的所有表反向工程为实体类型。</span><span class="sxs-lookup"><span data-stu-id="273b3-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="273b3-123">您可以通过指定架构和表来限制对哪些表进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="273b3-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="273b3-124">PMC 中的 `-Schemas` 参数和 CLI 中的 `--schema` 选项可用于包含架构中的每个表。</span><span class="sxs-lookup"><span data-stu-id="273b3-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="273b3-125">`-Tables` （PMC）和 `--table` （CLI）可用于包含特定表。</span><span class="sxs-lookup"><span data-stu-id="273b3-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="273b3-126">若要在 PMC 中包括多个表，请使用数组。</span><span class="sxs-lookup"><span data-stu-id="273b3-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="273b3-127">若要在 CLI 中包含多个表，请多次指定选项。</span><span class="sxs-lookup"><span data-stu-id="273b3-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="273b3-128">保留名称</span><span class="sxs-lookup"><span data-stu-id="273b3-128">Preserving names</span></span>

<span data-ttu-id="273b3-129">默认情况下，表和列名已固定，以便更好地匹配类型和属性的 .NET 命名约定。</span><span class="sxs-lookup"><span data-stu-id="273b3-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="273b3-130">在 PMC 中指定 `-UseDatabaseNames` 开关或在 CLI 中指定 `--use-database-names` 选项将禁用这一行为，从而尽可能保留原始数据库名称。</span><span class="sxs-lookup"><span data-stu-id="273b3-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="273b3-131">无效的 .NET 标识符仍然是固定的，而合成的名称（如导航属性）仍符合 .NET 命名约定。</span><span class="sxs-lookup"><span data-stu-id="273b3-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="273b3-132">熟知 API 或数据批注</span><span class="sxs-lookup"><span data-stu-id="273b3-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="273b3-133">默认情况下，实体类型是使用熟知 API 配置的。</span><span class="sxs-lookup"><span data-stu-id="273b3-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="273b3-134">将 `-DataAnnotations` （PMC）或 `--data-annotations` （CLI）指定为尽可能使用数据批注。</span><span class="sxs-lookup"><span data-stu-id="273b3-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="273b3-135">例如，使用熟知的 API 将基架：</span><span class="sxs-lookup"><span data-stu-id="273b3-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="273b3-136">使用数据注释时，将基架：</span><span class="sxs-lookup"><span data-stu-id="273b3-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="273b3-137">DbContext 名称</span><span class="sxs-lookup"><span data-stu-id="273b3-137">DbContext name</span></span>

<span data-ttu-id="273b3-138">默认情况下，基架 DbContext 类名称将是以默认值作为*后缀的数据库*的名称。</span><span class="sxs-lookup"><span data-stu-id="273b3-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="273b3-139">若要指定其他帐户，请使用 PMC 中的 `-Context`，并在 CLI 中 `--context`。</span><span class="sxs-lookup"><span data-stu-id="273b3-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="273b3-140">目录和命名空间</span><span class="sxs-lookup"><span data-stu-id="273b3-140">Directories and namespaces</span></span>

<span data-ttu-id="273b3-141">实体类和 DbContext 类将基架到项目的根目录中，并使用项目的默认命名空间。</span><span class="sxs-lookup"><span data-stu-id="273b3-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="273b3-142">可以使用 `-OutputDir` （PMC）或 `--output-dir` （CLI）指定基架的目录。</span><span class="sxs-lookup"><span data-stu-id="273b3-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="273b3-143">命名空间将为根命名空间加上项目根目录下的任何子目录的名称。</span><span class="sxs-lookup"><span data-stu-id="273b3-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="273b3-144">你还可以使用 `-ContextDir` （PMC）和 `--context-dir` （CLI）将 DbContext 类基架到不同于实体类型类的目录中。</span><span class="sxs-lookup"><span data-stu-id="273b3-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="273b3-145">工作原理</span><span class="sxs-lookup"><span data-stu-id="273b3-145">How it works</span></span>

<span data-ttu-id="273b3-146">反向工程从读取数据库架构开始。</span><span class="sxs-lookup"><span data-stu-id="273b3-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="273b3-147">它读取有关表、列、约束和索引的信息。</span><span class="sxs-lookup"><span data-stu-id="273b3-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="273b3-148">接下来，它使用架构信息创建 EF Core 模型。</span><span class="sxs-lookup"><span data-stu-id="273b3-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="273b3-149">表用于创建实体类型;列用于创建属性;和外键用于创建关系。</span><span class="sxs-lookup"><span data-stu-id="273b3-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="273b3-150">最后，模型用于生成代码。</span><span class="sxs-lookup"><span data-stu-id="273b3-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="273b3-151">为了从应用程序重新创建相同的模型，相应的实体类型类、熟知 API 和数据批注都是基架的。</span><span class="sxs-lookup"><span data-stu-id="273b3-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="273b3-152">限制</span><span class="sxs-lookup"><span data-stu-id="273b3-152">Limitations</span></span>

* <span data-ttu-id="273b3-153">不是有关模型的所有内容都可以使用数据库架构来表示。</span><span class="sxs-lookup"><span data-stu-id="273b3-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="273b3-154">例如，有关[**继承层次结构**](../modeling/inheritance.md)、[**附属类型**](../modeling/owned-entities.md)和[**表拆分**](../modeling/table-splitting.md)的信息在数据库架构中不存在。</span><span class="sxs-lookup"><span data-stu-id="273b3-154">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="273b3-155">因此，这些构造永远不会经过反向工程。</span><span class="sxs-lookup"><span data-stu-id="273b3-155">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="273b3-156">此外，EF Core 提供程序可能不支持**某些列类型**。</span><span class="sxs-lookup"><span data-stu-id="273b3-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="273b3-157">这些列不会包含在模型中。</span><span class="sxs-lookup"><span data-stu-id="273b3-157">These columns won't be included in the model.</span></span>
* <span data-ttu-id="273b3-158">可以在 EF Core 模型中定义[**并发标记**](../modeling/concurrency.md)，以防止两个用户同时更新同一实体。</span><span class="sxs-lookup"><span data-stu-id="273b3-158">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="273b3-159">某些数据库有一种特殊类型的类型来表示此类型的列（例如 SQL Server 中的 rowversion），在这种情况下，我们可以对此信息进行反向工程。但是，其他并发令牌不会进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="273b3-159">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="273b3-160">[反向C#工程当前不支持8可为 null 的引用类型功能](/dotnet/csharp/tutorials/nullable-reference-types)： EF Core 始终C#会生成假定禁用该功能的代码。</span><span class="sxs-lookup"><span data-stu-id="273b3-160">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="273b3-161">例如，可为 null 的文本列将被基架为具有类型 `string` 的属性，而不是 `string?`，其中使用的是用于配置是否需要属性的流畅 API 或数据批注。</span><span class="sxs-lookup"><span data-stu-id="273b3-161">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="273b3-162">您可以编辑基架代码并将其替换为C#可为 null 的批注。</span><span class="sxs-lookup"><span data-stu-id="273b3-162">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="273b3-163">[#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520)的问题跟踪了可为 null 的引用类型的基架支持。</span><span class="sxs-lookup"><span data-stu-id="273b3-163">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="273b3-164">自定义模型</span><span class="sxs-lookup"><span data-stu-id="273b3-164">Customizing the model</span></span>

<span data-ttu-id="273b3-165">EF Core 生成的代码是您的代码。</span><span class="sxs-lookup"><span data-stu-id="273b3-165">The code generated by EF Core is your code.</span></span> <span data-ttu-id="273b3-166">随意更改。</span><span class="sxs-lookup"><span data-stu-id="273b3-166">Feel free to change it.</span></span> <span data-ttu-id="273b3-167">仅当您再次对同一模型进行反向工程时，才会重新生成它。</span><span class="sxs-lookup"><span data-stu-id="273b3-167">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="273b3-168">基架代码表示*一个*可用于访问数据库的模型，但它当然不是*唯一*可以使用的模型。</span><span class="sxs-lookup"><span data-stu-id="273b3-168">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="273b3-169">根据需要自定义实体类型类和 DbContext 类。</span><span class="sxs-lookup"><span data-stu-id="273b3-169">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="273b3-170">例如，你可以选择重命名类型和属性、引入继承层次结构或将表拆分为多个实体。</span><span class="sxs-lookup"><span data-stu-id="273b3-170">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="273b3-171">还可以从模型中删除非唯一索引、未使用的序列和导航属性、可选的标量属性和约束名称。</span><span class="sxs-lookup"><span data-stu-id="273b3-171">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="273b3-172">还可以添加其他构造函数、方法、属性等。</span><span class="sxs-lookup"><span data-stu-id="273b3-172">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="273b3-173">在单独的文件中使用另一个分部类。</span><span class="sxs-lookup"><span data-stu-id="273b3-173">using another partial class in a separate file.</span></span> <span data-ttu-id="273b3-174">即使您要再次对模型进行反向工程，此方法也能正常工作。</span><span class="sxs-lookup"><span data-stu-id="273b3-174">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="273b3-175">更新模型</span><span class="sxs-lookup"><span data-stu-id="273b3-175">Updating the model</span></span>

<span data-ttu-id="273b3-176">更改数据库后，可能需要更新 EF Core 模型以反映这些更改。</span><span class="sxs-lookup"><span data-stu-id="273b3-176">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="273b3-177">如果数据库更改很简单，只需手动对 EF Core 模型进行更改即可。</span><span class="sxs-lookup"><span data-stu-id="273b3-177">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="273b3-178">例如，对表或列进行重命名、删除列或更新列的类型是在代码中进行的一些简单的更改。</span><span class="sxs-lookup"><span data-stu-id="273b3-178">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="273b3-179">但是，更重要的更改并不容易手动完成。</span><span class="sxs-lookup"><span data-stu-id="273b3-179">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="273b3-180">一个常见的工作流是通过使用 `-Force` （PMC）或 `--force` （CLI），使用更新的模型覆盖现有模型，从数据库反向对模型进行反向工程。</span><span class="sxs-lookup"><span data-stu-id="273b3-180">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="273b3-181">另一个常请求的功能是能够从数据库更新模型，同时保留自定义项（如重命名、类型层次结构等）。使用问题[#831](https://github.com/aspnet/EntityFrameworkCore/issues/831)跟踪此功能的进度。</span><span class="sxs-lookup"><span data-stu-id="273b3-181">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="273b3-182">如果您从数据库中再次对模型进行反向工程，则对这些文件所做的任何更改都将丢失。</span><span class="sxs-lookup"><span data-stu-id="273b3-182">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>

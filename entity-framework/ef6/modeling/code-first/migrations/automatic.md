---
title: 自动 Code First 迁移-EF6
description: 实体框架6中的自动 Code First 迁移
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: 541adf5aed517685b54d675730996c12ff0c824a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618088"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="3ec27-103">自动 Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="3ec27-103">Automatic Code First Migrations</span></span>
<span data-ttu-id="3ec27-104">自动迁移使你可以使用 Code First 迁移，而不会在项目中为你所做的每个更改提供代码文件。</span><span class="sxs-lookup"><span data-stu-id="3ec27-104">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="3ec27-105">并非所有更改都可以自动应用-例如，列重命名需要使用基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-105">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="3ec27-106">本文假设你知道如何在基本方案中使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-106">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="3ec27-107">如果没有，则需要先阅读 [Code First 迁移](xref:ef6/modeling/code-first/migrations/index) ，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="3ec27-107">If you don’t, then you’ll need to read [Code First Migrations](xref:ef6/modeling/code-first/migrations/index) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="3ec27-108">团队环境建议</span><span class="sxs-lookup"><span data-stu-id="3ec27-108">Recommendation for Team Environments</span></span>

<span data-ttu-id="3ec27-109">你可以点播自动和基于代码的迁移，但不建议在团队开发方案中使用。</span><span class="sxs-lookup"><span data-stu-id="3ec27-109">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="3ec27-110">如果你是使用源代码管理的开发人员团队的成员，则应使用纯粹自动迁移或纯粹基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-110">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="3ec27-111">考虑到自动迁移的限制，我们建议在团队环境中使用基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-111">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="3ec27-112">生成初始模型和数据库</span><span class="sxs-lookup"><span data-stu-id="3ec27-112">Building an Initial Model & Database</span></span>

<span data-ttu-id="3ec27-113">开始使用迁移之前，需要会用到项目和 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="3ec27-113">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="3ec27-114">对于此演练，我们将使用规范的“博客”和“帖子”模型   。</span><span class="sxs-lookup"><span data-stu-id="3ec27-114">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="3ec27-115">创建新的 **MigrationsAutomaticDemo** 控制台应用程序</span><span class="sxs-lookup"><span data-stu-id="3ec27-115">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="3ec27-116">将最新版本的 EntityFramework NuGet 包添加到项目中 </span><span class="sxs-lookup"><span data-stu-id="3ec27-116">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="3ec27-117">“工具”–&gt;“库包管理器”–&gt;“包管理器控制台” </span><span class="sxs-lookup"><span data-stu-id="3ec27-117">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="3ec27-118">运行 Install-Package EntityFramework 命令 </span><span class="sxs-lookup"><span data-stu-id="3ec27-118">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="3ec27-119">添加 Model.cs 文件，其代码如下所示  。</span><span class="sxs-lookup"><span data-stu-id="3ec27-119">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="3ec27-120">此代码定义了构成域模型的单个“博客”类和 EF Code First 上下文 BlogContext 类  </span><span class="sxs-lookup"><span data-stu-id="3ec27-120">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   <span data-ttu-id="3ec27-121">现在我们拥有一个模型，可用它执行数据访问操作。</span><span class="sxs-lookup"><span data-stu-id="3ec27-121">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="3ec27-122">更新 Program.cs 文件，其代码如下所示  。</span><span class="sxs-lookup"><span data-stu-id="3ec27-122">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   <span data-ttu-id="3ec27-123">运行你的应用程序，你将看到已为你创建 **MigrationsAutomaticCodeDemo. BlogContext** 数据库。</span><span class="sxs-lookup"><span data-stu-id="3ec27-123">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![数据库 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="3ec27-125">启用迁移</span><span class="sxs-lookup"><span data-stu-id="3ec27-125">Enabling Migrations</span></span>

<span data-ttu-id="3ec27-126">现可对模型进行更多更改。</span><span class="sxs-lookup"><span data-stu-id="3ec27-126">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="3ec27-127">将 Url 属性引入“博客”类。</span><span class="sxs-lookup"><span data-stu-id="3ec27-127">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="3ec27-128">如果要再次运行应用程序，则会收到一个 InvalidOperationException，指出“创建数据库后，支持‘BlogContext’上下文的模型已发生变化。  请考虑使用 Code First 迁移更新数据库 ( [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*</span><span class="sxs-lookup"><span data-stu-id="3ec27-128">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="3ec27-129">如异常情况所述，可开始使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-129">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="3ec27-130">由于我们要使用自动迁移，因此，我们将指定 **– EnableAutomaticMigrations** 开关。</span><span class="sxs-lookup"><span data-stu-id="3ec27-130">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="3ec27-131">在包管理器控制台中运行 " **启用-迁移– EnableAutomaticMigrations** " 命令。此命令已向项目添加了 " **迁移** " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="3ec27-131">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="3ec27-132">此新文件夹包含一个文件：</span><span class="sxs-lookup"><span data-stu-id="3ec27-132">This new folder contains one file:</span></span>

-   <span data-ttu-id="3ec27-133">**配置类。**</span><span class="sxs-lookup"><span data-stu-id="3ec27-133">**The Configuration class.**</span></span> <span data-ttu-id="3ec27-134">此类允许配置迁移对上下文的行为方式。</span><span class="sxs-lookup"><span data-stu-id="3ec27-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="3ec27-135">对于此演练，将只使用默认配置。</span><span class="sxs-lookup"><span data-stu-id="3ec27-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="3ec27-136">由于项目中只有一个 Code First 上下文，因此 Enable-Migrations 已自动填充此配置适用的上下文类型。 </span><span class="sxs-lookup"><span data-stu-id="3ec27-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="3ec27-137">第一次自动迁移</span><span class="sxs-lookup"><span data-stu-id="3ec27-137">Your First Automatic Migration</span></span>

<span data-ttu-id="3ec27-138">Code First 迁移具有两个需要用户了解的主要命令。</span><span class="sxs-lookup"><span data-stu-id="3ec27-138">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="3ec27-139">Add-Migration 将基于自上次迁移创建以来对模型所做的更改来构建下一次迁移 </span><span class="sxs-lookup"><span data-stu-id="3ec27-139">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="3ec27-140">Update-Database 将对数据库应用任意挂起的迁移 </span><span class="sxs-lookup"><span data-stu-id="3ec27-140">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="3ec27-141">我们将避免使用添加迁移 (，除非我们确实需要) 并专注于让 Code First 迁移自动计算和应用更改。</span><span class="sxs-lookup"><span data-stu-id="3ec27-141">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="3ec27-142">让我们使用 **更新数据库** 来 Code First 迁移将更改推送到模型 (新的 **Ur**l 属性) 写入数据库。</span><span class="sxs-lookup"><span data-stu-id="3ec27-142">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="3ec27-143">在 Package Manager Console 中运行 " **更新数据库** " 命令。</span><span class="sxs-lookup"><span data-stu-id="3ec27-143">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="3ec27-144">**MigrationsAutomaticDemo BlogContext**数据库现在已更新为包含**博客**表中的**Url**列。</span><span class="sxs-lookup"><span data-stu-id="3ec27-144">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="3ec27-145">第二次自动迁移</span><span class="sxs-lookup"><span data-stu-id="3ec27-145">Your Second Automatic Migration</span></span>

<span data-ttu-id="3ec27-146">接下来，让我们进行其他更改，并让 Code First 迁移自动将更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="3ec27-146">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="3ec27-147">同时添加一个新的“帖子”类 </span><span class="sxs-lookup"><span data-stu-id="3ec27-147">Let's also add a new **Post** class</span></span>

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   <span data-ttu-id="3ec27-148">此外，再将“帖子”集合添加到“博客”类，以形成“博客”和“帖子”之间的另一层关系    </span><span class="sxs-lookup"><span data-stu-id="3ec27-148">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="3ec27-149">现在使用 **更新数据库** 使数据库保持最新状态。</span><span class="sxs-lookup"><span data-stu-id="3ec27-149">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="3ec27-150">这次指定 –Verbose 标志，以便可以看到 Code First 迁移正在运行的 SQL  。</span><span class="sxs-lookup"><span data-stu-id="3ec27-150">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="3ec27-151">在包管理器控制台中运行 Update-Database –Verbose 命令  。</span><span class="sxs-lookup"><span data-stu-id="3ec27-151">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="3ec27-152">添加基于代码的迁移</span><span class="sxs-lookup"><span data-stu-id="3ec27-152">Adding a Code Based Migration</span></span>

<span data-ttu-id="3ec27-153">现在，让我们看看我们可能要对使用基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-153">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="3ec27-154">让我们向**博客**类添加**评级**属性</span><span class="sxs-lookup"><span data-stu-id="3ec27-154">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="3ec27-155">只需运行 " **更新数据库** " 即可将这些更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="3ec27-155">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="3ec27-156">但是，我们添加了不可为 null 的 **博客。分级** 列，如果表中存在现有数据，则会向其分配新列的数据类型的 CLR 默认值 (分级是整数，因此将为 **0**) 。</span><span class="sxs-lookup"><span data-stu-id="3ec27-156">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="3ec27-157">但我们要将默认值指定为 3，以便“博客”表中的现有行以合适的分级开始   。</span><span class="sxs-lookup"><span data-stu-id="3ec27-157">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="3ec27-158">让我们使用 "添加-迁移" 命令将此更改写入到基于代码的迁移，以便可以对其进行编辑。</span><span class="sxs-lookup"><span data-stu-id="3ec27-158">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="3ec27-159">" **添加-迁移** " 命令使我们可以为这些迁移命名，只需调用我们的 **AddBlogRating**即可。</span><span class="sxs-lookup"><span data-stu-id="3ec27-159">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="3ec27-160">在 Package Manager Console 中运行 **Add-迁移 AddBlogRating** 命令。</span><span class="sxs-lookup"><span data-stu-id="3ec27-160">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="3ec27-161">在 " **迁移** " 文件夹中，我们现在已有一个新的 **AddBlogRating** 迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-161">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="3ec27-162">迁移文件名预先修复了时间戳，以帮助进行排序。</span><span class="sxs-lookup"><span data-stu-id="3ec27-162">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="3ec27-163">让我们编辑生成的代码，为博客指定默认值 3 (第10行) </span><span class="sxs-lookup"><span data-stu-id="3ec27-163">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="3ec27-164">*迁移还具有捕获某些元数据的代码隐藏文件。此元数据将允许 Code First 迁移复制在此基于代码的迁移之前执行的自动迁移。如果另一位开发人员想要运行迁移或部署应用程序，这一点非常重要。*</span><span class="sxs-lookup"><span data-stu-id="3ec27-164">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

<span data-ttu-id="3ec27-165">已编辑的迁移一切正常，所以我们可使用 Update-Database 来更新数据库  。</span><span class="sxs-lookup"><span data-stu-id="3ec27-165">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="3ec27-166">在 Package Manager Console 中运行 " **更新数据库** " 命令。</span><span class="sxs-lookup"><span data-stu-id="3ec27-166">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="3ec27-167">返回到自动迁移</span><span class="sxs-lookup"><span data-stu-id="3ec27-167">Back to Automatic Migrations</span></span>

<span data-ttu-id="3ec27-168">现在，我们可以免费切换回自动迁移，以便进行更简单的更改。</span><span class="sxs-lookup"><span data-stu-id="3ec27-168">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="3ec27-169">Code First 迁移将按照在每个基于代码的迁移的代码隐藏文件中存储的元数据，以正确的顺序执行自动和基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-169">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="3ec27-170">让我们向模型中添加一个 Post. Abstract 属性</span><span class="sxs-lookup"><span data-stu-id="3ec27-170">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="3ec27-171">现在，我们可以使用 **更新数据库** 来获取 Code First 迁移，使用自动迁移将此更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="3ec27-171">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="3ec27-172">在 Package Manager Console 中运行 " **更新数据库** " 命令。</span><span class="sxs-lookup"><span data-stu-id="3ec27-172">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="3ec27-173">总结</span><span class="sxs-lookup"><span data-stu-id="3ec27-173">Summary</span></span>

<span data-ttu-id="3ec27-174">在本演练中，您学习了如何使用自动迁移将模型更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="3ec27-174">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="3ec27-175">还了解了在需要更多控制时，如何在自动迁移之间基架和运行基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="3ec27-175">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>

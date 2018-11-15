---
title: 自动 Code First 迁移的 EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283909"
---
# <a name="automatic-code-first-migrations"></a><span data-ttu-id="45ecd-102">自动 Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-102">Automatic Code First Migrations</span></span>
<span data-ttu-id="45ecd-103">自动迁移，可使用 Code First 迁移，而无需每个所做的更改在项目中的代码文件。</span><span class="sxs-lookup"><span data-stu-id="45ecd-103">Automatic Migrations allows you to use Code First Migrations without having a code file in your project for each change you make.</span></span> <span data-ttu-id="45ecd-104">并非所有更改都将自动都应用-例如列重命名要求使用基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-104">Not all changes can be applied automatically - for example column renames require the use of a code-based migration.</span></span>

> [!NOTE]
> <span data-ttu-id="45ecd-105">本文假定你知道如何在基本方案中使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="45ecd-106">如果不这样做，则你将需要读取[Code First 迁移](~/ef6/modeling/code-first/migrations/index.md)然后再继续。</span><span class="sxs-lookup"><span data-stu-id="45ecd-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="recommendation-for-team-environments"></a><span data-ttu-id="45ecd-107">对于团队环境的建议</span><span class="sxs-lookup"><span data-stu-id="45ecd-107">Recommendation for Team Environments</span></span>

<span data-ttu-id="45ecd-108">可以自动和基于代码的迁移单引号分隔文件，但这不建议在团队开发方案。</span><span class="sxs-lookup"><span data-stu-id="45ecd-108">You can intersperse automatic and code-based migrations but this is not recommended in team development scenarios.</span></span> <span data-ttu-id="45ecd-109">如果您属于一组使用源控件的开发人员应使用完全自动迁移或纯粹基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-109">If you are part of a team of developers that use source control you should either use purely automatic migrations or purely code-based migrations.</span></span> <span data-ttu-id="45ecd-110">给定的自动迁移的限制，我们建议在团队环境中使用基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-110">Given the limitations of automatic migrations we recommend using code-based migrations in team environments.</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="45ecd-111">生成初始模型和数据库</span><span class="sxs-lookup"><span data-stu-id="45ecd-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="45ecd-112">开始使用迁移之前，需要会用到项目和 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="45ecd-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="45ecd-113">对于此演练，我们将使用规范的“博客”和“帖子”模型。</span><span class="sxs-lookup"><span data-stu-id="45ecd-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="45ecd-114">创建一个新**MigrationsAutomaticDemo**控制台应用程序</span><span class="sxs-lookup"><span data-stu-id="45ecd-114">Create a new **MigrationsAutomaticDemo** Console application</span></span>
-   <span data-ttu-id="45ecd-115">将最新版本的 EntityFramework NuGet 包添加到项目中</span><span class="sxs-lookup"><span data-stu-id="45ecd-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="45ecd-116">“工具”–&gt;“库包管理器”–&gt;“包管理器控制台”</span><span class="sxs-lookup"><span data-stu-id="45ecd-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="45ecd-117">运行 Install-Package EntityFramework 命令</span><span class="sxs-lookup"><span data-stu-id="45ecd-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="45ecd-118">添加 Model.cs 文件，其代码如下所示。</span><span class="sxs-lookup"><span data-stu-id="45ecd-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="45ecd-119">此代码定义了构成域模型的单个“博客”类和 EF Code First 上下文 BlogContext 类</span><span class="sxs-lookup"><span data-stu-id="45ecd-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

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

-   <span data-ttu-id="45ecd-120">现在我们拥有一个模型，可用它执行数据访问操作。</span><span class="sxs-lookup"><span data-stu-id="45ecd-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="45ecd-121">更新 Program.cs 文件，其代码如下所示。</span><span class="sxs-lookup"><span data-stu-id="45ecd-121">Update the **Program.cs** file with the code shown below.</span></span>

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

-   <span data-ttu-id="45ecd-122">运行应用程序并将会看到**MigrationsAutomaticCodeDemo.BlogContext**为您创建数据库。</span><span class="sxs-lookup"><span data-stu-id="45ecd-122">Run your application and you will see that a **MigrationsAutomaticCodeDemo.BlogContext** database is created for you.</span></span>

    ![数据库 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="45ecd-124">启用迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-124">Enabling Migrations</span></span>

<span data-ttu-id="45ecd-125">现可对模型进行更多更改。</span><span class="sxs-lookup"><span data-stu-id="45ecd-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="45ecd-126">将 Url 属性引入“博客”类。</span><span class="sxs-lookup"><span data-stu-id="45ecd-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="45ecd-127">如果要再次运行应用程序，则会收到一个 InvalidOperationException，指出“创建数据库后，支持‘BlogContext’上下文的模型已发生变化。请考虑使用 Code First 迁移更新数据库 ([http://go.microsoft.com/fwlink/?LinkId=238269](https://go.microsoft.com/fwlink/?LinkId=238269))”\*。</span><span class="sxs-lookup"><span data-stu-id="45ecd-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="45ecd-128">如异常情况所述，可开始使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="45ecd-129">因为我们想要使用自动迁移，我们要指定 **– EnableAutomaticMigrations**切换。</span><span class="sxs-lookup"><span data-stu-id="45ecd-129">Because we want to use automatic migrations we’re going to specify the **–EnableAutomaticMigrations** switch.</span></span>

-   <span data-ttu-id="45ecd-130">运行**Enable-migrations – EnableAutomaticMigrations**添加了包管理器控制台此命令中的命令**迁移**到我们的项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="45ecd-130">Run the **Enable-Migrations –EnableAutomaticMigrations** command in Package Manager Console This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="45ecd-131">此新文件夹包含一个文件：</span><span class="sxs-lookup"><span data-stu-id="45ecd-131">This new folder contains one file:</span></span>

-   <span data-ttu-id="45ecd-132">**配置类。**</span><span class="sxs-lookup"><span data-stu-id="45ecd-132">**The Configuration class.**</span></span> <span data-ttu-id="45ecd-133">此类允许配置迁移对上下文的行为方式。</span><span class="sxs-lookup"><span data-stu-id="45ecd-133">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="45ecd-134">对于此演练，将只使用默认配置。</span><span class="sxs-lookup"><span data-stu-id="45ecd-134">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="45ecd-135">由于项目中只有一个 Code First 上下文，因此 Enable-Migrations 已自动填充此配置适用的上下文类型。</span><span class="sxs-lookup"><span data-stu-id="45ecd-135">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>

 

## <a name="your-first-automatic-migration"></a><span data-ttu-id="45ecd-136">第一个自动迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-136">Your First Automatic Migration</span></span>

<span data-ttu-id="45ecd-137">Code First 迁移具有两个需要用户了解的主要命令。</span><span class="sxs-lookup"><span data-stu-id="45ecd-137">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="45ecd-138">Add-Migration 将基于自上次迁移创建以来对模型所做的更改来构建下一次迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-138">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="45ecd-139">Update-Database 将对数据库应用任意挂起的迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-139">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="45ecd-140">我们要避免使用添加迁移 （除非我们真的有必要），专注于让 Code First 迁移自动计算并应用更改。</span><span class="sxs-lookup"><span data-stu-id="45ecd-140">We are going to avoid using Add-Migration (unless we really need to) and focus on letting Code First Migrations automatically calculate and apply the changes.</span></span> <span data-ttu-id="45ecd-141">让我们使用**Update-database**若要获取代码优先迁移来将更改推送到我们的模型 (新**Blog.Ur**l 属性) 到数据库。</span><span class="sxs-lookup"><span data-stu-id="45ecd-141">Let’s use **Update-Database** to get Code First Migrations to push the changes to our model (the new **Blog.Ur**l property) to the database.</span></span>

-   <span data-ttu-id="45ecd-142">运行**Update-database**程序包管理器控制台命令。</span><span class="sxs-lookup"><span data-stu-id="45ecd-142">Run the **Update-Database** command in Package Manager Console.</span></span>

<span data-ttu-id="45ecd-143">**MigrationsAutomaticDemo.BlogContext**数据库现已更新以包括**Url**中的列**博客**表。</span><span class="sxs-lookup"><span data-stu-id="45ecd-143">The **MigrationsAutomaticDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

 

## <a name="your-second-automatic-migration"></a><span data-ttu-id="45ecd-144">第二个自动迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-144">Your Second Automatic Migration</span></span>

<span data-ttu-id="45ecd-145">让我们进行一次更改并让代码优先迁移自动推送到数据库为我们所做的更改。</span><span class="sxs-lookup"><span data-stu-id="45ecd-145">Let’s make another change and let Code First Migrations automatically push the changes to the database for us.</span></span>

-   <span data-ttu-id="45ecd-146">同时添加一个新的“帖子”类</span><span class="sxs-lookup"><span data-stu-id="45ecd-146">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="45ecd-147">此外，再将“帖子”集合添加到“博客”类，以形成“博客”和“帖子”之间的另一层关系</span><span class="sxs-lookup"><span data-stu-id="45ecd-147">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="45ecd-148">现在，使用**Update-database**使数据库保持最新状态。</span><span class="sxs-lookup"><span data-stu-id="45ecd-148">Now use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="45ecd-149">这次指定 –Verbose 标志，以便可以看到 Code First 迁移正在运行的 SQL。</span><span class="sxs-lookup"><span data-stu-id="45ecd-149">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="45ecd-150">在包管理器控制台中运行 Update-Database –Verbose 命令。</span><span class="sxs-lookup"><span data-stu-id="45ecd-150">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="adding-a-code-based-migration"></a><span data-ttu-id="45ecd-151">将代码添加基于迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-151">Adding a Code Based Migration</span></span>

<span data-ttu-id="45ecd-152">现在让我们看看我们可能想要使用的基于代码的迁移的内容。</span><span class="sxs-lookup"><span data-stu-id="45ecd-152">Now let’s look at something we might want to use a code-based migration for.</span></span>

-   <span data-ttu-id="45ecd-153">让我们添加**评级**属性设置为**博客**类</span><span class="sxs-lookup"><span data-stu-id="45ecd-153">Let’s add a **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

<span data-ttu-id="45ecd-154">我们可能只需运行**Update-database**这些更改推送到数据库。</span><span class="sxs-lookup"><span data-stu-id="45ecd-154">We could just run **Update-Database** to push these changes to the database.</span></span> <span data-ttu-id="45ecd-155">但是，我们将添加不可为 null **Blogs.Rating**列中，如果表中的任何现有数据将获取其分配新的列的数据类型的 CLR 默认值 (分级是整数，那么这是**0**).</span><span class="sxs-lookup"><span data-stu-id="45ecd-155">However, we're adding a non-nullable **Blogs.Rating** column, if there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="45ecd-156">但我们要将默认值指定为 3，以便“博客”表中的现有行以合适的分级开始。</span><span class="sxs-lookup"><span data-stu-id="45ecd-156">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
<span data-ttu-id="45ecd-157">让我们使用 Add-migration 命令对基于代码的迁移编写出此更改，以便我们可以对其进行编辑。</span><span class="sxs-lookup"><span data-stu-id="45ecd-157">Let’s use the Add-Migration command to write this change out to a code-based migration so that we can edit it.</span></span> <span data-ttu-id="45ecd-158">**Add-migration**命令使我们能够为这些迁移提供一个名称，让我们只需调用我们的愿景**AddBlogRating**。</span><span class="sxs-lookup"><span data-stu-id="45ecd-158">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogRating**.</span></span>

-   <span data-ttu-id="45ecd-159">运行**Add-migration AddBlogRating**程序包管理器控制台命令。</span><span class="sxs-lookup"><span data-stu-id="45ecd-159">Run the **Add-Migration AddBlogRating** command in Package Manager Console.</span></span>
-   <span data-ttu-id="45ecd-160">在中**迁移**文件夹现在我们有了一个新**AddBlogRating**迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-160">In the **Migrations** folder we now have a new **AddBlogRating** migration.</span></span> <span data-ttu-id="45ecd-161">预先使用时间戳修复迁移文件名以帮助进行排序。</span><span class="sxs-lookup"><span data-stu-id="45ecd-161">The migration filename is pre-fixed with a timestamp to help with ordering.</span></span> <span data-ttu-id="45ecd-162">让我们编辑生成的代码以指定默认值为 3 的 Blog.Rating (在下面的代码行 10)</span><span class="sxs-lookup"><span data-stu-id="45ecd-162">Let’s edit the generated code to specify a default value of 3 for Blog.Rating (Line 10 in the code below)</span></span>

<span data-ttu-id="45ecd-163">*迁移还具有捕获一些元数据的代码隐藏文件。此元数据将允许代码优先迁移来复制此代码基于在迁移之前，我们通过自动迁移。如果其他开发人员想要运行我们的迁移或部署我们的应用程序时间时，这非常重要。*</span><span class="sxs-lookup"><span data-stu-id="45ecd-163">*The migration also has a code-behind file that captures some metadata. This metadata will allow Code First Migrations to replicate the automatic migrations we performed before this code-based migration. This is important if another developer wants to run our migrations or when it’s time to deploy our application.*</span></span>

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

<span data-ttu-id="45ecd-164">已编辑的迁移一切正常，所以我们可使用 Update-Database 来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="45ecd-164">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span>

-   <span data-ttu-id="45ecd-165">运行**Update-database**程序包管理器控制台命令。</span><span class="sxs-lookup"><span data-stu-id="45ecd-165">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="back-to-automatic-migrations"></a><span data-ttu-id="45ecd-166">返回到自动迁移</span><span class="sxs-lookup"><span data-stu-id="45ecd-166">Back to Automatic Migrations</span></span>

<span data-ttu-id="45ecd-167">我们现在可以随意切换回自动迁移的我们更简单的更改。</span><span class="sxs-lookup"><span data-stu-id="45ecd-167">We are now free to switch back to automatic migrations for our simpler changes.</span></span> <span data-ttu-id="45ecd-168">Code First 迁移将负责在正确的顺序基于每个基于代码的迁移的代码隐藏文件中存储的元数据中执行自动和基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-168">Code First Migrations will take care of performing the automatic and code-based migrations in the correct order based on the metadata it is storing in the code-behind file for each code-based migration.</span></span>

-   <span data-ttu-id="45ecd-169">让我们将 Post.Abstract 属性添加到我们的模型</span><span class="sxs-lookup"><span data-stu-id="45ecd-169">Let’s add a Post.Abstract property to our model</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="45ecd-170">现在我们可以使用**Update-database**获取代码优先迁移来将此更改推送到使用自动迁移的数据库。</span><span class="sxs-lookup"><span data-stu-id="45ecd-170">Now we can use **Update-Database** to get Code First Migrations to push this change to the database using an automatic migration.</span></span>

-   <span data-ttu-id="45ecd-171">运行**Update-database**程序包管理器控制台命令。</span><span class="sxs-lookup"><span data-stu-id="45ecd-171">Run the **Update-Database** command in Package Manager Console.</span></span>

## <a name="summary"></a><span data-ttu-id="45ecd-172">总结</span><span class="sxs-lookup"><span data-stu-id="45ecd-172">Summary</span></span>

<span data-ttu-id="45ecd-173">在本演练中了解到如何使用自动迁移推送模型对数据库进行更改。</span><span class="sxs-lookup"><span data-stu-id="45ecd-173">In this walkthrough you saw how to use automatic migrations to push model changes to the database.</span></span> <span data-ttu-id="45ecd-174">您还了解到如何搭建基架，并在需要更多控制时运行之间自动迁移基于代码的迁移。</span><span class="sxs-lookup"><span data-stu-id="45ecd-174">You also saw how to scaffold and run code-based migrations in between automatic migrations when you need more control.</span></span>

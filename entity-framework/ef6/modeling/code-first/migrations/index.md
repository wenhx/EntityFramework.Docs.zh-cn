---
title: Code First 迁移 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 36591d8f-36e1-4835-8a51-90f34f633d1e
ms.openlocfilehash: e5a91af73bab9d45b0f1f4242ce503c6b6f407f6
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413302"
---
# <a name="code-first-migrations"></a><span data-ttu-id="fce16-102">Code First 迁移</span><span class="sxs-lookup"><span data-stu-id="fce16-102">Code First Migrations</span></span>
<span data-ttu-id="fce16-103">如果使用的是 Code First 工作流，推荐使用 Code First 迁移改进应用程序的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="fce16-103">Code First Migrations is the recommended way to evolve your application's database schema if you are using the Code First workflow.</span></span> <span data-ttu-id="fce16-104">迁移提供一组允许以下操作的工具：</span><span class="sxs-lookup"><span data-stu-id="fce16-104">Migrations provide a set of tools that allow:</span></span>

1. <span data-ttu-id="fce16-105">创建可用于 EF 模型的初始数据库</span><span class="sxs-lookup"><span data-stu-id="fce16-105">Create an initial database that works with your EF model</span></span>
2. <span data-ttu-id="fce16-106">生成迁移以跟踪对 EF 模型所做的更改</span><span class="sxs-lookup"><span data-stu-id="fce16-106">Generating migrations to keep track of changes you make to your EF model</span></span>
2. <span data-ttu-id="fce16-107">使数据库随时掌握这些更改</span><span class="sxs-lookup"><span data-stu-id="fce16-107">Keep your database up to date with those changes</span></span>

<span data-ttu-id="fce16-108">下方演练将概述实体框架中的 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-108">The following walkthrough will provide an overview Code First Migrations in Entity Framework.</span></span> <span data-ttu-id="fce16-109">可以完成整个演练或跳到感兴趣的主题。</span><span class="sxs-lookup"><span data-stu-id="fce16-109">You can either complete the entire walkthrough or skip to the topic you are interested in.</span></span> <span data-ttu-id="fce16-110">包含以下主题：</span><span class="sxs-lookup"><span data-stu-id="fce16-110">The following topics are covered:</span></span>

## <a name="building-an-initial-model--database"></a><span data-ttu-id="fce16-111">生成初始模型和数据库</span><span class="sxs-lookup"><span data-stu-id="fce16-111">Building an Initial Model & Database</span></span>

<span data-ttu-id="fce16-112">开始使用迁移之前，需要会用到项目和 Code First 模型。</span><span class="sxs-lookup"><span data-stu-id="fce16-112">Before we start using migrations we need a project and a Code First model to work with.</span></span> <span data-ttu-id="fce16-113">对于此演练，我们将使用规范的“博客”和“帖子”模型   。</span><span class="sxs-lookup"><span data-stu-id="fce16-113">For this walkthrough we are going to use the canonical **Blog** and **Post** model.</span></span>

-   <span data-ttu-id="fce16-114">创建新的 MigrationsDemo 控制台应用程序 </span><span class="sxs-lookup"><span data-stu-id="fce16-114">Create a new **MigrationsDemo** Console application</span></span>
-   <span data-ttu-id="fce16-115">将最新版本的 EntityFramework NuGet 包添加到项目中 </span><span class="sxs-lookup"><span data-stu-id="fce16-115">Add the latest version of the **EntityFramework** NuGet package to the project</span></span>
    -   <span data-ttu-id="fce16-116">“工具”–&gt;“库包管理器”–&gt;“包管理器控制台” </span><span class="sxs-lookup"><span data-stu-id="fce16-116">**Tools –&gt; Library Package Manager –&gt; Package Manager Console**</span></span>
    -   <span data-ttu-id="fce16-117">运行 Install-Package EntityFramework 命令 </span><span class="sxs-lookup"><span data-stu-id="fce16-117">Run the **Install-Package EntityFramework** command</span></span>
-   <span data-ttu-id="fce16-118">添加 Model.cs 文件，其代码如下所示  。</span><span class="sxs-lookup"><span data-stu-id="fce16-118">Add a **Model.cs** file with the code shown below.</span></span> <span data-ttu-id="fce16-119">此代码定义了构成域模型的单个“博客”类和 EF Code First 上下文 BlogContext 类  </span><span class="sxs-lookup"><span data-stu-id="fce16-119">This code defines a single **Blog** class that makes up our domain model and a **BlogContext** class that is our EF Code First context</span></span>

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
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

-   <span data-ttu-id="fce16-120">现在我们拥有一个模型，可用它执行数据访问操作。</span><span class="sxs-lookup"><span data-stu-id="fce16-120">Now that we have a model it’s time to use it to perform data access.</span></span> <span data-ttu-id="fce16-121">更新 Program.cs 文件，其代码如下所示  。</span><span class="sxs-lookup"><span data-stu-id="fce16-121">Update the **Program.cs** file with the code shown below.</span></span>

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsDemo
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

-   <span data-ttu-id="fce16-122">运行应用程序，随即会创建 MigrationsCodeDemo.BlogContext 数据库  。</span><span class="sxs-lookup"><span data-stu-id="fce16-122">Run your application and you will see that a **MigrationsCodeDemo.BlogContext** database is created for you.</span></span>

    ![数据库  LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a><span data-ttu-id="fce16-124">启用迁移</span><span class="sxs-lookup"><span data-stu-id="fce16-124">Enabling Migrations</span></span>

<span data-ttu-id="fce16-125">现可对模型进行更多更改。</span><span class="sxs-lookup"><span data-stu-id="fce16-125">It’s time to make some more changes to our model.</span></span>

-   <span data-ttu-id="fce16-126">将 Url 属性引入“博客”类。</span><span class="sxs-lookup"><span data-stu-id="fce16-126">Let’s introduce a Url property to the Blog class.</span></span>

``` csharp
    public string Url { get; set; }
```

<span data-ttu-id="fce16-127">如果要再次运行应用程序，则会收到一个 InvalidOperationException，指出“创建数据库后，支持‘BlogContext’上下文的模型已发生变化。  请考虑使用 Code First 迁移更新数据库 ( [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *)。*</span><span class="sxs-lookup"><span data-stu-id="fce16-127">If you were to run the application again you would get an InvalidOperationException stating *The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database (* [*http://go.microsoft.com/fwlink/?LinkId=238269*](https://go.microsoft.com/fwlink/?LinkId=238269)*).*</span></span>

<span data-ttu-id="fce16-128">如异常情况所述，可开始使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-128">As the exception suggests, it’s time to start using Code First Migrations.</span></span> <span data-ttu-id="fce16-129">第一步是启用上下文迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-129">The first step is to enable migrations for our context.</span></span>

-   <span data-ttu-id="fce16-130">在包管理器控制台中运行 Enable-Migrations 命令 </span><span class="sxs-lookup"><span data-stu-id="fce16-130">Run the **Enable-Migrations** command in Package Manager Console</span></span>

    <span data-ttu-id="fce16-131">此命令已将“迁移”文件夹添加到项目中  。</span><span class="sxs-lookup"><span data-stu-id="fce16-131">This command has added a **Migrations** folder to our project.</span></span> <span data-ttu-id="fce16-132">此新文件夹包含两个文件：</span><span class="sxs-lookup"><span data-stu-id="fce16-132">This new folder contains two files:</span></span>

-   <span data-ttu-id="fce16-133">**配置类。**</span><span class="sxs-lookup"><span data-stu-id="fce16-133">**The Configuration class.**</span></span> <span data-ttu-id="fce16-134">此类允许配置迁移对上下文的行为方式。</span><span class="sxs-lookup"><span data-stu-id="fce16-134">This class allows you to configure how Migrations behaves for your context.</span></span> <span data-ttu-id="fce16-135">对于此演练，将只使用默认配置。</span><span class="sxs-lookup"><span data-stu-id="fce16-135">For this walkthrough we will just use the default configuration.</span></span>
    <span data-ttu-id="fce16-136">由于项目中只有一个 Code First 上下文，因此 Enable-Migrations 已自动填充此配置适用的上下文类型。 </span><span class="sxs-lookup"><span data-stu-id="fce16-136">*Because there is just a single Code First context in your project, Enable-Migrations has automatically filled in the context type this configuration applies to.*</span></span>
-   <span data-ttu-id="fce16-137">**InitialCreate 迁移**。</span><span class="sxs-lookup"><span data-stu-id="fce16-137">**An InitialCreate migration**.</span></span> <span data-ttu-id="fce16-138">之所以生成此迁移，是因为在启用迁移之前，我们已使用 Code First 创建了数据库。</span><span class="sxs-lookup"><span data-stu-id="fce16-138">This migration was generated because we already had Code First create a database for us, before we enabled migrations.</span></span> <span data-ttu-id="fce16-139">已构建的迁移中的代码表示已在数据库中创建的对象。</span><span class="sxs-lookup"><span data-stu-id="fce16-139">The code in this scaffolded migration represents the objects that have already been created in the database.</span></span> <span data-ttu-id="fce16-140">在本演练中，即为具有 BlogId 和“名称”列的 Blog 表    。</span><span class="sxs-lookup"><span data-stu-id="fce16-140">In our case that is the **Blog** table with a **BlogId** and **Name** columns.</span></span> <span data-ttu-id="fce16-141">文件名包含时间戳，这样有助于排序。</span><span class="sxs-lookup"><span data-stu-id="fce16-141">The filename includes a timestamp to help with ordering.</span></span>
    <span data-ttu-id="fce16-142">如果尚未创建数据库，则不会将此 InitialCreate 迁移添加到项目中。相反，第一次调用 Add-Migration 时，会将创建这些表的代码构建到新的迁移中  。</span><span class="sxs-lookup"><span data-stu-id="fce16-142">*If the database had not already been created this InitialCreate migration would not have been added to the project. Instead, the first time we call Add-Migration the code to create these tables would be scaffolded to a new migration.*</span></span>

### <a name="multiple-models-targeting-the-same-database"></a><span data-ttu-id="fce16-143">针对同一数据库的多个模型</span><span class="sxs-lookup"><span data-stu-id="fce16-143">Multiple Models Targeting the Same Database</span></span>

<span data-ttu-id="fce16-144">使用 EF6 之前的版本时，只能使用一个 Code First 模型生成/管理数据库的架构。</span><span class="sxs-lookup"><span data-stu-id="fce16-144">When using versions prior to EF6, only one Code First model could be used to generate/manage the schema of a database.</span></span> <span data-ttu-id="fce16-145">这是因为每个数据库的单个 \_\_MigrationsHistory 表无法识别哪些项属于哪个模型  。</span><span class="sxs-lookup"><span data-stu-id="fce16-145">This is the result of a single **\_\_MigrationsHistory** table per database with no way to identify which entries belong to which model.</span></span>

<span data-ttu-id="fce16-146">从 EF6 开始，配置类中包括 ContextKey 属性   。</span><span class="sxs-lookup"><span data-stu-id="fce16-146">Starting with EF6, the **Configuration** class includes a **ContextKey** property.</span></span> <span data-ttu-id="fce16-147">该属性充当每个 Code First 模型的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="fce16-147">This acts as a unique identifier for each Code First model.</span></span> <span data-ttu-id="fce16-148">\_\_MigrationsHistory 表中相应的列允许来自多个模型的项共享表  。</span><span class="sxs-lookup"><span data-stu-id="fce16-148">A corresponding column in the **\_\_MigrationsHistory** table allows entries from multiple models to share the table.</span></span> <span data-ttu-id="fce16-149">默认情况下，此属性设置为上下文的完全限定名称。</span><span class="sxs-lookup"><span data-stu-id="fce16-149">By default, this property is set to the fully qualified name of your context.</span></span>

## <a name="generating--running-migrations"></a><span data-ttu-id="fce16-150">生成和运行迁移</span><span class="sxs-lookup"><span data-stu-id="fce16-150">Generating & Running Migrations</span></span>

<span data-ttu-id="fce16-151">Code First 迁移具有两个需要用户了解的主要命令。</span><span class="sxs-lookup"><span data-stu-id="fce16-151">Code First Migrations has two primary commands that you are going to become familiar with.</span></span>

-   <span data-ttu-id="fce16-152">Add-Migration 将基于自上次迁移创建以来对模型所做的更改来构建下一次迁移 </span><span class="sxs-lookup"><span data-stu-id="fce16-152">**Add-Migration** will scaffold the next migration based on changes you have made to your model since the last migration was created</span></span>
-   <span data-ttu-id="fce16-153">Update-Database 将对数据库应用任意挂起的迁移 </span><span class="sxs-lookup"><span data-stu-id="fce16-153">**Update-Database** will apply any pending migrations to the database</span></span>

<span data-ttu-id="fce16-154">我们需要构建迁移来处理添加的新 Url 属性。</span><span class="sxs-lookup"><span data-stu-id="fce16-154">We need to scaffold a migration to take care of the new Url property we have added.</span></span> <span data-ttu-id="fce16-155">Add-Migration 命令可为这些迁移命名，仅需调用 AddBlogUrl   。</span><span class="sxs-lookup"><span data-stu-id="fce16-155">The **Add-Migration** command allows us to give these migrations a name, let’s just call ours **AddBlogUrl**.</span></span>

-   <span data-ttu-id="fce16-156">在包管理器控制台中运行 Add-Migration AddBlogUrl 命令 </span><span class="sxs-lookup"><span data-stu-id="fce16-156">Run the **Add-Migration AddBlogUrl** command in Package Manager Console</span></span>
-   <span data-ttu-id="fce16-157">现“迁移”文件夹中具有新的 AddBlogUrl 迁移   。</span><span class="sxs-lookup"><span data-stu-id="fce16-157">In the **Migrations** folder we now have a new **AddBlogUrl** migration.</span></span> <span data-ttu-id="fce16-158">迁移文件名以时间戳作为前缀，这样有助于排序</span><span class="sxs-lookup"><span data-stu-id="fce16-158">The migration filename is pre-fixed with a timestamp to help with ordering</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Blogs", "Url", c => c.String());
            }

            public override void Down()
            {
                DropColumn("dbo.Blogs", "Url");
            }
        }
    }
```

<span data-ttu-id="fce16-159">现可编辑或添加到此迁移，但所有内容看起来都很合适。</span><span class="sxs-lookup"><span data-stu-id="fce16-159">We could now edit or add to this migration but everything looks pretty good.</span></span> <span data-ttu-id="fce16-160">使用 Update-Database 将此迁移应用到数据库  。</span><span class="sxs-lookup"><span data-stu-id="fce16-160">Let’s use **Update-Database** to apply this migration to the database.</span></span>

-   <span data-ttu-id="fce16-161">在包管理器控制台运行 Update-Database 命令 </span><span class="sxs-lookup"><span data-stu-id="fce16-161">Run the **Update-Database** command in Package Manager Console</span></span>
-   <span data-ttu-id="fce16-162">Code First 迁移将比较“迁移”文件夹中的迁移和已应用于数据库的迁移  。</span><span class="sxs-lookup"><span data-stu-id="fce16-162">Code First Migrations will compare the migrations in our **Migrations** folder with the ones that have been applied to the database.</span></span> <span data-ttu-id="fce16-163">迁移会发现需应用 AddBlogUrl 迁移，并运行它  。</span><span class="sxs-lookup"><span data-stu-id="fce16-163">It will see that the **AddBlogUrl** migration needs to be applied, and run it.</span></span>

<span data-ttu-id="fce16-164">MigrationsDemo.BlogContext 数据库现已更新，其中包含“博客”表中的 Url 列    。</span><span class="sxs-lookup"><span data-stu-id="fce16-164">The **MigrationsDemo.BlogContext** database is now updated to include the **Url** column in the **Blogs** table.</span></span>

## <a name="customizing-migrations"></a><span data-ttu-id="fce16-165">自定义迁移</span><span class="sxs-lookup"><span data-stu-id="fce16-165">Customizing Migrations</span></span>

<span data-ttu-id="fce16-166">到目前为止，我们已在未进行任何更改的情况下生成并运行了迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-166">So far we’ve generated and run a migration without making any changes.</span></span> <span data-ttu-id="fce16-167">现在我们来看看如何编辑默认生成的代码。</span><span class="sxs-lookup"><span data-stu-id="fce16-167">Now let’s look at editing the code that gets generated by default.</span></span>

-   <span data-ttu-id="fce16-168">现在可对模型进行更多更改，我们将新的 Rating 属性添加到“博客”类  </span><span class="sxs-lookup"><span data-stu-id="fce16-168">It’s time to make some more changes to our model, let’s add a new **Rating** property to the **Blog** class</span></span>

``` csharp
    public int Rating { get; set; }
```

-   <span data-ttu-id="fce16-169">同时添加一个新的“帖子”类 </span><span class="sxs-lookup"><span data-stu-id="fce16-169">Let's also add a new **Post** class</span></span>

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

-   <span data-ttu-id="fce16-170">此外，再将“帖子”集合添加到“博客”类，以形成“博客”和“帖子”之间的另一层关系    </span><span class="sxs-lookup"><span data-stu-id="fce16-170">We'll also add a **Posts** collection to the **Blog** class to form the other end of the relationship between **Blog** and **Post**</span></span>

``` csharp
    public virtual List<Post> Posts { get; set; }
```

<span data-ttu-id="fce16-171">使用 Add-Migration 命令使 Code First 迁移提供对迁移的最佳猜测 </span><span class="sxs-lookup"><span data-stu-id="fce16-171">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span> <span data-ttu-id="fce16-172">我们将调用此迁移 AddPostClass  。</span><span class="sxs-lookup"><span data-stu-id="fce16-172">We’re going to call this migration **AddPostClass**.</span></span>

-   <span data-ttu-id="fce16-173">在包管理器控制台中运行 Add-Migration AddPostClass 命令  。</span><span class="sxs-lookup"><span data-stu-id="fce16-173">Run the **Add-Migration AddPostClass** command in Package Manager Console.</span></span>

<span data-ttu-id="fce16-174">Code First 迁移出色的构建了这些更改，但我们可能还需要做出一些更改：</span><span class="sxs-lookup"><span data-stu-id="fce16-174">Code First Migrations did a pretty good job of scaffolding these changes, but there are some things we might want to change:</span></span>

1.  <span data-ttu-id="fce16-175">首先，将唯一索引添加到 Posts.Title 列（添加在以下代码的 22 和 29 行）  。</span><span class="sxs-lookup"><span data-stu-id="fce16-175">First up, let’s add a unique index to **Posts.Title** column (Adding in line 22 & 29 in the code below).</span></span>
2.  <span data-ttu-id="fce16-176">同时添加不可为 NULL 的 Blogs.Rating 列  。</span><span class="sxs-lookup"><span data-stu-id="fce16-176">We’re also adding a non-nullable **Blogs.Rating** column.</span></span> <span data-ttu-id="fce16-177">如果表中存在任何现有数据，则会为数据分配新列数据类型的 CLR 默认值（分级是整数，因此将为 0）  。</span><span class="sxs-lookup"><span data-stu-id="fce16-177">If there is any existing data in the table it will get assigned the CLR default of the data type for new column (Rating is integer, so that would be **0**).</span></span> <span data-ttu-id="fce16-178">但我们要将默认值指定为 3，以便“博客”表中的现有行以合适的分级开始   。</span><span class="sxs-lookup"><span data-stu-id="fce16-178">But we want to specify a default value of **3** so that existing rows in the **Blogs** table will start with a decent rating.</span></span>
    <span data-ttu-id="fce16-179">（可以看到以下代码的第 24 行指定的默认值）</span><span class="sxs-lookup"><span data-stu-id="fce16-179">(You can see the default value specified on line 24 of the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

<span data-ttu-id="fce16-180">已编辑的迁移准备就绪，所以我们使用 Update-Database 来更新数据库  。</span><span class="sxs-lookup"><span data-stu-id="fce16-180">Our edited migration is ready to go, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="fce16-181">这次指定 –Verbose 标志，以便可以看到 Code First 迁移正在运行的 SQL  。</span><span class="sxs-lookup"><span data-stu-id="fce16-181">This time let’s specify the **–Verbose** flag so that you can see the SQL that Code First Migrations is running.</span></span>

-   <span data-ttu-id="fce16-182">在包管理器控制台中运行 Update-Database –Verbose 命令  。</span><span class="sxs-lookup"><span data-stu-id="fce16-182">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="data-motion--custom-sql"></a><span data-ttu-id="fce16-183">数据移动/自定义 SQL</span><span class="sxs-lookup"><span data-stu-id="fce16-183">Data Motion / Custom SQL</span></span>

<span data-ttu-id="fce16-184">到目前为止，我们已经介绍了不更改或移动任何数据的迁移操作，现在来看看需要移动数据的操作。</span><span class="sxs-lookup"><span data-stu-id="fce16-184">So far we have looked at migration operations that don’t change or move any data, now let’s look at something that needs to move some data around.</span></span> <span data-ttu-id="fce16-185">目前还没有对数据移动的原生支持，但我们可以在脚本中的任何位置运行一些任意 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="fce16-185">There is no native support for data motion yet, but we can run some arbitrary SQL commands at any point in our script.</span></span>

-   <span data-ttu-id="fce16-186">将 Post.Abstract 属性添加到模型中  。</span><span class="sxs-lookup"><span data-stu-id="fce16-186">Let’s add a **Post.Abstract** property to our model.</span></span> <span data-ttu-id="fce16-187">稍后，我们将使用“内容”列开头的一些文本预填充现有帖子的“摘要”   。</span><span class="sxs-lookup"><span data-stu-id="fce16-187">Later, we’re going to pre-populate the **Abstract** for existing posts using some text from the start of the **Content** column.</span></span>

``` csharp
    public string Abstract { get; set; }
```

<span data-ttu-id="fce16-188">使用 Add-Migration 命令使 Code First 迁移提供对迁移的最佳猜测 </span><span class="sxs-lookup"><span data-stu-id="fce16-188">We'll use the **Add-Migration** command to let Code First Migrations scaffold its best guess at the migration for us.</span></span>

-   <span data-ttu-id="fce16-189">在包管理器控制台中运行 Add-Migration AddPostAbstract 命令  。</span><span class="sxs-lookup"><span data-stu-id="fce16-189">Run the **Add-Migration AddPostAbstract** command in Package Manager Console.</span></span>
-   <span data-ttu-id="fce16-190">生成的迁移会处理架构更改，但我们还是希望使用每个帖子内容的前 100 个字符预填充“摘要”列  。</span><span class="sxs-lookup"><span data-stu-id="fce16-190">The generated migration takes care of the schema changes but we also want to pre-populate the **Abstract** column using the first 100 characters of content for each post.</span></span> <span data-ttu-id="fce16-191">要执行此操作，可在添加列之后下拉到 SQL 并运行 UPDATE 语句  。</span><span class="sxs-lookup"><span data-stu-id="fce16-191">We can do this by dropping down to SQL and running an **UPDATE** statement after the column is added.</span></span>
    <span data-ttu-id="fce16-192">（添加在以下代码的第 12 行中）</span><span class="sxs-lookup"><span data-stu-id="fce16-192">(Adding in line 12 in the code below)</span></span>

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

<span data-ttu-id="fce16-193">已编辑的迁移一切正常，所以我们可使用 Update-Database 来更新数据库  。</span><span class="sxs-lookup"><span data-stu-id="fce16-193">Our edited migration is looking good, so let’s use **Update-Database** to bring the database up-to-date.</span></span> <span data-ttu-id="fce16-194">我们将指定 –Verbose 标志，以便可以看到针对数据库运行的 SQL  。</span><span class="sxs-lookup"><span data-stu-id="fce16-194">We’ll specify the **–Verbose** flag so that we can see the SQL being run against the database.</span></span>

-   <span data-ttu-id="fce16-195">在包管理器控制台中运行 Update-Database –Verbose 命令  。</span><span class="sxs-lookup"><span data-stu-id="fce16-195">Run the **Update-Database –Verbose** command in Package Manager Console.</span></span>

## <a name="migrate-to-a-specific-version-including-downgrade"></a><span data-ttu-id="fce16-196">迁移到特定版本（包括降级）</span><span class="sxs-lookup"><span data-stu-id="fce16-196">Migrate to a Specific Version (Including Downgrade)</span></span>

<span data-ttu-id="fce16-197">到目前为止，我们一直在升级到最新迁移，但用户有时可能希望升级/降级到特定迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-197">So far we have always upgraded to the latest migration, but there may be times when you want upgrade/downgrade to a specific migration.</span></span>

<span data-ttu-id="fce16-198">假设想在运行 AddBlogUrl 迁移后将数据库迁移到其之前的状态  。</span><span class="sxs-lookup"><span data-stu-id="fce16-198">Let’s say we want to migrate our database to the state it was in after running our **AddBlogUrl** migration.</span></span> <span data-ttu-id="fce16-199">此时可使用 –TargetMigration 切换为降级到此迁移  。</span><span class="sxs-lookup"><span data-stu-id="fce16-199">We can use the **–TargetMigration** switch to downgrade to this migration.</span></span>

-   <span data-ttu-id="fce16-200">在包管理器控制台中运行 Update-Database –TargetMigration:  AddBlogUrl 命令。</span><span class="sxs-lookup"><span data-stu-id="fce16-200">Run the **Update-Database –TargetMigration: AddBlogUrl** command in Package Manager Console.</span></span>

<span data-ttu-id="fce16-201">此命令将为 AddBlogAbstract 和 AddPostClass 迁移运行 Down 脚本   。</span><span class="sxs-lookup"><span data-stu-id="fce16-201">This command will run the Down script for our **AddBlogAbstract** and **AddPostClass** migrations.</span></span>

<span data-ttu-id="fce16-202">如果想要一直回退到空数据库，可使用 Update-Database –TargetMigration: $InitialDatabase 命令  。</span><span class="sxs-lookup"><span data-stu-id="fce16-202">If you want to roll all the way back to an empty database then you can use the **Update-Database –TargetMigration: $InitialDatabase** command.</span></span>

## <a name="getting-a-sql-script"></a><span data-ttu-id="fce16-203">获取 SQL 脚本</span><span class="sxs-lookup"><span data-stu-id="fce16-203">Getting a SQL Script</span></span>

<span data-ttu-id="fce16-204">如果另一位开发人员希望在其计算机上进行这些更改，则只需在我们将更改签入源代码管理之后进行同步即可。</span><span class="sxs-lookup"><span data-stu-id="fce16-204">If another developer wants these changes on their machine they can just sync once we check our changes into source control.</span></span> <span data-ttu-id="fce16-205">在获得我们的新迁移后，他们只需运行 Update-database 命令即可在本地应用更改。</span><span class="sxs-lookup"><span data-stu-id="fce16-205">Once they have our new migrations they can just run the Update-Database command to have the changes applied locally.</span></span> <span data-ttu-id="fce16-206">但是，如果想将这些更改推送到测试服务器以及最终的产品，则可能需要一个可以传递给 DBA 的 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="fce16-206">However if we want to push these changes out to a test server, and eventually production, we probably want a SQL script we can hand off to our DBA.</span></span>

-   <span data-ttu-id="fce16-207">运行 Update-Database 命令，但是这次需指定 –Script 标志，以便将更改写入脚本，而不是应用更改   。</span><span class="sxs-lookup"><span data-stu-id="fce16-207">Run the **Update-Database** command but this time specify the **–Script** flag so that changes are written to a script rather than applied.</span></span> <span data-ttu-id="fce16-208">我们还将指定要为其生成脚本的源和目标迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-208">We’ll also specify a source and target migration to generate the script for.</span></span> <span data-ttu-id="fce16-209">我们希望脚本从空数据库 ($InitialDatabase) 转为最新版本（迁移 AddPostAbstract）   。</span><span class="sxs-lookup"><span data-stu-id="fce16-209">We want a script to go from an empty database (**$InitialDatabase**) to the latest version (migration **AddPostAbstract**).</span></span>
    <span data-ttu-id="fce16-210">如果未指定目标迁移，迁移将使用最新的迁移作为目标。如果未指定源迁移，迁移将使用数据库的当前状态  。</span><span class="sxs-lookup"><span data-stu-id="fce16-210">*If you don’t specify a target migration, Migrations will use the latest migration as the target. If you don't specify a source migrations, Migrations will use the current state of the database.*</span></span>
-   <span data-ttu-id="fce16-211">在包管理器控制台中运行 Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration:  AddPostAbstract 命令</span><span class="sxs-lookup"><span data-stu-id="fce16-211">Run the **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** command in Package Manager Console</span></span>

<span data-ttu-id="fce16-212">Code First 迁移将运行迁移管道，但并非是应用更改，而是将更改写入到 .sql 文件。</span><span class="sxs-lookup"><span data-stu-id="fce16-212">Code First Migrations will run the migration pipeline but instead of actually applying the changes it will write them out to a .sql file for you.</span></span> <span data-ttu-id="fce16-213">生成脚本后，将在 Visual Studio 中打开，以供查看或保存。</span><span class="sxs-lookup"><span data-stu-id="fce16-213">Once the script is generated, it is opened for you in Visual Studio, ready for you to view or save.</span></span>

### <a name="generating-idempotent-scripts"></a><span data-ttu-id="fce16-214">生成幂等脚本</span><span class="sxs-lookup"><span data-stu-id="fce16-214">Generating Idempotent Scripts</span></span>

<span data-ttu-id="fce16-215">从 EF6 开始，如果指定 –SourceMigration $InitialDatabase，则生成的脚本将为“幂等”  。</span><span class="sxs-lookup"><span data-stu-id="fce16-215">Starting with EF6, if you specify **–SourceMigration $InitialDatabase** then the generated script will be ‘idempotent’.</span></span> <span data-ttu-id="fce16-216">幂等脚本可以将当前任何版本的数据库升级到最新版本（或使用 – TargetMigration 升级到指定版本）  。</span><span class="sxs-lookup"><span data-stu-id="fce16-216">Idempotent scripts can upgrade a database currently at any version to the latest version (or the specified version if you use **–TargetMigration**).</span></span> <span data-ttu-id="fce16-217">生成的脚本包括检查 \_\_MigrationsHistory 表的逻辑，并且仅应用以前未应用的更改  。</span><span class="sxs-lookup"><span data-stu-id="fce16-217">The generated script includes logic to check the **\_\_MigrationsHistory** table and only apply changes that haven't been previously applied.</span></span>

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a><span data-ttu-id="fce16-218">应用程序启动时自动升级（MigrateDatabaseToLatestVersion 初始值设定项）</span><span class="sxs-lookup"><span data-stu-id="fce16-218">Automatically Upgrading on Application Startup (MigrateDatabaseToLatestVersion Initializer)</span></span>

<span data-ttu-id="fce16-219">如果要部署应用程序，则可能希望应用程序在启动时自动升级数据库（通过应用各种挂起的迁移）。</span><span class="sxs-lookup"><span data-stu-id="fce16-219">If you are deploying your application you may want it to automatically upgrade the database (by applying any pending migrations) when the application launches.</span></span> <span data-ttu-id="fce16-220">可以通过注册 MigrateDatabaseToLatestVersion 数据库初始值设定项来执行此操作  。</span><span class="sxs-lookup"><span data-stu-id="fce16-220">You can do this by registering the **MigrateDatabaseToLatestVersion** database initializer.</span></span> <span data-ttu-id="fce16-221">数据库初始值设定项仅包含一些用于确保正确设置数据库的逻辑。</span><span class="sxs-lookup"><span data-stu-id="fce16-221">A database initializer simply contains some logic that is used to make sure the database is setup correctly.</span></span> <span data-ttu-id="fce16-222">第一次在应用程序进程中使用上下文时，会运行此逻辑 (AppDomain)  。</span><span class="sxs-lookup"><span data-stu-id="fce16-222">This logic is run the first time the context is used within the application process (**AppDomain**).</span></span>

<span data-ttu-id="fce16-223">如下所示，可以更新 Program.cs 文件，以在使用上下文（第 14 行）之前，为 BlogContext 设置 MigrateDatabaseToLatestVersion 初始化值设定项   。</span><span class="sxs-lookup"><span data-stu-id="fce16-223">We can update the **Program.cs** file, as shown below, to set the **MigrateDatabaseToLatestVersion** initializer for BlogContext before we use the context (Line 14).</span></span> <span data-ttu-id="fce16-224">请注意，还需要为 System.Data.Entity 命名空间（第 5 行）添加 using 语句  。</span><span class="sxs-lookup"><span data-stu-id="fce16-224">Note that you also need to add a using statement for the **System.Data.Entity** namespace (Line 5).</span></span>

<span data-ttu-id="fce16-225">*创建此初始值设定项的实例时，需要指定上下文类型 (**BlogContext**) 和迁移配置（**配置**）- 迁移配置是启用迁移时添加到“**迁移**”文件夹的类*。</span><span class="sxs-lookup"><span data-stu-id="fce16-225">*When we create an instance of this initializer we need to specify the context type (**BlogContext**) and the migrations configuration (**Configuration**) - the migrations configuration is the class that got added to our **Migrations** folder when we enabled Migrations.*</span></span>

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

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

<span data-ttu-id="fce16-226">现在，每当应用程序运行时，它首先会检查其目标数据库是否为最新，如果不是，则会应用各种挂起的迁移。</span><span class="sxs-lookup"><span data-stu-id="fce16-226">Now whenever our application runs it will first check if the database it is targeting is up-to-date, and apply any pending migrations if it is not.</span></span>

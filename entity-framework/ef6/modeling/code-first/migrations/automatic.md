---
title: 自动 Code First 迁移-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0eb86787-2161-4cb4-9cb8-67c5d6e95650
ms.openlocfilehash: 2713afaf09707b7696e90464aac9945c2d82d274
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415699"
---
# <a name="automatic-code-first-migrations"></a>自动 Code First 迁移
自动迁移使你可以使用 Code First 迁移，而不会在项目中为你所做的每个更改提供代码文件。 并非所有更改都可以自动应用-例如，列重命名需要使用基于代码的迁移。

> [!NOTE]
> 本文假设你知道如何在基本方案中使用 Code First 迁移。 如果没有，则需要先阅读[Code First 迁移](~/ef6/modeling/code-first/migrations/index.md)，然后再继续。

## <a name="recommendation-for-team-environments"></a>团队环境建议

你可以点播自动和基于代码的迁移，但不建议在团队开发方案中使用。 如果你是使用源代码管理的开发人员团队的成员，则应使用纯粹自动迁移或纯粹基于代码的迁移。 考虑到自动迁移的限制，我们建议在团队环境中使用基于代码的迁移。

## <a name="building-an-initial-model--database"></a>生成初始模型和数据库

开始使用迁移之前，需要会用到项目和 Code First 模型。 对于此演练，我们将使用规范的“博客”和“帖子”模型。

-   创建新的**MigrationsAutomaticDemo**控制台应用程序
-   将最新版本的 EntityFramework NuGet 包添加到项目中
    -   “工具”– **“库包管理器”–&gt;“包管理器控制台”&gt;**
    -   运行 Install-Package EntityFramework 命令
-   添加 Model.cs 文件，其代码如下所示。 此代码定义了构成域模型的单个“博客”类和 EF Code First 上下文 BlogContext 类

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

-   现在我们拥有一个模型，可用它执行数据访问操作。 更新 Program.cs 文件，其代码如下所示。

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

-   运行你的应用程序，你将看到已为你创建**MigrationsAutomaticCodeDemo. BlogContext**数据库。

    ![数据库 LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>启用迁移

现可对模型进行更多更改。

-   将 Url 属性引入“博客”类。

``` csharp
    public string Url { get; set; }
```

如果您要再次运行该应用程序，将会出现一个 InvalidOperationException，指出*支持 "BlogContext" 上下文的模型在创建数据库后发生了更改。请考虑使用 Code First 迁移更新数据库（* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *）。*

如异常情况所述，可开始使用 Code First 迁移。 由于我们要使用自动迁移，因此，我们将指定 **– EnableAutomaticMigrations**开关。

-   在包管理器控制台中运行 "**启用-迁移– EnableAutomaticMigrations** " 命令。此命令已向项目添加了 "**迁移**" 文件夹。 此新文件夹包含一个文件：

-   **配置类。** 此类允许配置迁移对上下文的行为方式。 对于此演练，将只使用默认配置。
    由于项目中只有一个 Code First 上下文，因此 Enable-Migrations 已自动填充此配置适用的上下文类型。

 

## <a name="your-first-automatic-migration"></a>第一次自动迁移

Code First 迁移具有两个需要用户了解的主要命令。

-   Add-Migration 将基于自上次迁移创建以来对模型所做的更改来构建下一次迁移
-   Update-Database 将对数据库应用任意挂起的迁移

我们将避免使用添加迁移（除非我们确实需要），并专注于让 Code First 迁移自动计算和应用更改。 让我们使用**更新数据库**来获取 Code First 迁移将对模型（新的**博客 Ur**l 属性）的更改推送到数据库。

-   在 Package Manager Console 中运行 "**更新数据库**" 命令。

**MigrationsAutomaticDemo BlogContext**数据库现在已更新为包含**博客**表中的**Url**列。

 

## <a name="your-second-automatic-migration"></a>第二次自动迁移

接下来，让我们进行其他更改，并让 Code First 迁移自动将更改推送到数据库。

-   同时添加一个新的“帖子”类

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

-   此外，再将“帖子”集合添加到“博客”类，以形成“博客”和“帖子”之间的另一层关系

``` csharp
    public virtual List<Post> Posts { get; set; }
```

现在使用**更新数据库**使数据库保持最新状态。 这次指定 –Verbose 标志，以便可以看到 Code First 迁移正在运行的 SQL。

-   在包管理器控制台中运行 Update-Database –Verbose 命令。

## <a name="adding-a-code-based-migration"></a>添加基于代码的迁移

现在，让我们看看我们可能要对使用基于代码的迁移。

-   让我们向**博客**类添加**评级**属性

``` csharp
    public int Rating { get; set; }
```

只需运行 "**更新数据库**" 即可将这些更改推送到数据库。 但是，我们添加了不可为 null 的**博客。 "分级**" 列，如果表中存在任何现有数据，则会向其分配新列的数据类型的 CLR 默认值（评级为整数，以便为**0**）。 但我们要将默认值指定为 3，以便“博客”表中的现有行以合适的分级开始。
让我们使用 "添加-迁移" 命令将此更改写入到基于代码的迁移，以便可以对其进行编辑。 "**添加-迁移**" 命令使我们可以为这些迁移命名，只需调用我们的**AddBlogRating**即可。

-   在 Package Manager Console 中运行**Add-迁移 AddBlogRating**命令。
-   在 "**迁移**" 文件夹中，我们现在已有一个新的**AddBlogRating**迁移。 迁移文件名预先修复了时间戳，以帮助进行排序。 让我们编辑生成的代码，为博客指定默认值3（在下面的代码中为第10行）

*迁移还具有捕获某些元数据的代码隐藏文件。此元数据将允许 Code First 迁移复制在此基于代码的迁移之前执行的自动迁移。如果另一位开发人员想要运行迁移或部署应用程序，这一点非常重要。*

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

已编辑的迁移一切正常，所以我们可使用 Update-Database 来更新数据库。

-   在 Package Manager Console 中运行 "**更新数据库**" 命令。

## <a name="back-to-automatic-migrations"></a>返回到自动迁移

现在，我们可以免费切换回自动迁移，以便进行更简单的更改。 Code First 迁移将按照在每个基于代码的迁移的代码隐藏文件中存储的元数据，以正确的顺序执行自动和基于代码的迁移。

-   让我们向模型中添加一个 Post. Abstract 属性

``` csharp
    public string Abstract { get; set; }
```

现在，我们可以使用**更新数据库**来获取 Code First 迁移，使用自动迁移将此更改推送到数据库。

-   在 Package Manager Console 中运行 "**更新数据库**" 命令。

## <a name="summary"></a>摘要

在本演练中，您学习了如何使用自动迁移将模型更改推送到数据库。 还了解了在需要更多控制时，如何在自动迁移之间基架和运行基于代码的迁移。

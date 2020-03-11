---
title: Code First 数据批注-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 9fac2a90c46d78ff5fd632800cc0050276467773
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415855"
---
# <a name="code-first-data-annotations"></a>Code First 数据批注
> [!NOTE]
> **仅限 ef 4.1** -在实体框架4.1 中引入了本页中所述的功能、api 等。 如果你使用的是早期版本，则不会应用此信息中的部分或全部。

此页面上的内容适用于最初由 Julie Lerman （\<http://thedatafarm.com>)编写的文章。

利用实体框架 Code First，你可以使用自己的域类来表示 EF 依赖来执行查询、更改跟踪和更新功能的模型。 Code First 利用称为 "约定 over 配置" 的编程模式。 Code First 将假设你的类遵循实体框架的约定，在这种情况下，将自动处理如何执行其作业。 但是，如果你的类不遵循这些约定，则可以将配置添加到你的类，以便为 EF 提供必需的信息。

Code First 提供了向你的类添加这些配置的两种方法。 其中一种方法是使用名为 DataAnnotations 的简单特性，第二种方法是使用 Code First 的流畅 API，这为你提供了一种在代码中以强制方式描述配置的方式。

本文重点介绍如何使用 DataAnnotations （DataAnnotations 命名空间中的 System.componentmodel）来配置类-突出显示最常用的配置。 DataAnnotations 也可由许多 .NET 应用程序（如 ASP.NET MVC）理解，这允许这些应用程序利用相同的注释进行客户端验证。


## <a name="the-model"></a>模型

我将使用简单的类对 Code First DataAnnotations 进行演示：博客和文章。

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

正如他们一样，博客和文章类可以方便地遵循 code first 约定，无需进行调整即可启用 EF 兼容性。 但是，您还可以使用批注向 EF 提供有关它们所映射到的类和数据库的详细信息。

 

## <a name="key"></a>Key

实体框架依赖于每个实体，每个实体都有一个用于实体跟踪的键值。 Code First 的一种约定是隐式键属性;Code First 将查找名为 "Id" 的属性，或者查找类名称和 "Id" （如 "BlogId"）的组合。 此属性将映射到数据库中的主键列。

博客和帖子类都遵循此约定。 如果不是，怎么办？ 如果博客使用的是名称*PrimaryTrackingKey* （甚至是*foo*），该怎么办？ 如果代码优先找不到与此约定相匹配的属性，则会引发异常，因为实体框架要求必须有一个键属性。 你可以使用密钥批注来指定要用作 EntityKey 的属性。

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

如果使用代码优先的数据库生成功能，博客表将具有名为 PrimaryTrackingKey 的主键列，默认情况下，此列也定义为标识。

![带主键的博客表](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>组合键

实体框架支持组合键-由多个属性组成的主键。 例如，你可以有一个 Passport 类，其 primary key 是 PassportNumber 和 IssuingCountry 的组合。

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

尝试在 EF 模型中使用以上类将导致 `InvalidOperationException`：

*无法确定类型 "Passport" 的组合键。使用 ColumnAttribute 或 HasKey 方法为组合主键指定顺序。*

若要使用组合键，实体框架要求您定义键属性的顺序。 为此，可以使用列批注指定顺序。

>[!NOTE]
> 顺序值是相对的（而不是基于索引的），因此可以使用任何值。 例如，100和200可接受而不是1和2。

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

如果有包含复合外键的实体，则必须指定用于相应主键属性的相同列排序。

只有外键属性中的相对顺序需要相同，分配给**Order**的确切值不需要匹配。 例如，在下面的类中，可以使用3和4代替1和2。

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>必需

必需的批注告诉 EF 需要特定属性。

向 Title 属性添加所需的将强制 EF （和 MVC）确保属性中包含数据。

``` csharp
    [Required]
    public string Title { get; set; }
```

如果应用程序中没有额外的代码或标记更改，MVC 应用程序将执行客户端验证，甚至使用属性和批注名称动态生成消息。

![需要标题的 "创建页面" 错误](~/ef6/media/jj591583-figure02.png)

必需的属性还会影响生成的数据库，方法是将映射的属性设为不可为 null。 请注意，Title 字段已更改为 "not null"。

>[!NOTE]
> 在某些情况下，即使属性是必需的，也无法使数据库中的列不可为 null。 例如，对多个类型使用 TPH 继承战略数据时，会将其存储在一个表中。 如果派生的类型包含所需的属性，则列不能为 null，因为并不是层次结构中的所有类型都具有此属性。

 

![博客表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength 和 MinLength

MaxLength 和 MinLength 属性允许您指定附加的属性验证，就像您在需要时所做的那样。

下面是具有长度要求的 BloggerName。 该示例还演示了如何合并特性。

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

MaxLength 批注将属性的长度设置为10，将影响数据库。

![显示 BloggerName 列的最大长度的博客表格](~/ef6/media/jj591583-figure04.png)

MVC 客户端批注和 EF 4.1 服务器端批注都将接受此验证，并再次动态生成错误消息： "字段 BloggerName 必须是最大长度为" 10 "的字符串或数组类型。"该消息只需很长时间。 许多批注允许您使用 ErrorMessage 特性指定错误消息。

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

您还可以在所需的批注中指定 ErrorMessage。

![创建具有自定义错误消息的页面](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

Code first 约定规定每个属于受支持数据类型的属性都在数据库中表示。 但在应用程序中并不总是如此。 例如，你可能在博客类中有一个属性，该属性基于 "标题" 和 "BloggerName" 字段创建代码。 该属性可以动态创建，不需要存储。 可以用 NotMapped 批注（如此 BlogCode 属性）来标记不映射到数据库的任何属性。

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

请不要在一组类中描述域实体，然后将这些类分层以描述完整的实体。 例如，可以将名为 BlogDetails 的类添加到模型。

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

请注意，BlogDetails 没有任何类型的键属性。 在域驱动设计中，BlogDetails 称为值对象。 实体框架将值对象引用为复杂类型。  不能自行跟踪复杂类型。

但作为博客类中的属性，BlogDetails 将作为博客对象的一部分进行跟踪。 为了使代码优先识别这一点，必须将 BlogDetails 类标记为 ComplexType。

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

现在，可以在博客类中添加一个属性，用于表示该博客的 BlogDetails。

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

在数据库中，博客表将包含博客的所有属性，包括其 BlogDetail 属性中包含的属性。 默认情况下，每个名称的前面都有复杂类型 BlogDetail 的名称。

![包含复杂类型的博客表](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

ConcurrencyCheck 批注允许标记一个或多个属性，用户在编辑或删除实体时，此属性将用于数据库中的并发检查。 如果你使用的是 EF 设计器，则会将属性的 ConcurrencyMode 设置为 Fixed。

让我们通过将其添加到 BloggerName 属性来了解 ConcurrencyCheck 的工作原理。

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

当调用 SaveChanges 时，由于 BloggerName 字段上的 ConcurrencyCheck 批注，将在更新中使用该属性的原始值。 此命令将尝试通过筛选键值而不是 BloggerName 的原始值来查找正确的行。  下面是发送到数据库的 UPDATE 命令的关键部分，您可以在其中看到命令将更新 PrimaryTrackingKey 为1的行，BloggerName 为 "Julie"，这是从数据库中检索到该博客时的原始值。

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

如果用户同时更改了博客的博客名称，则此更新将会失败，并且你将收到需要处理的 DbUpdateConcurrencyException。

 

## <a name="timestamp"></a>TimeStamp

更常见的情况是使用 rowversion 或时间戳字段进行并发检查。 而不是使用 ConcurrencyCheck 批注，只要属性的类型是字节数组，就可以使用更具体的时间戳注释。 Code first 会将 Timestamp 属性视为与 ConcurrencyCheck 属性相同，但它还将确保代码第一次生成的数据库字段不可为 null。 给定的类中只能有一个时间戳属性。

将以下属性添加到博客类：

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

在数据库表中生成一个不可以为 null 的时间戳列的代码。

![带有时间戳列的博客表](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>表和列

如果允许 Code First 创建数据库，则可能需要更改要创建的表和列的名称。 您还可以将 Code First 与现有数据库一起使用。 但并非总是域中类和属性的名称与数据库中的表和列的名称相匹配。

我的类称为博客和惯例，代码优先假设这将映射到名为 Blog 的表。 如果不是这种情况，则可以指定具有表属性的表的名称。 例如，批注正在将表名指定为 InternalBlogs。

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

在指定映射列的特性时，列批注更熟练地使用。 您可以规定名称、数据类型，甚至是列在表中的显示顺序。 下面是列属性的示例。

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

不要将列的 TypeName 特性与 DataType DataAnnotation 混淆。 数据类型是一种用于 UI 的批注，Code First 会将其忽略。

下面是重新生成表后的表。 表名称已更改为 InternalBlogs，并且来自复杂类型的说明列现在为 BlogDescription。 由于在注释中指定了名称，因此 code first 将不会使用以复杂类型名称开头的列名称。

![博客表和列已重命名](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

重要的数据库功能是具有计算属性的能力。 如果要将 Code First 类映射到包含计算列的表，则不希望实体框架尝试更新这些列。 但是，在插入或更新数据后，您确实需要 EF 从数据库返回这些值。 可以使用 DatabaseGenerated 批注来标记类中的这些属性以及计算所得的枚举。 其他枚举为 None 和 Identity。

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

当代码优先生成数据库时，可以使用在字节或时间戳列上生成的数据库，否则，只应在指向现有数据库时使用此数据库，因为 code first 无法确定计算列的公式。

如上所述，在默认情况下，作为整数的键属性将成为数据库中的标识键。 这与将 DatabaseGenerated 设置为 DatabaseGeneratedOption 相同。 如果您不希望它是标识密钥，则可以将该值设置为 DatabaseGeneratedOption。

 

## <a name="index"></a>Index

> [!NOTE]
> **Ef 6.1 仅**往上-索引属性是在实体框架6.1 中引入的。 如果你使用的是早期版本，则本部分中的信息不适用。

您可以使用**IndexAttribute**对一个或多个列创建索引。 将属性添加到一个或多个属性时，将导致 EF 在创建数据库时在数据库中创建相应的索引，或者如果使用 Code First 迁移，则为相应的**CreateIndex**调用基架。

例如，下面的代码将生成一个索引，该索引是在数据库的 "**发布**" 表的 "**分级**" 列上创建的。

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

默认情况下，索引将命名为**IX\_&lt;属性名称&gt;** （在上面的示例中为 Ix\_评级）。 您还可以指定索引的名称。 下面的示例指定索引应命名为**PostRatingIndex**。

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

默认情况下，索引是不唯一的，但您可以使用**IsUnique**命名参数指定索引应是唯一的。 下面的示例对**用户**的登录名引入唯一索引。

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>多列索引

跨多个列的索引通过在给定表的多个索引批注中使用相同的名称来指定。 创建多列索引时，需要指定索引中列的顺序。 例如，下面的代码创建一个名为**IX\_BlogIdAndRating**的**分级**和**BlogId**的多列索引。 **BlogId**是索引中的第一列，而**评级**是第二列。

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>关系属性： InverseProperty 和 ForeignKey

> [!NOTE]
> 本页提供有关使用数据批注设置 Code First 模型中的关系的信息。 有关 EF 中的关系以及如何使用关系访问和操作数据的一般信息，请参阅[关系 & 导航属性](~/ef6/fundamentals/relationships.md)。 *

Code first 约定将负责您的模型中最常见的关系，但在某些情况下，需要帮助。

更改博客类中键属性的名称时，会创建一个与发布的关系相关的问题。 

生成数据库时，代码优先会在 Post 类中看到 BlogId 属性，并将其识别为与博客类的外键匹配的类名和 "Id"。 但博客类中没有 BlogId 属性。 此解决方案的作用是在 Post 中创建一个导航属性，并使用外 DataAnnotation 帮助代码首先了解如何使用 BlogId 属性构建这两个类之间的关系，以及如何在数据.

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

数据库中的约束显示 InternalBlogs 和 BlogId 之间的关系。 

![InternalBlogs 和 PrimaryTrackingKey 之间的关系。 BlogId](~/ef6/media/jj591583-figure09.png)

当类之间存在多个关系时，将使用 InverseProperty。

在 Post 类中，你可能需要跟踪博客文章的作者，以及编辑者。 下面是 Post 类的两个新导航属性。

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

还需要添加到这些属性引用的 Person 类中。 Person 类将导航属性返回到张贴内容，一个用于该用户撰写的所有帖子，另一个用于该人员所更新的所有帖子。

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code first 不能自行匹配两个类中的属性。 用于发布的数据库表应具有 System.createdby 人员的一个外键，另一个用于 UpdatedBy 人员，而 code first 将创建四个外键属性： Person\_Id、Person\_Id1、System.createdby\_Id 和 UpdatedBy\_Id。

![带有额外外键的 post 表](~/ef6/media/jj591583-figure10.png)

若要解决这些问题，可以使用 InverseProperty 批注来指定属性的对齐方式。

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

因为 Person 中的 PostsWritten 属性知道这指的是 Post 类型，它将生成与 System.createdby 的关系。 同样，PostsUpdated 将连接到 UpdatedBy。 和 code first 不会创建额外的外键。

![发送无额外外键的表](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>摘要

DataAnnotations 不仅使你能够在代码优先类中描述客户端和服务器端验证，还允许你增强，甚至更正代码优先根据其约定对类进行的假设。 使用 DataAnnotations，不仅可以驱动数据库架构生成，还可以将代码的第一类映射到预先存在的数据库。

尽管它们非常灵活，但请记住，DataAnnotations 仅提供您可以在代码优先类上进行的最常见的配置更改。 若要为某些边缘事例配置类，应查看备用配置机制，Code First 的 "流畅" API。

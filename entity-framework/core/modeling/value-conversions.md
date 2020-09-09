---
title: 值转换-EF Core
description: 在 Entity Framework Core 模型中配置值转换器
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 79e54392bf5503b4b651f25ce6e5fc63d418df90
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616671"
---
# <a name="value-conversions"></a>值转换

> [!NOTE]  
> 此功能是 EF Core 2.1 中的新增功能。

值转换器允许在读取或写入数据库时转换属性值。 此转换可以从一个值转换为同一类型的另一个值 (例如，将字符串) 或从一种类型的值加密为另一种类型的值 (例如，在数据库中将枚举值与字符串相互转换。 ) 

## <a name="fundamentals"></a>基础知识

值转换器以和的形式指定 `ModelClrType` `ProviderClrType` 。 模型类型是实体类型中的属性的 .NET 类型。 提供程序类型是数据库提供程序理解的 .NET 类型。 例如，若要将枚举作为字符串保存在数据库中，模型类型是枚举的类型，而提供程序类型为 `String` 。 这两种类型可以相同。

使用两个 `Func` 表达式树来定义转换：一个从 `ModelClrType` 到 `ProviderClrType` ，另一个由 `ProviderClrType` 到 `ModelClrType` 。 使用表达式树，以便可以将它们编译到数据库访问代码中以便进行有效的转换。 对于复杂转换，表达式树可能是对执行转换的方法的简单调用。

## <a name="configuring-a-value-converter"></a>配置值转换器

值转换是在的中的属性上定义的 `OnModelCreating` `DbContext` 。 例如，假设枚举和实体类型定义为：

``` csharp
public class Rider
{
    public int Id { get; set; }
    public EquineBeast Mount { get; set; }
}

public enum EquineBeast
{
    Donkey,
    Mule,
    Horse,
    Unicorn
}
```

然后，可以在中定义转换， `OnModelCreating` 以将枚举值存储为字符串 (例如，在数据库中指定 "Donkey"、"Mule" 和 ... ) ：

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]  
> `null`值绝不会传递到值转换器。 这使得转换的实现变得更简单，并使其能够在可以为 null 和不可为 null 的属性之间共享。

## <a name="the-valueconverter-class"></a>ValueConverter 类

`HasConversion`如上所述调用会创建一个 `ValueConverter` 实例，并在属性中设置该实例。 `ValueConverter`可以显式创建。 例如：

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

当多个属性使用相同的转换时，这可能很有用。

> [!NOTE]  
> 当前无法在一个位置指定给定类型的每个属性都必须使用相同的值转换器。 将来的版本将考虑此功能。

## <a name="built-in-converters"></a>内置转换器

EF Core 附带一组预定义的 `ValueConverter` 类，这些类位于 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空间中。 它们是：

* `BoolToZeroOneConverter` -布尔值为零和一个
* `BoolToStringConverter` -Bool 到字符串（如 "Y" 和 "N"）
* `BoolToTwoValuesConverter` -布尔值到任意两个值
* `BytesToStringConverter` -字节数组到 Base64 编码的字符串
* `CastingConverter` -只需要类型强制转换的转换
* `CharToStringConverter` -Char 到单字符字符串
* `DateTimeOffsetToBinaryConverter` -DateTimeOffset 到二进制编码的64位值
* `DateTimeOffsetToBytesConverter` -DateTimeOffset 到字节数组
* `DateTimeOffsetToStringConverter` -DateTimeOffset 到字符串
* `DateTimeToBinaryConverter` -DateTime 到64位值，包括 Datetimekind.utc
* `DateTimeToStringConverter` -DateTime 到 string
* `DateTimeToTicksConverter` -DateTime 到计时周期
* `EnumToNumberConverter` -枚举到基础数字
* `EnumToStringConverter` -枚举到字符串
* `GuidToBytesConverter` -Guid 到字节数组
* `GuidToStringConverter` -Guid 到字符串
* `NumberToBytesConverter` -任何数值到字节数组
* `NumberToStringConverter` -任何数值到字符串
* `StringToBytesConverter` -字符串到 UTF8 字节
* `TimeSpanToStringConverter` -TimeSpan 到字符串
* `TimeSpanToTicksConverter` -TimeSpan 到计时周期

请注意， `EnumToStringConverter` 此列表中包含。 这意味着无需显式指定转换，如上所示。 相反，只需使用内置转换器：

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

请注意，所有内置的转换器都是无状态的，因此，多个属性可以安全地共享单个实例。

## <a name="pre-defined-conversions"></a>预定义的转换

对于内置转换器存在的常见转换，无需显式指定转换器。 相反，只需配置应使用的提供程序类型，EF 会自动使用适当的内置转换器。 枚举到字符串的转换用作上面的示例，但如果配置了提供程序类型，则 EF 实际上会自动执行此操作：

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

可以通过显式指定列类型来实现相同的目的。 例如，如果定义了实体类型，如下所示：

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

然后，枚举值将作为字符串保存在数据库中，而不会在中进行任何进一步的配置 `OnModelCreating` 。

## <a name="limitations"></a>限制

值转换系统存在一些已知的当前限制：

* 如上所述， `null` 无法转换。
* 目前没有办法将一个属性转换为多个列，反之亦然。
* 使用值转换可能会影响 EF Core 将表达式转换为 SQL 的能力。 这种情况下会记录警告。
将来的版本将考虑删除这些限制。

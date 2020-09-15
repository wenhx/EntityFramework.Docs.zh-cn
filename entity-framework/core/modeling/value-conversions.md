---
title: 值转换-EF Core
description: 在 Entity Framework Core 模型中配置值转换器
author: ajcvickers
ms.date: 02/19/2018
uid: core/modeling/value-conversions
ms.openlocfilehash: 1d347eb6a7fcdcb55239e1fa854f6c38ab081b21
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072546"
---
# <a name="value-conversions"></a><span data-ttu-id="94fd0-103">值转换</span><span class="sxs-lookup"><span data-stu-id="94fd0-103">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="94fd0-104">此功能是 EF Core 2.1 中的新增功能。</span><span class="sxs-lookup"><span data-stu-id="94fd0-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="94fd0-105">值转换器允许在读取或写入数据库时转换属性值。</span><span class="sxs-lookup"><span data-stu-id="94fd0-105">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="94fd0-106">此转换可以从一个值转换为同一类型的另一个值 (例如，将字符串) 或从一种类型的值加密为另一种类型的值 (例如，在数据库中将枚举值与字符串相互转换。 ) </span><span class="sxs-lookup"><span data-stu-id="94fd0-106">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="94fd0-107">基础知识</span><span class="sxs-lookup"><span data-stu-id="94fd0-107">Fundamentals</span></span>

<span data-ttu-id="94fd0-108">值转换器以和的形式指定 `ModelClrType` `ProviderClrType` 。</span><span class="sxs-lookup"><span data-stu-id="94fd0-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="94fd0-109">模型类型是实体类型中的属性的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="94fd0-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="94fd0-110">提供程序类型是数据库提供程序理解的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="94fd0-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="94fd0-111">例如，若要将枚举作为字符串保存在数据库中，模型类型是枚举的类型，而提供程序类型为 `String` 。</span><span class="sxs-lookup"><span data-stu-id="94fd0-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="94fd0-112">这两种类型可以相同。</span><span class="sxs-lookup"><span data-stu-id="94fd0-112">These two types can be the same.</span></span>

<span data-ttu-id="94fd0-113">使用两个 `Func` 表达式树来定义转换：一个从 `ModelClrType` 到 `ProviderClrType` ，另一个由 `ProviderClrType` 到 `ModelClrType` 。</span><span class="sxs-lookup"><span data-stu-id="94fd0-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="94fd0-114">使用表达式树，以便可以将它们编译到数据库访问代码中以便进行有效的转换。</span><span class="sxs-lookup"><span data-stu-id="94fd0-114">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="94fd0-115">对于复杂转换，表达式树可能是对执行转换的方法的简单调用。</span><span class="sxs-lookup"><span data-stu-id="94fd0-115">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="94fd0-116">配置值转换器</span><span class="sxs-lookup"><span data-stu-id="94fd0-116">Configuring a value converter</span></span>

<span data-ttu-id="94fd0-117">值转换是在的中的属性上定义的 `OnModelCreating` `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="94fd0-117">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="94fd0-118">例如，假设枚举和实体类型定义为：</span><span class="sxs-lookup"><span data-stu-id="94fd0-118">For example, consider an enum and entity type defined as:</span></span>

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

<span data-ttu-id="94fd0-119">然后，可以在中定义转换， `OnModelCreating` 以将枚举值存储为字符串 (例如，在数据库中指定 "Donkey"、"Mule" 和 ... ) ：</span><span class="sxs-lookup"><span data-stu-id="94fd0-119">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

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
> <span data-ttu-id="94fd0-120">`null`值绝不会传递到值转换器。</span><span class="sxs-lookup"><span data-stu-id="94fd0-120">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="94fd0-121">这使得转换的实现变得更简单，并使其能够在可以为 null 和不可为 null 的属性之间共享。</span><span class="sxs-lookup"><span data-stu-id="94fd0-121">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="94fd0-122">ValueConverter 类</span><span class="sxs-lookup"><span data-stu-id="94fd0-122">The ValueConverter class</span></span>

<span data-ttu-id="94fd0-123">`HasConversion`如上所述调用会创建一个 `ValueConverter` 实例，并在属性中设置该实例。</span><span class="sxs-lookup"><span data-stu-id="94fd0-123">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="94fd0-124">`ValueConverter`可以显式创建。</span><span class="sxs-lookup"><span data-stu-id="94fd0-124">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="94fd0-125">例如：</span><span class="sxs-lookup"><span data-stu-id="94fd0-125">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="94fd0-126">当多个属性使用相同的转换时，这可能很有用。</span><span class="sxs-lookup"><span data-stu-id="94fd0-126">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="94fd0-127">当前无法在一个位置指定给定类型的每个属性都必须使用相同的值转换器。</span><span class="sxs-lookup"><span data-stu-id="94fd0-127">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="94fd0-128">将来的版本将考虑此功能。</span><span class="sxs-lookup"><span data-stu-id="94fd0-128">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="94fd0-129">内置转换器</span><span class="sxs-lookup"><span data-stu-id="94fd0-129">Built-in converters</span></span>

<span data-ttu-id="94fd0-130">EF Core 附带一组预定义的 `ValueConverter` 类，这些类位于 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="94fd0-130">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="94fd0-131">它们是：</span><span class="sxs-lookup"><span data-stu-id="94fd0-131">These are:</span></span>

* <span data-ttu-id="94fd0-132">`BoolToZeroOneConverter` -布尔值为零和一个</span><span class="sxs-lookup"><span data-stu-id="94fd0-132">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="94fd0-133">`BoolToStringConverter` -Bool 到字符串（如 "Y" 和 "N"）</span><span class="sxs-lookup"><span data-stu-id="94fd0-133">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="94fd0-134">`BoolToTwoValuesConverter` -布尔值到任意两个值</span><span class="sxs-lookup"><span data-stu-id="94fd0-134">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="94fd0-135">`BytesToStringConverter` -字节数组到 Base64 编码的字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-135">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="94fd0-136">`CastingConverter` -只需要类型强制转换的转换</span><span class="sxs-lookup"><span data-stu-id="94fd0-136">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="94fd0-137">`CharToStringConverter` -Char 到单字符字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-137">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="94fd0-138">`DateTimeOffsetToBinaryConverter` -DateTimeOffset 到二进制编码的64位值</span><span class="sxs-lookup"><span data-stu-id="94fd0-138">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="94fd0-139">`DateTimeOffsetToBytesConverter` -DateTimeOffset 到字节数组</span><span class="sxs-lookup"><span data-stu-id="94fd0-139">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="94fd0-140">`DateTimeOffsetToStringConverter` -DateTimeOffset 到字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-140">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="94fd0-141">`DateTimeToBinaryConverter` -DateTime 到64位值，包括 Datetimekind.utc</span><span class="sxs-lookup"><span data-stu-id="94fd0-141">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="94fd0-142">`DateTimeToStringConverter` -DateTime 到 string</span><span class="sxs-lookup"><span data-stu-id="94fd0-142">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="94fd0-143">`DateTimeToTicksConverter` -DateTime 到计时周期</span><span class="sxs-lookup"><span data-stu-id="94fd0-143">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="94fd0-144">`EnumToNumberConverter` -枚举到基础数字</span><span class="sxs-lookup"><span data-stu-id="94fd0-144">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="94fd0-145">`EnumToStringConverter` -枚举到字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-145">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="94fd0-146">`GuidToBytesConverter` -Guid 到字节数组</span><span class="sxs-lookup"><span data-stu-id="94fd0-146">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="94fd0-147">`GuidToStringConverter` -Guid 到字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-147">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="94fd0-148">`NumberToBytesConverter` -任何数值到字节数组</span><span class="sxs-lookup"><span data-stu-id="94fd0-148">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="94fd0-149">`NumberToStringConverter` -任何数值到字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-149">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="94fd0-150">`StringToBytesConverter` -字符串到 UTF8 字节</span><span class="sxs-lookup"><span data-stu-id="94fd0-150">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="94fd0-151">`TimeSpanToStringConverter` -TimeSpan 到字符串</span><span class="sxs-lookup"><span data-stu-id="94fd0-151">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="94fd0-152">`TimeSpanToTicksConverter` -TimeSpan 到计时周期</span><span class="sxs-lookup"><span data-stu-id="94fd0-152">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="94fd0-153">请注意， `EnumToStringConverter` 此列表中包含。</span><span class="sxs-lookup"><span data-stu-id="94fd0-153">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="94fd0-154">这意味着无需显式指定转换，如上所示。</span><span class="sxs-lookup"><span data-stu-id="94fd0-154">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="94fd0-155">相反，只需使用内置转换器：</span><span class="sxs-lookup"><span data-stu-id="94fd0-155">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="94fd0-156">请注意，所有内置的转换器都是无状态的，因此，多个属性可以安全地共享单个实例。</span><span class="sxs-lookup"><span data-stu-id="94fd0-156">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="94fd0-157">预定义的转换</span><span class="sxs-lookup"><span data-stu-id="94fd0-157">Pre-defined conversions</span></span>

<span data-ttu-id="94fd0-158">对于内置转换器存在的常见转换，无需显式指定转换器。</span><span class="sxs-lookup"><span data-stu-id="94fd0-158">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="94fd0-159">相反，只需配置应使用的提供程序类型，EF 会自动使用适当的内置转换器。</span><span class="sxs-lookup"><span data-stu-id="94fd0-159">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="94fd0-160">枚举到字符串的转换用作上面的示例，但如果配置了提供程序类型，则 EF 实际上会自动执行此操作：</span><span class="sxs-lookup"><span data-stu-id="94fd0-160">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="94fd0-161">可以通过显式指定列类型来实现相同的目的。</span><span class="sxs-lookup"><span data-stu-id="94fd0-161">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="94fd0-162">例如，如果定义了实体类型，如下所示：</span><span class="sxs-lookup"><span data-stu-id="94fd0-162">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="94fd0-163">然后，枚举值将作为字符串保存在数据库中，而不会在中进行任何进一步的配置 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="94fd0-163">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="94fd0-164">限制</span><span class="sxs-lookup"><span data-stu-id="94fd0-164">Limitations</span></span>

<span data-ttu-id="94fd0-165">值转换系统存在一些已知的当前限制：</span><span class="sxs-lookup"><span data-stu-id="94fd0-165">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="94fd0-166">如上所述， `null` 无法转换。</span><span class="sxs-lookup"><span data-stu-id="94fd0-166">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="94fd0-167">目前没有办法将一个属性转换为多个列，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="94fd0-167">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="94fd0-168">使用值转换可能会影响 EF Core 将表达式转换为 SQL 的能力。</span><span class="sxs-lookup"><span data-stu-id="94fd0-168">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="94fd0-169">这种情况下会记录警告。</span><span class="sxs-lookup"><span data-stu-id="94fd0-169">A warning will be logged for such cases.</span></span>
<span data-ttu-id="94fd0-170">将来的版本将考虑删除这些限制。</span><span class="sxs-lookup"><span data-stu-id="94fd0-170">Removal of these limitations is being considered for a future release.</span></span>

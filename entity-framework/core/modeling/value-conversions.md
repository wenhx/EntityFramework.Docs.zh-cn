---
title: 值转换-EF Core
description: 在 Entity Framework Core 模型中配置值转换器
author: ajcvickers
ms.date: 02/19/2018
uid: core/modeling/value-conversions
ms.openlocfilehash: 221560a145fe25c2b7bf094839dd37791bc25955
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063954"
---
# <a name="value-conversions"></a><span data-ttu-id="08475-103">值转换</span><span class="sxs-lookup"><span data-stu-id="08475-103">Value Conversions</span></span>

<span data-ttu-id="08475-104">值转换器允许在读取或写入数据库时转换属性值。</span><span class="sxs-lookup"><span data-stu-id="08475-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="08475-105">此转换可以从一个值转换为同一类型的另一个值 (例如，将字符串) 或从一种类型的值加密为另一种类型的值 (例如，在数据库中将枚举值与字符串相互转换。 ) </span><span class="sxs-lookup"><span data-stu-id="08475-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="08475-106">基础知识</span><span class="sxs-lookup"><span data-stu-id="08475-106">Fundamentals</span></span>

<span data-ttu-id="08475-107">值转换器以和的形式指定 `ModelClrType` `ProviderClrType` 。</span><span class="sxs-lookup"><span data-stu-id="08475-107">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="08475-108">模型类型是实体类型中的属性的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="08475-108">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="08475-109">提供程序类型是数据库提供程序理解的 .NET 类型。</span><span class="sxs-lookup"><span data-stu-id="08475-109">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="08475-110">例如，若要将枚举作为字符串保存在数据库中，模型类型是枚举的类型，而提供程序类型为 `String` 。</span><span class="sxs-lookup"><span data-stu-id="08475-110">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="08475-111">这两种类型可以相同。</span><span class="sxs-lookup"><span data-stu-id="08475-111">These two types can be the same.</span></span>

<span data-ttu-id="08475-112">使用两个 `Func` 表达式树来定义转换：一个从 `ModelClrType` 到 `ProviderClrType` ，另一个由 `ProviderClrType` 到 `ModelClrType` 。</span><span class="sxs-lookup"><span data-stu-id="08475-112">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="08475-113">使用表达式树，以便可以将它们编译到数据库访问代码中以便进行有效的转换。</span><span class="sxs-lookup"><span data-stu-id="08475-113">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="08475-114">对于复杂转换，表达式树可能是对执行转换的方法的简单调用。</span><span class="sxs-lookup"><span data-stu-id="08475-114">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="08475-115">配置值转换器</span><span class="sxs-lookup"><span data-stu-id="08475-115">Configuring a value converter</span></span>

<span data-ttu-id="08475-116">值转换是在的中的属性上定义的 `OnModelCreating` `DbContext` 。</span><span class="sxs-lookup"><span data-stu-id="08475-116">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="08475-117">例如，假设枚举和实体类型定义为：</span><span class="sxs-lookup"><span data-stu-id="08475-117">For example, consider an enum and entity type defined as:</span></span>

```csharp
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

<span data-ttu-id="08475-118">然后，可以在中定义转换， `OnModelCreating` 以将枚举值存储为字符串 (例如，在数据库中指定 "Donkey"、"Mule" 和 ... ) ：</span><span class="sxs-lookup"><span data-stu-id="08475-118">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

```csharp
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
> <span data-ttu-id="08475-119">`null`值绝不会传递到值转换器。</span><span class="sxs-lookup"><span data-stu-id="08475-119">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="08475-120">这使得转换的实现变得更简单，并使其能够在可以为 null 和不可为 null 的属性之间共享。</span><span class="sxs-lookup"><span data-stu-id="08475-120">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="08475-121">ValueConverter 类</span><span class="sxs-lookup"><span data-stu-id="08475-121">The ValueConverter class</span></span>

<span data-ttu-id="08475-122">`HasConversion`如上所述调用会创建一个 `ValueConverter` 实例，并在属性中设置该实例。</span><span class="sxs-lookup"><span data-stu-id="08475-122">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="08475-123">`ValueConverter`可以显式创建。</span><span class="sxs-lookup"><span data-stu-id="08475-123">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="08475-124">例如：</span><span class="sxs-lookup"><span data-stu-id="08475-124">For example:</span></span>

```csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="08475-125">当多个属性使用相同的转换时，这可能很有用。</span><span class="sxs-lookup"><span data-stu-id="08475-125">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]
> <span data-ttu-id="08475-126">当前无法在一个位置指定给定类型的每个属性都必须使用相同的值转换器。</span><span class="sxs-lookup"><span data-stu-id="08475-126">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="08475-127">将来的版本将考虑此功能。</span><span class="sxs-lookup"><span data-stu-id="08475-127">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="08475-128">内置转换器</span><span class="sxs-lookup"><span data-stu-id="08475-128">Built-in converters</span></span>

<span data-ttu-id="08475-129">EF Core 附带一组预定义的 `ValueConverter` 类，这些类位于 `Microsoft.EntityFrameworkCore.Storage.ValueConversion` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="08475-129">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="08475-130">这些是：</span><span class="sxs-lookup"><span data-stu-id="08475-130">These are:</span></span>

* <span data-ttu-id="08475-131">`BoolToZeroOneConverter` -布尔值为零和一个</span><span class="sxs-lookup"><span data-stu-id="08475-131">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="08475-132">`BoolToStringConverter` -Bool 到字符串（如 "Y" 和 "N"）</span><span class="sxs-lookup"><span data-stu-id="08475-132">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="08475-133">`BoolToTwoValuesConverter` -布尔值到任意两个值</span><span class="sxs-lookup"><span data-stu-id="08475-133">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="08475-134">`BytesToStringConverter` -字节数组到 Base64 编码的字符串</span><span class="sxs-lookup"><span data-stu-id="08475-134">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="08475-135">`CastingConverter` -只需要类型强制转换的转换</span><span class="sxs-lookup"><span data-stu-id="08475-135">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="08475-136">`CharToStringConverter` -Char 到单字符字符串</span><span class="sxs-lookup"><span data-stu-id="08475-136">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="08475-137">`DateTimeOffsetToBinaryConverter` -DateTimeOffset 到二进制编码的64位值</span><span class="sxs-lookup"><span data-stu-id="08475-137">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="08475-138">`DateTimeOffsetToBytesConverter` -DateTimeOffset 到字节数组</span><span class="sxs-lookup"><span data-stu-id="08475-138">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="08475-139">`DateTimeOffsetToStringConverter` -DateTimeOffset 到字符串</span><span class="sxs-lookup"><span data-stu-id="08475-139">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="08475-140">`DateTimeToBinaryConverter` -DateTime 到64位值，包括 Datetimekind.utc</span><span class="sxs-lookup"><span data-stu-id="08475-140">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="08475-141">`DateTimeToStringConverter` -DateTime 到 string</span><span class="sxs-lookup"><span data-stu-id="08475-141">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="08475-142">`DateTimeToTicksConverter` -DateTime 到计时周期</span><span class="sxs-lookup"><span data-stu-id="08475-142">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="08475-143">`EnumToNumberConverter` -枚举到基础数字</span><span class="sxs-lookup"><span data-stu-id="08475-143">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="08475-144">`EnumToStringConverter` -枚举到字符串</span><span class="sxs-lookup"><span data-stu-id="08475-144">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="08475-145">`GuidToBytesConverter` -Guid 到字节数组</span><span class="sxs-lookup"><span data-stu-id="08475-145">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="08475-146">`GuidToStringConverter` -Guid 到字符串</span><span class="sxs-lookup"><span data-stu-id="08475-146">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="08475-147">`NumberToBytesConverter` -任何数值到字节数组</span><span class="sxs-lookup"><span data-stu-id="08475-147">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="08475-148">`NumberToStringConverter` -任何数值到字符串</span><span class="sxs-lookup"><span data-stu-id="08475-148">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="08475-149">`StringToBytesConverter` -字符串到 UTF8 字节</span><span class="sxs-lookup"><span data-stu-id="08475-149">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="08475-150">`TimeSpanToStringConverter` -TimeSpan 到字符串</span><span class="sxs-lookup"><span data-stu-id="08475-150">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="08475-151">`TimeSpanToTicksConverter` -TimeSpan 到计时周期</span><span class="sxs-lookup"><span data-stu-id="08475-151">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="08475-152">请注意， `EnumToStringConverter` 此列表中包含。</span><span class="sxs-lookup"><span data-stu-id="08475-152">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="08475-153">这意味着无需显式指定转换，如上所示。</span><span class="sxs-lookup"><span data-stu-id="08475-153">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="08475-154">相反，只需使用内置转换器：</span><span class="sxs-lookup"><span data-stu-id="08475-154">Instead, just use the built-in converter:</span></span>

```csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="08475-155">请注意，所有内置的转换器都是无状态的，因此，多个属性可以安全地共享单个实例。</span><span class="sxs-lookup"><span data-stu-id="08475-155">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="08475-156">预定义的转换</span><span class="sxs-lookup"><span data-stu-id="08475-156">Pre-defined conversions</span></span>

<span data-ttu-id="08475-157">对于内置转换器存在的常见转换，无需显式指定转换器。</span><span class="sxs-lookup"><span data-stu-id="08475-157">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="08475-158">相反，只需配置应使用的提供程序类型，EF 会自动使用适当的内置转换器。</span><span class="sxs-lookup"><span data-stu-id="08475-158">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="08475-159">枚举到字符串的转换用作上面的示例，但如果配置了提供程序类型，则 EF 实际上会自动执行此操作：</span><span class="sxs-lookup"><span data-stu-id="08475-159">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

```csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="08475-160">可以通过显式指定列类型来实现相同的目的。</span><span class="sxs-lookup"><span data-stu-id="08475-160">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="08475-161">例如，如果定义了实体类型，如下所示：</span><span class="sxs-lookup"><span data-stu-id="08475-161">For example, if the entity type is defined like so:</span></span>

```csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="08475-162">然后，枚举值将作为字符串保存在数据库中，而不会在中进行任何进一步的配置 `OnModelCreating` 。</span><span class="sxs-lookup"><span data-stu-id="08475-162">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="08475-163">限制</span><span class="sxs-lookup"><span data-stu-id="08475-163">Limitations</span></span>

<span data-ttu-id="08475-164">值转换系统存在一些已知的当前限制：</span><span class="sxs-lookup"><span data-stu-id="08475-164">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="08475-165">如上所述， `null` 无法转换。</span><span class="sxs-lookup"><span data-stu-id="08475-165">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="08475-166">目前没有办法将一个属性转换为多个列，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="08475-166">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="08475-167">使用值转换可能会影响 EF Core 将表达式转换为 SQL 的能力。</span><span class="sxs-lookup"><span data-stu-id="08475-167">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="08475-168">这种情况下会记录警告。</span><span class="sxs-lookup"><span data-stu-id="08475-168">A warning will be logged for such cases.</span></span>
<span data-ttu-id="08475-169">将来的版本将考虑删除这些限制。</span><span class="sxs-lookup"><span data-stu-id="08475-169">Removal of these limitations is being considered for a future release.</span></span>

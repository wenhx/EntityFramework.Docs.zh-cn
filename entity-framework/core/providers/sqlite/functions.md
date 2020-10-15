---
title: 函数映射-SQLite 数据库提供程序-EF Core
description: SQLite EF Core 数据库提供程序的函数映射
author: bricelam
ms.date: 10/06/2020
uid: core/providers/sqlite/functions
ms.openlocfilehash: 0787981a0c6fa401a7ef4b4c3c2b406f78117ad1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066513"
---
# <a name="function-mappings-of-the-sqlite-ef-core-provider"></a>SQLite EF Core 提供程序的函数映射

此页显示在使用 SQLite 提供程序时，哪些 .NET 成员转换为哪些 SQL 函数。

## <a name="binary-functions"></a>二进制函数

.NET                        | SQL                             | 在
--------------------------- | ------------------------------- | --------
字节.包含 (值)        | instr (@bytes ，char (@value) # A3 > 0 | EF Core 5.0
字节.长短                | 长度 (@bytes)                   | EF Core 5.0
字节.SequenceEqual (second)  | @bytes = @second                | EF Core 5.0

## <a name="date-and-time-functions"></a>日期和时间函数

.NET                            | SQL                                                                      | 在
------------------------------- | ------------------------------------------------------------------------ | --------
DateTime.Now                    | datetime ( "now"、"localtime" ) 
日期时间. 今天                  | datetime ( "now"、"localtime"、"day day start" ) 
DateTime.UtcNow                 | datetime ( "now" ) 
AddDays (值)          | datetime (@dateTime ， @value \| \| "days" ) 
AddHours (值)         | datetime (@dateTime ， @d \| \| "hours" ) 
AddMilliseconds (值)  | datetime (@dateTime ， (@value /1000.0) \| \| "秒" )                    | EF Core 2.2
AddMinutes (值)       | datetime (@dateTime @value \| \| "分钟" ) 
AddMonths (月)       | datetime (@dateTime ， @months \| \| "months" ) 
AddSeconds (值)       | datetime (@dateTime ， @value \| \| "seconds" ) 
AddTicks (值)         | datetime (@dateTime ， (@value /10000000.0) \| \| "秒" )                | EF Core 2.2
AddYears (值)         | datetime (@dateTime ， @value \| \| "年" ) 
日期时间                   | datetime (@dateTime "天的开头" ) 
dateTime. Day                    | strftime ( '% d '， @dateTime) 
DayOfWeek              | strftime ( "% w"， @dateTime)                                                 | EF Core 2.2
DayOfYear              | strftime ( "% j" @dateTime) 
dateTime. 小时                   | strftime ( "% H"， @dateTime) 
dateTime. 毫秒            |  (strftime ( "% f"， @dateTime) * 1000) % 1000
dateTime. 分钟                 | strftime ( "% M" @dateTime) 
日期时间月                  | strftime ( "% m" @dateTime) 
dateTime。秒                 | strftime ( '% S '， @dateTime) 
dateTime 计时周期                  |  (julianday (@dateTime) -julianday ( "0001-01-01 00:00:00" ) # A5 * 864000000000 | EF Core 2.2
dateTime. TimeOfDay              | 时间 (@dateTime)                                                           | EF Core 3.0
日期时间年份                   | strftime ( "% Y"， @dateTime) 

> [!NOTE]
> 出于说明目的，已经简化了一些 SQL。 实际 SQL 更复杂，可以处理更大范围的值。

## <a name="numeric-functions"></a>数字函数

.NET                  | SQL                                  | 在
--------------------- | ------------------------------------ | --------
-decimalValue         | ef_negate (@decimalValue)              | EF Core 5.0
decimalValue-d      | ef_add (@decimalValue ，ef_negate (@d) # A3 | EF Core 5.0
decimalValue * d      | ef_multiply (@decimalValue ， @d)        | EF Core 5.0
decimalValue/d      | ef_divide (@decimalValue ， @d)          | EF Core 5.0
decimalValue% d      | ef_mod (@decimalValue ， @d)             | EF Core 5.0
decimalValue + d      | ef_add (@decimalValue ， @d)             | EF Core 5.0
decimalValue < d      | ef_compare (@decimalValue ， @d) < 0    | EF Core 5.0
decimalValue <= d     | ef_compare (@decimalValue ， @d) <= 0   | EF Core 5.0
decimalValue > d      | ef_compare (@decimalValue ， @d) > 0    | EF Core 5.0
decimalValue >= d     | ef_compare (@decimalValue ， @d) >= 0   | EF Core 5.0
doubleValue% d       | ef_mod (@doubleValue ， @d)              | EF Core 5.0
floatValue% d        | ef_mod (@floatValue ， @d)               | EF Core 5.0
Math (值)        | abs (@value) 
Max (val1，val2)   | 最大 (@val1 ， @val2) 
Math (val1，val2)   | 最小 (@val1 ， @val2) 
 (d) 的数学运算         | 舍入 (@d) 
 (d，数字)  | 舍入 (@d ， @digits) 

> [!TIP]
> 使用 *ef* 作为前缀的 SQL 函数由 EF Core 创建。

## <a name="string-functions"></a>字符串函数

.NET                                                         | SQL                                                    | 在
------------------------------------------------------------ | ------------------------------------------------------ | --------
EF.函数 (操作数，排序规则)                      | @operand COLLATE @collation                            | EF Core 5.0
EF.函数。例如 (matchExpression，pattern)                   | @matchExpression 外观 @pattern
EF.函数。例如 (matchExpression、pattern、escapeCharacter)  | @matchExpression LIKE @pattern ESCAPE @escapeCharacter
string. 比较 (strA，strB)                                    | @strA等于 = @strB THEN 0 .。。端面
类似.Concat (str0，str1)                                     | @str0 \|\| @str1
类似.IsNullOrEmpty (值)                                   | @value 为 NULL 或 @value = ""
类似.String.isnullorwhitespace (值)                              | @value 为 NULL 或 trim (@value) = ""
stringValue. CompareTo (strB)                                   | @stringValue等于 = @strB THEN 0 .。。端面
stringValue 包含 (值)                                   | instr (@stringValue ， @value) > 0
stringValue. EndsWith (值)                                   | @stringValue类似于 "%" \| \|@value
stringValue. FirstOrDefault ( # A1                                 | substr (@stringValue ，1，1)                              | EF Core 5.0
stringValue. IndexOf (值)                                    | instr (@stringValue ， @value) -1
stringValue. LastOrDefault ( # A1                                  | substr (@stringValue ，长度 (@stringValue) ，1)           | EF Core 5.0
stringValue                                           | 长度 (@stringValue) 
stringValue (oldValue，newValue)                       | 替换 (@stringValue 、 @oldValue @newValue) 
stringValue. StartsWith (值)                                 | @stringValue类似于 @value \| \| "%"
stringValue (startIndex，长度)                     | substr (@stringValue ， @startIndex + 1 @length) 
stringValue. ToLower ( # A1                                        | 降低 (@stringValue) 
stringValue. ToUpper ( # A1                                        |  (的 @stringValue) 
stringValue ( # A1                                           | 剪裁 (@stringValue) 
stringValue (trimChar)                                    | 剪裁 (@stringValue ， @trimChar) 
stringValue. TrimEnd ( # A1                                        | rtrim (@stringValue) 
stringValue. TrimEnd (trimChar)                                 | rtrim (@stringValue ， @trimChar) 
stringValue. TrimStart ( # A1                                      | ltrim (@stringValue) 
stringValue. TrimStart (trimChar)                               | ltrim (@stringValue ， @trimChar) 

> [!NOTE]
> 出于说明目的，已经简化了一些 SQL。 实际 SQL 更复杂，可以处理更大范围的值。

## <a name="miscellaneous-functions"></a>杂项函数

.NET                                     | SQL                                | 在
---------------------------------------- | ---------------------------------- | --------
集合.包含 (项)                 | @item 中 @collection               | EF Core 3.0
属于 enumvalue. Enum.hasflag\ (标志)                   | @enumValue & @flag = @flag
可以为 null.Context.request.headers.getvalueordefault ( # A1             | 合并 (@nullable ，0) 
可以为 null.Context.request.headers.getvalueordefault (defaultValue)  | 合并 (@nullable ， @defaultValue) 

## <a name="see-also"></a>另请参阅

* [空间函数映射](xref:core/providers/sqlite/spatial#spatial-function-mappings)

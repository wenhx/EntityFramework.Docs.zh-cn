---
title: 实体框架术语表-EF6
description: 实体框架6术语表
author: divega
ms.date: 10/23/2016
ms.assetid: 3f05ffdd-49bc-499c-9732-4a368bf5d2d7
uid: ef6/resources/glossary
ms.openlocfilehash: 19d5e9e3a480337c2bcb93be5f989cc622b67dad
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620200"
---
# <a name="entity-framework-glossary"></a>实体框架术语表
## <a name="code-first"></a>Code First
使用代码创建实体框架模型。 该模型可以面向现有数据库或新数据库。

## <a name="context"></a>上下文
一个类，它表示与数据库的会话，使您能够查询和保存数据。 上下文派生自 DbContext 或 ObjectContext 类。

## <a name="convention-code-first"></a>约定 (Code First) 
实体框架使用来从类推断模型形状的规则。

## <a name="database-first"></a>Database First
使用 EF 设计器创建面向现有数据库的实体框架模型。

## <a name="eager-loading"></a>预先加载
加载相关数据的一种模式，其中一种类型的实体的查询还会将相关实体作为查询的一部分进行加载。

## <a name="ef-designer"></a>EF 设计器
Visual Studio 中的可视化设计器，可用于使用框和线条创建实体框架模型。

## <a name="entity"></a>实体
表示客户、产品和订单等应用程序数据的类或对象。

## <a name="entity-data-model"></a>实体数据模型
描述实体和它们之间的关系的模型。 EF 使用 EDM 来描述开发人员计划的概念模型。 EDM 在 Chen 引入的实体关系模型的基础上生成。 EDM 最初是使用成为 Microsoft 的一系列开发人员和服务器技术的主要目标而开发的。 EDM 还可作为 OData 协议的一部分。

## <a name="explicit-loading"></a>显式加载
一种用于加载相关数据的模式，其中通过调用 API 加载相关的对象。

## <a name="fluent-api"></a>Fluent API
可用于配置 Code First 模型的 API。

## <a name="foreign-key-association"></a>外键关联
实体之间的关联，其中表示外键的属性包含在依赖实体的类中。 例如，Product 包含 "类别 Id" 属性。

## <a name="identifying-relationship"></a>标识关系
一种关系，其中主体实体的主键是依赖实体的主键的一部分。 在这种关系中，没有主体实体，依赖实体就不能存在。

## <a name="independent-association"></a>独立关联
实体之间的关联，其中不存在表示依赖实体的类中的外键的属性。 例如，Product 类包含与 Category 的关系，但没有 "类别 Id" 属性。 实体框架跟踪关联的状态，这与两个关联端的实体状态无关。

## <a name="lazy-loading"></a>延迟加载
一种加载相关数据的模式，在访问导航属性时，会自动加载相关的对象。

## <a name="model-first"></a>Model First
使用 EF 设计器创建实体框架模型，然后使用该设计器创建新的数据库。

## <a name="navigation-property"></a>导航属性
引用其他实体的实体的属性。 例如，Product 包含类别导航属性，类别包含 Products 导航属性。

## <a name="poco"></a>POCO
纯旧式 CLR 对象的缩写。 不与任何框架具有依赖关系的简单用户类。 在 EF 的上下文中，不是派生自 EntityObject 的实体类实现任何接口，或携带 EF 中定义的任何属性。 与持久性框架分离的此类实体类也称为 "持久性未知"。  

## <a name="relationship-inverse"></a>逆关系
关系的相反结束，例如 product。类别和类别。产品.

## <a name="self-tracking-entity"></a>自跟踪实体
从代码生成模板生成的实体，可帮助进行 N 层开发。

## <a name="table-per-concrete-type-tpc"></a>每个具体的表类型 (TPC) 
一种映射继承的方法，其中层次结构中的每个非抽象类型都映射到数据库中的单独表。

## <a name="table-per-hierarchy-tph"></a>每个层次结构一个表 (TPH) 
一种映射继承的方法，其中，层次结构中的所有类型都映射到数据库中的同一表。 鉴别器列 (s) 用于标识与每行关联的类型。

## <a name="table-per-type-tpt"></a>每种类型一个表 (TPT) 
一种映射继承的方法，其中层次结构中所有类型的公共属性都映射到数据库中的同一个表，但每个类型独有的属性将映射到一个单独的表。

## <a name="type-discovery"></a>类型发现
标识应属于实体框架模型的类型的过程。

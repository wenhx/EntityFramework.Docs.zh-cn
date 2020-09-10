---
title: 恢复到 Entity Framework Designer 中的 ObjectContext EF6
description: 恢复到实体框架6的 Entity Framework Designer 中的 ObjectContext
author: divega
ms.date: 10/23/2016
ms.assetid: 36550569-a1de-47cb-ba6d-544794ffd500
uid: ef6/modeling/designer/codegen/legacy-objectcontext
ms.openlocfilehash: e09cdfe994033bc8dea26bb09c4560788dd746f8
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620536"
---
# <a name="reverting-to-objectcontext-in-entity-framework-designer"></a>恢复到 Entity Framework Designer 中的 ObjectContext
使用早期版本的实体框架使用 EF 设计器创建的模型将生成派生自 EntityObject 的 ObjectContext 和实体类的上下文。

从 EF 4.1 开始，我们建议换到代码生成模板，该模板生成派生自 DbContext 和 POCO 实体类的上下文。

在 Visual Studio 2012 中，会为使用 EF 设计器创建的所有新模型获取默认生成的 DbContext 代码。 现有模型将继续生成基于 ObjectContext 的代码，除非你决定要切换到基于 DbContext 的代码生成器。

## <a name="reverting-back-to-objectcontext-code-generation"></a>恢复到 ObjectContext 代码生成

### <a name="1-disable-dbcontext-code-generation"></a>1. 禁用 DbContext 代码生成

派生的 DbContext 和 POCO 类的生成由项目中的两个 tt 文件处理，如果在 "解决方案资源管理器" 中展开 .edmx 文件，则会看到这些文件。 从项目中删除这两个文件。

![代码生成文件](~/ef6/media/codegenfiles.png)

如果你使用的是 VB.NET，则需要选择 " **显示所有文件** " 按钮以查看嵌套的文件。

![显示所有文件](~/ef6/media/showallfiles.png)

### <a name="2-re-enable-objectcontext-code-generation"></a>2. 重新启用 ObjectContext 代码生成

在 EF 设计器中打开模型，右键单击设计图面的空白部分，然后选择 " **属性**"。

在属性窗口将 **代码生成策略** 从 **None** 更改为 **默认值**。

![代码生成策略](~/ef6/media/codegenstrategy.png)

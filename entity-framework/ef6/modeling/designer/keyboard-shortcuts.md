---
title: Entity Framework Designer 键盘快捷方式-EF6
description: Entity Framework Designer 实体框架6中的键盘快捷方式
author: divega
ms.date: 10/23/2016
ms.assetid: 3c76cdd5-17c5-4c54-a6a5-cf21b974636b
ms.openlocfilehash: 8b93581e68e3a5d83685794f034c50cfe74eee9f
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620455"
---
# <a name="entity-framework-designer-keyboard-shortcuts"></a>Entity Framework Designer 键盘快捷方式
本页提供适用于 Visual Studio Entity Framework Tools 的各种屏幕中的键盘 shorcuts 列表。

## <a name="adonet-entity-data-model-wizard"></a>ADO.NET 实体数据模型向导

### <a name="step-one-choose-model-contents"></a>第一步：选择模型内容

![向导一](~/ef6/media/wizardone.png)

| 快捷方式  | 操作                                                     | 说明                                               |
|:----------|:-----------------------------------------------------------|:----------------------------------------------------|
| **Alt + n** | 转到下一个屏幕                                        | 不适用于所有模型内容选择。 |
| **Alt + f** | 结束向导                                              | 不适用于所有模型内容选择。 |
| **Alt + w** | 将焦点切换到 "模型应包含哪些内容？" 窗格中显示与该命名空间关联的连接字符串和其他元数据。 |                                                     |

### <a name="step-two-choose-your-connection"></a>步骤2：选择连接

![向导二](~/ef6/media/wizardtwo.png)

| 快捷方式  | 操作                                                     | 说明                                                   |
|:----------|:-----------------------------------------------------------|:--------------------------------------------------------|
| **Alt + n** | 转到下一个屏幕                                        |                                                         |
| **Alt + p** | 移到上一个屏幕                                    |                                                         |
| **Alt + w** | 将焦点切换到 "模型应包含哪些内容？" 窗格中显示与该命名空间关联的连接字符串和其他元数据。 |                                                         |
| **Alt + c** | 打开 "连接属性" 窗口                    | 允许定义新数据库连接。 |
| **Alt + e** | 从连接字符串中排除敏感数据          |                                                         |
| **Alt + i** | 在连接字符串中包含敏感数据            |                                                         |
| **Alt + s** | 切换 "App.Config 中保存连接设置" 选项 |                                                         |

### <a name="step-three-choose-your-version"></a>步骤3：选择你的版本

![向导三](~/ef6/media/wizardthree.png)

| 快捷方式  | 操作                                             | 说明                                                                                 |
|:----------|:---------------------------------------------------|:--------------------------------------------------------------------------------------|
| **Alt + n** | 转到下一个屏幕                                |                                                                                       |
| **Alt + p** | 移到上一个屏幕                            |                                                                                       |
| **Alt + w** | 将焦点切换到实体框架版本选择 | 允许指定不同版本的实体框架以便在项目中使用。 |

### <a name="step-four-choose-your-database-objects-and-settings"></a>步骤4：选择数据库对象和设置

![向导4](~/ef6/media/wizardfour.png)

| 快捷方式  | 操作                                                                                    | 说明                                                               |
|:----------|:------------------------------------------------------------------------------------------|:--------------------------------------------------------------------|
| **Alt + f** | 结束向导                                                                             |                                                                     |
| **Alt + p** | 移到上一个屏幕                                                                   |                                                                     |
| **Alt + w** | 将焦点切换到数据库对象选择窗格                                            | 允许指定要进行反向工程的数据库对象。    |
| **Alt + s** | 切换 "复数形式或确定所生成的对象名称" 选项                       |                                                                     |
| **Alt + k** | 切换 "在模型中包括外键列" 选项                              | 不适用于所有模型内容选择。                 |
| **Alt + i** | 切换 "将选定的存储过程和函数导入实体模型" 选项 | 不适用于所有模型内容选择。                 |
| **Alt + m** | 将焦点切换到 "模型命名空间" 文本字段                                        | 不适用于所有模型内容选择。                 |
| **空间** | 切换元素上的选定内容                                                               | 如果元素有子级，则将同时切换所有子元素 |
| **Left**  | 折叠子树                                                                       |                                                                     |
| **Right** | 展开子树                                                                         |                                                                     |
| **Up**    | 在树中导航到上一个元素                                                      |                                                                     |
| **向下**  | 导航到树中的下一个元素                                                          |                                                                     |

## <a name="ef-designer-surface"></a>EF 设计器图面

![设计器图面](~/ef6/media/designersurface.png)

| 快捷方式                                                                                | 操作                      | 说明                                                                                                                                                                                                                               |
|:----------------------------------------------------------------------------------------|:----------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **空格键/Enter**                                                                         | 切换选定内容            | 通过焦点切换对象上的选定内容。                                                                                                                                                                                         |
| Esc                                                                                  | 取消选择            | 取消当前选择。                                                                                                                                                                                                      |
| **Ctrl+A**                                                                            | 全选                  | 选择设计图面上的所有形状。                                                                                                                                                                                       |
| **向上键**                                                                            | 上移                     | 将所选实体向上移动一个网格增量。 <br/> 如果在列表中，则移动到上一个同级子字段。                                                                                                                            |
| **向下键**                                                                          | 下移                   | 将所选实体向下移动一个网格增量。 <br/> 如果在列表中，则移动到下一个同级子字段。                                                                                                                              |
| **向左键**                                                                          | 左移                   | 将所选实体向左移动一个网格增量。 <br/> 如果在列表中，则移动到上一个同级子字段。                                                                                                                          |
| **向右键**                                                                         | 右移                  | 将所选实体向右移动一个网格增量。 <br/> 如果在列表中，则移动到下一个同级子字段。                                                                                                                             |
| **Shift + 向左键**                                                                  | 左侧大小形状             | 将所选实体的宽度降低一个网格增量。                                                                                                                                                                     |
| **Shift + 向右键**                                                                 | 向右调整形状大小            | 将所选实体的宽度增加一个网格增量。                                                                                                                                                                   |
| **主页**                                                                                | 第一个对等                  | 将焦点和所选内容移到设计图面上处于同一对等级别的第一个对象。                                                                                                                                         |
| **End**                                                                                 | 上一个对等                   | 将焦点和所选内容移到设计图面上处于同一对等级别的最后一个对象。                                                                                                                                          |
| **Ctrl+Home**                                                                         | 第一个对等 (焦点)           | 与第一个对等方相同，但是移动焦点，而不是移动焦点和选择。                                                                                                                                                          |
| **Ctrl+End**                                                                          | 上一个对等 (焦点)            | 与上一对等方相同，但是移动焦点，而不是移动焦点和选择。                                                                                                                                                           |
| **选项卡**                                                                                 | 下一个对等                   | 将焦点和所选内容移到设计图面上处于同一对等级别的下一个对象。                                                                                                                                          |
| **Shift+Tab**                                                                           | 上一个对等               | 将焦点和选择内容移到设计图面上同一对等级别上的上一个对象。                                                                                                                                      |
| **Alt + Ctrl + Tab**                                                                        | 下一个对等 (焦点)            | 与下一个对等方相同，但是移动焦点，而不是移动焦点和选择。                                                                                                                                                           |
| **Alt + Ctrl + Shift + Tab**                                                                  | 上一个对等 (焦点)        | 与上一对等方相同，但是移动焦点，而不是移动焦点和选择。                                                                                                                                                       |
| **&lt;**                                                                                | 递增                      | 移动到层次结构中较高级别的设计图面上的下一个对象。 如果层次结构中此形状上没有任何形状 (即，对象直接放置在设计图面上) ，则选择该关系图。 |
| **&gt;**                                                                                | 降                     | 在设计图面上，将其移动到层次结构中这一级别下的下一个包含对象。 如果没有包含对象，则这是一个无操作。                                                                              |
| **Ctrl + &lt;**                                                                         | 递增 (焦点)               | 与 "递增" 命令相同，但移动时无需选择。                                                                                                                                                                          |
| **Ctrl + &gt;**                                                                         |  (焦点)              | 与 "向下" 命令相同，但移动时不选择。                                                                                                                                                                         |
| **Shift + End**                                                                         | 遵循连接         | 在实体中，移动到此实体所连接到的实体。                                                                                                                                                               |
| **Del**                                                                                 | 删除                      | 从关系图中删除对象或连接器。                                                                                                                                                                                     |
| **插件**                                                                                 | Insert                      | 当选择 "标量属性" 隔离舱标头或属性本身时，将新属性添加到实体。                                                                                                           |
| **Pg**                                                                               | 向上滚动关系图           | 将设计图面向上滚动，增加等于当前可见设计图面的高度的75%。                                                                                                                    |
| **下一页**                                                                             | 向下滚动关系图         | 向下滚动设计图面。                                                                                                                                                                                                    |
| **Shift + 向下移动**                                                                     | 向右滚动图示        | 将设计图面向右滚动。                                                                                                                                                                                            |
| **Shift + 向上翻**                                                                       | 向左滚动图示         | 向左滚动设计图面。                                                                                                                                                                                             |
| F2                                                                                  | 进入编辑模式             | 用于进入文本控件的编辑模式的标准键盘快捷键。                                                                                                                                                               |
| **Shift+F10**                                                                         | 显示快捷菜单       | 用于显示选定项的快捷菜单的标准键盘快捷方式。                                                                                                                                                          |
| **Ctrl + Shift + 鼠标左键单击**  <br/> **Ctrl + Shift + 鼠标滚轮前进**  | 语义放大            | 在鼠标指针下方的关系图视图区域中放大。                                                                                                                                                                 |
| **Ctrl + Shift + 鼠标右键单击** <br/> **按住 ctrl + Shift + 鼠标滚轮** | 语义缩小           | 缩小鼠标指针下关系图视图的区域。 当您缩小当前关系图中心时，它会将关系图重新居中。                                                                          |
| **Control + Shift + "+"** <br/> **控制 + 鼠标滚轮前进**                        | 放大                     | 放大关系图视图的中心。                                                                                                                                                                                         |
| **Control + Shift + "-"** <br/> **向后控制 + 鼠标滚轮**                       | 缩小                    | 从关系图视图的单击区域缩小。 当您缩小当前关系图中心时，它会将关系图重新居中。                                                                                            |
| **按住 ctrl 键的同时按下鼠标左键绘制矩形**                  | 缩放区域                   | 放大所选区域的中心。 按住 Ctrl + Shift 键的同时，您会看到光标变为放大镜，这允许您定义要缩小到的区域。                        |
| **上下文菜单键 + ' i '**                                                              | 打开映射详细信息窗口 | 打开 "映射详细信息" 窗口以编辑所选实体的映射                                                                                                                                                               |

## <a name="mapping-details-window"></a>“映射详细信息”窗口

![映射详细信息快捷方式](~/ef6/media/mappingdetailsshortcuts.png)

| 快捷方式                  | 操作         | 说明                                                                                                                                 |
|:--------------------------|:---------------|:--------------------------------------------------------------------------------------------------------------------------------------|
| **选项卡**                   | 切换上下文 | 在主窗口区域和左侧工具栏之间切换                                                                     |
| **箭头键**            | 导航     | 在主窗口区域中上下移动行，或在列上向右和向左移动。 在左侧工具栏中的按钮之间移动。 |
| **Enter** <br/> **空间** | Select         | 选择左侧工具栏中的按钮。                                                                                          |
| **Alt + 向下键**      | 打开列表      | 如果选定的单元格包含下拉列表，则下拉列表。                                                                     |
| **Enter**                 | 列表选择    | 选择下拉列表中的元素。                                                                                               |
| Esc                    | 列表关闭     | 关闭下拉列表。                                                                                                              |

## <a name="visual-studio-navigation"></a>Visual Studio 导航

实体框架还提供了一些操作，这些操作可将自定义键盘快捷方式映射 (默认情况下) 映射的快捷方式。 若要创建这些自定义快捷方式，请单击 "工具" 菜单，然后单击 "选项"。在 "环境" 下，选择 "键盘"。在中间向下滚动列表，直到可以选择所需的命令，在 "按快捷键" 文本框中输入该快捷方式，然后单击 "分配"。 可能的快捷方式如下：

| 快捷方式                                                                                       |
|:-----------------------------------------------------------------------------------------------|
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ComplexProperty. ComplexTypes**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddCodeGenerationItem**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddFunctionImport**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. AddEnumType**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ComplexProperty**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                      |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ScalarProperty**                   |
| **其他上下文菜单.Microsoft 数据实体设计上下文.添加新关系图**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.AddtoDiagram**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 关闭**                                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ConverttoEnum**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. CollapseAll**                     |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 展开**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ExportasImage**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. LayoutDiagram**                   |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                               |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. FunctionImportMapping**                   |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GenerateDatabasefromModel**               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.GoToDefinition**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ShowGrid**                           |
| **OtherContextMenus MicrosoftDataEntityDesignContext**                         |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.IncludeRelated**                          |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MappingDetails**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Modelbrowser 对于 modelelementbrowser**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MoveDiagramstoSeparateFile**              |
| **其他上下文菜单.Microsoft 数据实体设计上下文.移动属性.向下**                     |
| **其他上下文菜单.Microsoft 数据实体设计上下文.移动属性.向下 5**                    |
| **其他上下文菜单.Microsoft 数据实体设计上下文.移动属性.到底部**                 |
| **其他上下文菜单.Microsoft 数据实体设计上下文.移动属性.到顶部**                    |
| **其他上下文菜单.Microsoft 数据实体设计上下文.移动属性.向上**                       |
| **其他上下文菜单.Microsoft 数据实体设计上下文.移动属性.向上 5**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.MovetonewDiagram**                        |
| **OtherContextMenus. MicrosoftDataEntityDesignContext。**                                    |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. MovetoNewComplexType**           |
| **其他上下文菜单.Microsoft 数据实体设计上下文.重构.重命名**                         |
| **其他上下文菜单.Microsoft 数据实体设计上下文.从关系图中移除**                       |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 重命名**                                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ScalarPropertyFormat**        |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ScalarPropertyFormat.DisplayNameandType** |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 选择 BaseType**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 实体**                           |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 属性**                         |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                          |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. SelectAll**                               |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.SelectAssociation**                       |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinDiagram**                           |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.ShowinModelBrowser**                      |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.StoredProcedureMapping**                  |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. TableMapping**                            |
| **OtherContextMenus.MicrosoftDataEntityDesignContext.UpdateModelfromDatabase**                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. Validate**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus MicrosoftDataEntityDesignContext**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                                |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. 50**                                 |
| **OtherContextMenus MicrosoftDataEntityDesignContext**                                 |
| **OtherContextMenus MicrosoftDataEntityDesignContext**                                 |
| **OtherContextMenus. MicrosoftDataEntityDesignContext**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ZoomIn**                             |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ZoomOut**                            |
| **OtherContextMenus. MicrosoftDataEntityDesignContext. ZoomtoFit**                          |
| **视图.实体数据模型资源浏览器**                                                                |
| **视图.实体数据模型映射详细信息**                                                         |

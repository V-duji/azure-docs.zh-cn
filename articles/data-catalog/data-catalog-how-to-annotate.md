---
title: "如何批注数据源 | Microsoft Docs"
description: "操作方法文章强调如何在 Azure 数据目录中批注数据资产，包括友好名称、标记、说明和专家。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 4518fc126c717cc79ca7950c0b1ddcd9f1d8c7d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-annotate-data-sources"></a>如何批注数据源
## <a name="introduction"></a>介绍
**Microsoft Azure 数据目录**是一个完全托管的云服务，具有企业数据源的注册系统和发现系统的功能。 换而言之，“数据目录”旨在帮助人们发现、了解和使用数据源，并帮助组织从其现有数据中获取更多价值。 数据源在“数据目录”中注册时，该服务将复制数据源的元数据并为其建立索引，但事情并未就此结束。 用户可使用数据目录提供自己的描述性元数据（例如说明和标记）补充从数据源中提取的元数据并让数据源更易于理解。

## <a name="annotation-and-crowdsourcing"></a>批注和众包
每个人都有自己的观点。 这是一件好事。
数据目录意识到不同的用户对于企业数据源有不同的观点，而且每个观点都很有价值。 请考虑以下方案：

* 系统管理员知道服务器或托管数据源的服务的服务级别协议。
* 数据库管理员知道每个数据库的备份计划和允许的 ETL 处理窗口。
* 系统所有者知道用户请求访问数据源的流程。
* 数据专员知道数据源中的资产和属性是如何映射到企业数据模型的。
* 分析师知道数据是如何用于他支持的业务流程上下文的。

每个观点都很有价值，数据目录使用众包方式捕获每个元数据和完全呈现已注册数据源。 使用数据目录门户，每个用户可添加和编辑自己的批注，还能同时查看他人的批注。

## <a name="different-types-of-annotations"></a>不同类型的批注
数据目录支持以下类型的批注：

| 批注 | 说明 |
| --- | --- |
| 友好名称 |可在数据资产级别提供友好名称让数据资产更易于理解。 基础对象名称有隐晦的含义、缩写形式或对于用户来说无意义时，友好名称会非常有用。 |
| 说明 |可在数据资产和属性/列级别中提供说明。 说明可为任意格式的简短文本批注，说明用户在数据资产及其用法方面的观点。 |
| 标记（用户标记） |可在数据资产和属性/列级别中提供标记。 用户标记是用户定义的标签，用于对数据资产或属性进行分类。 |
| 标记（术语表标记） |可在数据资产和属性/列级别中提供标记。 术语表标记是集中定义的术语表术语，可用于通过常见业务分类对数据资产或属性进行分类。 有关详细信息，请参阅[如何设置受管标记的业务术语表](data-catalog-how-to-business-glossary.md) |
| 专家 |可在数据资产级别提供专家。 专家会使用数据方面的专业观点来标识用户或用户组，还可作为发现已注册数据源和具有现有批注未解问题的用户的联系点。 |
| 请求访问权限 |可在数据资产级别提供请求访问权限信息。 此信息为发现没有访问权限的数据源的用户提供。 用户可输入授予访问权限的用户或用户组的电子邮件地址、用户需要获取访问权限的进程 URL 或工具或以文本格式输入进程本身。 |
| 文档 |可在数据资产级别提供文档。 资产文档为格式文本信息，可包含连接和图像，提供无法通过描述和标记传达的所有信息。 |

## <a name="annotating-multiple-assets"></a>对多个资产进行批注
在数据目录门户中选择多个数据资产时，用户可在单个操作中对所有选定的资产进行批注。 批注将应用于所有选定的资产，这样可以轻松选择和提供一致的说明、标记集和相关数据资产的专家。

> [!NOTE]
> 使用数据目录数据源注册工具注册数据资产时也可提供标记和专家。
>
>

选择多个表和视图时，仅所有选定的数据资产共同拥有的列才会在数据目录门户中显示。 这样用户则可以使用所选资产的同一名称为所有列提供标记和说明。

## <a name="annotations-and-discovery"></a>批注和发现
与注册过程从数据源提取的元数据添加到数据目录搜索索引一样，用户提供的元数据也会创建索引。 这意味着批注可让用户更易理解他们发现的数据，还可帮助用户根据对他们来说有意义的术语进行搜索发现批注的数据资产。

## <a name="summary"></a>摘要
使用“数据目录”注册数据源，将结构性和描述性元数据从数据源复制到目录服务，从而使数据可被检测到。 注册数据源后，用户可提供批注帮助自己在数据目录门户内进行发现和理解。

## <a name="see-also"></a>另请参阅
* [Azure 数据目录入门](data-catalog-get-started.md)教程提供有关如何批注数据源的分步详细说明。

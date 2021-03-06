---
title: "在 Azure 开发测试实验室中配置虚拟网络 | Microsoft Docs"
description: "了解如何配置现有虚拟网络和子网并通过 Azure 开发测试实验室在 VM 中使用它们"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中配置虚拟网络
如文章[将含有项目的 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)中所述，在实验室中创建 VM 时，可指定已配置虚拟网络。 执行此操作的一种情形是需要使用 ExpressRoute 或站点到站点的 VPN 配置的虚拟网络从 VM 访问 corpnet 资源。 以下部分说明了如何将现有虚拟网络添加到实验室的虚拟网络设置中，以便在创建 VM 时可进行选择。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>使用 Azure 门户为实验室配置虚拟网络
以下步骤介绍如何将现有虚拟网络（以及子网）添加到实验室，以便在同一实验室创建 VM 时使用它。 

1. 登录到 [Azure 门户](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 选择“更多服务”，并从列表中选择“开发测试实验室”。
3. 从实验室列表中，选择所需的实验室。 
4. 在实验室的边栏选项卡中，选择“配置和策略”。
5. 在实验室的“配置和策略”边栏选项卡中，选择“虚拟网络”。
6. 在“虚拟网络”边栏选项卡上，会看到为当前实验室配置的虚拟网络列表和为实验室创建的默认虚拟网络。 
7. 选择“+ 添加”。
   
    ![将现有虚拟网络添加到实验室](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. 在“虚拟网络”边栏选项卡上，选择“[选择虚拟网络]”。
   
    ![选择现有虚拟网络](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. 在“选择虚拟网络”边栏选项卡上，选择所需虚拟网络。 边栏选项卡会显示订阅中所有与实验室在同一区域的虚拟网络。  
10. 选择虚拟网络之后，可返回到“虚拟网络”，单击边栏选项卡底部列表中的子网。

    ![子网列表](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    此时会显示“实验室子网”边栏选项卡。

    ![“实验室子网”边栏选项卡](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. 指定**实验室子网名称**。
12. 若要允许在实验室 VM 创建中使用子网，请选择“在虚拟机创建中使用”。
13. 若要启用 [共享公共 IP 地址](devtest-lab-shared-ip.md)，请选择“启用共享公共 IP”。
14. 若要在子网中允许公共 IP 地址，请选择“允许公共 IP 创建”。
15. 在“每个用户的最大虚拟机数量”字段中，指定每个子网中每个用户的最大 VM 数量。 如果不希望限制的 VM 的数目，请将此字段留空。
16. 选择“确定”关闭“实验室子网”边栏选项卡。
17. 选择“保存”关闭“虚拟网络”边栏选项卡。
18. 既然已配置虚拟网络，则可在创建 VM 时选择它。 
    要了解如何创建 VM 和指定虚拟网络，请参阅文章[将含有项目的 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>后续步骤
将所需虚拟网络添加到实验室后，下一步为[将 VM 添加到实验室](devtest-lab-add-vm-with-artifacts.md)。


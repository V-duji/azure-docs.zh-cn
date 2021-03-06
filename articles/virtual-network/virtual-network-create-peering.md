---
title: "创建 Azure 虚拟网络对等互连 - Resource Manager - 同一订阅 | Microsoft Docs"
description: "了解如何在同一 Azure 订阅中通过 Resource Manager 创建的虚拟网络之间创建虚拟网络对等互连。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: anavin;jdial
ms.openlocfilehash: ebe418f03c2edf176790f654f3f9f4d7eec09165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-peering---resource-manager-same-subscription"></a>创建虚拟网络对等互连 - Resource Manager，同一订阅

本教程介绍如何在通过 Resource Manager 创建的虚拟网络间创建虚拟网络对等互连。 这两个虚拟网络在同一个订阅中。 在两个虚拟网络之间建立对等互连可让不同虚拟网络中的资源以相同的带宽和延迟彼此通信，就像这些资源位于同一个虚拟网络中一样。 了解有关[虚拟网络对等互连](virtual-network-peering-overview.md)的详细信息。 

创建虚拟网络对等互连的步骤有所不同，具体取决于虚拟网络是否位于相同订阅，以及创建虚拟网络的 [ Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 单击下表中的方案，了解如何采用其他方案创建虚拟网络对等互连：

|Azure 部署模型  | Azure 订阅  |
|--------- |---------|
|[都是 Resource Manager 模型](create-peering-different-subscriptions.md) |不同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models.md) |相同|
|[一个为 Resource Manager 模型，一个为经典模型](create-peering-different-deployment-models-subscriptions.md) |不同|

只能在同一 Azure 区域中的两个虚拟网络之间创建虚拟网络对等互连。

  > [!WARNING]
  > 预览版中当前支持在不同区域的虚拟网络之间创建虚拟网络对等互连。 可注册订阅获取以下预览版。 采用此方案创建的虚拟网络对等互连与在正式发布版中创建的虚拟网络对等互连相比，二者的可用性和可靠性等级可能不同。 不支持采用此方案创建的虚拟网络对等体互连，其功能可能会受限，且可能无法用于所有 Azure 区域。 有关此功能可用性和状态方面的最新通知，请参阅 [Azure Virtual Network updates](https://azure.microsoft.com/updates/?product=virtual-network)（Azure 虚拟网络更新）页。

无法在通过经典部署模型部署的两个虚拟网络之间创建虚拟网络对等互连。如需连接均通过经典部署模型创建的虚拟网络，或位于不同 Azure 区域中的虚拟网络，可使用 Azure [VPN 网关](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)连接虚拟网络。 

可以使用 [Azure 门户](#portal)、Azure [命令行接口](#cli) (CLI)、Azure [PowerShell](#powershell)、或 [Azure Resource Manager 模板](#template)创建虚拟网络对等互连。 单击以前的任何工具链接直接转到使用所选工具创建虚拟网络对等互连的步骤。

## <a name="register"></a>注册全局 VNet 对等互连预览版

若要跨区域建立虚拟网络对等互连，请注册预览版，并完成包含要对等互连的虚拟网络的两个订阅的后续步骤。 唯一可用于注册预览版的工具是 PowerShell。

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 使用 `Login-AzureRmAccount` 命令启动 PowerShell 会话并登录到 Azure。
3. 输入以下命令，注册预览版订阅：

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
    输入以下命令后，收到的两个订阅的 RegistrationState 输出为“已注册”后，才能完成本文中在门户、Azure CLI 或 PowerShell 部分中进行的步骤：

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```
  > [!WARNING]
  > 预览版中当前支持在不同区域的虚拟网络之间创建虚拟网络对等互连。 采用此方案创建的虚拟网络对等互连可能功能受限，且可能无法用于所有 Azure 区域。 有关此功能可用性和状态方面的最新通知，请参阅 [Azure Virtual Network updates](https://azure.microsoft.com/updates/?product=virtual-network)（Azure 虚拟网络更新）页。
  
## <a name="portal"></a>创建对等互连 - Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com)。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
2. 依次单击“+ 新建”、“网络”、“虚拟网络”。
3. 在“创建虚拟网络”边栏选项卡中，为以下设置输入或选择值，然后单击“创建”：
    - **名称**：*myVnet1*
    - **地址空间**：*10.0.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.0.0.0/24*
    - **订阅**：选择你的订阅
    - **资源组**：选择“新建”并输入 *myResourceGroup*
    - **位置**：美国东部
4. 再次完成步骤 2-3 并在步骤 3 中指定以下值：
    - **名称**：*myVnet2*
    - **地址空间**：*10.1.0.0/16*
    - **子网名称**：默认值
    - **子网地址范围**：*10.1.0.0/24*
    - **订阅**：选择你的订阅
    - **资源组**：选择“使用现有”和“myResourceGroup”
    - **位置**：美国东部
5. 在门户顶部的“搜索资源”框中键入 *myResourceGroup*。 当“myResourceGroup”出现在搜索结果中时，请单击它。 随后将显示 **myresourcegroup** 资源组的边栏选项卡。 该资源组包含前面步骤中创建的两个虚拟网络。
6. 单击“myVNet1”。
7. 在显示的“myVnet1”边栏选项卡中，单击左侧垂直选项列表中的“对等互连”。
8. 在显示的“myVnet1 - 对等互连”边栏选项卡中，单击“+ 添加”
9. 在显示的“添加对等互连”边栏选项卡中，输入或选择以下选项，然后单击“确定”：
     - **名称**：*myVnet1ToMyVnet2*
     - **虚拟网络部署模型**：选择“Resource Manager”。 
     - **订阅**：选择你的订阅
     - **虚拟网络**：单击“选择虚拟网络”，然后单击“myVnet2”。
     - **允许虚拟网络访问：**确保选择“已启用”。
    本教程不使用其他任何设置。 若要了解所有对等互连设置，请参阅[管理虚拟网络对等互连](virtual-network-manage-peering.md#create-a-peering)。
10. 在上一步骤中单击“确定”后，“添加对等互连”边栏选项卡将会关闭，并再次出现“myVnet1 - 对等互连”边栏选项卡。 几秒钟后，创建的对等互连将显示在该边栏选项卡中。 所创建的 **myVnet1ToMyVnet2** 对等互连的“对等互连状态”列中列出了“已启动”。 已将 Vnet1 对等互连到 Vnet2，但现在必须将 myVnet2 对等互连到 myVnet1。 必须朝两个方向创建对等互连才能让虚拟网络中的资源相互通信。
11. 针对 myVnet2 再次完成步骤 5-10。  将对等互连命名为 *myVnet2ToMyVnet1*。
12. 单击“确定”创建 MyVnet2 的对等互连几秒钟后，将会列出刚刚创建的 **myVnet2ToMyVnet1** 对等互连，“对等互连状态”列中显示“已连接”。
13. 针对 MyVnet1 再次完成步骤 5-7。 **myVnet1ToVNet2** 对等互连的“对等互连状态”现在也显示为“已连接”。 对等互连中两个虚拟网络的“对等互连状态”列都显示为“已连接”后，即表示已成功建立对等互连。
14. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
15. **可选：**若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-portal)部分中所述的步骤。

在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="cli"></a>创建对等互连 - Azure CLI

以下脚本：

- 需要 Azure CLI 2.0.4 或更高版本。 若要查找版本，请运行 `az --version` 命令。 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 在 Bash shell 中操作。 有关在 Windows 客户端上运行 Azure CLI 脚本的选项，请参阅[在 Windows 中运行 Azure CLI](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 

除安装 CLI 及其依赖项外，还可使用 Azure Cloud Shell。 Azure Cloud Shell 是可直接在 Azure 门户中运行的免费 Bash shell。 它预安装有 Azure CLI 并将其配置与你的帐户一起使用。 单击下面脚本中的“试用”按钮，调用一个可用于登录到 Azure 帐户的 Cloud Shell。 若要执行脚本，请单击“复制”按钮，将内容粘贴到 Cloud Shell。

1. 创建一个资源组和两个虚拟网络。

    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    ```

2. 在两个虚拟网络之间创建虚拟网络对等互连。
 
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    ```

3. 执行该脚本后，请检查每个虚拟网络的对等互连。 

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    ```
    
    再次运行上述命令，但请将 *myVnet1* 替换为 *myVnet2*。 这两条命令的输出将在 **PeeringState** 列中显示 **Connected**。

     在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

4. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
5. 可选：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-cli)中所述的步骤。


## <a name="powershell"></a>创建对等互连 - PowerShell

1. 安装最新版本的 PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模块。 如果不熟悉 Azure PowerShell，请参阅 [Azure PowerShell 概述](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 若要启动 PowerShell 会话，请转到“开始”，输入“powershell”，然后单击“PowerShell”。
3. 在 PowerShell 中，输入 `login-azurermaccount` 命令登录到 Azure。 用于登录的帐户必须拥有创建虚拟网络对等互连的必要权限。 有关详细信息，请参阅本文的[权限](#permissions)部分。
4. 创建一个资源组和两个虚拟网络。 若要执行脚本，请复制以下脚本，将其粘贴到 PowerShell，在屏幕上显示最后一行后按 `Enter`：

    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    ```

5. 在两个虚拟网络之间创建虚拟网络对等互连。 复制以下脚本，将其粘贴到 PowerShell，在屏幕上显示最后一行后按 `Enter`：
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    ```
6. 若要查看虚拟网络的子网，请复制以下命令，将其粘贴到 PowerShell，按 `Enter`：

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    再次运行上述命令，但请将 *myVnet1* 替换为 *myVnet2*。 这两条命令的输出将在 **PeeringState** 列中显示 **Connected**。
7. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
8. **可选**：若要删除在本教程中创建的资源，请完成本文的[删除资源](#delete-powershell)中所述的步骤。

在任一虚拟网络中创建的任何 Azure 资源现在都可通过其 IP 地址相互通信。 如果为虚拟网络使用默认的 Azure 名称解析，则虚拟网络中的资源无法跨虚拟网络解析名称。 若要跨对等互连中的虚拟网络解析名称，必须创建自己的 DNS 服务器。 了解如何[使用自己的 DNS 服务器进行名称解析](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="template"></a>创建对等互连 - 资源管理器模板

1. 请参考[创建虚拟网络对等互连](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) Resource Manager 模板。 此文提供了相关说明，以及用于通过 Azure 门户、PowerShell 或 Azure CLI 部署模板的模板。 登录到所选的任何工具，以便使用拥有创建虚拟网络对等互连所需的权限的帐户来部署模板。 有关详细信息，请参阅本文的[权限](#permissions)部分。
2. **可选**：尽管本教程未介绍如何创建虚拟机，但你可以在每个虚拟网络中创建一个虚拟机并将其相互连接，以验证连接性。
3. **可选：**若要删除在本教程中创建的资源，请使用 Azure 门户、PowerShell 或 Azure CLI 完成本文的[删除资源](#delete)部分中所述的步骤。

## <a name="permissions"></a>权限

用于创建虚拟网络对等互连的帐户必须具有所需的角色或权限。 例如，如果在分别名为 VNet1 和 VNet2 的两个虚拟网络之间创建了对等互连，则必须为帐户分配针对每个虚拟网络的最小角色或权限：
    
|虚拟网络|角色|权限|
|---|---|---|
|VNet1|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|VNet2|[网络参与者](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

详细了解[内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)以及将特定的权限分配到[自定义角色](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)（仅限 Resource Manager）。

## <a name="delete"></a>删除资源
完成本教程后，你可能想要删除本教程中创建的资源，以免产生使用费。 删除资源组会删除其中包含的所有资源。

### <a name="delete-portal"></a>Azure 门户

1. 在门户的搜索框中，输入 **myResourceGroup**。 在搜索结果中，单击“myResourceGroup”。
2. 在“myResourceGroup”边栏选项卡中，单击“删除”图标。
3. 若要确认删除，请在“键入资源组名称”框中输入 **myResourceGroup**，然后单击“删除”。

### <a name="delete-cli"></a>Azure CLI

输入以下命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

输入以下命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -force
```

## <a name="next-steps"></a>后续步骤

- 在为生产用途创建虚拟网络对等互连之前，请充分熟悉重要的[虚拟网络对等互连约束和行为](virtual-network-manage-peering.md#requirements-and-constraints)。
- 了解所有的[虚拟网络对等互连设置](virtual-network-manage-peering.md#create-a-peering)。
- 了解如何使用虚拟网络对等互连[创建中心和分支网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering)。

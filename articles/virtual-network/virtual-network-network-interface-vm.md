---
title: "添加或删除 Azure 虚拟机的网络接口 | Microsoft Docs"
description: "了解如何添加或删除虚拟机的网络接口 (NIC)。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 0d609b20040572e3fb371a277603109d64a0ba37
ms.contentlocale: zh-cn
ms.lasthandoff: 05/13/2017


---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>添加或删除虚拟机的网络接口

了解如何在创建 VM 时添加现有的网络接口 (NIC)，或者添加或删除处于“已停止”（“已解除分配”）状态的现有 VM 的 NIC。 Azure 虚拟机 (VM) 通过 NIC 与 Internet、Azure 及本地资源通信。 一个 VM 可以有一个或多个 NIC。 

若需为 NIC 添加、更改或删除 IP 地址，请阅读[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)一文。 若需创建、更改或删除 NIC，请阅读 [NIC 设置和任务](virtual-network-network-interface.md)一文。

## <a name="before"></a>准备工作

在完成本文的任何部分中的任何步骤之前完成以下任务：

- 若要了解每种 Linux 和 Windows VM 大小支持的 NIC 数量，请参阅有关 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。
- 使用 Azure 帐户登录到 Azure 门户、Azure 命令行界面 (CLI) 或 Azure PowerShell。 如果还没有 Azure 帐户，请注册[免费试用帐户](https://azure.microsoft.com/free)。
- 如果使用 PowerShell 命令完成本文中的任务，请按[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 Azure PowerShell。 确保已安装最新版本的 Azure PowerShell commandlet。 若要获取 PowerShell 命令的帮助和示例，请键入 `get-help <command> -full`。
- 如果使用 Azure 命令行界面 (CLI) 命令完成本文中的任务，请按[如何安装和配置 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文中的步骤安装和配置 Azure CLI。 确保已安装最新版本的 Azure CLI。若要获取 CLI 命令的帮助，请键入 `az <command> --help`。

## <a name="about"></a>关于 NIC 和 VM

可以在创建 VM 时向该 VM 添加（附加）现有的 NIC，前提是该 NIC 当前未附加到其他 VM。 可以为现有的 VM 添加或删除（分离）NIC，前提是该 VM 处于“已停止”（“已解除分配”）状态。 如果通过 Azure 门户创建 VM，该门户会使用默认设置创建 NIC。 门户不允许：

- 在创建 VM 时指定要添加的现有 NIC
- 创建具有多个 NIC 的 VM
- 指定 NIC 的名称（门户将使用默认名称创建 NIC）

使用 Azure PowerShell 或 CLI 可以创建具有上述所有属性的 NIC 或 VM，而使用门户却做不到这一点。 在完成以下部分中的任务之前，请考虑以下约束和行为：

- 所有 VM 大小都支持至少两个 NIC，但某些 VM 大小支持两个以上的 NIC。 在过去，某些 VM 大小仅支持一个 NIC。 若要了解每种 VM 大小支持的 NIC 数量，请阅读有关 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 
- 过去，NIC 只能添加到支持多个 NIC 且至少使用两个 NIC 创建的 VM 中。 NIC 不能添加到使用一个 NIC 创建的 VM 中；即使 VM 的大小支持多个 NIC，也是如此。 相反，只能从至少有三个 NIC 的 VM 中删除 NIC，因为至少使用两个 NIC 创建的 VM 必须始终有至少两个 NIC。 这些约束全都不再适用。 现在，可以使用任意数量的 NIC（最大数目为 VM 大小所支持的数目）来创建 VM，也可以（为处于“已停止”（“已解除分配”）状态的 VM）添加或删除任意数目的 NIC，只要 VM 始终有至少一个 NIC。
- 默认情况下，VM 中的第一个 NIC 定义为主 NIC。 VM 中的其他所有 NIC 为辅助 NIC。
- Azure DHCP 服务器为主 NIC 分配默认的网关，但不为辅助 NIC 分配。 由于辅助 NIC 没有分配默认网关，因此默认情况下无法与子网外部的资源通信。 若要让 Windows VM 中的辅助 NIC 与其子网外部的资源通信，请在 Windows 命令行中使用 `route add` 命令向操作系统添加路由。 对于 Linux VM，由于默认行为使用弱主机路由，因此建议将辅助 NIC 的流量限制到单个子网。 如果需要辅助 NIC 子网外的连接，请启用基于策略的路由，确保入口和出口流量使用同一 NIC。
- 默认情况下，来自 VM 的所有出站流量是通过分配给主 NIC 主 IP 配置的 IP 地址发出的。 可以在 VM 的操作系统中控制要将哪个 IP 地址用于出站流量，但默认情况下，流量通过主 NIC。
- 过去，同一个可用性集中的所有 VM 都需要有一个或多个 NIC。 现在，同一个可用性集中可以包含具有任意数目的 NIC 的 VM，只要 VM 大小支持该数目。 不过，只能在创建 VM 时将 VM 添加到可用性集。 若要详细了解可用性集，请参阅[在 Azure 中管理 VM 的可用性](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)一文。
- 尽管同一个 VM 中的 NIC 可以连接到 VNet 中的不同子网，但这些 NIC 必须全部连接到同一个 VNet。
- 可将任何主要或辅助 NIC 的任何 IP 配置的任何 IP 地址添加到 Azure 负载均衡器后端池。 以往，只能将主要 NIC 的主要 IP 地址添加到后端池。 若要详细了解 IP 地址和配置，请阅读[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)一文。
- 删除 VM 不会删除附加到其中的 NIC。 删除 VM 时，NIC 将从此 VM分离。 可将 NIC 添加到不同的 VM，也可将其删除。

## <a name="vm-create"></a>将现有 NIC 添加到新的 VM
通过门户创建 VM 时，门户会使用默认设置创建一个 NIC，并将其附加到 VM。 无法使用 Azure 门户将现有 NIC 添加到新的 VM，或创建具有多个 NIC 的 VM。 可以使用 CLI 或 PowerShell 执行这两项操作。 可以向 VM 添加任意数目的 NIC，只要所创建的 VM 大小支持。 若要详细了解每种 VM 大小支持的 NIC 数量，请阅读有关 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 添加到某个 VM 的 NIC 目前不能附加到其他 VM。 若要详细了解如何创建 NIC，请阅读 [NIC 设置和任务](virtual-network-network-interface.md#create-nic)一文。

命令

|工具|命令|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>将现有 NIC 添加到现有 VM

可以向 VM 添加任意数目的 NIC，只要向其添加 NIC 的 VM 大小支持。 若要了解每种 VM 大小支持的 NIC 数量，请阅读有关 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 或 [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的文章。 要将 NIC 添加到其中的 VM 必须支持多个 NIC 并处于“已停止”（“已解除分配”）状态。 要添加的 NIC 目前不能附加到其他 VM。 无法使用 Azure 门户将 NIC 添加到现有的 VM。 必须使用 CLI 或 PowerShell 才能将 NIC 添加到现有 VM。

|工具|命令|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-view-nic"></a> 查看 VM 的 NIC

可以查看当前附加到 VM 的 NIC，了解每个 NIC 的配置，以及分配到每个 NIC 的 IP 地址。 

1. 使用分配有订阅“所有者”、“参与者”或“网络参与者”角色的帐户登录到 [Azure 门户](https://portal.azure.com)。 请参阅[用于 Azure 基于角色的访问控制的内置角色](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)一文，详细了解如何将角色分配给帐户。
2. 在 Azure 门户顶部包含“搜索资源”文本的框中，键入“虚拟机”。 当“虚拟机”出现在搜索结果中时，请单击它。
3. 在出现的“虚拟机”边栏选项卡中，单击要查看其 NIC 的 VM 的名称。
4. 在针对所选 VM 显示的“虚拟机”边栏选项卡的“设置”部分，单击“网络接口”。 若要了解 NIC 设置以及如何更改该设置，请阅读 [NIC 设置和任务](virtual-network-network-interface.md)一文。 若要了解如何添加、更改或删除分配到 NIC 的 IP 地址，请阅读[添加、更改或删除 IP 地址](virtual-network-network-interface-addresses.md)一文。

命令

|工具|命令|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> 从 VM 中删除 NIC

要从中删除 NIC 的 VM 必须处于“已停止”（“已解除分配”）状态，并且当前必须至少有两个附加到其中的 NIC。 可以删除任何 NIC，但 VM 必须始终至少有一个附加到其中的 NIC。 如果删除主 NIC，Azure 会将主属性分配给附加到 VM 时间最长的 NIC。 可以自行将任何 NIC 指定为主 NIC。 无法使用 Azure 门户从 VM 中删除 NIC 或者设置 NIC 的主属性，但可以使用 CLI 或 PowerShell 完成这两项操作。 

命令

|工具|命令|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>后续步骤
若要创建具有多个 NIC 或 IP 地址的 VM，请阅读以下文章：

命令

|任务|工具|
|---|---|
|创建具有多个 NIC 的 VM|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|创建具有多个 IP 地址的单 NIC VM|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|


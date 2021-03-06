---
title: "在 Azure 虚拟机中配置 AlwaysOn 可用性组（经典）| Microsoft Docs"
description: "创建包含 Azure 虚拟机的 AlwaysOn 可用性组。 本教程主要使用用户界面和工具而不是脚本。"
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 0d9afb1554158a4d88b7f161c62fa51c1bf61a7d
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.lasthandoff: 04/12/2017


---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>在 Azure 虚拟机中配置 AlwaysOn 可用性组（经典）
> [!div class="op_single_selector"]
> * [经典：UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [经典：PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

在开始之前，请先假设现在可以在 Azure Resource Manager 模型中完成此任务。 我们建议使用 Azure Resource Manager 模型来进行新的部署。 请参阅 [Azure 虚拟机上的 SQL Server AlwaysOn 可用性组](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)。

> [!IMPORTANT] 
> Microsoft 建议大多数新部署使用资源管理器模型。 Azure 具有两种不同的部署模型可用来创建和处理资源：[Resource Manager 模型和经典模型](../../../azure-resource-manager/resource-manager-deployment-model.md)。 本文将介绍如何使用经典部署模型。 

若要使用 Azure Resource Manager 模型完成此任务，请参阅 [Azure 虚拟机上的 SQL Server AlwaysOn 可用性组](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)。

本端到端教程介绍了如何使用 Azure 虚拟机上运行的 SQL Server AlwaysOn 来实施可用性组。

在本教程结束时，Azure 中的 SQL Server Always On 解决方案将包括以下要素：

* 含有多个子网的虚拟网络，其中包含一个前端子网和一个后端子网
* 包含 Active Directory (Azure AD) 域的域控制器
* 两个运行 SQL Server、已部署到后端子网并已加入 Azure AD 域的虚拟机
* 采用节点多数仲裁模型的三节点故障转移群集
* 具有可用性数据库的两个同步提交副本的可用性组

下图显示了该解决方案的示意图。

![Azure 中可用性组的测试实验体系结构](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

请注意，这是一个可能的配置。 例如，可最大程度减少某个双副本可用性组的虚拟机数目。 此配置会通过将域控制器用作双节点群集中的仲裁文件共享见证服务器来节省 Azure 中的计算时间。 此方法可从所示配置中减少一个虚拟机。

本教程的假设条件如下：

* 你已有一个 Azure 帐户。
* 你已经知道如何使用虚拟机库中的 GUI 来预配运行 SQL Server 的经典虚拟机。
* 你已经深入了解 AlwaysOn 可用性组。 有关详细信息，请参阅 [Always On 可用性组 (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)。

> [!NOTE]
> 若要将 AlwaysOn 可用性组与 SharePoint 结合使用，另请参阅[为 SharePoint 2013 配置 SQL Server 2012 AlwaysOn 可用性组](https://technet.microsoft.com/library/jj715261.aspx)。
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>创建虚拟网络和域控制器服务器
你将从一个新的 Azure 试用帐户开始。 完成帐户设置后，则应位于 Azure 经典门户的主页屏幕。

1. 单击页面左下角的“新建”按钮，如以下屏幕截图所示。
   
    ![在门户中单击“新建”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. 单击“网络服务” > “虚拟网络” > “自定义创建”，如以下屏幕截图所示。
   
    ![创建虚拟网络](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. 在“创建虚拟网络”对话框中，通过逐页完成下表中的设置来创建新的虚拟网络。 
   
   | Page | 设置 |
   | --- | --- |
   | 虚拟网络详细信息 |**名称 = ContosoNET**<br/>**区域 = 美国西部** |
   | DNS 服务器和 VPN 连接 |无 |
   | 虚拟网络地址空间 |设置显示在以下屏幕截图中： ![创建虚拟网络](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. 创建将用作域控制器 (DC) 的虚拟机。 单击“新建” > “计算” > “虚拟机” > “从库中”，如以下屏幕截图所示。
   
    ![创建虚拟机](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. 在“创建虚拟机”对话框中，通过逐页完成下表中的设置来配置新的虚拟机。 
   
   | Page | 设置 |
   | --- | --- |
   | 选择虚拟机操作系统 |Windows Server 2012 R2 Datacenter |
   | 虚拟机配置 |**版本发布日期** = (最新)<br/>**虚拟机名称** = ContosoDC<br/>**层** = 标准<br/>**大小** = A2（双核）<br/>**新用户名** = AzureAdmin<br/>**新密码** = Contoso!000<br/>**确认** = Contoso!000 |
   | 虚拟机配置 |**云服务** = 创建新的云服务<br/>**云服务 DNS 名称** = 唯一云服务名称<br/>**DNS 名称** = 唯一名称（例如：ContosoDC123）<br/>**区域/地缘组/虚拟网络** = ContosoNET<br/>**虚拟网络子网** = Back(10.10.2.0/24)<br/>**存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = (无) |
   | 虚拟机选项 |使用默认值 |

配置好新虚拟机后，请等待该虚拟机完成预配。 此过程需要一些时间才能完成。 如果单击 Azure 经典门户中的“虚拟机”选项卡，则可看到 ContosoDC 逐一经历的各个状态：从“正在启动(正在预配)”、“已停止”、“正在启动”、“正在运行(正在预配)”到最后的“正在运行”。

现在已成功预配 DC 服务器。 接下来，请在 DC 服务器上配置 Active Directory 域。

## <a name="configure-the-domain-controller"></a>配置域控制器
在以下步骤中，你可以将 ContosoDC 计算机配置为 corp.contoso.com 的域控制器。

1. 在门户中，选择 **ContosoDC** 计算机。 在“仪表板”选项卡上，单击“连接”以打开用于远程桌面访问的远程桌面 (RDP) 文件。
   
    ![连接到虚拟机](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. 使用已配置的管理员帐户 (**\AzureAdmin**) 和密码 (**Contoso!000**) 登录。
3. 默认情况下，应显示“服务器管理器”仪表板。
4. 单击仪表板上的“添加角色和功能”链接。
   
    ![服务器资源管理器中的“添加角色”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. 单击“下一步”，直到到达“服务器角色”部分。
6. 选择“Active Directory 域服务”和“DNS 服务器”角色。 出现提示时，添加这些角色所需的更多功能。
   
   > [!NOTE]
   > 你将收到无静态 IP 地址的验证警告。 如果要测试配置，请单击“继续”。 对于生产方案，请[使用 PowerShell 设置域控制器计算机的静态 IP 地址](../../../virtual-network/virtual-networks-reserved-private-ip.md)。
   > 
   > 
   
    ![添加角色对话框](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. 单击“下一步”，直到显示“确认”部分。 选中“必要时自动重新启动目标服务器”复选框。
8. 单击“安装” 。
9. 功能安装完毕后，返回到“服务器管理器”仪表板。
10. 选择左侧窗格中的新“AD DS”选项。
11. 单击黄色警告栏上的“更多”链接。
    
     ![DNS 服务器虚拟机上的“AD DS”对话框](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. 在“所有服务器任务详细信息”对话框的“操作”栏中，单击“将此服务器提升为域控制器”。
13. 在“Active Directory 域服务配置向导”中，使用以下值：
    
    | Page | 设置 |
    | --- | --- |
    | 部署配置 |**添加新林** = 选定<br/>**根域名** = corp.contoso.com |
    | 域控制器选项 |**密码** = Contoso!000<br/>**确认密码** = Contoso!000 |
14. 单击“下一步”浏览向导中的其他页。 在“必备项检查”页上，确认看到以下消息：“所有先决条件检查都成功通过”。 请注意，应该查看所有适用的警告消息，但可以继续安装。
15. 单击“安装” 。 **ContosoDC** 虚拟机将自动重新启动。

## <a name="configure-domain-accounts"></a>配置域帐户
接下来的步骤用于配置 Active Directory 帐户以供稍后使用。

1. 重新登录到 **ContosoDC** 计算机。
2. 在“服务器管理器”中，单击“工具” > “Active Directory 管理中心”。
   
    ![Active Directory 管理中心](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. 在“Active Directory 管理中心”的左窗格中，选择“corp (本地)”。
4. 在右侧的“任务”窗格上，单击“新建” > “用户”。 使用以下设置：
   
   | 设置 | 值 |
   | --- | --- |
   | **第一个名称** |安装 |
   | **用户 SamAccountName** |安装 |
   | **密码** |Contoso!000 |
   | **确认密码** |Contoso!000 |
   | **其他密码选项** |选定 |
   | **密码永不过期** |已选中 |
5. 单击“确定”创建 **Install** 用户。 此帐户将用于配置故障转移群集和可用性组。
6. 使用相同的步骤创建另外两个用户：**CORP\SQLSvc1** 和 **CORP\SQLSvc2**。 这些帐户将用于 SQL Server 实例。 接下来，需要为 **CORP\Install** 分配所需权限以配置 Windows 故障转移群集。
7. 在“Active Directory 管理中心”的左窗格中，单击“corp (本地)”。 在“任务”窗格中，单击“属性”。
   
    ![CORP 用户属性](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. 选择“扩展”，然后单击“安全性”选项卡上的“高级”按钮。
9. 在“corp 的高级安全设置”对话框中，单击“添加”。
10. 单击“选择主体”，搜索“CORP\Install”，然后单击“确定”。
11. 选择“读取全部属性”和“创建计算机对象”权限。
    
     ![CORP 用户权限](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. 单击“确定”，然后再次单击“确定”。 关闭 corp 属性窗口。

现在 Active Directory 和用户对象已配置好，接下来将创建三个 SQL Server 虚拟机并将其加入到此域中。

## <a name="create-the-sql-server-virtual-machines"></a>创建 SQL Server 虚拟机
创建三个虚拟机。 一个用作群集节点，两个用作 SQL Server。 若要创建各个虚拟机，请返回到 Azure 经典门户，然后单击“新建” > “计算” > “虚拟机” > “从库中”。 然后，使用下表中的模板来帮助创建虚拟机。

| Page | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 选择虚拟机操作系统 |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| 虚拟机配置 |**版本发布日期** = (最新)<br/>**虚拟机名称** = ContosoWSFCNode<br/>**层** = 标准<br/>**大小** = A2（双核）<br/>**新用户名** = AzureAdmin<br/>**新密码** = Contoso!000<br/>**确认** = Contoso!000 |**版本发布日期** = (最新)<br/>**虚拟机名称** = ContosoSQL1<br/>**层** = 标准<br/>**大小** = A3（4 核）<br/>**新用户名** = AzureAdmin<br/>**新密码** = Contoso!000<br/>**确认** = Contoso!000 |**版本发布日期** = (最新)<br/>**虚拟机名称** = ContosoSQL2<br/>**层** = 标准<br/>**大小** = A3（4 核）<br/>**新用户名** = AzureAdmin<br/>**新密码** = Contoso!000<br/>**确认** = Contoso!000 |
| 虚拟机配置 |**云服务** = 之前创建的唯一云服务 DNS 名称（例如：ContosoDC123）<br/>**区域/地缘组/虚拟网络** = ContosoNET<br/>**虚拟网络子网** = Back(10.10.2.0/24)<br/>**存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = 创建可用性集<br/>**可用性集名称** = SQLHADR |**云服务** = 之前创建的唯一云服务 DNS 名称（例如：ContosoDC123）<br/>**区域/地缘组/虚拟网络** = ContosoNET<br/>**虚拟网络子网** = Back(10.10.2.0/24)<br/>**存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = SQLHADR（还可以在虚拟机创建后配置可用性集。 所有三个计算机都应分配到 SQLHADR 可用性集。） |**云服务** = 之前创建的唯一云服务 DNS 名称（例如：ContosoDC123）<br/>**区域/地缘组/虚拟网络** = ContosoNET<br/>**虚拟网络子网** = Back(10.10.2.0/24)<br/>**存储帐户** = 使用自动生成的存储帐户<br/>**可用性集** = SQLHADR（还可以在虚拟机创建后配置可用性集。 所有三个计算机都应分配到 SQLHADR 可用性集。） |
| 虚拟机选项 |使用默认值 |使用默认值 |使用默认值 |

<br/>

> [!NOTE]
> 前面的配置建议使用标准层虚拟机，因为基本层计算机不支持负载均衡终结点。 之后，需要使用负载均衡终结点来创建可用性组侦听器。 此外，此处建议的虚拟机大小是为了在 Azure 虚拟机中测试可用性组。 为获得生产工作负荷的最佳性能，请参阅 [Azure 虚拟机中 SQL Server 的性能最佳实践](../sql/virtual-machines-windows-sql-performance.md)中关于 SQL Server 计算机大小和配置的建议。
> 
> 

在三个虚拟机预配完毕之后，需要将它们加入到 **corp.contoso.com** 域中，并向这些虚拟机授予 CORP\Install 管理权限。 为此，请逐一针对这三个虚拟机执行以下步骤。

1. 首先，更改首选 DNS 服务器地址。 请通过在列表中选择虚拟机并单击“连接”按钮，将各个虚拟机的 RDP 文件下载到本地目录。 若要选择某个虚拟机，请单击除行中第一个单元格之外的任意位置，如以下屏幕截图所示。
   
    ![下载 RDP 文件](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. 打开下载的 RDP 文件，并使用已配置的管理员帐户 (**BUILTIN\AzureAdmin**) 和密码 (**Contoso!000**) 登录到虚拟机。
3. 登录后，应会看到“服务器管理器”仪表板。 单击左窗格中的“本地服务器”。
4. 单击“由 DHCP 分配的启用 IPv6 的 IPv4 地址”链接。
5. 在“网络连接”对话框中，单击网络图标。
   
    ![更改虚拟机的首选 DNS 服务器](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. 在命令栏中，单击“更改此连接的设置”。 （根据窗口大小，可能需要单击双右箭头才能看到此命令）。
7. 选择“Internet 协议版本 4 (TCP/IPv4)”，然后单击“属性”。
8. 选择“使用以下 DNS 服务器地址”，并在“首选 DNS 服务器”中指定 **10.10.2.4**。
9. 地址 **10.10.2.4** 是分配给 Azure 虚拟网络中 10.10.2.0/24 子网内的虚拟机的地址。 该虚拟机就是 **ContosoDC**。 若要验证 **ContosoDC** 的 IP 地址，请在命令提示窗口中使用 **nslookup contosodc**，如以下屏幕截图所示。
   
    ![使用 NSLOOKUP 查找域控制器的 IP 地址](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. 单击“确定” > “关闭”以提交更改。 现在，可以将虚拟机加入到 **corp.contoso.com** 中。
11. 再次在“本地服务器”窗口中，单击“工作组”链接。
12. 在“计算机名”选项上，单击“更改”。
13. 选中“域”复选框，在文本框中键入 **corp.contoso.com**，然后单击“确定”。
14. 在“Windows 安全性”对话框中，指定默认域管理员帐户 (**CORP\AzureAdmin**) 和密码 (**Contoso!000**) 的凭据。
15. 在看到“欢迎使用 corp.contoso.com 域”消息时，请单击“确定”。
16. 在对话框中单击“关闭” > “立即重新启动”。

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>添加 Corp\Install 用户作为每个虚拟机上的管理员
1. 一直等到虚拟机重新启动，然后再次打开 RDP 文件以使用 **BUILTIN\AzureAdmin** 帐户登录到虚拟机。
2. 在“服务器管理器”中，单击“工具” > “计算机管理”。
   
    ![计算机管理](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. 在“计算机管理”对话框中，展开“本地用户和组”，然后单击“组”。
4. 双击“管理员”组。
5. 在“管理员属性”对话框中，单击“添加”按钮。
6. 输入用户 **CORP\Install**，然后单击“确定”。 当系统提示你输入凭据时，使用 **AzureAdmin** 帐户与 **Contoso!000** 密码。
7. 单击“确定”以关闭“管理员属性”对话框。

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>将故障转移群集功能添加到每个虚拟机
1. 在“服务器管理器”仪表板中，单击“添加角色和功能”。
2. 在“添加角色和功能向导”中，单击“下一步”，直到出现“功能”页。
3. 选择“故障转移群集”。 出现提示时，请添加其他相关功能。
   
    ![将故障转移群集功能添加到虚拟机](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. 单击“下一步”，然后单击“确认”页上的“安装”。
5. “故障转移群集”功能安装完成后，单击“关闭”。
6. 从虚拟机注销。
7. 分别为三个服务器重复本部分中的步骤：**ContosoWSFCNode**、**ContosoSQL1** 和 **ContosoSQL2**。

这些 SQL Server 虚拟机现已完成预配，并且正在运行，但每个虚拟机都有默认的 SQL Server 选项。

## <a name="create-the-failover-cluster"></a>创建故障转移群集
在本部分中，将创建用于托管稍后要创建的可用性组的故障转移群集。 至此，应已针对要在故障转移群集中使用的三个虚拟机分别完成以下操作：

* 已在 Azure 中对虚拟机进行完全预配
* 已将虚拟机加入到域中
* 已将 **CORP\Install** 添加到本地管理员组
* 已添加故障转移群集功能

必须在每个虚拟机上执行所有上述操作，才能将相应的虚拟机加入到故障转移群集中。

另请注意，Azure 虚拟网络的行为与本地网络不同。 你需要按以下顺序来创建群集：

1. 在一个节点 (**ContosoSQL1**) 上创建单节点群集。
2. 将群集 IP 地址修改为未使用的 IP 地址 (**10.10.2.101**)。
3. 将群集名称联机。
4. 添加其他节点（**ContosoSQL2** 和 **ContosoWSFCNode**）。

按照以下步骤完成群集完全配置任务。

1. 打开 **ContosoSQL1** 的 RDP 文件，然后使用域帐户 **CORP\Install** 登录。
2. 在“服务器管理器”仪表板中，单击“工具” > “故障转移群集管理器”。
3. 在左窗格中，右键单击“故障转移群集管理器”，然后单击“创建群集”，如以下屏幕截图所示。
   
    ![创建群集](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. 在“创建群集向导”中，逐页完成下表中的设置来创建一个单节点群集：
   
   | Page | 设置 |
   | --- | --- |
   | 开始之前 |使用默认值 |
   | 选择服务器 |在“输入服务器名称”中键入 **ContosoSQL1**，然后单击“添加” |
   | 验证警告 |选择**“否。不需要 Microsoft 对该群集的支持，因此不希望运行验证测试。单击‘下一步’时，继续创建群集。**” |
   | 用于管理群集的访问点 |在“群集名称”中键入 **Cluster1** |
   | 确认 |除非你使用的是存储空间，否则请使用默认值。 请参阅此表后面的警告。 |
   
   > [!WARNING]
   > 如果使用[存储空间](https://technet.microsoft.com/library/hh831739)来将多个磁盘分组到存储池中，则必须取消选中“确认”页上的“将所有符合条件的存储添加到群集”复选框。 如果不取消选中该选项，则虚拟磁盘会在群集过程中分离。 因此，这些虚拟磁盘也不会出现在磁盘管理器或资源管理器之中，除非从群集中删除存储空间，并使用 PowerShell 将其重新附加。
   > 
   > 
5. 在左窗格中，展开“故障转移群集管理器”，然后单击“Cluster1.corp.contoso.com”。
6. 在中心窗格中，向下滚动到“群集核心资源”部分并展开“名称: Clutser1”详细信息。 应会看到“名称”和“IP 地址”资源都处于“已失败”状态。 不能将 IP 地址资源联机，因为向该群集分配的 IP 地址与计算机本身的地址相同，地址重复。
7. 右键单击失败的“IP 地址”资源，然后单击“属性”。
   
    ![群集属性](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. 选择“静态 IP 地址”，在“地址”文本框中指定 **10.10.2.101**，然后单击“确定”。
9. 在“群集核心资源”部分中，右键单击“名称: Cluster1”，然后单击“联机”。 等待两个资源都已联机。 当该群集名称资源联机时，它会用新的 Active Directory 计算机帐户更新 DC 服务器。 此 Active Directory 帐户将在以后用于运行可用性组群集服务。
10. 将剩余节点添加到群集中。 在浏览器树中，右键单击“Cluster1.corp.contoso.com”，然后单击“添加节点”，如以下屏幕截图所示。
    
     ![将节点添加到群集中](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. 在“添加节点向导”中，在“选择服务器”页上，单击“下一步”，然后在“输入服务器名称”中键入服务器名称，然后单击“添加”，以将“ContosoSQL2”和“ContosoWSFCNode”添加到列表中。 完成后，单击“下一步”。
12. 在“验证警告”页上，单击“否”（在生产方案中，应执行验证测试）。 然后单击“下一步”。
13. 在“确认”页中，单击“下一步”以添加节点。
    
    > [!WARNING]
    > 如果使用[存储空间](https://technet.microsoft.com/library/hh831739)来将多个磁盘分组到存储池中，则必须取消选中“将所有符合条件的存储添加到群集”复选框。 如果不取消选中该选项，则虚拟磁盘会在群集过程中分离。 因此，这些虚拟磁盘也不会出现在磁盘管理器或资源管理器之中，除非从群集中删除存储空间，并使用 PowerShell 将其重新附加。
    > 
    > 
14. 将节点添加到群集中后，单击“完成”。 “故障转移群集管理器”现在应显示群集具有三个节点，并将这些节点在“节点”容器中列出。
15. 从远程桌面会话注销。

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>为可用性组准备 SQL Server 实例
在本部分中，将针对 **ContosoSQL1** 和 **contosoSQL2** 执行以下操作：

* 添加 **NT AUTHORITY\System** 的登录名，该登录名具有对默认 SQL Server 实例的必要权限集。
* 将 **CORP\Install** 作为 sysadmin 角色添加到默认 SQL Server 实例。
* 打开防火墙以便远程访问 SQL Server。
* 启用“AlwaysOn 可用性组”功能。
* 将 SQL Server 服务帐户分别更改为 **CORP\SQLSvc1** 和 **CORP\SQLSvc2**。

上述操作可按任意顺序执行。 不过，以下步骤应按顺序执行。 针对 **ContosoSQL1** 和 **ContosoSQL2** 按照步骤操作：

1. 如果尚未从虚拟机的远程桌面会话注销，请立即注销。
2. 打开 **ContosoSQL1** 和 **ContosoSQL2** 的 RDP 文件，然后以 **BUILTIN\AzureAdmin** 身份登录。
3. 向 SQL Server 登录名添加 **NT AUTHORITY\System** 以及必要的权限。 打开 **SQL Server Management Studio**。
4. 单击“连接”连接到默认 SQL Server 实例。
5. 在“对象资源管理器”中，展开“安全性”，然后展开“登录名”。
6. 右键单击“NT AUTHORITY\System”登录名，然后单击“属性”。
7. 在“安全对象”页上，对于本地服务器，选择“授予”以授予以下权限，然后单击“确定”。
   
   * 更改任何可用性组
   * 连接 SQL
   * 查看服务器状态
8. 将 **CORP\Install** 作为 **sysadmin** 角色添加到默认 SQL Server 实例。 在“对象资源管理器”中，右键单击“登录名”，然后单击“新建登录名”。
9. 在“登录名”中，键入 **CORP\Install**。
10. 在“服务器角色”页上，选择“sysadmin”，然后单击“确定”。 创建登录名后，可通过在“对象资源管理器”中展开“登录名”来查看该登录名。
11. 若要为 SQL Server 创建防火墙规则，则在“启动”屏幕上打开“高级安全 Windows 防火墙”。
12. 在左窗格中，选择“入站规则”。 在右窗格上，单击“新建规则”。
13. 在“规则类型”页上，单击“程序” > “下一步”。
14. 在“程序”页上，选择“此程序路径”，在文本框中键入 **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe**，然后单击“下一步”。 如果遵循了这些指示但使用的是 SQL Server 2012，则 SQL Server 目录为 **MSSQL11.MSSQLSERVER**。
15. 在“操作”页上，保持选中“允许连接”，然后单击“下一步”。
16. 在“配置文件”页上，接受默认设置，然后单击“下一步”。
17. 在“名称”页上的“名称”文本框中指定一个规则名称，如 **SQL Server (Program Rule)**，然后单击“完成”。
18. 若要启用“AlwaysOn 可用性组”功能，请在“启动”屏幕上打开“SQL Server 配置管理器”。
19. 在浏览器树中，单击“SQL Server 服务”，右键单击“SQL Server (MSSQLSERVER)”服务，然后单击“属性”。
20. 单击“AlwaysOn 高可用性”选项卡，选择“启用 AlwaysOn 可用性组”（如以下屏幕截图所示），然后单击“应用”。 在对话框中单击“确定”，但不要关闭“属性”对话框。 在更改服务帐户后，将重新启动 SQL Server 服务。
    
     ![启用 AlwaysOn 可用性组](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. 若要更改 SQL Server 服务帐户，请单击“登录”选项卡，在“帐户名”中键入 **CORP\SQLSvc1**（对于 **ContosoSQL1**）或 **CORP\SQLSvc2**（对于 **ContosoSQL2**），填入并确认密码，然后单击“确定”。
22. 在打开的对话框中，单击“是”以重新启动 SQL Server 服务。 重新启动 SQL Server 服务后，在“属性”对话框中所做的更改即会生效。
23. 从虚拟机注销。

## <a name="create-the-availability-group"></a>创建可用性组
此时，你已做好配置可用性组的准备。 下面概括了将要执行的操作：

* 在 **ContosoSQL1** 上创建新数据库 (**MyDB1**)。
* 获取数据库的完整备份和事务日志备份。
* 使用 **NORECOVERY** 选项将完整备份和日志备份还原到 **ContosoSQL2**。
* 通过同步提交、自动故障转移和可读辅助副本来创建可用性组 (**AG1**)。

### <a name="create-the-mydb1-database-on-contososql1"></a>在 ContosoSQL1 上创建 MyDB1 数据库
1. 如果尚未从 **ContosoSQL1** 和 **ContosoSQL2** 的远程桌面会话注销，请立即注销。
2. 打开 **ContosoSQL1** 的 RDP 文件，然后以 **CORP\Install** 身份登录。
3. 在“文件资源管理器”中的“C:\\”下，创建名为 **backup** 的目录。 此目录将用于备份并还原数据库。
4. 右键单击新目录，指向“共享”，然后单击“特定用户”，如以下屏幕截图所示。
   
    ![创建备份文件夹](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. 添加 **CORP\SQLSvc1**，然后为其分配**读/写**权限。 添加 **CORP\SQLSvc2**，为其分配**读取**权限（如以下屏幕截图所示），然后单击“共享”。 文件共享过程完成后，请单击“完成”。
   
    ![授予对备份文件夹的权限](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. 若要创建数据库，请从“开始”菜单中打开“SQL Server Management Studio”，然后单击“连接”以连接到默认 SQL Server 实例。
7. 在“对象资源管理器”中，右键单击“数据库”，然后单击“新建数据库”。
8. 在“数据库名称”中，键入 **MyDB1**，然后单击“确定”。

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>创建 MyDB1 的完整备份并在 ContosoSQL2 上还原该数据库
1. 若要创建该数据库的完整备份，请在“对象资源管理器”中，展开“数据库”，右键单击“MyDB1”，指向“任务”，然后单击“备份”。
2. 在“源”部分中，将“备份类型”设置为“完整”。 在“目标”部分中，单击“删除”删除备份文件的默认文件路径。
3. 在“目标”部分中，单击“添加”。
4. 在“文件名”文本框中，键入 **\\ContosoSQL1\backup\MyDB1.bak**，单击“确定”，然后再次单击“确定”以备份数据库。 备份操作完成后，再次单击“确定”以关闭对话框。
5. 若要创建该数据库的事务日志备份，请在“对象资源管理器”中，展开“数据库”，右键单击“MyDB1”，指向“任务”，然后单击“备份”。
6. 在“备份类型”中，选择“事务日志”。 仍将“目标”文件路径设置为此前指定的文件路径，然后单击“确定”。 备份操作完成后，再次单击“确定”。
7. 若要在 **ContosoSQL2** 上还原完整备份和事务日志备份，请打开 **ContosoSQL2** 的 RDP 文件，然后以 **CORP\Install** 身份登录。 将 **ContosoSQL1** 的远程桌面会话保持打开。
8. 从“开始”菜单中打开“SQL Server Management Studio”，然后单击“连接”以连接到默认 SQL Server 实例。
9. 在“对象资源管理器”中，右键单击“数据库”，然后单击“还原数据库”。
10. 在“源”部分中，选择“设备”，然后单击省略号“...” 按钮。
11. 在“选择备份设备”中，单击“添加”。
12. 在“备份文件位置”中，键入 **\\ContosoSQL1\backup**，单击“刷新”，选择“MyDB1.bak”，单击“确定”，然后再次单击“确定”。 现在，应该会在“要还原的备份集”窗格中看到完整备份和日志备份。
13. 转到“选项”页，在“恢复状态”中选择“RESTORE WITH NORECOVERY”，然后单击“确定”以还原数据库。 还原操作完成后，单击“确定”。

### <a name="create-the-availability-group"></a>创建可用性组
1. 返回到 **ContosoSQL1** 的远程桌面会话。 在 SQL Server Management Studio 的“对象资源管理器”中，右键单击“AlwaysOn 高可用性”，然后单击“新建可用性组向导”，如以下屏幕截图所示。
   
    ![启动“新建可用性组”向导](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. 在“简介”页上，单击“下一步”。 在“指定可用性组名称”页上的“可用性组名称”中键入 **AG1**，然后再次单击“下一步”。
   
    ![“新建可用性组”向导中的“指定可用性组名称”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. 在“选择数据库”页上，选择“MyDB1”，然后单击“下一步”。 这些数据库满足可用性组的先决条件，因为你针对预定主副本进行了至少一个完整备份。
   
    ![“新建可用性组”向导中的“选择数据库”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. 在“指定副本”页上，单击“添加副本”。
   
    ![“新建可用性组”向导中的“指定副本”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. 在“连接到服务器”对话框的“服务器名称”中键入 **ContosoSQL2**，然后单击“连接”。
   
    ![“新建可用性组”向导中的“连接到服务器”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. 返回到“指定副本”页，此时应看到“可用副本”中列出了 **ContosoSQL2**。 配置副本，如以下屏幕截图中所示。 完成后，单击“下一步”。
   
    ![“新建可用性组”向导中的“指定副本”（完整）](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. 在“选择初始数据同步”页上，选择“仅联接”，然后单击“下一步”。 在对 **ContosoSQL1** 进行完整备份和事务备份并在 **ContosoSQL2** 上还原这些备份时，已手动执行了数据同步。 可以选择不对数据库执行备份和还原操作，并转而选择“完整”以便让“新建可用性组”向导执行数据同步。 不过，对于某些企业中存在的超大型数据库，我们不建议使用此选项。
   
    ![“新建可用性组”向导中的“选择初始数据同步”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. 在“验证”页上，单击“下一步”。 此页面应与以下屏幕截图类似： 因为你尚未配置可用性组侦听器，会出现一个侦听器配置警告。 你可以忽略此警告，因为本教程不会配置侦听器。 若要在完成本教程后配置侦听器，请参阅[在 Azure 中配置 AlwaysOn 可用性组的 ILB 侦听器](../classic/ps-sql-int-listener.md)。
   
    ![“新建可用性组”向导中的“验证”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. 在“摘要”页上，单击“完成”，然后等待向导配置好新的可用性组。 在“进度”页上，可单击“更多详细信息”以查看详细进度。 向导运行完成后，检查“结果”页以验证是否已成功创建可用性组（如以下屏幕截图所示），然后单击“关闭”以退出向导。
   
    ![“新建可用性组”向导中的“结果”](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. 在“对象资源管理器”中，展开“AlwaysOn 高可用性”，然后展开“可用性组”。 此时，你应在此容器中看到新的可用性组。 右键单击“AG1 (主)”，然后单击“显示仪表板”。
    
     ![显示可用性组仪表板](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. **AlwaysOn 仪表板**应如以下屏幕截图所示。 可以在其中查看副本、每个副本的故障转移模式以及同步状态。
    
     ![可用性组仪表板](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. 返回到“服务器管理器”，单击“工具”，然后打开“故障转移群集管理器”。
13. 展开 **Cluster1.corp.contoso.com**，然后展开“服务和应用程序”。 选择“角色”，并注意已创建“AG1”可用性组角色。 请注意，AG1 没有数据库客户端可按其连接到可用性组的 IP 地址，因为未配置侦听器。 你可以直接连接到主节点进行读写操作，直接连接到辅助节点进行只读查询。
    
     ![故障转移群集管理器中的可用性组](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> 请勿尝试从故障转移群集管理器对可用性组进行故障转移。 所有故障转移操作都应在 SQL Server Management Studio 中的 **AlwaysOn 仪表板**内执行。 有关详细信息，请参阅[将故障转移群集管理器用于可用性组的限制](https://msdn.microsoft.com/library/ff929171.aspx)。
> 
> 

## <a name="next-steps"></a>后续步骤
现在，你已通过在 Azure 中创建可用性组成功实现了 SQL Server Always On。 若要为此可用性组配置侦听器，请参阅[在 Azure 中配置 AlwaysOn 可用性组的 ILB 侦听器](../classic/ps-sql-int-listener.md)。

有关在 Azure 中使用 SQL Server 的其他信息，请参阅 [SQL Server on Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)（Azure 虚拟机上的 SQL Server）。



---

title: "将许可证分配到 Azure Active Directory 中的组 | Microsoft 文档"
description: "如何使用基于 Azure Active Directory 组的许可分配许可证"
services: active-directory
keywords: "Azure AD 许可"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: cd2c8b8bd006d3172cb75f12f4a16fef9b698974
ms.lasthandoff: 03/10/2017


---

# <a name="assign-licenses-to-a-group-of-users-in-azure-active-directory"></a>将许可证分配到 Azure Active Directory 中的用户组

本文逐步讲解有关将产品许可证分配到 Azure Active Directory (Azure AD) 中的用户组，然后验证组的所有成员是否已正确获得许可的一个基本方案。

在此示例中，租户包含名为 *HR Department* 的安全组。 此组包括人力资源部的所有成员（在此示例中，为大约 1,000 个用户）。 管理员想要将 Office 365 企业版 E3 许可证分配给整个部门。 需要暂时禁用产品中包含的 Yammer Enterprise 服务，直到今后该部门准备好开始使用它为止。 管理员还希望将企业移动性 + 安全性许可证部署到同一个用户组。

## <a name="step-1-assign-the-required-licenses"></a>步骤 1：分配所需的许可证

1. 使用管理员帐户登录到 [**Azure 门户**](https://portal.azure.com)。 若要能够管理许可证，该帐户需要具有全局管理员角色或用户帐户管理员角色。

2. 在左侧导航窗格中选择“更多服务”，然后选择“Azure Active Directory”。 可以单击星形图标“收藏”此边栏选项卡，也可以将它固定到门户仪表板。

3. 在“Azure Active Directory”边栏选项卡中，选择“许可证”。 此时将打开一个边栏选项卡，可在其中查看和管理租户中所有可许可的产品。

4. 在“所有产品”，通过选择产品名称选中 Office 365 企业版 E3 和企业移动性 + 安全性。 在边栏选项卡顶部选择“分配”开始分配。

  ![所有产品, 分配许可证](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. 在“分配许可证”边栏选项卡中，单击“用户和组”打开“用户和组”边栏选项卡。 搜索组名称“人力资源部”，选择该组，然后请务必在边栏选项卡底部单击“选择”确认操作。

  ![选择组](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. 在“分配许可证”边栏选项卡中，单击“分配选项(可选)”，显示包含在前面选择的两个产品中的所有服务计划。 查找“Yammer Enterprise”并将设置为“关闭”，以便在产品许可证中禁用该服务。 在“分配选项”的底部单击“确定”确认操作。

  ![分配选项](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. 最后，在“分配许可证”边栏选项卡的底部，单击“分配”完成分配。

8. 右上角会显示一条通知，显示分配过程的状态和结果。 如果无法完成向组分配许可证（例如，由于该组中已有许可证），请单击该通知查看失败详细信息。

现在，我们已为“HR Department”组指定一个许可证模板。 Azure AD 中已启动一个后台进程来处理该组的所有现有成员。 这项初始操作可能需要花费一段时间，具体时间取决于组的当前大小。 在下一步骤中，我们将介绍如何验证该过程是否完成，以及是否需要采取进一步的措施来解决问题。

> [!NOTE]
> 也可以从另一个位置开始相同的分配：Azure AD 中的“用户和组”。 转到“Azure Active Directory” > “用户和组” > “所有组”。 然后找到该组，选择它，并转到“许可证”选项卡。 单击边栏选项卡顶部的“分配”按钮会打开许可证分配边栏选项卡。

## <a name="step-2-verify-that-the-initial-assignment-has-completed"></a>步骤 2：验证初始分配是否已完成

1. 转到“Azure Active Directory” > “用户和组” > “所有组”。 然后找到已向其分配许可证的 *HR Department* 组。

2. 在“HR Department”组边栏选项卡中，选择“许可证”可快速确认是否已完全将许可证分配给用户，以及是否有任何需要调查的错误。 该边栏选项卡中提供以下信息：

  - 当前分配到组的产品许可证列表。 选择一个条目可以显示已启用的特定服务以及进行更改。

  - 上次对组所做的许可证更改的状态（是否正在处理更改，或者是否对所有用户成员的处理已完成）。

  - 有关处于错误状态的用户的信息，因为无法将许可证分配给这些用户。

  ![分配选项](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. 可在“Azure Active Directory” > “用户和组” > “组名称” > “审核日志”下查看有关许可证处理的更多详细信息。 请注意以下活动：

  - 活动：“开始向用户应用基于组的许可证”。 当系统选取针对组所做的许可证分配更改并开始向所有用户成员应用该更改时，记录此活动。 其中包含有关所做的更改的信息。

  - 活动：“完成向用户应用基于组的许可证”。 当系统完成对组中所有用户的处理时，记录此活动。 它包含有关已成功处理多少个用户，以及无法为多少个用户分配组许可证的摘要。

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>步骤 3：检查和解决许可证问题

1. 转到“Azure Active Directory” > “用户和组” > “所有组”，找到已向其分配许可证的 *HR Department* 组。
2. 在“人力资源部”组边栏选项卡中，选择“许可证”。 边栏选项卡顶部的通知显示无法为 10 个用户分配许可证。 单击此通知会打开此组中处于许可错误状态的所有用户的列表。
3. “失败的分配”列告知无法向这些用户分配这两个产品许可证。 **故障的首要原因**包含失败的原因。 在此示例中，原因是**冲突的服务计划**。

  ![失败的分配](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. 选择用户以打开“许可证”边栏选项卡。 此边栏选项卡显示当前已分配给该用户的所有许可证。 在此示例中，可以看到该用户具有从“网亭用户”组继承的 Office 365 企业版 E1 许可证。 这与系统尝试从 **HR Department** 组应用的 E3 许可证冲突。 因此，该组中的许可证均未分配给该用户。

  ![查看用户的许可证](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. 为解决此问题，我们从“网亭用户”组中删除了该用户。 Azure AD 处理更改后，将正常分配“人力资源部”许可证。

  ![正确分配的许可证](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>后续步骤

若要详细了解用于通过组进行许可证管理的功能集，请参阅以下部分：

* [Azure Active Directory 中基于组的许可是什么？](active-directory-licensing-whatis-azure-portal.md)
* [识别和解决 Azure Active Directory 中组的许可问题](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [如何将单个许可用户迁移到 Azure Active Directory 中基于组的许可](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory 基于组的许可的其他方案](active-directory-licensing-group-advanced.md)


---
title: "教程：Azure Active Directory 与 xMatters OnDemand 的集成 | Microsoft Docs"
description: "了解如何使用 xMatters OnDemand 与 Azure Active Directory 以启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 86742dc7ee1429a0cdb23f4da3fa58341468afbc
ms.openlocfilehash: 02ed9be91db6de7f27810fe6b91d8604425073ae
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>教程：Azure Active Directory 与 xMatters OnDemand 的集成
本教程的目的是说明 Azure 与 xMatters OnDemand 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 一个 xMatters OnDemand 租户

完成本教程后，已分配到 xMatters OnDemand 的 Azure AD 用户可在 xMatters OnDemand 公司站点（服务提供商发起的登录）单一登录到应用程序或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)。

在本教程中概述的方案由以下构建基块组成：

1. 启用 xMatters OnDemand 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "方案")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>启用 xMatters OnDemand 的应用程序集成
本部分旨在概述如何启用 xMatters OnDemand 的应用程序集成。

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>若要启用 xMatters OnDemand 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "从库添加应用程序")

6. 在“搜索框”中，键入“xMatters OnDemand”。
   
    ![应用程序库](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "应用程序库")

7. 在“结果”窗格中，选择“XMatters OnDemand”，然后单击“完成”添加该应用程序。
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分旨在概述如何使用基于 SAML 协议的联合身份验证，使用户通过其 Azure AD 中的帐户对 XMatters OnDemand 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户的“XMatters OnDemand”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "配置单一登录")

2. 在“你希望用户如何登录 XMatters OnDemand”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "配置单一登录")

3. 在“配置应用 URL”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "配置应用 URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“XMatters OnDemand 登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.XMattersOnDemandapp.com`
   
    b. 单击“下一步”。

4. 在“配置 XMatters OnDemand 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件本地保存为“c:\\XMatters OnDemand.cer”。
   
    > [!IMPORTANT]
    > 需要将证书转发给 xMatters 支持团队。 xMatters 支持团队上传该证书后，用户才能最终完成单一登录配置。
    > 
    > 
   
    ![配置单一登录](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "配置单一登录")

5. 在其他 Web 浏览器窗口中，以管理员身份登录到 XMatters OnDemand 公司站点。

6. 在顶部工具栏中，单击“管理员”，然后单击左侧导航栏中的“公司详细信息”。
   
    ![管理员](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "管理员")

7. 在“SAML 配置”页上，执行以下步骤：
   
    ![SAML 配置](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML 配置")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“启用 SAML”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户的“配置 XMatters OnDemand 的单一登录”对话框页上，复制“标识提供者 ID”值，然后将其粘贴到“标识提供者 ID”文本框中。
   
    c. 在 Azure 经典门户的“配置 XMatters OnDemand 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“单一登录 URL”文本框中。
   
    d.单击“下一步”。 在 Azure 经典门户的“配置 XMatters OnDemand 的单一登录”对话框页上，复制“单一注销服务 URL”值，然后将其粘贴到“单一注销 URL”文本框中。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在“公司详细信息”页顶部，单击“保存更改”。
    
    ![公司详细信息](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "公司详细信息")

8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录到 XMatters OnDemand，必须将其预配到 XMatters OnDemand 中。  
对于 XMatters OnDemand，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **XMatters OnDemand** 租户。

2. 单击“用户”选项卡。

3. 单击“添加用户”。
  
    ![用户](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "用户")

4. 选择“活动”。

5. 在“添加用户”部分中，执行以下步骤：
   
    ![添加用户](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "添加用户")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 输入想要预配的有效 AAD 帐户的“UserID”、“名字”、“姓氏”和“站点”。
    
    b. 单击“保存” 。


## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>若要将用户分配到 XMatters OnDemand，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“XMatters OnDemand”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。



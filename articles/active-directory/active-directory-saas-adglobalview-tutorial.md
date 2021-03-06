---
title: "教程：Azure Active Directory 与 ADP GlobalView 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 ADP GlobalView 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0f767b96fa8f8f9d1a22709633bee64f0477ffb3
ms.openlocfilehash: 46fbe3233af22605d4cd89227b91313128e523f9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>教程：Azure Active Directory 与 ADP GlobalView 集成
本教程旨在展示如何将 ADP GlobalView 与 Azure Active Directory (Azure AD) 集成。  

将 ADP GlobalView 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 ADP GlobalView
* 可使用户通过其 Azure AD 帐户自动登录 ADP GlobalView 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 ADP GlobalView 的集成，需要以下项目：

* Azure AD 订阅
* 已启用 ADP GlobalView SSO 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
本教程旨在介绍如何在测试环境中测试 Azure AD SSO。  

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ADP GlobalView
2. 配置和测试 Azure AD SSO

## <a name="add-adp-globalview-from-the-gallery"></a>从库中添加 ADP GlobalView
若要配置 ADP GlobalView 与 Azure AD 的集成，需要将库中的 ADP GlobalView 添加到托管的 SaaS 应用列表。

**若要从库中添加 ADP GlobalView，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“ADP GlobalView”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. 在“结果”窗格中，选择“ADP GlobalView”，然后单击“完成”，添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
本部分的目的是说明如何基于名为“Britta Simon”的测试用户配置和测试 ADP GlobalView 的 Azure AD SSO。

若要运行 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 ADP GlobalView 用户。 换句话说，需要建立 Azure AD 用户与 ADP GlobalView 中相关用户之间的关联关系。  

通过将 Azure AD 中的 **user name** 值分配为 ADP GlobalView 中的 **Username** 值，建立此关联关系。

若要通过 ADP GlobalView 配置和测试 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ADP GlobalView 测试用户](#creating-a-adp-globalview-test-user)** - 在 ADP GlobalView 中创建 Britta Simon 的对立用户，并且将其链接到她在 Azure AD 中的代表。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO
本部分旨在介绍如何在 Azure 经典门户中启用 Azure AD SSO 并在 ADP GlobalView 应用程序中配置 SSO。

ADP GlobalView 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 

以下屏幕截图显示一个示例。 声明名称将始终是**“PersonImmutableID”**和映射到包含用户 EmployeeID 的 ExtensionAttribute2 的值。 将用户从 Azure AD 映射到 ADP GlobalView 在此是在 EmployeeID 上完成，但可将其映射到同样基于应用程序设置的其他值。 

首先可与 ADP GlobalView 团队合作，使用用户的正确标识符，并将该值与**“PersonImmutableID”**声明一起映射。 也可映射电子邮件和 UserID 声明，如图中所示。

![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

在配置 SAML 断言前，需要联系 ADP GlobalView 支持团队，并为租户请求唯一标识符属性值。 拥有此值才能为应用程序配置自定义声明。

**若要通过 ADP GlobalView 配置 Azure AD SSO，请执行以下步骤：**

1. 在 Azure 经典门户的“ADP GlobalView”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 ADP GlobalView”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 
  1. 在“标识符”文本框中，使用以下格式之一键入用于标识 ADP GlobalView 应用程序的 URL：`https://<server name>.globalview.adp.com/federate2` 或 `https://<server name>.globalview.adp.com/federate`
  2. 在“回复 URL”文本框中，使用以下格式之一键入 Azure AD 用于将响应发布到 ADP GlobalView 应用程序的 URL：`https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` 或 `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2`
  3. 单击“资源组名称” 的 Azure 数据工厂。
4. 在“配置 ADP GlobalView 的单一登录”页面上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)   
  1. 单击“下载证书”，然后将文件保存在计算机上。
  2. 单击“资源组名称” 的 Azure 数据工厂。
5. 若要为应用程序配置 SSO，请联系 ADP GlobalView 支持团队，并向其提供以下信息： 
   
   * 下载的**证书**
   * **实体 ID**
   * **SAML SSO URL**
   * **单一注销服务 URL**

    >[!NOTE]
    >在 **ADP GlobalView** 团队配置实例以后，从该团队获取 **RelayState** 值。 按照下述步骤对其进行配置。 这样配置后，可以测试集成。 因此，请注意，若要使此应用程序集成正常运行，这是一项重要配置。
    >

6. 若要在 Azure AD 中配置 RelayState 值，请执行以下步骤：   
 1. 以管理员身份登录 [Azure 管理门户](https://portal.azure.com)。
 2. 在左侧导航窗格中，单击“更多服务”。 
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)  
 3. 在“搜索”文本框中，键入 **Azure Active Directory**，然后单击相关链接。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)  
 4. 单击“企业应用程序”。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png) 
 5. 在“管理”部分单击“所有应用程序”。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png) 
 6. 在“搜索”文本框中，键入 **ADP eTime**，然后单击相关链接。 
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png) 
 7. 在“管理”部分单击“单一登录”。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)  
 8. 选择“显示高级 URL 设置”。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
 9. 在“回复状态”文本框中，使用以下格式键入值：
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)  
 10. 保存设置。
7. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
8. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

* 在“用户列表”中，选择“Britta Simon”。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)  
 1. 在“用户类型”中，选择“你的组织中的新用户”。  
 2. 在“用户名”文本框中，键入“BrittaSimon”。 
 3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)  
 1. 在“名字”文本框中，键入“Britta”。    
 2. 在“姓氏”文本框中，键入“Simon”。 
 3. 在“显示名称”文本框中，键入“Britta Simon”。 
 4. 在“角色”列表中，选择“用户”。 
 5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)  
 1. 写下“新密码”的值。  
 2. 单击“完成”。   

### <a name="create-a-adp-globalview-test-user"></a>创建 ADP GlobalView 测试用户
本部分要在 ADP GlobalView 中创建名为“Britta Simon”的用户。 请与 ADP GlobalView 支持团队合作，添加 ADP GlobalView 帐户中的用户。 

>[!NOTE]
>如果需要手动创建用户，需要联系 ADP GlobalView 支持团队。
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分旨在通过授予 Britta Simon 访问 ADP GlobalView 的权限，允许她使用 Azure SSO。

![分配用户][200] 

**若要将 Britta Simon 分配到 ADP GlobalView，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在“应用程序列表”中，选择“ADP GlobalView”。
   
    ![配置单一登录](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试 Azure AD SSO 配置。  

当在访问面板中单击“ADP GlobalView”磁贴时，应该会自动登录“ADP GlobalView”应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png


---
title: "教程：Azure Active Directory 与 YouEarnedIt 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 YouEarnedIt 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 3011d44d-dfcf-4061-888f-cff90fbc8150
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: a1e6f6738a3e6426a5ec5e0dab6f479a0a74b0ad
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-youearnedit"></a>教程：Azure Active Directory 与 YouEarnedIt 集成
在本教程中，了解如何将 YouEarnedIt 与 Azure Active Directory (Azure AD) 集成。

将 YouEarnedIt 与 Azure AD 集成可带来以下好处：

* 可在 Azure AD 中控制谁有权访问 YouEarnedIt
* 可以让用户通过其 Azure AD 帐户自动登录到 YouEarnedIt 单一登录 (SSO)
* 可以在一个中心位置（即 Azure 经典门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 YouEarnedIt 的集成，需要以下各项：

* Azure AD 订阅
* 已启用 YouEarnedIt 单一登录的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，在测试环境中测试 Azure AD 单一登录。

本教程中概述的方案包括两个主要构建基块：

1. 从库添加 YouEarnedIt
2. 配置和测试 Azure AD 单一登录

## <a name="add-youearnedit-from-the-gallery"></a>从库添加 YouEarnedIt
若要配置 YouEarnedIt 与 Azure AD 的集成，需要从库中将 YouEarnedIt 添加到托管 SaaS 应用列表。

**若要从库添加 YouEarnedIt，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入“YouEarnedIt”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_01.png)
7. 在“结果”窗格中，选择“YouEarnedIt”，然后单击“完成”，添加该应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_06.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户，使用 YouEarnedIt 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 YouEarnedIt 用户。 换句话说，需要建立 Azure AD 用户与 YouEarnedIt 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 YouEarnedIt 中“用户名”的值来建立此链接关系。

若要使用 YouEarnedIt 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 YouEarnedIt 测试用户](#creating-a-predictix-price-reporting-test-user)** -获取 YouEarnedIt 中 Britta Simon 的副本，该副本链接到她的 Azure AD 表示。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在本部分中，在经典门户中启用 Azure AD 单一登录，并在 YouEarnedIt 应用程序中配置单一登录。

**若要使用 YouEarnedIt 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在经典门户中的“YouEarnedIt”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“你希望用户如何登录 YouEarnedIt”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_04.png) 
  1. 在“登录 URL”文本框中，使用以下模式键入用户用于登录 YouEarnedIt 应用程序的 URL：  
    * 沙盒环境：`https://<company name>.sandbox.youearnedit.com/users/sign_in`
    * 生产环境：`https://<company name>.youearnedit.com/users/sign_in`  
   2. 单击“下一步”
4. 在“配置 YouEarnedIt 的单一登录”页上，执行以下步骤：
   
    ![配置单一登录](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_05.png)
  1. 单击“下载证书”，然后将文件保存在计算机上。  
  2. 单击“资源组名称” 的 Azure 数据工厂。
5. 若要为应用程序配置 SSO，请联系 YouEarnedIt 支持团队，并向其提供以下内容：  
    • 下载的**证书**
    • **SAML SSO URL**
6. 在经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
7. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在经典门户中创建一个名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：

    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_05.png)  
  1. 在“用户类型”中，选择“你的组织中的新用户”。
  2. 在“用户名”文本框中，键入“BrittaSimon”。  
  3. 单击“资源组名称” 的 Azure 数据工厂。
6. 在“用户配置文件”对话框页上，执行以下步骤：

   ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_06.png)    
  1. 在“名字”文本框中，键入“Britta”。  
  2. 在“姓氏”文本框中，键入“Simon”。
  3. 在“显示名称”文本框中，键入“Britta Simon”。
  4. 在“角色”列表中，选择“用户”。
  5. 单击“资源组名称” 的 Azure 数据工厂。
7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-youearnedit-tutorial/create_aaduser_08.png) 
  1. 写下“新密码”的值。
  2. 单击“完成”。   

### <a name="create-an-youearnedit-test-user"></a>创建 YouEarnedIt 测试用户
在本部分中，将在 YouEarnedIt 中创建一个名为“Britta Simon”的用户。 请与 YouEarnedIt 支持团队协作，将用户添加到 YouEarnedIt 平台中。

>[!NOTE]
>YouEarnedIt 需要标识提供者在 NameID 属性中提供 EmailAddress 或 UserName。 如果在数据库中找不到相应的 UserName 或 EmailAddress，或不完全匹配，则身份验证失败。 这需要在 SSO 集成之前将帐户导入到 YouEarnedIt 系统中（通常通过 API 或 CSV 导入）。
>  

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
在本部分中，通过授予 Britta Simon 访问 YouEarnedIt 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 YouEarnedIt，请执行以下步骤：**

1. 在经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“YouEarnedIt”。
   
    ![配置单一登录](./media/active-directory-saas-youearnedit-tutorial/tutorial_youearnedit_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203]
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="test-single-sign-on"></a>测试单一登录
在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“YouEarnedIt”磁贴时，应自动登录到 YouEarnedIt 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-youearnedit-tutorial/tutorial_general_205.png


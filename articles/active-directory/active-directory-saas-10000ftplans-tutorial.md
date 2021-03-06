---
title: "教程：Azure Active Directory 与 10,000ft Plans 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 10,000ft Plans 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: b60c955e-8fa3-4872-a897-c4e81fd7beac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2a1f3df856116e372518b2f88460b3c6905c4c9a
ms.openlocfilehash: 584ddcfc53afee6a8ff11a03d662d8acf58e4820
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-10000ft-plans"></a>教程：Azure Active Directory 与 10,000ft Plans 的集成
本教程用于向你显示如何将 10,000ft Plans 与 Azure Active Directory (Azure AD) 集成。  
将 10,000ft Plans 与 Azure AD 集成可带来以下好处：

* 可在 Azure AD 中控制谁有权访问 10,000ft Plans
* 可让你的用户使用其 Azure AD 帐户自动登录到 10,000ft Plans（单一登录）
* 可在一个中心位置（即 Azure 经典门户）管理你的帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 10,000ft Plans 的集成，需要以下各项：

* 一个 Azure AD 订阅
* 启用了 10,000ft Plans 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。
> 
> 

测试本教程中的步骤应遵循以下建议：

* 不应使用生产环境，除非有此必要。
* 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
本教程的目的是介绍如何在测试环境中测试 Azure AD 单一登录。  
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 10,000ft Plans
2. 配置和测试 Azure AD 单一登录

## <a name="adding-10000ft-plans-from-the-gallery"></a>从库中添加 10,000ft Plans
若要配置 10,000ft Plans 到 Azure AD 的集成，需要将 10,000ft Plans 从库中添加到托管的 SaaS 应用列表。

**若要从库中添加 10,000ft Plans，请执行以下步骤：**

1. 在 **Azure 经典门户**的左侧导航窗格上，单击“Active Directory”。 
   
    ![Active Directory][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![应用程序][4]
6. 在搜索框中，键入 **10,000ft Plans**。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_01.png)
7. 在结果窗格中，选择“10,000ft Plans”，然后单击“完成”以添加应用程序。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分用于向你显示如何根据名为“Britta Simon”的测试用户使用 10,000ft Plans 配置和测试 Azure AD 单一登录。

若要使单一登录生效，Azure AD 需要了解 Azure AD 中的用户对应于 10,000ft Plans 中的哪个用户。 换言之，需要建立 Azure AD 用户与 10,000ft Plans 中的相关用户之间的链接关系。  
通过将 Azure AD 中的 **user name** 值分配为 10,000ft Plans 中的 **Username** 值建立此链接关系。

若要使用 10,000ft Plans 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 10,000ft Plans 测试用户](#creating-a-10000ft-plans-test-user)** - 在 Certify 中有一个已链接到 Azure AD 中 Britta Simon 表示形式的对应项。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
本部分要在 Azure 经典门户中启用 Azure AD 单一登录，并在 10,000ft Plans 应用程序中配置单一登录。

**若要使用 10,000ft Plans 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中，在“10,000ft Plans”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
    ![配置单一登录][6] 
2. 在“希望用户如何登录到 10,000ft Plans”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_03.png) 
3. 在“配置应用设置”对话框页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_04.png) 

    a. 在“登录 URL”文本框中，键入 `https://app.10000ft.com`。

    b. 在“标识符”文本框中，键入 `https://app.10000ft.com/saml/metadata`。

    > [!NOTE] 
    > 如果你有自定义域，**Identifier** 的值将不同。 如果你需要帮助，请联系 [10,000ft Plans 支持团队](mailto:support@10000ft.com)。  

    c. 单击“下一步”


1. 在“在 10,000ft Plans 上配置单一登录”页上，执行以下步骤，然后单击“下一步”：
   
    ![配置单一登录](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_05.png) 
   
    a. 单击“下载证书”，然后将文件保存在计算机上。
   
    b. 单击“下一步”。
2. 若要获取为你的应用程序配置的 SSO，请联系 [10,000ft Plans 支持团队](mailto:support@10000ft.com)、附加下载的证书并向他们提供颁发者 URL、SAML SSO URL 和注销 URL。
3. 在 Azure 经典门户中，选择单一登录配置确认，然后单击“下一步”。
   
    ![Azure AD 单一登录][10]
4. 在“单一登录确认”页上，单击“完成”。  
   
    ![Azure AD 单一登录][11]

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 经典门户中创建名为 Britta Simon 的测试用户。  

![创建 Azure AD 用户][20]

**若要在 Azure AD 中创建 SECURE DELIVER 测试用户，请执行以下步骤：**

1. 在 **Azure 经典门户**中，在左侧导航窗格上，单击“Active Directory”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_09.png) 
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要显示用户列表，请在顶部菜单中，单击“用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_03.png) 
4. 若要打开“添加用户”对话框，请在底部工具栏中单击“添加用户”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_04.png) 
5. 在“告诉我们有关此用户的信息”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_05.png) 
   
    a. 对于“用户类型”，选择“组织中的新用户”。
   
    b. 在“用户名”文本框中，键入“BrittaSimon”。
   
    c. 单击“下一步”。
6. 在“用户配置文件”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_06.png) 
   
    a. 在“名字”文本框中，键入“Britta”。  
   
    b. 在“姓氏”文本框中，键入“Simon”。
   
    c. 在“显示名称”文本框中，键入“Britta Simon”。
   
    d.单击“下一步”。 在“角色”列表中，选择“用户”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“下一步”。

7. 在“获取临时密码”对话框页上，单击“创建”。
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_07.png) 
8. 在“获取临时密码”对话框页上，执行以下步骤：
   
    ![创建 Azure AD 测试用户](./media/active-directory-saas-10000ftplans-tutorial/create_aaduser_08.png) 
   
    a. 写下“新密码”的值。
   
    b. 单击“完成”。   

### <a name="creating-a-10000ft-plans-test-user"></a>创建 10,000ft Plans 测试用户
本部分要在 10,000ft Plans 中创建一个名为 Britta Simon 的用户。 10,000ft Plans 支持在默认情况下启用的实时预配。

此部分不存在任何操作项。 如果尚不存在用户，将在尝试访问 10,000ft Plans 期间创建一个新用户。 

> [!NOTE]
> 如果需要手动创建用户，你需要联系 Certify 支持团队。
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
本部分用于通过向 Britta Simon 授权访问 10,000ft Plans 让她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 10,000ft Plans，请执行以下步骤：**

1. 在 Azure 经典门户中，若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![分配用户][201] 
2. 在应用程序列表中，选择“10,000ft Plans”。
   
    ![配置单一登录](./media/active-directory-saas-10000ftplans-tutorial/tutorial_10000ftplans_50.png) 
3. 在顶部菜单中，单击“用户”。
   
    ![分配用户][203] 
4. 在“用户”列表中，选择“Britta Simon”。
5. 在底部工具栏中，单击“分配”。
   
    ![分配用户][205]

### <a name="testing-single-sign-on"></a>测试单一登录
本部分旨在使用“访问面板”测试你的 Azure AD 单一登录配置。  
在“访问面板”中单击 10,000ft Plans 磁贴时，应自动登录到 10,000ft Plans 应用程序。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-10000ftplans-tutorial/tutorial_general_205.png


---
title: "Tutorial: Integração do Azure Active Directory com o Procore SSO | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Procore SSO."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 042a41eaa6bb21670b4c12068f1b017222f64b99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: integração do Azure Active Directory com o Procore SSO

Neste tutorial, você aprenderá a integrar o Procore SSO ao Azure AD (Azure Active Directory).

A integração do Procore SSO com o Azure AD oferece os seguintes benefícios:

- É possível controlar, no Azure AD, quem tem acesso ao Procore SSO
- É possível permitir que seus usuários façam logon automaticamente no Procore SSO (Logon Único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Procore SSO, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Procore SSO.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Procore SSO, são necessários os seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura do Procore SSO habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Procore SSO da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionando o Procore SSO da galeria
Para configurar a integração do Procore SSO com o Azure AD, é necessário adicionar o Procore SSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Procore SSO da galeria, siga as etapas abaixo:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Procore SSO**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. No painel de resultados, selecione **Procore SSO** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Procore SSO, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Procore SSO é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Procore SSO.

Essa relação de vínculo é estabelecida atribuindo o valor de **nome de usuário** ao Azure AD como sendo o valor de **Nome de usuário** no Procore SSO.

Para configurar e testar o logon único do Azure AD com o Procore SSO, é necessário concluir os seguintes blocos de construção:

1. **[Configuração do logon único do AD do Azure](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criando um usuário de teste do Procore SSO](#creating-a-procore-sso-test-user)** – para ter um equivalente de Brenda Fernandes no Procore SSO que esteja vinculado à representação dela no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** : para permitir que Brenda Fernandes use o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal de Gerenciamento do Azure e configurará o logon único em seu aplicativo Procore SSO.

**Para configurar o logon único do Azure AD com o Procore SSO, siga as etapas abaixo:**

1. No portal de Gerenciamento do Azure, na página de integração de aplicativos do **Procore SSO**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. Na seção **URLs e Domínio do Procore SSO**, o usuário não precisa seguir as etapas, já que o aplicativo está previamente integrado com o Azure.

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo XML em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Procore SSO**, clique em **Configurar Procore SSO** para abrir a janela **Configurar logon**. Copie a **ID da entidade SAML e URL de serviço logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Para configurar o logon único no lado do **Procore SSO**, faça logon no site da sua empresa Procore como administrador.

8. Na lista suspensa da caixa de ferramentas, clique em **Admin** para abrir a página de configurações de SSO.

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Cole os valores nas caixas conforme descrito abaixo-

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. Na caixa **URL do emissor de logon único**, cole a ID da entidade SAML copiada do portal do Azure.

    b. Na caixa **URL de destino de logon único SAML**, cole a URL de serviço logon único SAML copiada do portal do Azure.

    c. Agora, abra o **XML de metadados** baixado acima do portal do Azure e copie o certificado na marcação nomeada **X509Certificate**. Cole o valor copiado para a caixa **Certificado x509 de logon único**.

10. Clique em **Salvar alterações**.

11. Após essas configurações, é necessário enviar o **nome de domínio** (por exemplo, **contoso.com**) por meio do qual você está fazendo logon no Procore para a [equipe de suporte Procore](https://support.procore.com/) e eles ativarão o SSO federado para esse domínio.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-procore-sso-test-user"></a>Criando um usuário de teste do Procore SSO

Siga as etapas abaixo para criar um usuário de teste do Procore no outro lado.

1. Faça logon no site da sua empresa do Procore como administrador.  

2. Na lista suspensa da caixa de ferramentas, clique em **Diretório** para abrir a página do diretório da empresa.

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Clique na opção **Adicionar uma pessoa** para abrir o formulário e insira as seguintes opções –

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    c. Na caixa de texto **Endereço de email**, digite o endereço de email do usuário como **BrittaSimon@contoso.com**.

    d. Selecione **Modelo de permissão** como **Aplicar o modelo de permissão mais tarde**.

    e. Clique em **Criar**.

4. Verifique e atualize os detalhes do contato recém-adicionado.

    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Clique em **Salvar e enviar convite** (se for necessário enviar um convite por email) ou **Salvar** (Salvar diretamente) para concluir o registro do usuário.
    
    ![Configurar Logon Único](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure, concedendo-lhe acesso ao Procore SSO.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Procore SSO, siga as etapas abaixo:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Procore SSO**.

    ![Configurar o logon único](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586). Ao clicar no bloco Procore SSO no Painel de Acesso, seu logon deverá ser feito automaticamente no aplicativo Procore SSO.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png


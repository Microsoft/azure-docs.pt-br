---
title: "Tutorial: integração do Azure Active Directory com o Teamphoria | Microsoft Docs"
description: "Saiba como configurar o logon único entre o Azure Active Directory e o Teamphoria."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 2a35efb04d7fe22abc6894c149caf090666ce016
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Tutorial: integração do Azure Active Directory ao Teamphoria

Neste tutorial, você aprenderá a integrar o Teamphoria ao Azure AD (Azure Active Directory).

A integração do Teamphoria com o Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Teamphoria
- É possível permitir que seus usuários façam logon automaticamente no Teamphoria (logon único) com suas contas do Azure AD
- Você pode gerenciar suas contas em um único local - o portal de Gerenciamento do Azure

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Teamphoria, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Teamphoria.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Teamphoria, você precisará dos seguintes itens:

- Uma assinatura do AD do Azure
- Uma assinatura habilitada para logon único do Teamphoria

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você poderá obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o Teamphoria da galeria
2. Configurar e testar o logon único do AD do Azure

## <a name="adding-teamphoria-from-the-gallery"></a>Adicionar o Teamphoria da galeria
Para configurar a integração do Teamphoria com o Azure AD, você precisará adicionar o Teamphoria da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Teamphoria da galeria, realize as seguintes etapas:**

1. No **[Portal de Gerenciamento do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![Aplicativos][2]
    
3. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicativos][3]

4. Na caixa de pesquisa, digite **Teamphoria**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_search.png)

5. No painel de resultados, selecione **Teamphoria** e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurar e testar o logon único do AD do Azure
Nesta seção, você configurará e testará o logon único do Azure AD com o Teamphoria, com base em um usuário de teste chamado “Brenda Fernandes”.

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Teamphoria é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Teamphoria.

Essa relação de vínculo é estabelecida atribuindo o valor do **nome de usuário** no Azure AD como o valor do **Nome de usuário** no Teamphoria.

Para configurar e testar o logon único do Azure AD com o Teamphoria, você precisará concluir os seguintes blocos de construção:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** : para habilitar seus usuários a usar esse recurso.
2. **[Criação de um usuário de teste do AD do Azure](#creating-an-azure-ad-test-user)** : para testar o logon único do AD do Azure com Brenda Fernandes.
3. **[Criação de um usuário de teste do Teamphoria](#creating-a-teamphoria-test-user)** – para ter um equivalente a Brenda Fernandes no Teamphoria que esteja vinculado à sua representação no Azure AD.
4. **[Atribuição do usuário de teste do AD do Azure](#assigning-the-azure-ad-test-user)** - para habilitar Britta Simon a usar o logon único do AD do Azure.
5. **[Testing Single Sign-On](#testing-single-sign-on)** : para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do logon único do Azure AD

Nesta seção, você habilita o logon único do Azure AD no Portal de Gerenciamento do Azure e configura o logon único em seu aplicativo Teamphoria.

**Para configurar o logon único do Azure AD com o Teamphoria, execute as seguintes etapas:**

1. No Portal de Gerenciamento do Azure, na página de integração de aplicativos do **Teamphoria**, clique em **Logon único**.

    ![Configurar o logon único][4]

2. Na caixa de diálogo **Logon único**, como **Modo**, selecione **Logon baseado em SAML** para habilitar o logon único.
 
    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. Na seção **URLs e Domínio do Teamphoria**, execute as seguintes etapas:

    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_url.png)

    a. Na caixa de texto **URL de Entrada**, digite a URL usando o seguinte padrão: `https://<sub-domain>.teamphoria.com/login`    

    > [!NOTE] 
    > Observe que esses não são os valores reais. Você precisa atualizar esses valores com a URL de Entrada real. Entre em contato com a [Equipe de suporte do cliente do Teamphoria](https://www.teamphoria.com/) para obter a URL de Entrada. 

4. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o certificado em seu computador.

    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_certificate.png) 

5. Clique no botão **Salvar** .

    ![Configurar o logon único](./media/active-directory-saas-teamphoria-tutorial/tutorial_general_400.png)

6. Na seção **Configuração do Teamphoria**, clique em **Configurar o Teamphoria** para abrir a janela **Configurar logon**. Copie a **URL de serviço de logon único SAML** da **seção de Referência Rápida.**

    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_configure.png) 

7. Para configurar o logon único pelo **Teamphoria**, faça logon em seu aplicativo Teamphoria como administrador.

8. Vá até a opção **CONFIGURAÇÕES DE ADMINISTRAÇÃO** na barra de ferramentas à esquerda e, na guia Configurar, clique em **LOGON ÚNICO** para abrir a janela de configuração de SSO.

    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/admin_sso_configure.png)

9. Clique na opção **ADICIONAR NOVO PROVEDOR DE IDENTIDADE** no canto superior direito para abrir o formulário para adicionar as configurações de SSO.

    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/add_new_identity_provider.png)

10. Insira os detalhes nos campos conforme descrito abaixo:

    ![Configurar o logon único](./media/active-directory-saas-teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **NOME DE EXIBIÇÃO**: insira o nome de exibição do plug-in na página de administração.

    b. **NOME DO BOTÃO**: o nome da guia que será exibido na página de logon para fazer logon por meio do SSO.

    c. **CERTIFICADO**: abra o certificado baixado anteriormente do Portal do Azure no bloco de notas, copie seu conteúdo e cole-o nesta caixa.

    d. **PONTO DE ENTRADA**: cole a **URL de serviço de logon único SAML** copiada do anteriormente do portal do Azure.

    e. Alterne a opção para **LIGADO** e clique em **SALVAR**.   

<!--### Next steps

To ensure users can sign-in to Teamphoria after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Teamphoria prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Teamphoria in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Teamphoria users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Criação de um usuário de teste do AD do Azure
O objetivo desta seção é criar um usuário de teste no Portal de Gerenciamento do Azure chamado Britta Simon.

![Criar um usuário do AD do Azure][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No **portal de Gerenciamento do Azure**, no painel navegação à esquerda, clique em **Azure Active Directory**.

    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_01.png) 

2. Vá para **usuários e grupos** e clique em **todos os usuários** para exibir a lista de usuários.
    
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_02.png) 

3. Na parte superior da caixa de diálogo clique **adicionar** para abrir o **usuário** caixa de diálogo.
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_03.png) 

4. Na página do diálogo **Usuário**, execute as seguintes etapas:
 
    ![Criação de um usuário de teste do AD do Azure](./media/active-directory-saas-teamphoria-tutorial/create_aaduser_04.png) 

    a. Na caixa de texto **Nome**, digite **Brenda Fernandes**.

    b. Na caixa de texto **Nome de usuário**, digite o **endereço de email** da conta de Brenda Fernandes.

    c. Selecione **Mostrar senha** e anote o valor de **senha**.

    d. Clique em **Criar**.
 
### <a name="creating-a-teamphoria-test-user"></a>Criar um usuário de teste do Teamphoria

Para permitir que usuários do Azure AD façam logon no Teamphoria, eles deverão ser provisionados no Teamphoria. No caso do Teamphoria, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site da empresa do Teamphoria como administrador.

2. Clique nas configurações de **ADMIN** na barra de ferramentas à esquerda e, na guia **GERENCIAR**, clique em **USUÁRIOS** para abrir a página de administração para usuários.

    ![Adicionar Funcionário](./media/active-directory-saas-teamphoria-tutorial/admin_manage_users.png)

3. Clique na opção **CONVITE MANUAL**.

    ![Convidar Pessoas](./media/active-directory-saas-teamphoria-tutorial/admin_manage_add_users.png)  

4. Nessa página, realize a ação a seguir. 
    
    ![Convidar Pessoas](./media/active-directory-saas-teamphoria-tutorial/manual_user_invite.png)  

    a. Na caixa de texto **ENDEREÇO DE EMAIL**, digite o **endereço de email** de Brenda Fernandes.

    b. Na caixa de texto **NOME**, digite **Brenda**.

    c. Na caixa de texto **SOBRENOME**, digite **Fernandes**.

    d. Clique em **CONVIDAR 1 USUÁRIO**. O usuário deve aceitar o convite para ser criado no sistema.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuição do usuário de teste do AD do Azure

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Teamphoria.

![Atribuir usuário][200] 

**Para atribuir Brenda Fernandes ao Teamphoria, execute as seguintes etapas:**

1. No portal de gerenciamento do Azure, abra a exibição de aplicativos e, em seguida, navegue até o modo de exibição de diretório e vá para **aplicativos empresariais** e clique em **todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Teamphoria**.

    ![Configurar Logon Único](./media/active-directory-saas-teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. No menu à esquerda, clique em **usuários e grupos**.

    ![Atribuir usuário][202] 

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![Atribuir usuário][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste do logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-teamphoria-tutorial/tutorial_general_203.png


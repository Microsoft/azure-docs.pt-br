---
title: 'Tutorial: Integração do Azure Active Directory com o Workteam | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd986544dfb1472f5cc8a013fec951dca42a59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60523775"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Tutorial: Integração do Azure Active Directory com o Workteam

Neste tutorial, você aprenderá a integrar o Workteam com o Azure AD (Azure Active Directory).

A integração do Workteam com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Workteam.
- Você pode permitir que os usuários entrem automaticamente no Workteam (logon único) com a conta do Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Workteam, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Uma assinatura do Workteam habilitada para logon único

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Workteam da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-workteam-from-the-gallery"></a>Adicionando o Workteam da galeria
Para configurar a integração do Workteam ao Azure AD, você precisa adicionar o Workteam da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Workteam da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Workteam**, selecione **Workteam** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Workteam na lista de resultados](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Workteam com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Workteam é equivalente a um usuário do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Workteam.

Para configurar e testar o logon único do Azure AD com o Workteam, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Criar um usuário de teste do Workteam](#create-a-workteam-test-user)** – para ter um equivalente de Brenda Fernandes no Workteam vinculado à representação do usuário no Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o único logon do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Workteam.

**Para configurar o logon único do Azure AD com o Workteam, execute as seguintes etapas:**

1. No portal do Azure, na página de integração do aplicativo **Workteam**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. Na seção **URLs e Domínio do Workteam**, o usuário não precisa seguir nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações de domínio e de URLs do Workteam para logon único](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Marque **Mostrar configurações avançadas de URL** e realize a seguinte etapa se quiser configurar o aplicativo no modo iniciado pelo **SP**:

    ![Informações de domínio e de URLs do Workteam para logon único](./media/workteam-tutorial/tutorial_workteam_url1.png)

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.workte.am`
     
5. Na seção **Certificado de Autenticação SAML**, clique em **Certificado (Base64)** e, em seguida, salve o arquivo do certificado em seu computador.

    ![O link de download do Certificado](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/workteam-tutorial/tutorial_general_400.png)
    
7. Na seção **Configuração do Workteam**, clique em **Configurar Workteam** para abrir a janela **Configurar logon**. Copie a **ID da Entidade SAML e a URL do Serviço de Logon Único SAML** da **seção Referência Rápida.**

    ![Configuração do Workteam](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. Em um navegador da Web diferente, entre no Workteam como Administrador de Segurança.

9. No canto superior direito, clique no **logotipo do perfil** e, em seguida, em **Configurações da organização**. 

    ![Configurações do Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. Na seção **AUTENTICAÇÃO**, clique no **Logotipo de configurações**.

     ![Workteam Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. Na página **Configurações de SAML** , execute as seguintes etapas:

     ![Workteam SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

     a. Selecione o **IdP do SAML** como **Azure AD**.

    b. Cole o valor da **URL do Serviço de Logon Único SAML** que você copiou do portal do Azure em **URL do Serviço de Logon Único SAML**.

    c. Na caixa de texto **ID da Entidade SAML**, cole o valor da **ID da Entidade SAML** que você copiou do portal do Azure.

    d. No Bloco de Notas, abra o **certificado codificado em base64** baixado do portal do Azure, copie seu conteúdo e cole-o na caixa **Certificado de Autenticação SAML (Base64)** .

    e. Clique em **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/workteam-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/workteam-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/workteam-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/workteam-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
 
### <a name="create-a-workteam-test-user"></a>Criar um usuário de teste do Workteam

Para permitir que os usuários do Azure AD façam logon no Workteam, eles precisam ser provisionados no Workteam. No Workteam, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon no Workteam como Administrador de Segurança.

2. Na parte superior central da página **Configurações da organização**, clique em **USUÁRIOS** e, em seguida, em **NOVO USUÁRIO**.

    ![Usuário do Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Na página **Novo funcionário**, realize as seguintes etapas:

    ![Novo usuário do Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

     a. Na caixa de texto **Nome**, insira o nome do usuário como **Brendafernandes**.

    b. Na caixa de texto **Email**, insira o email do usuário como **Brendafernandes\@contoso.com**.

    c. Clique em **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo o acesso ao Workteam.

![Atribuir a função de usuário][200] 

**Para atribuir Brenda Fernandes ao Workteam, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201] 

2. Na lista de aplicativos, selecione **Workteam**.

    ![O link do Workteam na lista de aplicativos](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Workteam no Painel de Acesso, você será conectado automaticamente ao aplicativo Workteam.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png


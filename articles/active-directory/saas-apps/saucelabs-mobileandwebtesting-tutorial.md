---
title: 'Tutorial: Integração do Azure Active Directory com o Sauce Labs - Mobile and Web Testing | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Sauce Labs - Mobile e Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99f794c4eb9db78f50f63f14ccfad08c65ddb07
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60590849"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutorial: Integração do Azure Active Directory com o Sauce Labs - Mobile and Web Testing

Neste tutorial, você aprenderá como integrar o Sauce Labs - Mobile e Web Testing ao Azure AD (Azure Active Directory).

A integração do Sauce Labs - Mobile e Web Testing com o Azure AD oferece os seguintes benefícios:

- Você pode controlar no Azure AD quem tem acesso ao Sauce Labs - Mobile e Web Testing.
- Você pode permitir que seus usuários façam logon automaticamente no Sauce Labs - teste móvel e da Web (logon único) com suas contas do AD do Azure.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Sauce Labs - Mobile e Web Testing, você precisa dos seguintes itens:

- Uma assinatura do Azure AD
- Um Sauce Labs - Assinatura habilitada para logon único para dispositivos móveis e Web Testing

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.  O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Sauce Labs - Mobile e Web Testing na galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Adicionando Sauce Labs - Mobile e Web Testing na galeria
Para configurar a integração do Sauce Labs - Mobile e Web Testing no Azure AD, você precisa adicionar o Sauce Labs - Mobile e Web Testing da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Sauce Labs - Mobile e Web Testing da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **Sauce Labs - Mobile e Web Testing**, selecione **Sauce Labs - Mobile e Web Testing** no painel de resultados e clique no botão **Add** para adicionar o aplicação.

    ![Sauce Labs - Mobile e Web Testing na lista de resultados](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Sauce Labs - Mobile e Web Testing, com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do Sauce Labs - Mobile e Web Testing é um usuário do Azure AD. Em outras palavras, um relacionamento de link entre um usuário do Azure AD e o usuário relacionado no Sauce Labs - Mobile e Web Testing precisa ser estabelecido.

Para configurar e testar o logon único do Azure AD com o Sauce Labs - Mobile e Web Testing, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
3. **[Crie um usuário de teste do Sauce Labs - Mobile e Web Testing](#create-a-sauce-labs---mobile-and-web-testing-test-user)** - para ter um equivalente de Brenda Fernandes no Sauce Labs - Mobile e Web Testing vinculado à representação de usuário do Azure AD.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único no seu aplicativo Sauce Labs - Mobile e Web Testing.

**Para configurar o logon único do Azure AD com o Sauce Labs - Mobile e Web Testing, execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos **Sauce Labs - Mobile e Web Testing**, clique em **Single sign-on**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.

    ![Caixa de diálogo Logon único](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. Na seção **Sauce Labs - Domínio e URLs de Mobile e Web Testing**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Sauce Labs - Mobile e Web Testing Informações de logon único de domínio e URLs](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. Em uma janela diferente do navegador da Web, faça login no seu site da empresa Sauce Labs - Mobile e Web Testing como administrador.

7. Clique no **ícone do usuário** e selecione a guia **Gerenciamento de equipe**.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Digite seu **nome de domínio** na caixa de texto.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Clique na guia **configurar**.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. Na seção **Configurar Logon Único**, execute as etapas a seguir.

    ![Configurar o logon único](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

     a. Clique em **Procurar** e carregue o arquivo de metadados baixado do AD do Azure.

    b. Selecione a caixa de seleção **PERMITE O PROVISIONAMENTO JUST-IN-TIME**.

    c. Clique em **Salvar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

   ![Criar um usuário de teste do Azure AD][100]

**Para criar um usuário de teste no AD do Azure, execute as seguintes etapas:**

1. No portal do Azure, no painel esquerdo, clique no botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, acesse **Usuários e grupos** e, depois, clique em **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Para abrir a caixa de diálogo **Usuário**, clique em **Adicionar** na parte superior da caixa de diálogo **Todos os Usuários**.

    ![O botão Adicionar](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. Na caixa de diálogo **Usuário**, execute as seguintes etapas:

    ![A caixa de diálogo Usuário](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Clique em **Criar**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Criar um Sauce Labs - usuário de teste de dispositivos móveis e de teste da Web

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Sauce Labs - Mobile e Web Testing. O Sauce Labs - Mobile e Web Testing oferece suporte ao provisionamento just-in-time, que é ativado por padrão. Não há itens de ação para você nesta seção. Um novo usuário é criado durante uma tentativa de acessar o Sauce Labs - Mobile e Web Testing, se ele ainda não existir.
>[!Note]
>Se você precisar criar um usuário manualmente, entre em contato com a  [equipe de suporte do Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure, concedendo acesso ao Sauce Labs - Mobile e Web Testing.

![Atribuir a função de usuário][200]

**Para atribuir Britta Simon ao Sauce Labs - Mobile e Web Testing, execute as seguintes etapas:**

1. No Portal do Azure, abra a exibição de aplicativos e, em seguida, navegue até a exibição de diretório e vá para **Aplicativos Empresariais** e clique em **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de aplicativos, selecione **Sauce Labs - Mobile e Web Testing**.

    ![O link do Sauce Labs - Mobile e Web Testing na lista de aplicativos](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. No menu à esquerda, clique em **usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Clique no botão **Adicionar**. Em seguida, selecione **usuários e grupos** na **Adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar Atribuição][203]

5. Em **usuários e grupos** caixa de diálogo, selecione **Britta Simon** na lista de usuários.

6. Clique em **selecione** botão **usuários e grupos** caixa de diálogo.

7. Clique em **atribuir** botão **Adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Sauce Labs - Mobile e Web Testing no Painel de Acesso, você deve fazer logon automaticamente no seu aplicativo Sauce Labs - Mobile e Web Testing.
Para saber mais sobre o Painel de Acesso, confira [Introdução ao Painel de Acesso](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png

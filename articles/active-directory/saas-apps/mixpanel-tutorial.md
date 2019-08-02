---
title: 'Tutorial: Integração do Azure Active Directory ao Mixpanel | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Mixpanel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.openlocfilehash: ae93c9911034cf9cba1fdcf8c9472e4d3332bcad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096990"
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Tutorial: Integração do Active Directory do Azure ao Mixpanel

Neste tutorial, você aprenderá a integrar o Mixpanel ao Azure AD (Azure Active Directory).
A integração do Mixpanel ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao Mixpanel.
* Você pode permitir que os usuários sejam conectados automaticamente ao Mixpanel (Logon Único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Mixpanel, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Mixpanel habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Mixpanel dá suporte ao SSO iniciado por **SP**

## <a name="adding-mixpanel-from-the-gallery"></a>Adição do Mixpanel da galeria

Para configurar a integração do Mixpanel ao Azure AD, você precisará adicionar o Mixpanel à sua lista de aplicativos SaaS gerenciados a partir da galeria.

**Para adicionar o Mixpanel a partir da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Mixpanel**, selecione **Mixpanel** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Mixpanel na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Mixpanel, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Mixpanel.

Para configurar e testar o logon único do Azure AD com o Mixpanel, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o Logon Único do Mixpanel](#configure-mixpanel-single-sign-on)** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Mixpanel](#create-mixpanel-test-user)** – para ter um equivalente de Brenda Fernandes no Mixpanel que esteja vinculado à representação do usuário no Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Mixpanel, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Mixpanel**, clique em **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de domínio e URLs do Mixpanel](common/sp-signonurl.png)

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://mixpanel.com/login/`

    > [!NOTE]
    > Registre-se em [https://mixpanel.com/register/](https://mixpanel.com/register/) para configurar suas credenciais de logon e entre em contato com a [equipe de suporte do Mixpanel](mailto:support@mixpanel.com) para habilitar as configurações de SSO para seu locatário. Você também poderá obter o valor da URL de Entrada, se for necessário, da equipe de suporte do Mixpanel. 

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Mixpanel**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-mixpanel-single-sign-on"></a>Configurar o logon único do Mixpanel

1. Em uma janela diferente do navegador, faça logon no aplicativo Mixpanel como administrador.

2. Na parte inferior da página, clique no ícone de **engrenagem** no canto esquerdo. 
   
    ![Logon único do Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_06.png) 

3. Clique na guia **Segurança de acesso** e clique em **Alterar configurações**.
   
    ![Configurações do Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_08.png) 

4. Na página de diálogo **Alterar seu certificado**, clique em **Escolher arquivo** para carregar o certificado baixado e, em seguida, clique em **AVANÇAR**.
   
    ![Configurações do Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_09.png) 

5.  Na caixa de texto da URL de autenticação na página de diálogo **Alterar a URL de autenticação**, cole o valor da **URL de Logon** que você copiou do portal do Azure e, em seguida, clique em **AVANÇAR**.
   
    ![Configurações do Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_10.png) 

6. Clique em **Concluído**.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@dominiodaempresa.extensao**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure ao conceder acesso ao Mixpanel.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Mixpanel**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Mixpanel**.

    ![O link do Mixpanel na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-mixpanel-test-user"></a>Criar um usuário de teste do Mixpanel

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Mixpanel. 

1. Faça logon em seu site de empresa do Mixpanel como administrador.

2. Na parte inferior da página, clique no pequeno botão de engrenagem no canto esquerdo para abrir a janela **Configurações** .

3. Clique na guia **Equipe** .

4. Na caixa de texto **membro da equipe** , digite o endereço de email de Brenda no Azure.
   
    ![Configurações do Mixpanel](./media/mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Clique em **Convidar**. 

> [!Note]
> O usuário receberá um email para configurar o perfil.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Mixpanel no Painel de Acesso, você deverá ser conectado automaticamente ao Mixpanel para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


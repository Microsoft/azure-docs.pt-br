---
title: 'Tutorial: Integração do Azure Active Directory ao Asana | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Asana.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf1b583189810b61ea349a29698ff6f12c39699
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106739"
---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>Tutorial: Integração do Azure Active Directory ao Asana

Neste tutorial, você aprenderá a integrar o Asana ao Azure AD (Azure Active Directory).
A integração do Asana ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao Asana.
* Você pode permitir que seus usuários entrem automaticamente no Asana (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Asana, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do Asana habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Asana dá suporte ao SSO iniciado por **SP**

* O Asana dá suporte ao [provisionamento **automatizado** de usuários](asana-provisioning-tutorial.md)

## <a name="adding-asana-from-the-gallery"></a>Adicionando Asana da galeria

Para configurar a integração do Asana ao Azure AD, você precisará adicionar o Asana da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Asana da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Asana**, selecione **Asana** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Asana na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configura e testa o logon único do Azure AD com o Asana, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Asana.

Para configurar e testar o logon único do Azure AD com o Asana, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Asana](#configure-asana-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Asana](#create-asana-test-user)** – para ter um equivalente de Brenda Fernandes no Asana que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Asana, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Asana**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de logon único de Domínio e URLs do Asana](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite a URL: `https://app.asana.com/`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite a URL: `https://app.asana.com/`

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o Asana**, copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-asana-single-sign-on"></a>Configurar logon único do Asana

1. Em uma janela de navegador diferente, entre em seu aplicativo Asana. Para configurar o SSO no Asana, acesse as configurações de workspace clicando no nome do workspace no canto superior direito da tela. Em seguida, clique em **\<nome do workspace\>Configurações**.

    ![configurações de sso do Asana](./media/asana-tutorial/tutorial_asana_09.png)

2. Na janela **Configurações da organização**, clique em **Administração**. Em seguida, clique em **Os membros devem fazer logon por meio de SAML** para habilitar a configuração de SSO. Execute as seguintes etapas:

    ![Definições de Configurar Organização de Logon Único](./media/asana-tutorial/tutorial_asana_10.png)  

    a. Na caixa de texto **URL da página de entrada**, cole a **URL de logon**.

    b. Clique com o botão direito do mouse no certificado baixado no portal do Azure e, em seguida, abra o arquivo de certificado usando o Bloco de notas ou seu editor de texto preferido. Copie o conteúdo entre o início e o final do título do certificado e cole-o na caixa de texto **Certificado X.509**.

3. Clique em **Save** (Salvar). Vá para [Guia do Asana para configurar SSO](https://asana.com/guide/help/premium/authentication#gl-saml) se precisar de assistência adicional.

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

Nesta seção, você permite que Brenda Fernandes use o logon único do Azure concedendo acesso ao Asana.

1. No portal do Azure, escolha **Aplicativos empresariais**, escolha **Todos os aplicativos** e, em seguida, escolha **Asana**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escolha **Asana**.

    ![O link do Asana na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-asana-test-user"></a>Criar um usuário de teste do Asana

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Asana. O Asana dá suporte ao provisionamento automático de usuário, que é habilitado por padrão. Você pode encontrar [aqui](asana-provisioning-tutorial.md) mais detalhes de como configurar o provisionamento automático de usuário.

**Se você precisar criar um usuário manualmente, siga estas etapas:**

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Asana.

1. No **Asana**, vá para a seção **Equipes** no painel esquerdo. Clique no botão de sinal de adição.

    ![Criação de um usuário de teste do AD do Azure](./media/asana-tutorial/tutorial_asana_12.png)

2. Digite o email do usuário como **brenda.fernandes\@contoso.com** na caixa de texto e, em seguida, selecione **Convidar**.

3. Clique em **Enviar Convite**. O novo usuário receberá um email em sua conta de email. Ele precisará criar e validar a conta.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Asana no Painel de Acesso, você deverá ser conectado automaticamente ao Asana no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Configurar Provisionamento de Usuário](asana-provisioning-tutorial.md)

---
title: 'Tutorial: Integração do Azure Active Directory com o Keeper Password Manager & Digital Vault | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Gerenciador de Senhas Protetor e Cofre Digital.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e1a98f6a-2dae-4734-bdbf-4fba742a61d2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d12c31e098765209d80c7b5f400333fc11e8bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098870"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Tutorial: Integração do Azure Active Directory com o Keeper Password Manager & Digital Vault

Neste tutorial, você aprenderá a integrar o Gerenciador de Senhas Protetor e o Cofre Digital ao Azure Active Directory (Azure AD).
Integrar do Gerenciador de Senhas Protetor e do Cofre Digital ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem tem acesso ao Keeper Password Manager & Digital Vault.
* Você pode habilitar os usuários a serem conectados automaticamente com o Keeper Password Manager & Digital Vault (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao Gerenciador de Senhas Protetor e Cofre Digital, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Uma assinatura habilitada para logon único do Keeper Password Manager & Digital Vault

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Keeper Password Manager & Digital Vault é compatível com o SSO iniciado por **SP**

* O Keeper Password Manager & Digital Vault é compatível com o provisionamento do usuário **Just-In-Time**

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionando Gerenciador de Senhas Protetor e Cofre Digital da galeria

Para configurar a integração do Gerenciador de Senhas Protetor e Cofre Digital no Azure AD, você precisa adicionar o Gerenciador de Senhas Protetor e Cofre Digital da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Gerenciador de Senhas Protetor e Cofre Digital da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Keeper Password Manager & Digital Vault**, selecione **Keeper Password Manager & Digital Vault** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

     ![Keeper Password Manager & Digital Vault na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você pode configurar e testar o logon único do Azure AD com o Keeper Password Manager & Digital Vault com base em um usuário de teste chamado **Brenda Fernandes**.
Para o logon único funcionar, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Keeper Password Manager & Digital Vault.

Para configurar e testar o logon único do Azure AD com o Gerenciador de Senhas Protetor e Cofre Digital, você precisa concluir os blocos de construção a seguir:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **Configurar o Logon Único do Keeper Password Manager & Digital Vault** – para definir as configurações de Logon Único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)** – para ter um equivalente de Brenda Fernandes no Keeper Password Manager & Digital Vault vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Keeper Password Manager & Digital Vault, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **Keeper Password Manager & Digital Vault**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de Domínio e URLs para logon único do Keeper Password Manager & Digital Vault](common/sp-identifier-reply.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://{SSO CONNECT SERVER}/sso-connect/saml/login`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://{SSO CONNECT SERVER}/sso-connect`

    c. Na caixa de texto **URL de resposta**, digite uma URL no seguinte padrão: `https://{SSO CONNECT SERVER}/sso-connect/saml/sso`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de suporte do Gerenciador de Senhas Protetor e Cliente do Cofre Digital](https://keepersecurity.com/contact.html) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com suas necessidades e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Keeper Password Manager & Digital Vault**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure Ad

    c. URL de logoff

### <a name="configure-keeper-password-manager--digital-vault-single-sign-on"></a>Configurar o logon único do Keeper Password Manager & Digital Vault

Para configurar o logon único no lado da **Configuração do Gerenciador de Senhas Protetor e Cofre Digital**, siga as orientações fornecidas no [Guia de Suporte do Protetor](https://keepersecurity.com/assets/pdf/KeeperSSOConnect_v11.pdf).

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

Nesta seção, você habilita Brenda Fernandes a usar logon único do Azure concedendo acesso ao Gerenciador de Senhas Protetor e Cofre Digital.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e **Keeper Password Manager & Digital Vault**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Gerenciador de Senhas Protetor e Cofre Digital**.

    ![O link do Keeper Password Manager & Digital Vault na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Criar um usuário de teste do Keeper Password Manager & Digital Vault

Para permitir que os usuários do Azure AD fazer logon no Gerenciador de Senhas Protetor e Cofre Digital, eles devem ser provisionados no Gerenciador de Senhas Protetor e Cofre Digital. O aplicativo dá suporte ao provisionamento de usuário just-in-time e, após a autenticação, os usuários serão automaticamente criados no aplicativo. Você poderá contatar [Suporte Protetor](https://keepersecurity.com/contact.html) se quiser configurar usuários manualmente.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando clicar no bloco do Keeper Password Manager & Digital Vault no Painel de Acesso, você deverá se conectar automaticamente ao Keeper Password Manager & Digital Vault para o qual o SSO foi configurado. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


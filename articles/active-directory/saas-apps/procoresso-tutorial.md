---
title: 'Tutorial: Integração do Azure Active Directory com o Procore SSO | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093672"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Integração do Azure Active Directory com o Procore SSO

Neste tutorial, você aprenderá a integrar o Procore SSO ao Azure AD (Azure Active Directory).
A integração do Procore SSO com o Azure AD oferece os seguintes benefícios:

* É possível controlar, no Azure AD, quem tem acesso ao Procore SSO.
* Você pode permitir que os usuários sejam conectados automaticamente ao Procore SSO (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Procore SSO, são necessários os seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do Procore SSO

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Procore SSO dá suporte ao SSO iniciado por **IDP**

## <a name="adding-procore-sso-from-the-gallery"></a>Adicionando o Procore SSO da galeria

Para configurar a integração do Procore SSO com o Azure AD, é necessário adicionar o Procore SSO da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Procore SSO da galeria, siga as etapas abaixo:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Procore SSO**, selecione **Procore SSO** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Procore SSO na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Procore SSO, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Procore SSO.

Para configurar e testar o logon único do Azure AD com o Procore SSO, é necessário concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do Procore SSO](#configure-procore-sso-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do Procore SSO](#create-procore-sso-test-user)** – para ter um equivalente de Brenda Fernandes no Procore SSO que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o Procore SSO, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Procore SSO**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

    ![Informações do domínio Procore SSO Domain e URLs single sign-on](common/preintegrated.png)

5. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Procore SSO**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-procore-sso-single-sign-on"></a>Configurar o logon único do Procore SSO

1. Para configurar o logon único no lado do **Procore SSO**, entre em seu site de empresa do Procore como administrador.

2. Na lista suspensa da caixa de ferramentas, clique em **Admin** para abrir a página de configurações de SSO.

    ![Configurar o logon único](./media/procoresso-tutorial/procore_tool_admin.png)

3. Cole os valores nas caixas conforme descrito abaixo-

    ![Configurar o logon único](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Na caixa de texto **Single Sign On Issuer URL**, cole o valor de **Identificador do Microsoft Azure Active Directory** que você copiou do portal do Azure.

    b. Na caixa **URL de destino do logon SAML**, cole o valor do **URL de login** que você copiou do portal do Microsoft Azure.

    c. Agora, abra o **XML de metadados de federação** baixado acima do portal do Azure e copie o certificado na tag denominada **X509Certificate**. Cole o valor copiado para a caixa **Certificado x509 de logon único**.

4. Clique em **Salvar alterações**.

5. Após essas configurações, é necessário enviar o **nome de domínio** (por exemplo, **contoso.com**) por meio do qual você está fazendo logon no Procore para a [equipe de suporte Procore](https://support.procore.com/) e eles ativarão o SSO federado para esse domínio.

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite `brittasimon@yourcompanydomain.extension`. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que o Brenda Fernandes use o logon único do Azure, concedendo acesso ao Procore SSO.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **Procore SSO**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Procore SSO**.

    ![O link do Procore SSO na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-procore-sso-test-user"></a>Criar um usuário de teste do Procore SSO

Siga as etapas abaixo para criar um usuário de teste do Procore no lado do Procore SSO.

1. Entre em seu site de empresa do Procore como administrador.    

2. Na lista suspensa da caixa de ferramentas, clique em **Diretório** para abrir a página do diretório da empresa.

    ![Configurar o logon único](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Clique na opção **Adicionar uma pessoa** para abrir o formulário e insira as seguintes opções –

    ![Configurar o logon único](./media/procoresso-tutorial/Procore_user_add.png)

    a. Na caixa de texto **Nome**, digite o nome do usuário como **Brenda**.

    b. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    c. Na caixa de texto **Endereço de Email**, digite o endereço de email do usuário como BrittaSimon@contoso.com.

    d. Selecione **Modelo de permissão** como **Aplicar o modelo de permissão mais tarde**.

    e. Clique em **Criar**.

4. Verifique e atualize os detalhes do contato recém-adicionado.

    ![Configurar o logon único](./media/procoresso-tutorial/Procore_user_check.png)

5. Clique em **Salvar e Enviar Convite** (se for necessário enviar um convite por email) ou em **Salvar** (Salvar diretamente) para concluir o registro do usuário.
    
    ![Configurar o logon único](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Procore SSO no Painel de Acesso, você deverá ser conectado automaticamente ao Procore SSO, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


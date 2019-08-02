---
title: 'Tutorial: Integração do Microsoft Azure Active Directory ao dmarcian | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 232742c9ca3f090e2900566c1e80f9720e284135
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67104050"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Tutorial: Integração do Microsoft Azure Active Directory ao dmarcian

Neste tutorial, você aprenderá a integrar o dmarcian ao Azure AD (Azure Active Directory).
A integração do dmarcian ao Azure AD oferece os seguintes benefícios:

* Você pode controlar no Azure AD quem terá acesso ao dmarcian.
* Você pode permitir que seus usuários entrem automaticamente no dmarcian (Logon Único) usando suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao dmarcian, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, você pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura do dmarcian habilitada para logon único

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O dmarcian dá suporte ao SSO iniciado por **SP** e **IDP**

## <a name="adding-dmarcian-from-the-gallery"></a>Como adicionar dmarcian da galeria

Para configurar a integração do dmarcian ao Azure AD, você precisará adicionar o dmarcian da galeria à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o dmarcian da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **dmarcian**, selecione **dmarcian** no painel de resultados e, depois, clique no botão **Adicionar** para adicionar o aplicativo.

     ![dmarcian na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o dmarcian, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do dmarcian.

Para configurar e testar o logon único do Azure AD com o dmarcian, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do dmarcian](#configure-dmarcian-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do dmarcian](#create-dmarcian-test-user)** – para ter um equivalente de Brenda Fernandes no dmarcian vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o dmarcian, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração do aplicativo **dmarcian**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica do SAML**, execute as etapas a seguir caso deseje configurar o aplicativo no modo **IDP** iniciado:

    ![Informações de logon único de Domínio e URLs do dmarcian](common/idp-intiated.png)

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

5. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    ![Informações de logon único de Domínio e URLs do dmarcian](common/metadata-upload-additional-signon.png)

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:
    
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Esses valores não são reais. Você atualizará esses valores com o identificador, URL de resposta e URL de logon reais, explicado mais adiante no tutorial. 

6. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique no botão copiar para copiar **URL de metadados de federação de aplicativos** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

### <a name="configure-dmarcian-single-sign-on"></a>Configurar o logon único do dmarcian

1. Em uma janela de navegador da Web diferente, entre no dmarcian como um administrador de segurança.

2. Clique em **Perfil** no canto superior direito canto e navegue até **Preferências**.

    ![As preferências](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

3. Role para baixo e clique na seção **Logon Único** e, em seguida, clique em **Configurar**.

    ![Único](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

4. Na página **Logon Único de SAML**, defina o **Status** como **Habilitado** e execute as seguintes etapas:

    ![A de autenticação](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * Na seção **Adicionar dmarcian ao seu provedor de identidade**, clique em **Copiar** para copiar a **URL de serviço do consumidor de declaração** para sua instância e colá-lo na caixa de texto **URL de Resposta** na **seção de Configuração SAML Básica** no portal do Azure.

    * Na seção **Adicionar dmarcian ao seu Provedor de Identidade**, clique em **COPY** para copiar a **ID da Entidade** para sua instância e colá-lo na caixa de texto **Identificador** na **seção de Configuração SAML Básica** no portal do Azure.

    * Na seção **Configurar Autenticação**, a caixa de texto **Metadados do Provedor de Identidade** cola a **Url de Metadados de Federação do Aplicativo**, que você copiou do portal do Azure.

    * Na seção **Configurar a Autenticação**, na caixa de texto **Declarações de Atributo**, cole a URL `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * Na seção **Configurar URL de Logon**, copie a **URL de Logon** para sua instância e cole-a na caixa de texto **URL de Logon** na **seção de Configuração SAML Básica** no portal do Azure.

        > [!Note]
        > Você pode modificar a **URL de Logon** de acordo com a sua organização.

    * Clique em **Save** (Salvar).

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite **brendafernandes\@domíniodaempresa.extensão**  
    Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo acesso ao dmarcian.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **dmarcian**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **dmarcian**.

    ![O link dmarcian na lista de Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-dmarcian-test-user"></a>Criar um usuário de teste do dmarcian

Para permitir que os usuários do Azure AD entrem no dmarcian, eles devem ser provisionados no dmarcian. No dmarcian, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no dmarcian como um Administrador de Segurança.

2. Clique em **Perfil** no canto superior direito e navegue até **Gerenciar Usuários**.

    ![O usuário](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. No lado direito da seção **Usuários de SSO**, clique em **Adicionar Novo Usuário**.

    ![A ação de adicionar usuário](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Na janela pop-up **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![O novo usuário](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. Na caixa de texto **Email do novo usuário**, insira o email do usuário como **brendafernandes\@contoso.com**.

    b. Se você quiser conceder direitos de administrador ao usuário, selecione **Tornar o Usuário um Administrador**.

    c. Clique em **Adicionar Usuário**.

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do dmarcian no Painel de Acesso, você deve ser conectado automaticamente ao dmarcian para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o Acesso Condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


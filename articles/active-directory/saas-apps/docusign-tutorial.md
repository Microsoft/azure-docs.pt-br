---
title: 'Tutorial: integração do Azure Active Directory com o DocuSign | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 5c7d6116ed2925e57f094a67f27a11f9e2d61831
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499252"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: integração do Active Directory do Azure com o DocuSign

Neste tutorial, você aprende a integrar o DocuSign ao Azure AD (Azure Active Directory).
A integração do DocuSign ao Azure AD oferece os seguintes benefícios:

* No Azure AD, é possível controlar quem tem acesso ao DocuSign.
* Você pode permitir que os usuários sejam conectados automaticamente ao DocuSign (logon único) com suas contas do Azure AD.
* Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao AD do Azure, consulte [O que é o acesso a aplicativos e logon único com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD ao DocuSign, você precisa dos seguintes itens:

* Uma assinatura do Azure AD. Se não tiver um ambiente do Azure AD, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura habilitada para logon único do DocuSign

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O DocuSign dá suporte ao SSO iniciado por **SP**

* O DocuSign dá suporte a [provisionamento automático de usuários](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Adicionando o DocuSign por meio da galeria

Para configurar a integração do DocuSign ao Azure AD, é necessário adicionar o DocuSign à lista de aplicativos SaaS gerenciados por meio da galeria.

**Para adicionar o DocuSign por meio da galeria, realize as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)** , no painel navegação à esquerda, clique no ícone **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Navegue até **Aplicativos Empresariais** e, em seguida, selecione a opção **Todos os Aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **DocuSign**, selecione **DocuSign** no painel de resultados e, depois, clique no botão **Adicionar** para incorporar o aplicativo.

     ![DocuSign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o DocuSign, com base em um usuário de teste chamado **Brenda Fernandes**.
Para que o logon único funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do DocuSign.

Para configurar e testar o logon único do Azure AD com o DocuSign, você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Configurar o logon único do DocuSign](#configure-docusign-single-sign-on)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** – para testar o logon único do Azure AD com Brenda Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que Brenda Fernandes use o logon único do Azure AD.
5. **[Criar um usuário de teste do DocuSign](#create-docusign-test-user)** – para ter um equivalente de Brenda Fernandes no DocuSign que esteja vinculado à representação de usuário do Azure AD.
6. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure.

Para configurar o logon único do Azure AD com o DocuSign, execute as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **DocuSign**, selecione **Logon único**.

    ![Link Configurar logon único](common/select-sso.png)

2. Na caixa de diálogo **Selecionar um método de logon único**, selecione o modo **SAML/WS-Fed** para habilitar o logon único.

    ![Modo de seleção de logon único](common/select-saml-option.png)

3. Na página **Definir logon único com SAML**, clique no ícone **Editar** para abrir a caixa de diálogo **Configuração básica do SAML**.

    ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações sobre logon único de domínio e URLs do DocuSign](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Logon e o Identificador reais, que são explicados em seguida na seção **Exibir pontos de extremidade do SAML 2.0** do tutorial.

5. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, clique em **Fazer o download** para fazer o download do **Certificado (Base64)** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

6. Na seção **Configurar o DocuSign**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="configure-docusign-single-sign-on"></a>Configurar o logon único do DocuSign

1. Em outra janela do navegador da Web, entre no **portal de administração do DocuSign** como administrador.

2. No canto superior direito da página, clique no **logotipo** do perfil e, em seguida, em **Ir para Administrador**.
  
    ![Configurando o logon único][51]

3. Na página de soluções do domínio, clique em **Domínios**

    ![Configurando o logon único][50]

4. Na seção **Domínios**, clique em **DECLARAR DOMÍNIO**.

    ![Configurando o logon único][52]

5. Na caixa de diálogo **Declarar um domínio**, na caixa de texto **Nome do Domínio**, digite o domínio de sua empresa e clique em **DECLARAR**. Verifique o domínio e confira se o status está ativo.

    ![Configurando o logon único][53]

6. Na página de soluções do domínio, clique em **Provedores de Identidade**.
  
    ![Configurando o logon único][54]

7. Na seção **Provedores de Identidade**, clique em **ADICIONAR PROVEDOR DE IDENTIDADE**. 

    ![Configurando o logon único][55]

8. Na página **Configurações do Provedor de Identidade** , execute as seguintes etapas:

    ![Configurando o logon único][56]

    a. Na caixa de texto **Nome** , digite um nome exclusivo para sua configuração. Não use espaços.

    b. Na caixa de texto **Emissor de provedor de identidade**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    c. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **URL de Logout do Provedor de Identidade**, cole o valor de **URL de Logout** que você copiou do portal do Azure.

    e. Selecione **Assinar solicitação AuthN**.

    f. Para **Enviar solicitação AuthN por**, selecione **POST**.

    g. Em **Enviar solicitação de logoff por**, selecione **GET**.

    h. Na seção **Mapeamento de atributo personalizado**, clique em **ADICIONAR NOVO MAPEAMENTO**.

    ![Configurando o logon único][62]

    i. Escolha o campo a mapear com a declaração do Azure AD. Neste exemplo, a declaração **emailaddress** é mapeada com o valor de **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Esse é o nome de declaração padrão do Azure AD para a declaração de email; depois clique em **SALVAR**.

    ![Configurando o logon único][57]

    > [!NOTE]
    > Use o **Identificador de usuário** apropriado para mapear o usuário do Azure AD para o mapeamento de usuário do DocuSign. Selecione o campo adequado e insira o valor apropriado com base nas configurações de sua organização.

    j. Na seção **Certificado do Provedor de Identidade**, clique em **ADICIONAR CERTIFICADO** e, depois, carregue o certificado baixado do portal do Azure AD e clique em **SALVAR**.

    ![Configurando o logon único][58]

    k. Na seção **Provedores de Identidade**, clique em **AÇÕES** e em **Pontos de Extremidade**.

    ![Configurando o logon único][59]

    l. Na seção **Exibir Pontos de Extremidade do SAML 2.0** do **portal de administração do DocuSign**, realize as seguintes etapas:

    ![Configurando o logon único][60]

    * Copie a **URL do Emissor de Provedor de Serviços** e, em seguida, cole-a na caixa de texto **Identificador** da seção **Configuração Básica do SAML** no portal do Azure.

    * Copie a **URL de Logon do Provedor de Serviços** e, em seguida, cole-a na caixa de texto **URL de Logon** da seção **Configuração Básica do SAML** no portal do Azure.

    * Clique em **Fechar**

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD 

O objetivo desta seção é criar um usuário de teste no Portal do Azure chamado Brenda Fernandes.

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**, selecione **Usuários** e, em seguida, **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](common/users.png)

2. Selecione **Novo usuário** na parte superior da tela.

    ![Botão Novo usuário](common/new-user.png)

3. Nas Propriedades do usuário, execute as etapas a seguir.

    ![A caixa de diálogo Usuário](common/user-properties.png)

    a. No campo **Nome**, insira **BrendaFernandes**.
  
    b. No campo **Nome de usuário**, digite brittasimon@yourcompanydomain.extension. Por exemplo, BrittaSimon@contoso.com

    c. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa Senha.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permite que Clara Barbosa use o logon único do Azure, concedendo acesso ao DocuSign.

1. No portal do Azure, selecione **Aplicativos Empresariais**, **Todos os aplicativos** e, em seguida, **DocuSign**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **DocuSign**.

    ![O link do DocuSign na lista Aplicativos](common/all-applications.png)

3. No menu à esquerda, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

4. Escolha o botão **Adicionar usuário** e, em seguida, escolha **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O painel Adicionar Atribuição](common/add-assign-user.png)

5. Na caixa de diálogo **Usuários e grupos**, escolha **Brenda Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.

6. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar função**, escolha a função de usuário apropriada na lista e clique no botão **Selecionar** na parte inferior da tela.

7. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-docusign-test-user"></a>Criar um usuário de teste do DocuSign

Nesta seção, um usuário chamado Brenda Fernandes será criado no DocuSign. O DocuSign dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no DocuSign, um novo será criado após a autenticação.

>[!Note]
>Caso precise criar um usuário manualmente, contate a [equipe de suporte do DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Testar logon único 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do DocuSign no Painel de Acesso, você deverá ser conectado automaticamente ao DocuSign, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png

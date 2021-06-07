---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SD Elements | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o SD Elements.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: a9bcda4affa19cf8793cd078fdc5b96d842eb42b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92893567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao SD Elements

Neste tutorial, você aprenderá a integrar o SD Elements ao Azure AD (Azure Active Directory). Ao integrar o SD Elements ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao SD Elements.
* Permitir que os usuários sejam conectados automaticamente ao SD Elements com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SD Elements.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SD Elements é compatível com o SSO iniciado por **IDP**

## <a name="adding-sd-elements-from-the-gallery"></a>Adicionando elementos SD da galeria

Para configurar a integração de SD Elements ao Azure AD, você precisa adicionar SD Elements da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SD Elements** na caixa de pesquisa.
1. Selecione **SD Elements** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Configurar e testar o logon único do Azure AD para o SD Elements

Configure e teste o SSO do Azure AD com o SD Elements usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado no SD Elements.

Para configurar e testar o SSO do Azure AD com o SD Elements, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do SD Elements](#configure-sd-elements-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do SD Elements](#create-sd-elements-test-user)** – para ter um equivalente de B.Fernandes no SD Elements que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SD Elements**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do SD Elements](mailto:support@sdelements.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo SD Elements espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo SD Elements espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome |  Atributo de Origem|
    | --- | --- |
    | email |user.mail |
    | nome |user.givenname |
    | sobrenome |user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o SD Elements**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao SD Elements.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **SD Elements**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-sd-elements-sso"></a>Configurar o SSO do SD Elements

1. Para habilitar logon único, entre em contato com a [equipe de suporte do SD Elements](mailto:support@sdelements.com) e forneça a ela o arquivo de certificado baixado.

1. Em outra janela do navegador, faça logon no locatário do SD Elements como administrador.

1. No menu na parte superior, clique em **Sistema** e, depois, em **Logon Único**.

    ![Captura de tela que mostra a opção "Sistema" selecionada e a opção "Logon único" selecionada na lista suspensa.](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Na caixa de diálogo **Configurações de Logon Único** , execute as seguintes etapas:

    ![Configurar o logon único](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Como **Tipo de SSO**, selecione **SAML**.

    b. Na caixa de texto **ID da Entidade do Provedor de Identidade**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure.

    c. Na caixa de texto **Serviço de Logon Único do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    d. Clique em **Save** (Salvar).

### <a name="create-sd-elements-test-user"></a>Criar um usuário de teste do SD Elements

O objetivo desta seção é criar um usuário com o nome B.Fernandes no SD Elements. No caso de SD Elements, criar usuários do SD Elements é uma tarefa manual.

**Para criar B.Fernandes no SD Elements, execute as seguintes etapas:**

1. Em uma janela de navegador da web, faça logon no site SD Elements da sua empresa como administrador.

1. No menu na parte superior, clique em **Gerenciamento de Usuários** e, depois, em **Usuários**.

    ![Captura de tela que mostra a opção "Usuários" selecionada na lista suspensa "Gerenciamento de Usuários".](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Clique em **Adicionar Novo Usuário**.

    ![Uma captura de tela que mostra o botão "Adicionar Novo Usuário" selecionado.](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Na caixa de diálogo **Adicionar Novo Usuário**, realize as seguintes etapas:

    ![Criar um usuário de teste de elementos de SD](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Na caixa de texto **Email**, insira o email do usuário, como **b.simon@contoso.com** .

    b. Na caixa de texto **Nome**, digite o nome do usuário, como **B.** .

    c. Na caixa de texto **Sobrenome**, digite o sobrenome do usuário como **Fernandes**.

    d. Como **Função**, selecione **Usuário**.

    e. Clique em **Criar Usuário**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do SD Elements no Painel de Acesso, você será conectado automaticamente ao aplicativo SD Elements para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o SD Elements com o Azure AD](https://aad.portal.azure.com/)
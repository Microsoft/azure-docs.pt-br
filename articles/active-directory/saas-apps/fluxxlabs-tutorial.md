---
title: 'Tutorial: Integração do Azure Active Directory ao Fluxx Labs | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure Active Directory e o Fluxx Labs.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: d201f80aea2c22e1bee06c776d2a84fb69f2bd7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92452573"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Fluxx Labs

Neste tutorial, você aprenderá a integrar o Fluxx Labs ao Azure AD (Azure Active Directory). Ao integrar o Fluxx Labs ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Fluxx Labs.
* Permitir que os usuários sejam conectados automaticamente ao Fluxx Labs com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Fluxx Labs.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Fluxx Labs dá suporte ao SSO iniciado por **IdP**
* Após configurar o Fluxx Labs, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fluxx-labs-from-the-gallery"></a>Adição do Fluxx Labs da galeria

Para configurar a integração do Fluxx Labs ao Microsoft Azure Active Directory, você precisará adicionar o Fluxx Labs da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Fluxx Labs** na caixa de pesquisa.
1. Escolha **Fluxx Labs** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-fluxx-labs"></a>Configurar e testar logon único do Azure AD para o Fluxx Labs

Configure e teste o SSO do Azure AD com o Fluxx Labs usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Fluxx Labs.

Para configurar e testar o SSO do Azure AD com o Fluxx Labs, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Fluxx Labs](#configure-fluxx-labs-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Fluxx Labs](#create-fluxx-labs-test-user)** – para ter um equivalente de B.Fernandes no Fluxx Labs que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Fluxx Labs**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar Logon Único com SAML**, execute as seguintes etapas:

    a. Na caixa de texto **Identificador**, digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão:

    | Ambiente | Padrão de URL|
    |-------------|------------|
    | Produção | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pré-produção | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Fluxx Labs](https://fluxx.zendesk.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Fluxx Labs**, copie as URLs apropriadas de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Fluxx Labs.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Fluxx Labs**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-fluxx-labs-sso"></a>Configurar SSO do Fluxx Labs

1. Em outra janela do navegador da Web, entre em seu site de empresa do Fluxx Labs como administrador.

2. Selecione **Administrador** abaixo da seção **Configurações**.

    ![Captura de tela que mostra a seção "Configurações" com "Administrador" selecionado.](./media/fluxxlabs-tutorial/config1.png)

3. No Painel de Administração, selecione **Plug-ins** > **Integrações** e, em seguida, selecione **SSO de SAML (Desabilitado)**

    ![Captura de tela que mostra a guia "Integrações" com "SSO SAML (Desabilitado)" selecionado.](./media/fluxxlabs-tutorial/config2.png)

4. Na seção de atributos, realize as seguintes etapas:

    ![Captura de tela que mostra a seção "Atributos" com "SSO SAML" marcado, valores inseridos em campos e o botão "Salvar" selecionado.](./media/fluxxlabs-tutorial/config3.png)

    a. Marque a caixa de seleção **SSO do SAML**.

    b. Na caixa de texto **Caminho de solicitação**, digite **/auth/saml**.

    c. Na caixa de texto **Caminho de retorno de chamada**, digite **/auth/saml/callback**.

    d. Na caixa de texto **URL do Serviço do Consumidor de Declaração (URL de Logon Único)** , insira o valor da **URL de Resposta** que você inseriu no portal do Azure.

    e. Na caixa de texto **Público (ID da Entidade SP)** , insira o valor de **Identificador**, que você inseriu no portal do Azure.

    f. Na caixa de texto **URL de Destino de SSO do Provedor de Identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.

    g. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade**.

    h. Na caixa de texto **Formato do identificador de nome**, insira o valor `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    i. Clique em **Save** (Salvar).

    > [!NOTE]
    > Depois que o conteúdo for salvado, o campo aparecerá em branco por questões de segurança, mas o valor foi salvo na configuração.

### <a name="create-fluxx-labs-test-user"></a>Criar um usuário de teste do Fluxx Labs

Para permitir que os usuários do Azure AD entrem no Fluxx Labs, eles precisam ser provisionados no Fluxx Labs. No caso do Fluxx Labs, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre em seu site de empresa do Fluxx Labs como administrador.

2. Clique no **ícone** exibido abaixo.

    ![Captura de tela que mostra as opções do administrador com o ícone "Mais" selecionado em "Seu Painel Está Vazio".](./media/fluxxlabs-tutorial/config6.png)

3. No painel, clique no ícone exibido abaixo para abrir o cartão **Novas PESSOAS**.

    ![Captura de tela que mostra o menu "Gerenciamento de Contatos" com o ícone "Mais" ao lado de "Pessoas" selecionado.](./media/fluxxlabs-tutorial/config4.png)

4. Na seção **NOVAS PESSOAS**, execute as etapas a seguir:

    ![Configuração do Fluxx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. O Fluxx Labs usa email como identificador exclusivo para logons únicos. Preencha o campo **UID do SSO** com o endereço de email do usuário, que corresponde ao endereço de email, que estão usando como logon com o SSO.

    b. Clique em **Save** (Salvar).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Fluxx Labs no Painel de Acesso, você deverá ser conectado automaticamente ao Fluxx Labs, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Fluxx Labs com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Fluxx Labs com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
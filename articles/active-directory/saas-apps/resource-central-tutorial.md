---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Resource Central | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Resource Central.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Resource Central

Neste tutorial, você aprenderá a integrar o Resource Central ao Azure AD (Azure Active Directory). Quando você integra o Resource Central ao Azure AD, você pode:

* Controlar, no Azure AD, quem tem acesso ao Resource Central.
* Permitir que os usuários sejam conectados automaticamente ao Resource Central com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Resource Central habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Resource Central é compatível com SSO iniciado por **SP**

* O Resource Central é compatível com o provisionamento de usuário **just-in-time**

## <a name="add-resource-central-from-the-gallery"></a>Adicionar o Resource Central por meio da galeria

Para configurar a integração do Resource Central ao Azure AD, você precisa adicionar o Resource Central da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, na caixa de pesquisa, insira **Resource Central**.
1. Selecione **Resource Central** do painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Configurar e testar o SSO do Azure AD para o Resource Central

Configure e teste o SSO do Azure AD com o Resource Central usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Resource Central.

Para configurar e testar o SSO do Azure AD com o Resource Central, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
    1. **[Criar usuário de teste do Resource Central](#create-resource-central-test-user)** – para que haja um equivalente de B.Fernandes no Resource Central vinculado à representação do usuário no Azure AD.
1. **[Configurar o SSO do Resource Central](#configure-resource-central-sso)** – para configurar o logon único no lado do aplicativo.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Resource Central**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Em **Configuração Básica do SAML**, insira os valores para os seguintes campos:

   1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<DOMAIN_NAME>/ResourceCentral`

   1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<DOMAIN_NAME>/ResourceCentral`

   1. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Esses valores não são valores literais. Atualize esses valores com os valores reais da URL de Logon, do Identificador e da URL de Resposta. Entre em contato com a [equipe de suporte do Resource Center](mailto:st@aod.vn) para obter esses valores.  Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, em **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Em **Configurar o Resource Central**, copie as URLs apropriadas de acordo com as suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira `username@companydomain.extension`. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Resource Central.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Resource Central**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e **Usuários e grupos** no painel **Adicionar Atribuição**.
1. No painel **Usuários e grupos**, selecione **B.Fernandes** na lista **Usuários** e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a em **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função **Acesso Padrão** selecionada.
1. No painel **Adicionar Atribuição**, clique no botão **Atribuir**.

### <a name="create-resource-central-test-user"></a>Criar usuário de teste do Resource Central

Nesta seção, um usuário chamado **B.Fernandes** será criado no **Resource Central**.

1. No Resource Central, selecione **Segurança** > **Pessoas** > **Novo**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Captura de tela que mostra o painel Pessoas no Resource Central, com o botão Novo realçado.":::

1. Em **Detalhes da Pessoa**, como **Nome de exibição**, insira o usuário **B.Fernandes**. Em **Endereço SMTP**, insira o nome de usuário do Azure AD do usuário. Por exemplo, `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Captura de tela que mostra o painel Detalhes da Pessoa no Resource Central.":::

## <a name="configure-resource-central-sso"></a>Configurar o SSO do Resource Central

Nesta seção, você vai configurar o logon único no **Administrador do Sistema do Resource Central**.

1. Em Administrador do Sistema do Resource Central, selecione **Autenticação Externa**.
1.  Em **Habilitar Configuração**, selecione **Sim**.

    ![Captura de tela que mostra a opção Habilitar Configuração selecionada no painel Autenticação Externa no Resource Central.](./media/resource-central/enable.png)

1. Em **Protocolo de Autenticação**, selecione **SAML2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Captura de tela que mostra o SAML2 selecionado em Protocolo de Autenticação no Resource Central.":::

1. Em **Configuração do SAML2**, insira os valores para os seguintes campos:

    1. Em **Identificador (ID da Entidade)** , **URL de Logon**, **URL de Logoff** e **Identificador do Azure AD**, insira as URLs relevantes:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Captura de tela do painel Configuração do SAML2 no Resource Central.":::

        Copie as URLs do painel **Configurar o Resource Central**:

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Captura de tela do painel Configurar o Resource Central no Resource Central.":::

   1. Em **URL de Retorno**, insira `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler`.
  
1. Em **Certificado**, carregue seu certificado e insira sua senha.

   ![Captura de tela da seção Certificado no Resource Central.](./media/resource-central/cert.png)
   
1. Selecione **Salvar**.

1. Volte para o **portal do Azure**. Em **Certificado de Autenticação SAML**, carregue seu certificado e insira sua senha.

   ![Captura de tela do painel Importar Certificado no portal do Azure.](./media/resource-central/cert2.png).

1. Selecione **Adicionar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD. Para testar o logon único, você tem três opções:

* No portal do Azure, selecione **Testar este aplicativo**. O link redirecionará você para a URL de logon do Resource Central, em que poderá iniciar o logon.

* Acesse diretamente a URL de logon do Resource Central e inicie o logon.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Captura de tela da página da Web de teste de logon único do Resource Central.":::

* Use o portal Meus Aplicativos da Microsoft. No portal Meus Aplicativos, selecione o bloco **Resource Central** para ser redirecionado à URL de logon do Resource Central. Para obter mais informações, confira [Entrar e iniciar aplicativos no portal Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Resource Central para o logon único com o Azure AD, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

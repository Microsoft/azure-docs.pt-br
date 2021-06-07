---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ARC Facilities| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ARC Facilities.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/08/2020
ms.author: jeedes
ms.openlocfilehash: 2ca26d9730c335023864c071657522270880f74a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735417"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-arc-facilities"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ARC Facilities

Neste tutorial, você aprenderá a integrar o ARC Facilities ao Azure AD (Azure Active Directory). Com a integração do ARC Facilities ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao ARC Facilities.
* Permitir que os usuários sejam conectados automaticamente ao ARC Facilities com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do ARC Facilities com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ARC Facilities é compatível com SSO iniciado por **IDP**

* O ARC Facilities é compatível com o provisionamento de usuário **Just In Time**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-arc-facilities-from-the-gallery"></a>Adicionando o ARC Facilities da galeria

Para configurar a integração do ARC Facilities com o Microsoft Azure AD, você precisa adicionar o ARC Facilities da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **ARC Facilities** na caixa de pesquisa.
1. Selecione **ARC Facilities** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-arc-facilities"></a>Configurar e testar logon único do Azure AD para o ARC Facilities

Configure e teste o SSO do Azure AD com o ARC Facilities usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do ARC Facilities.

Para configurar e testar o SSO do Azure AD com o ARC Facilities, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ARC Facilities](#configure-arc-facilities-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do ARC Facilities](#create-arc-facilities-test-user)** – para ter um equivalente de B. Fernandes no ARC Facilities que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **ARC Facilities**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. O aplicativo ARC Facilities espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![Captura de tela que mostra a caixa de diálogo Atributos do Usuário, com o ícone Editar em destaque.](common/edit-attribute.png)

1. Além do demonstrado acima, o aplicativo ARC Facilities espera que mais alguns atributos sejam passados na resposta SAML. Na seção **Atributos e Declarações de Usuário** na caixa de diálogo **Declarações de Grupo (Versão Prévia)** , execute as seguintes etapas:

    a. Clique na **caneta** ao lado de **Grupos retornados na declaração**.

    ![Captura de tela que mostra Atributos e Declarações de Usuário com uma caneta ao lado de Grupos retornados na declaração em destaque.](./media/arc-facilities-tutorial/config01.png)

    ![Captura de tela mostra Declarações de Grupo com Todos os grupos e a ID do Grupo selecionados e o botão Salvar em destaque.](./media/arc-facilities-tutorial/config02.png)

    b. Selecione **Todos os Grupos** na lista de opções.

    c. Selecione **Atributo de Origem** da **ID do Grupo**.

    d. Clique em **Save** (Salvar).

    > [!NOTE]
    > O ARC Facilities espera funções para usuários atribuídos ao aplicativo. Configure essas funções no Azure AD para que os usuários possam ser atribuídos às funções apropriadas. Para entender como configurar funções no Azure AD, confira [aqui](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o ARC Facilities**, copie as URLs apropriadas com base em suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure dando a ela acesso ao ARC Facilities.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **ARC Facilities**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-arc-facilities-sso"></a>Configurar o SSO do ARC Facilities

Para configurar o logon único no lado do **ARC Facilities**, é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do ARC Facilities](mailto:support@arcfacilities.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-arc-facilities-test-user"></a>Criar usuário de teste do ARC Facilities

Nesta seção, um usuário chamado Brenda Fernandes será criado no ARC Facilities. O ARC Facilities é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no ARC Facilities, um novo usuário será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você será conectado automaticamente ao ARC Facilities para o qual configurou o SSO

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do ARC Facilities em Meus Aplicativos, você deverá ser conectado automaticamente ao ARC Facilities, no qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o ARC Facilities, você poderá impor um Controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

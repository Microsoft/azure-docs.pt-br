---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BlogIn | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o BlogIn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/21/2020
ms.author: jeedes
ms.openlocfilehash: a47a3ae27fd1a18b7e9acd7d8b25748f6274c3e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456986"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao BlogIn

Neste tutorial, você aprenderá a integrar o BlogIn ao Azure AD (Azure Active Directory). Ao integrar o BlogIn ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao BlogIn.
* Permitir que os usuários sejam conectados automaticamente ao BlogIn com as suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do BlogIn habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O BlogIn é compatível com SSO iniciado por **SP e IDP**
* O BlogIn é compatível com o provisionamento de usuário **Just-In-Time**
* Depois de configurar o BlogIn, você poderá impor um controle de sessão, que fornece proteção contra a exfiltração e a infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-blogin-from-the-gallery"></a>Adicionar o BlogIn da galeria

Para configurar a integração do BlogIn ao Azure AD, é preciso adicioná-lo por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **BlogIn** na caixa de pesquisa.
1. Selecione **BlogIn** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>Configurar e testar o SSO do Azure AD para o BlogIn

Configure e teste o SSO do Azure AD com o BlogIn usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do BlogIn.

Para configurar e testar o SSO do Azure AD com o BlogIn, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do BlogIn](#configure-blogin-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do BlogIn](#create-blogin-test-user)** – para ter um equivalente de B.Fernandes no BlogIn que esteja vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **BlogIn**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.blogin.co/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.blogin.co/sso/saml/callback`

1. Clique em **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.blogin.co/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Você pode obter os valores exatos para esses campos na página **Configurações** em BlogIn (na guia **Autenticação do Usuário** > **Configurar SSO e Provisionamento de Usuário**). Como alternativa, você pode entrar em contato com a [equipe de suporte ao Cliente do BlogIn](mailto:support@blogin.co) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo BlogIn espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo BlogIn espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de Origem |
    | ------ | --------- |
    | título |user.jobtitle |
    

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao BlogIn.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **BlogIn**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-blogin-sso"></a>Configurar o SSO do BlogIn

Para configurar o logon único no lado do **BlogIn** faça logon na sua conta do BlogIn e siga estas etapas:

1. Acesse **Configurações** > **Autenticação de Usuário** > **Configurar SSO e Provisionamento de usuário**.
2. Na próxima tela, altere o status de Logon Único para **Ativado** e escolha um nome personalizado para o botão de logon SSO que será exibido na tela de logon.

3. Se você tiver salvado a **URL de Metadados de Federação do Aplicativo** na última etapa da seção anterior, escolha o método de configuração **URL de Metadados** e cole a **URL de Metadados de Federação do Aplicativo** no campo URL de Metadados. Caso contrário, altere o método de Configuração para **manual**, preencha manualmente a **URL de SSO do Provedor de Identidade (URL de Logon)** e o **Emissor do Provedor de Identidade (ID da entidade)** e carregue o **Certificado (base64)** obtido do Azure AD.

4. Escolha a função de usuário padrão para novos usuários que ingressam no BlogIn usando o SSO.

5. Selecione **Salvar alterações**.

Para obter uma explicação mais detalhada de como configurar o SSO no BlogIn, confira [Como configurar o SSO para o Microsoft Azure AD no BlogIn](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/). Fique à vontade para entrar em contato com a [equipe de suporte do BlogIn](mailto:support@blogin.co) a qualquer momento se você tiver alguma dúvida ou precisar de ajuda.

### <a name="create-blogin-test-user"></a>Criar usuário de teste no BlogIn

Nesta seção, um usuário chamado B.Fernandes será criado no BlogIn. O BlogIn é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no BlogIn, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do BlogIn no Painel de Acesso, você deverá ser conectado automaticamente ao BlogIn, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o BlogIn com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o BlogIn com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Prezi | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Prezi.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 4ff8196ff4e30aa6ed036cabe00a72bfa8a5c51f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515314"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>Tutorial: Integração do logon único do Azure Active Directory ao Prezi

Neste tutorial, você aprende a integrar o Prezi ao Azure AD (Azure Active Directory). Ao integrar o Prezi ao Azure AD, você poderá:

* Controlar quem tem acesso ao Prezi no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Prezi com suas contas do Azure AD.
* Gerenciar suas contas no portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Prezi habilitada com o SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Prezi é compatível com o SSO iniciado por SP e IDP.
* O Prezi é compatível com o provisionamento de usuário Just-In-Time.
* Depois de configurar o Prezi, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. Para obter mais informações, confira [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-prezi-from-the-gallery"></a>Adicionar o Prezi da galeria

Para configurar a integração do Prezi ao Azure AD, é preciso adicioná-lo por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel mais à esquerda, selecione **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, insira **Prezi** na caixa de pesquisa.
1. Selecione **Prezi** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>Configurar e testar o SSO do Azure AD para o Prezi

Configure e teste o SSO do Azure AD com o Prezi usando um usuário de teste chamado B.Fernandes. Para que o SSO funcione, você estabelece uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Prezi.

Para configurar e testar o SSO do Azure AD com o Prezi, conclua esse blocos de construção:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user) para testar o SSO do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o SSO do Azure AD.
1. [Configure o SSO do Prezi](#configure-prezi-sso) para definir as configurações de SSO do lado do aplicativo.
    1. [Crie um usuário de teste no Prezi](#create-a-prezi-test-user) para ter um equivalente de B.Fernandes no Prezi que esteja vinculado à representação do usuário no Azure AD.
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Prezi**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone **Editar** para editar as definições da **Configuração Básica do SAML**.

   ![Editar as definições da Configuração Básica do SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, o usuário não precisa executar nenhuma etapa, pois o aplicativo já está pré-integrado ao Azure.

1. Selecione **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP**:

    Na caixa **URL de logon**, insira a URL `https://prezi.com/login/sso/`.

1. Clique em **Salvar**.

1. O aplicativo Prezi espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![Declarações e Atributos do Usuário](common/default-attributes.png)

1. O aplicativo Prezi também espera que mais alguns atributos sejam transmitidos de volta na resposta do SAML, como mostrado aqui. Esses atributos também são preenchidos previamente, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação do SAML**, localize **Certificado (Base64)** . Selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do certificado](common/certificatebase64.png)

1. Na seção **Configurar o Prezi**, copie as URLs apropriadas de acordo com seus requisitos.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No portal do Azure, no painel mais à esquerda, selecione **Azure Active Directory**. Vá para **Usuários** e selecione **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do usuário, siga essas etapas:
   1. Na caixa **Nome**, insira **B.Fernandes**.
   1. Na caixa **Nome de usuário**, insira `username@companydomain.extension`, por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de seleção **Mostrar Senha**. Anote o valor que aparece na caixa **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o SSO do Azure concedendo-lhe acesso ao Prezi.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Prezi**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link Usuários e grupos](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários e clique em **Selecionar**, na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista e, em seguida, clique em **Selecionar**, na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-prezi-sso"></a>Configurar o SSO do Prezi

1. Em uma janela diferente do navegador da Web, entre no Prezi com sua conta de equipe e vá para o [Console de Administração](https://prezi.com/organizations/manage).

1. No **Console de Administração**, selecione a guia **Configurações**.

    ![Guia Configurações](./media/prezi-tutorial/settings-image.png)

1. Vá para a seção **SSO (logon único)** e ative a opção para habilitar o SSO.
    
    ![Opção SSO (logon único)](./media/prezi-tutorial/single-signon.png)

1. Na seção **SSO (logon único)** , siga essas etapas:

    ![Sessão SSO (logon único)](./media/prezi-tutorial/configuration.png)

    1. Na caixa **URL do Identificador ou Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    1. Na caixa **Ponto de Extremidade do SAML 2.0 (HTTP)** , cole o valor da **URL de Logon** copiado do portal do Azure.

    1. Abra o **Certificado (Base64)** baixado do portal do Azure no bloco de notas. Copie o conteúdo do certificado e cole na caixa **Certificado (X.509)** .

    1. Clique em **Salvar**.

### <a name="create-a-prezi-test-user"></a>Criar um usuário de teste no Prezi

Nesta seção, um usuário chamado B.Fernandes será criado no Prezi. O Prezi é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Prezi, um será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de SSO do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Prezi no Painel de Acesso, você deverá ser conectado automaticamente à conta do Prezi para a qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](./tutorial-list.md)
- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
- [Avaliar o Prezi com o Azure AD](https://aad.portal.azure.com/)
- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
- [Como proteger o Prezi com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
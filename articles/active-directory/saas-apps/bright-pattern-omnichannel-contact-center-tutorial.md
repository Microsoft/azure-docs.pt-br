---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Bright Pattern Omnichannel Contact Center | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Bright Pattern Omnichannel Contact Center.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.openlocfilehash: 9929dc3dbe5870fbe1a5047fbc3fc552e32aba59
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456681"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bright-pattern-omnichannel-contact-center"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Bright Pattern Omnichannel Contact Center

Neste tutorial, você aprenderá como integrar o Bright Pattern Omnichannel Contact Center ao Azure Active Directory (Azure AD). Ao integrar o Bright Pattern Omnichannel Contact Center com o Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Bright Pattern Omnichannel Contact Center.
* Permitir que os usuários sejam conectados automaticamente ao Bright Pattern Omnichannel Contact Center usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Bright Pattern Omnichannel Contact Center habilitada para logon único (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.



* O Bright Pattern Omnichannel Contact Center dá suporte ao SSO iniciado por **SP e IDP**
* O Bright Pattern Omnichannel Contact Center dá suporte ao provisionamento de usuário **Just In Time**


## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Adicionar o Bright Pattern Omnichannel Contact Center da galeria

Para configurar a integração do Bright Pattern Omnichannel Contact Center com o Azure AD, você precisará adicionar o Bright Pattern Omnichannel Contact Center da galeria em sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar da galeria** , digite **Bright Pattern Omnichannel Contact Center** na caixa de pesquisa.
1. Selecione **Bright Pattern Omnichannel Contact Center** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bright-pattern-omnichannel-contact-center"></a>Configurar e testar o logon único do Azure AD para o Bright Pattern Omnichannel Contact Center

Configure e teste o SSO do Azure AD com o Bright Pattern Omnichannel Contact Center usando um usuário de teste chamado **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado do Bright Pattern Omnichannel Contact Center.

Para configurar e testar o SSO do Azure AD com o Bright Pattern Omnichannel Contact Center, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Bright Pattern Omnichannel Contact Center](#configure-bright-pattern-omnichannel-contact-center-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Bright Pattern Omnichannel Contact Center](#create-bright-pattern-omnichannel-contact-center-test-user)** – para ter um equivalente de B.Fernandes no Bright Pattern Omnichannel Contact Center que esteja vinculado à representação do usuário do AD do Azure.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Bright Pattern Omnichannel Contact Center** , localize a seção **Gerenciar** e selecione **logon único** .
1. Na página **Selecionar um método de logon único** , escolha **SAML** .
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , caso deseje configurar o aplicativo no modo iniciado por **IDP** , digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<SUBDOMAIN>_sso`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon** , digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte do Bright Pattern Omnichannel Contact Center](mailto:support@brightpattern.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Bright Pattern Omnichannel Contact Center espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo Bright Pattern Omnichannel Contact Center espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Namespace  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |

1. Na página **Configurar o logon único com o SAML** , na seção **Certificado de Autenticação SAML** , localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar Bright Pattern Omnichannel Contact Center** , copie as URLs apropriadas, com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure ao conceder acesso para o Bright Pattern Omnichannel Contact Center.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, selecione **Bright Pattern Omnichannel Contact Center** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Configurar o SSO do Bright Pattern Omnichannel Contact Center

Para configurar o logon único no lado do **Bright Pattern Omnichannel Contact Center** , é necessário enviar o **Certificado (Base64)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Bright Pattern Omnichannel Contact Center](mailto:support@brightpattern.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Criar usuário de teste do Bright Pattern Omnichannel Contact Center

Nesta seção, o usuário B.Fernandes será criado no Bright Pattern Omnichannel Contact Center. O Bright Pattern Omnichannel Contact Center dá suporte ao provisionamento de usuário Just In Time, por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no Bright Pattern Omnichannel Contact Center, um novo usuário será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Bright Pattern Omnichannel Contact Center no Painel de Acesso, você deverá ser conectado automaticamente ao Bright Pattern Omnichannel Contact Center, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Bright Pattern Omnichannel Contact Center com o Azure AD](https://aad.portal.azure.com/)
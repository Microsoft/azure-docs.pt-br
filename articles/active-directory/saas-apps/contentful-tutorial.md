---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Contentful | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Contentful.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.openlocfilehash: b664a7e4aac571359fb1494cf69d5bbe3e841b4d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92455302"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Contentful

Neste tutorial, você aprenderá a integrar o Contentful ao Azure AD (Azure Active Directory). Ao integrar o Contentful ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Contentful.
* Permitir que os usuários sejam conectados automaticamente ao Contentful com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Contentful.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Contentful dá suporte ao SSO iniciado por **SP e IDP**
* O Contentful dá suporte ao Provisionamento de Usuário **just-in-time**
* Após configurar o Contentful, você poderá impor controles de sessão, que protegem contra o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo. Só uma instância pode ser configurada em um locatário.

## <a name="adding-contentful-from-the-gallery"></a>Como adicionar o Contentful por meio da galeria

Para configurar a integração do Contentful ao Azure AD, você precisará adicionar o Contentful por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Contentful** na caixa de pesquisa.
1. Selecione **Contentful** nos resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Configurar e testar o logon único do Azure AD para o Contentful

Configure e teste o SSO do Azure AD com o Contentful usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Contentful.

Para configurar e testar o SSO do Azure AD com o Contentful, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Contentful](#configure-contentful-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Contentful](#create-contentful-test-user)** – para ter um equivalente de B.Fernandes no Contentful que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Contentful**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    - Na caixa de texto **URL de Resposta**, copie a URL do ACS (Serviço do Consumidor de Declaração) da página de configuração do SSO no Contentful. Ele terá esta aparência: `https://be.contentful.com/sso/<organization_id>/consume`

1. Clique em **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    - Na caixa de texto **URL de Entrada**, copie a mesma URL do ACS (Serviço do Consumidor de Declaração). Ele terá esta aparência: `https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Entrada reais copiando a URL do ACS (Serviço do Consumidor de Declaração) da página de configuração do SSO no Contentful.

1. O aplicativo Contentful espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Contentful espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | --------------- | --------- |
    | email | user.userprincipalname |

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Contentful**, copie a URL de logon para configurar o SSO do Contentful.

    ![Copiar URLs de configuração](media/contentful-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Contentful.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Contentful**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da página.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Selecionar** na parte inferior da página.
1. Na caixa de diálogo **Adicionar Atribuição**, clique no botão **Atribuir**.

## <a name="configure-contentful-sso"></a>Configurar o SSO do Contentful

Siga estas etapas para configurar o logon único no lado do **Contentful**.

1. Em [Contentful](https://app.contentful.com), navegue até a página de configuração do SSO em **Configurações da Organização**.
1. Clique em **Configurar o SSO**.
1. Copie e cole a URL de logon da seção **Configurar o Contentful** no Azure AD.
1. Copie e cole o certificado do arquivo de certificado Base64 que você baixou do Azure AD.
1. Configure um nome de SSO para logon iniciado pelo SP.
1. Clique em **Habilitar SSO**.

Se isso não funcionar, entre em contato com a [equipe de suporte do Contentful](mailto:support@contentful.com).

### <a name="create-contentful-test-user"></a>Criar um usuário de teste do Contentful

Nesta seção, um usuário chamado B.Fernandes será criado no Contentful. O Contentful dá suporte ao Provisionamento de Usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Contentful, será criado um após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Contentful no Painel de Acesso, você deverá ser conectado automaticamente ao Contentful, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Contentful com o Azure AD](https://aad.portal.azure.com/)
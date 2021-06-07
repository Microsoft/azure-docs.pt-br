---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Druva | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Druva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: a7868d702ff3f1190d7f51e4ad7316508d453015
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92454517"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Druva

Neste tutorial, você aprenderá a integrar o Druva ao Azure AD (Azure Active Directory). Quando integrar o Druva ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Druva.
* Permitir que os usuários sejam conectados automaticamente ao Druva com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Druva.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Druva dá suporte ao SSO iniciado por **IdP**
* Depois de configurar o SSO do Druva, você poderá impor um controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-druva-from-the-gallery"></a>Adicionar o Druva da galeria

Para configurar a integração do Druva com o Azure AD, você precisará adicionar o Druva da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Druva** na caixa de pesquisa.
1. Escolha **Druva** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Configurar e testar logon único do Azure AD para o Druva

Configure e teste o SSO do Azure AD com o Druva usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Druva.

Para configurar e testar o SSO do Azure AD com o Druva, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Druva](#configure-druva-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Druva](#create-druva-test-user)** – para ter um equivalente de B. Fernandes no Druva vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Druva**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **Identificador (ID da Entidade)** , digite o valor da cadeia de caracteres: `DCP-login`.
    
    b. Na caixa de texto **URL de Resposta (URL do Serviço do Consumidor de Declaração)** , digite a URL: `https://cloud.druva.com/wrsaml/consume`.

1. Clique em **Save** (Salvar).

1. O aplicativo Druva espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Druva espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ------------------- | -------------------- |
    | emailAddress | user.email |
    | druva_auth_token | Token de SSO gerado no Console de Administração do DCP, sem aspas.  Por exemplo:  X-XXXXX-XXXX-S-A-M-P-L-E+TXOXKXEXNX=. O Azure adiciona automaticamente as aspas ao token de autenticação. |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Druva**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Druva.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Druva**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-druva-sso&quot;></a>Configurar o SSO do Druva

1. Em outra janela do navegador da Web, entre em seu site de empresa do Druva como administrador.

1. Clique no logotipo do Druva no canto superior esquerdo e, em seguida, clique em **Configurações de Nuvem do Druva**.

    ![Configurações](./media/druva-tutorial/ic795091.png &quot;Configurações")

1. Na guia **Logon Único**, clique em **Editar**.

    ![Captura de tela que mostra a guia "Configurações de Acesso – Logon Único" com o botão "Editar" selecionado.](./media/druva-tutorial/ic795092.png "Configurações de logon único")

1. Na página **Editar configurações de logon único**, realize as seguintes etapas:

    ![Configurações de logon único](./media/druva-tutorial/ic795095.png "Configurações de logon único")

    1. Na caixa de texto **URL de Logon do Provedor de Identidade**, cole o valor de **URL de Logon** que você copiou do portal do Azure.

    1. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de ID**

       > [!NOTE]
       > Para habilitar o logon único para administradores, marque as caixas de seleção **Administradores fazem logon no Druva Cloud por meio do provedor de SSO** e **Permitir acesso failsafe para administradores do Druva Cloud (recomendado)** . O Druva recomenda habilitar a **FailSafe para administradores** para que eles tenham que acessar o console do DCP em caso de falhas no IdP. Ele também permite que os administradores usem a senha de SSO e do DCP para acessar o console do DCP.

    1. Clique em **Save** (Salvar). Isso permite o acesso à plataforma Druva Cloud usando o SSO.

### <a name="create-druva-test-user"></a>Criar um usuário de teste do Druva

Nesta seção, uma usuária chamada B.Fernandes será criada no Druva. O Druva dá suporte ao o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Druva, será criado um novo usuário após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Druva no Painel de Acesso, você deve ser conectado automaticamente ao Druva para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Druva com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
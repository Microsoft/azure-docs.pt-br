---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PureCloud by Genesys | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o PureCloud by Genesys.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: d7aa43a94c7b49eef5b3273617ad9038fd1202e4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92505690"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao PureCloud by Genesys

Neste tutorial, você aprenderá a integrar o PureCloud by Genesys ao Azure AD (Azure Active Directory). Depois de fazer isso, você pode:

* Usar o Azure AD para controlar quais usuários podem acessar o PureCloud by Genesys.
* Permitir que os usuários sejam conectados automaticamente ao PureCloud by Genesys com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Se você não tiver uma, poderá obter uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do PureCloud by Genesys.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O PureCloud by Genesys dá suporte ao SSO iniciado por **SP e IDP** .

> [!NOTE]
> Como a ID desse aplicativo é um valor de cadeia de caracteres fixo, apenas uma instância pode ser configurada em um locatário.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Adicionando o PureCloud by Genesys por meio da galeria

Para configurar a integração do PureCloud by Genesys ao Azure AD, você precisará adicionar o PureCloud by Genesys por meio da galeria à lista de aplicativos SaaS gerenciados. Para fazer isso, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Acesse **Aplicativos Empresariais** e, em seguida, selecione **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar por meio da galeria** , digite **PureCloud by Genesys** na caixa de pesquisa.
1. Selecione **PureCloud by Genesys** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Configurar e testar o logon único do Azure AD para o PureCloud by Genesys

Configure e teste o SSO do Azure AD com o PureCloud by Genesys usando um usuário de teste chamado **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do PureCloud by Genesys.

Para configurar e testar o SSO do Azure AD com o PureCloud by Genesys, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do PureCloud by Genesys](#configure-purecloud-by-genesys-sso)** para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do PureCloud by Genesys](#create-purecloud-by-genesys-test-user)** para ter um equivalente de B.Fernandes no PureCloud by Genesys que esteja vinculado à representação de usuário do Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para habilitar o SSO do Azure AD no portal do Azure, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **PureCloud by Genesys** , localize a seção **Gerenciar** e selecione **Logon único** .
1. Na página **Escolher um método de logon único** , escolha **SAML** .
1. Na página **Configurar o Logon Único com SAML** , selecione o ícone de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , caso deseje configurar o aplicativo no modo iniciado por **IDP** , insira os valores para os seguintes campos:

    a. Na caixa **Identificador** , insira uma URL que corresponda à sua região:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.au/saml
    ```

    b. Na caixa **URL de Resposta** , insira uma URL que corresponda à sua região:

    ```http
    https://login.mypurecloud.com/saml
    https://login.mypurecloud.de/saml
    https://login.mypurecloud.jp/saml
    https://login.mypurecloud.ie/saml
    https://login.mypurecloud.com.au/saml
    ```

1. Selecione **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP** :

    Na caixa **URL de Logon** , insira uma URL que corresponda à sua região:
    
    ```http
    https://login.mypurecloud.com
    https://login.mypurecloud.de
    https://login.mypurecloud.jp
    https://login.mypurecloud.ie
    https://login.mypurecloud.com.au
    ```

1. O aplicativo PureCloud by Genesys espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão:

    ![image](common/default-attributes.png)

1. Além disso, o aplicativo PureCloud by Genesys espera que mais alguns atributos sejam passados de volta na resposta SAML, conforme mostrado na tabela a seguir. Esses atributos também são pré-populados, mas você pode examiná-los conforme necessário.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Na página **Configurar Logon Único com SAML** , na seção **Certificado de Autenticação SAML** , localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o PureCloud by Genesys** , copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará uma usuária de teste no portal do Azure chamada B.Fernandes:

1. No painel esquerdo no portal do Azure, selecione **Azure Active Directory** , selecione **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira o nome de usuário no seguinte formato: username@companydomain.extension. Por exemplo: `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Selecione **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao PureCloud by Genesys.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, selecione **PureCloud by Genesys** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Selecione **Adicionar usuário** e, em seguida, selecione **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e pressione o botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição** , selecione o botão **Atribuir** .

## <a name="configure-purecloud-by-genesys-sso"></a>Configurar o SSO do PureCloud by Genesys

1. Em outra janela do navegador da Web, entre no PureCloud by Genesys como administrador.

1. Selecione **Administrador** na parte superior e, em seguida, vá para **Logon Único** em **Integrações** .

    ![Captura de tela que mostra a janela Administrador do PureCloud, na qual você pode selecionar Logon Único.](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Alterne para a guia **ADFS/Azure AD (Premium)** e siga estas etapas:

    ![Captura de tela que mostra a página Integrações, na qual você pode inserir os valores descritos.](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Selecione **Procurar** para carregar o certificado codificado em base-64 baixado no portal do Azure no **Certificado do ADFS** .

    b. Na caixa **URI do Emissor do ADFS** , cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Na caixa **URI de Destino** , cole o valor da **URL de Logon** copiado do portal do Azure.

    d. Para o valor do **Identificador de Terceira Parte Confiável** , acesse o portal do Azure e, na página de integração de aplicativos do **PureCloud by Genesys** , selecionar a guia **Propriedades** e copiar o valor da **ID do Aplicativo** . Cole-o na caixa **Identificador de Terceira Parte Confiável** .

    ![Captura de tela que mostra o painel Propriedades, no qual você pode encontrar o valor ID do Aplicativo.](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Clique em **Salvar** .

### <a name="create-purecloud-by-genesys-test-user"></a>Criar um usuário de teste do PureCloud by Genesys

Para permitir que os usuários do Azure AD entrem no PureCloud by Genesys, eles precisam ser provisionados no PureCloud by Genesys. No PureCloud by Genesys, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, siga estas etapas:**

1. Faça logon no PureCloud by Genesys como administrador.

1. Selecione **Administrador** na parte superior e vá para **Pessoas** em **Pessoas e Permissões** .

    ![Captura de tela que mostra a janela Administrador do PureCloud, na qual você pode selecionar Pessoas.](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Na página **Pessoas** , selecione **Adicionar Pessoa** .

    ![Captura de tela que mostra a página Pessoas, na qual você pode adicionar uma pessoa.](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Na caixa de diálogo **Adicionar Pessoas à Organização** , siga estas etapas:

    ![A captura de tela mostra a página em que você pode inserir os valores descritos.](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Na caixa **Nome Completo** , digite o nome do usuário. Por exemplo:  **B.fernandes** .

    b. Na caixa **Email** , insira o email do usuário. Por exemplo: **b.fernandes\@contoso.com** .

    c. Selecione **Criar** .

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar no bloco **PureCloud by Genesys** no Painel de Acesso, você deverá ser conectado automaticamente à conta PureCloud by Genesys para a qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure AD](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure AD?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure AD?](../conditional-access/overview.md)

- [Experimentar o PureCloud by Genesys com o Azure AD](https://aad.portal.azure.com/)
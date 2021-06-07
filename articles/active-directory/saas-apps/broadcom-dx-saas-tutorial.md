---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Broadcom DX SaaS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 35ab4d335560689a7e55bf1a8dff9fcb0bcc7d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601509"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Broadcom DX SaaS

Neste tutorial, você aprenderá a integrar o Broadcom DX SaaS ao Azure AD (Azure Active Directory). Ao integrar o Broadcom DX SaaS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Broadcom DX SaaS.
* Permitir que os usuários entrem automaticamente no Broadcom DX SaaS com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Broadcom DX SaaS habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Broadcom DX SaaS dá suporte ao SSO iniciado por **IdP**.

* O Broadcom DX SaaS dá suporte ao provisionamento de usuário **just-in-time**.

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Adicionar o Broadcom DX SaaS por meio da galeria

Para configurar a integração do Broadcom DX SaaS ao Azure AD, você precisará adicionar o Broadcom DX SaaS por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Broadcom DX SaaS** na caixa de pesquisa.
1. Selecione **Broadcom DX SaaS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Configurar e testar o SSO do Azure AD para o Broadcom DX SaaS

Configure e teste o SSO do Azure AD com o Broadcom DX SaaS por meio de um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Broadcom DX SaaS.

Para configurar e testar o SSO do Azure AD com o Broadcom DX SaaS, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Broadcom DX SaaS](#configure-broadcom-dx-saas-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Broadcom DX SaaS](#create-broadcom-dx-saas-test-user)** : para ter um equivalente de B.Fernandes no Broadcom DX SaaS que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração do aplicativo **Broadcom DX SaaS**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `DXI_<TENANT_NAME>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao cliente do Broadcom DX SaaS](mailto:dxi-na1@saas.broadcom.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Broadcom DX SaaS espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Broadcom DX SaaS espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.
    
    | Nome |  Atributo de Origem|
    | --------------- | --------- |
    | Agrupar | user.groups |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Broadcom DX SaaS**, copie as URLs apropriadas de acordo com as suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Broadcom DX SaaS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Broadcom DX SaaS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name=&quot;configure-broadcom-dx-saas-sso&quot;></a>Configurar o SSO do Broadcom DX SaaS

1. Faça logon no seu site de empresa do Broadcom DX SaaS como administrador.

2. Acesse **Configurações** e clique na guia **Usuários**.

    ![Usuários](./media/broadcom-dx-saas-tutorial/settings.png &quot;Usuários")

3. Na seção **Gerenciamento de usuário**, clique nas reticências e selecione **SAML**.

    ![Gerenciamento de usuários](./media/broadcom-dx-saas-tutorial/local.png "Gerenciamento de usuários")

4. Na seção **Identificar conta do SAML**, execute as etapas a seguir.
   
    ![Conta](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Conta") 

    a. Na caixa de texto **Emissor**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Na caixa de texto **URL de Logon do Provedor de Identidade (IdP)** , cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL de Logoff do Provedor de Identidade (IdP)** , cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Abra o **Certificado (Base64)** baixado do portal do Azure no Bloco de notas e cole o conteúdo na caixa de texto **Certificado do provedor de identidade**.

    e. Clique em **AVANÇAR**.

5. Na seção **Mapear atributos**, preencha os atributos do SAML necessários na página a seguir e clique em **AVANÇAR**.

    ![Atributos](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Atributos") 


6. Na seção **Identificar grupo de usuários**, insira a ID de objeto desse grupo e clique em **AVANÇAR**.

    ![Adicionar grupo](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Adicionar grupo") 

7. Na seção **Preencher Conta do SAML**, verifique os valores preenchidos e clique em **SALVAR**.

    ![Conta do SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Conta do SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Criar um usuário de teste do Broadcom DX SaaS

Nesta seção, um usuário chamado Brenda Fernandes será criado no Broadcom DX SaaS. O Broadcom DX SaaS dá suporte ao provisionamento de usuário just-in-time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se ainda não existir um usuário no Broadcom DX SaaS, ele será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e você entrará automaticamente no Broadcom DX SaaS, para o qual configurou o SSO.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco do Broadcom DX SaaS em Meus Aplicativos, você entrará automaticamente no Broadcom DX SaaS, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Broadcom DX SaaS, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
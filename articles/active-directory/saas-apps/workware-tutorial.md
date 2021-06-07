---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workware | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Workware.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: f4996943fd95252be45f7a270d143f14e7f2d48a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workware"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Workware

Neste tutorial, você aprenderá a integrar o Workware ao Azure AD (Azure Active Directory). Quando integrar o Workware ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Workware.
* Permitir que os usuários sejam conectados automaticamente ao Workware usando as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Workware com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Workware dá suporte ao SSO iniciado por **IDP**

## <a name="adding-workware-from-the-gallery"></a>Adicionar o Workware por meio da galeria

Para configurar a integração do Workware ao Azure AD, você precisará adicionar o Workware da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Workware** na caixa de pesquisa.
1. Selecione **Workware** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Configurar e testar o SSO do Azure AD para o Workware

Configure e teste o SSO do Azure AD com o Workware usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Workware.

Para configurar e testar o SSO do Azure AD com o Workware, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Workware](#configure-workware-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Workware](#create-workware-test-user)** – Para ter um equivalente de B.Fernandes no Workware que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Workware**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `<WORKWARE_URL>/WW/AuthServices`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `<WORKWARE_URL>/WW/AuthServices/Acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Entre em contato com a [equipe de suporte ao Cliente do Workware](mailto:support@activeops.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Workware**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você habilitará que B.Fernandes use o logon único do Azure ao permitir a ela o acesso ao Workware.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Workware**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-workware-sso"></a>Configurar o SSO do Workware

Para usar o recurso de SSO no Workware, a seguinte configuração precisa ser concluída:

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Habilitar permissões de SSO para administradores do sistema do Workware

* Para permitir que os administradores do Workware configurem a autenticação de SSO, a permissão de Autenticação de SSO (na categoria **Administração > Permissões de configuração do sistema na tela Configurações do Sistema > Permissões para a Função**) precisa estar habilitada para administradores do sistema do Workware.

    ![Permissão de autenticação de SSO](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Configurar a autenticação de SSO no Workware

1. Acesse a página **Configurações do Sistema** e clique em **Autenticação de SSO**

1. Na seção **Autenticação de SSO**, clique no botão **Adicionar Autenticação de SSO** e execute as seguintes etapas: 

    ![Autenticação de SSO](./media/workware-tutorial/authentication.png)

    1. No **Provedor de Identidade Externo**, forneça o nome do IDP.
    1. Selecione **SAML2.0** como o **Tipo de Autenticação**
    1. Na caixa de texto **URL de Entrada do Provedor de Identidade**, cole o valor da **URL de Logon** copiado do portal do Azure.
    1. Na caixa de texto **URL do Emissor de Provedor de Identidade**, cole o valor do **Identificador do Azure AD**, copiado do portal do Azure.
    1. Na caixa de texto **URL de Logoff do Provedor de Identidade**, insira o valor da **URL de Logoff** copiado do portal do Azure.
    1. Clique em **Habilitar**.
    1. Faça upload do **Certificado** baixado no **Certificado do Provedor de Identidade** no portal do Azure.
    1. Clique em **Salvar**


### <a name="create-workware-test-user"></a>Criar usuário de teste do Workware

1. Entre no site do Workware como administrador.

1. Selecione **Administrador > Criar/Exibir > Contas de Usuário > Adicionar Novo**

1. Execute as etapas abaixo na página a seguir.

    ![Testar usuário](./media/workware-tutorial/create-user.png)

    a. Insira um nome válido no campo **Nome**.

    b. Selecione **Tipo de Autenticação** como **SSO**.

    c. Insira os campos obrigatórios e clique em **Salvar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em Testar este aplicativo no portal do Azure e entre automaticamente no Workware para o qual você configurou o SSO

* Use o Painel de Acesso da Microsoft. Ao clicar no bloco Workware no Painel de Acesso, você deverá ser conectado automaticamente ao Workware, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Workware, você poderá impor um controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Palo Alto Networks – GlobalProtect | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – GlobalProtect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Palo Alto Networks – GlobalProtect

Neste tutorial, você aprenderá a integrar o Palo Alto Networks – GlobalProtect ao Azure AD (Azure Active Directory). Com a integração do Palo Alto Networks – GlobalProtect ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Palo Alto Networks – GlobalProtect.
* Permitir que os usuários sejam conectados automaticamente ao Palo Alto Networks – GlobalProtect usando suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Palo Alto Networks – GlobalProtect habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* Palo Alto Networks – o GlobalProtect dá suporte a SSO iniciado por **SP**
* O Palo Alto Networks – o GlobalProtect dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Adicionando o Palo Alto Networks – GlobalProtect da Galeria

Para configurar a integração do Palo Alto Networks – GlobalProtect com o Azure AD, você precisará adicionar o Palo Alto Networks – GlobalProtect da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Palo Alto Networks – GlobalProtect** na caixa de pesquisa.
1. Selecione **Palo Alto Networks – GlobalProtect** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Configurar e testar o SSO do Azure AD para o Palo Alto Networks – GlobalProtect

Configure e teste o SSO do Azure AD com o Palo Alto Networks – GlobalProtect usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks – GlobalProtect.

Para configurar e testar o SSO do Azure AD com o Palo Alto Networks – GlobalProtect, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Palo Alto Networks – GlobalProtect](#configure-palo-alto-networks---globalprotect-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Palo Alto Networks – GlobalProtect](#create-palo-alto-networks---globalprotect-test-user)** – para ter um equivalente de B. Fernandes no Palo Alto Networks – GlobalProtect vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativo **Palo Alto Networks – GlobalProtect**, encontre a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<Customer Firewall URL>`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Entre em contato com a [equipe de suporte ao cliente do Palo Alto Networks – GlobalProtect](https://support.paloaltonetworks.com/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar Palo Alto Networks – GlobalProtect**, copie as URLs apropriadas com base em suas necessidades.

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

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure concedendo-lhe acesso ao Palo Alto Networks – GlobalProtect.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Palo Alto Networks – GlobalProtect**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Configurar o SSO do Palo Alto Networks – GlobalProtect

1. Abra o Palo Alto Networks – GlobalProtect como administrador em outra janela do navegador.

2. Clique em **Dispositivo**.

    ![Configurar o logon único da Palo Alto 1](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Selecione **Provedor de Identidade SAML** na barra de navegação à esquerda e clique em "Importar" para importar o arquivo de metadados.

    ![Configurar o logon único da Palo Alto 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Execute as ações a seguir na janela de importação

    ![Configurar o logon único da Palo Alto 3](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Na caixa de texto **Nome do Perfil**, forneça um nome, por exemplo, Azure AD GlobalProtect.

    b. Em **Metadados do Provedor de Identidade**, clique em **Procurar** e selecione o arquivo metadata.xml que você baixou do Portal do Azure

    c. Clique em **OK**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Criar um usuário de teste do Palo Alto Networks – GlobalProtect

Nesta seção, uma usuária chamada B. Fernandes é criada no Palo Alto Networks – GlobalProtect. O Palo Alto Networks – o GlobalProtect dá suporte ao provisionamento de usuário Just-In-Time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Palo Alto Networks – GlobalProtect, um novo será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você para a URL de Logon do Palo Alto Networks – GlobalProtect, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Palo Alto Networks – GlobalProtect diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Ao clicar no bloco Palo Alto Networks – GlobalProtect em Meus Aplicativos, você deverá ser conectado automaticamente ao aplicativo Palo Alto Networks – GlobalProtect, para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Palo Alto Networks – GlobalProtect, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
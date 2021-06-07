---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Zoom | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zoom.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: d105c83ba3db9502cf83f943dec6fcbfd5640d07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735590"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Zoom

Neste tutorial, você aprenderá a integrar o Zoom ao Azure AD (Azure Active Directory). Ao integrar o Zoom ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Zoom.
* Permitir que os usuários sejam conectados automaticamente ao Zoom com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Zoom habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Zoom dá suporte ao SSO iniciado por **SP** e 
* O Zoom dá suporte ao [provisionamento de usuário **automatizado**](./zoom-provisioning-tutorial.md).

## <a name="adding-zoom-from-the-gallery"></a>Adicionar o Zoom da galeria

Para configurar a integração do Zoom com o Azure AD, você precisará adicionar o Zoom da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Zoom** na caixa de pesquisa.
1. Selecione **Zoom** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zoom"></a>Configurar e testar o SSO do Azure AD para o Zoom

Configure e teste o SSO do Azure AD com o Zoom usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zoom.

Para configurar e testar o SSO do Azure AD com o Zoom, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Zoom](#configure-zoom-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zoom](#create-zoom-test-user)** – para ter um equivalente de B. Fernandes no Zoom vinculado à representação do usuário no Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zoom**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<companyname>.zoom.us`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<companyname>.zoom.us`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<companyname>.zoom.us`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Zoom](https://support.zoom.us/hc/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e escolha **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Zoom**, copie as URLs apropriadas de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

> [!NOTE]
> Para saber como configurar a Função no Azure AD, confira [Configurar a declaração de função emitida no token SAML para aplicativos empresariais](../develop/active-directory-enterprise-app-role-management.md).

> [!NOTE]
> O Zoom pode esperar uma declaração de grupo no conteúdo SAML. Se tiver criado algum grupo, entre em contato com a [equipe de suporte ao cliente do Zoom](https://support.zoom.us/hc/) e forneça as informações sobre o grupo para que eles possam configurar as informações do grupo no lado deles. Você também precisará fornecer a ID do Objeto para a [equipe de suporte ao cliente do Zoom](https://support.zoom.us/hc/) para que eles possam configurá-la no lado deles. Para obter a ID de Objeto, confira [Configurando o Zoom com o Azure](https://support.zoom.us/hc/articles/115005887566).

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao Zoom.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zoom**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-zoom-sso"></a>Configurar o SSO do Zoom

1. Para automatizar a configuração no Zoom, é necessário instalar a **extensão do navegador My Apps Secure Sign-in** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após a adição da extensão ao navegador, clique em **Configurar o Zoom** e você será direcionado ao aplicativo Zoom. Nele, forneça as credenciais de administrador para entrar no Zoom. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

3. Se desejar configurar o Zoom manualmente, em uma janela diferente do navegador da Web, entre em seu site da empresa do Zoom como administrador.

2. Clique na guia **Logon Único**.

    ![Guia Logon único](./media/zoom-tutorial/zoom-sso1.png "Logon único")

3. Clique na guia **Controle de segurança** e, em seguida, vá para as configurações de **Logon Único**.

4. Na seção de Configurações de Logon Único, execute as seguintes etapas:

    ![Seção Logon único](./media/zoom-tutorial/zoom-sso2.png "Logon único")

    a. Na caixa de texto **URL da página de entrada**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Para o valor de **URL de página de logout**, você precisa acessar o portal do Azure e clicar em **Azure Active Directory** à esquerda e, em seguida, navegar até **Registros de aplicativo**.

    ![O botão Azure Active Directory](./media/zoom-tutorial/appreg.png)

    c. Clique em **Pontos de extremidade**

    ![O botão Ponto de extremidade](./media/zoom-tutorial/endpoint.png)

    d. Copie o **PONTO DE EXTREMIDADE DE SAÍDA DO SAML-P** e cole-o na caixa de texto da **URL de página de saída**.

    ![O botão Copiar ponto de extremidade](./media/zoom-tutorial/endpoint1.png)

    e. Abra o seu certificado codificado em base 64 no bloco de notas, copie o seu conteúdo para a sua área de transferência e cole na caixa de texto **Certificado do provedor de identidade**.

    f. Na caixa de texto **Emissor**, cole o valor de **Identificador do Azure AD**, copiado do portal do Azure. 

    g. Clique em **Salvar Alterações**.

    > [!NOTE]
    > Para obter mais informações, visite a documentação de zoom [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Criar um usuário de teste do Zoom

O objetivo desta seção é criar um usuário chamado B.Fernandes no Zoom. O Zoom dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](./zoom-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

> [!NOTE]
> Se for necessário criar um usuário manualmente, você precisará entrar em contato com a [Equipe de suporte do cliente do Zoom](https://support.zoom.us/hc/)

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir.

* Clique em **Testar este aplicativo** no portal do Azure. Isso redirecionará você à URL de Logon do Zoom, na qual você poderá iniciar o fluxo de logon.

* Acesse diretamente a URL de Logon do Zoom e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Zoom em Meus Aplicativos, isso redirecionará você à URL de Logon do Zoom. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Zoom do Azure AD, você poderá impor o Controle de sessão, que fornece proteção contra a exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O Controle de Sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
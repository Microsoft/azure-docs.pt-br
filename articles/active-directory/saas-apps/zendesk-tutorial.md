---
title: 'Tutorial: Integração do Azure Active Directory ao Zendesk | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Zendesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 58e9cc2fda7779cf0d62db8e0b6f78e5bb4d95f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731778"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Zendesk

Neste tutorial, você aprenderá a integrar o Zendesk ao Azure AD (Azure Active Directory). Ao integrar o Zendesk ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Zendesk.
* Permitir que os usuários sejam conectados automaticamente ao Zendesk com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Zendesk.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Zendesk dá suporte ao SSO iniciado por **SP**
* O Zendesk dá suporte ao [provisionamento de usuário **Automatizado**](zendesk-provisioning-tutorial.md)


## <a name="adding-zendesk-from-the-gallery"></a>Adicionar o Zendesk da galeria

Para configurar a integração do Zendesk ao Azure AD, você precisa adicionar o Zendesk por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Zendesk** na caixa de pesquisa.
1. Escolha **Zendesk** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-zendesk"></a>Configurar e testar o SSO do Azure AD para o Zendesk

Configure e teste o SSO do Azure AD com o Zendesk usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Zendesk.

Para configurar e testar o SSO do Azure AD com o Zendesk, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Zendesk](#configure-zendesk-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Zendesk](#create-zendesk-test-user)** : para ter um equivalente de B.Fernandes no Zendesk que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Zendesk**, localize a seção **Gerenciar** e escolha **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com`

    c. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta, o Identificador e a URL de Logon reais. Entre em contato com a [equipe de atendimento ao cliente do Zendesk](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo Zendesk espera as declarações SAML em um formato específico. Não há nenhum atributo SAML obrigatório, mas, opcionalmente, você pode realizar o gerenciamento na seção **Atributos de Usuário** na página de integração de aplicativos. Na página **Definir Logon Único com SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Atributos do Usuário**.

    ![Captura de tela que mostra Atributos de usuário com o ícone Editar selecionado.](common/edit-attribute.png)

    > [!NOTE]
    > Você pode usar atributos de extensão para adicionar atributos que não estão no Azure AD por padrão. Clique em [Atributos do usuário que podem ser definidos no SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) para obter a lista completa de atributos SAML que o **Zendesk** aceita.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o Zendesk**, copie as URLs apropriadas de acordo com as suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Zendesk.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Zendesk**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função "Acesso Padrão" selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-zendesk-sso"></a>Configurar SSO do Zendesk

1. Para automatizar a configuração no **Zendesk**, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![A captura de tela mostra o botão Instalar a extensão.](./media/target-process-tutorial/install_extension.png)

1. Depois de adicionar a extensão ao navegador, clique em **Configurar o Zendesk** para ser direcionado ao aplicativo Zendesk. Em seguida, forneça as credenciais de administrador para entrar no Zendesk. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 6.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o Zendesk manualmente, abra uma nova janela do navegador da Web, entre no seu site da empresa do Zendesk como administrador e execute as seguintes etapas:

1. No **Centro de Administração do Zendesk**, clique em **Configurações de segurança** na guia **Segurança**.

    ![A captura de tela mostra o Centro de Administração do Zendesk com as Configurações de segurança selecionadas.](./media/zendesk-tutorial/settings.png "Segurança")

1. Acesse a página **Logon único** e clique em **Editar** no **SAML**.

    ![A captura de tela mostra a página Logon único com a opção Editar selecionada.](./media/zendesk-tutorial/saml-sso.png "Segurança")

1. Execute as etapas a seguir na página **SSO**.

    ![Logon Único](./media/zendesk-tutorial/saml-configuration.png "Logon único")

    a. Na caixa de texto **URL de SSO do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure.

    b. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.

    c. Na caixa de texto **URL de Logoff Remoto**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    d. Clique em **Save** (Salvar).

### <a name="create-zendesk-test-user"></a>Criar um usuário de teste do Zendesk

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Zendesk. O Zendesk suporta o provisionamento automático de usuários, que é ativado por padrão. Você pode encontrar mais detalhes [ aqui ](Zendesk-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso o redirecionará para a URL de Logon do Zendesk, na qual você poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Zendesk diretamente e inicie o fluxo de logon de lá.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Zendesk em Meus Aplicativos, isso redirecionará para a URL de logon do Zendesk. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Após configurar o Zendesk, você poderá impor o controle de sessão, que protege contra a exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
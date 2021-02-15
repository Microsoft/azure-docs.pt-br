---
title: 'Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex Meetings| Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cisco Webex Meetings.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.openlocfilehash: 7a4e3a4a6049b48fa70e5352680bdc7342676c0b
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670223"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Integração SSO (logon único) do Azure Active Directory com o Cisco Webex Meetings

Neste tutorial, você aprenderá como integrar o Cisco Webex Meetings ao Azure AD (Azure Active Directory). Com a integração do Cisco Webex Meetings ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao Cisco Webex Meetings.
* Permitir que os usuários entrem automaticamente no Cisco Webex Meetings com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do Cisco Webex Meetings habilitada para SSO (logon único).

> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cisco Webex Meetings dá suporte ao SSO iniciado por **SP e IDP**

* O Cisco Webex Meetings dá suporte ao provisionamento de usuário **Just-In-Time**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Adicionando o Cisco Webex Meetings da galeria

Para configurar a integração do Cisco Webex Meetings ao Azure AD, você precisa adicionar o Cisco Webex Meetings da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Cisco Webex Meetings** na caixa de pesquisa.
1. Selecione **Cisco Webex Meetings** no painel de resultados e, depois, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Configurar e testar o logon único do Azure AD para Cisco WebEx Meetings

Configure e teste o SSO do Azure AD com o Cisco Webex Meetings usando um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cisco Webex Meetings.

Para configurar e testar o SSO do Azure AD com o Cisco Webex Meetings, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
2. **[Configurar o SSO do Cisco Webex Meetings](#configure-cisco-webex-meetings-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Cisco Webex Meetings](#create-cisco-webex-meetings-test-user)** – para ter um equivalente de B. Fernandes no Cisco Webex Meetings que esteja vinculado à representação de usuário do Azure AD.
3. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cisco Webex Meetings**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar Logon Único com SAML**, você pode configurar o aplicativo no modo iniciado por **IDP** carregando o arquivo **Metadados do Provedor de Serviços** da seguinte maneira:

    a. Clique em **Carregar arquivo de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o arquivo de metadados e depois em **Carregar**.

    c. Após a conclusão bem-sucedida do upload do arquivo de metadados do Provedor de Serviços, os valores **Identificador** e **URL de Resposta** são preenchidos automaticamente na seção **Configuração Básica do SAML**.

    >[!Note]
    >Você obterá o arquivo de Metadados do Provedor de Serviços da seção **Configurar o SSO do Cisco Webex Meetings**, que será explicada posteriormente neste tutorial. 

1. Se desejar configurar o aplicativo no modo iniciado pelo **SP**, realize as seguintes etapas:  

    a. Na seção **Configuração básica do SAML**, clique no ícone editar/de caneta.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

    b. Na caixa de texto **URL de Logon**, digite a URL usando o seguinte padrão: `https://<customername>.my.webex.com`

5. O aplicativo Cisco Webex Meetings espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de usuário.

    ![image](common/edit-attribute.png)

6. Além do indicado acima, o aplicativo Cisco Webex Meetings espera que mais alguns atributos sejam passados novamente na resposta SAML. Na seção Declarações de Usuário da caixa de diálogo Atributos de Usuário, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo: 

    | Nome | Atributo de Origem|
    | ---------------|  --------- |
    |   nome    | user.givenname |
    |   sobrenome    | user.surname |
    |   email       | user.mail |
    |   uid    | user.mail |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, selecione o valor do atributo mostrado para essa linha na lista suspensa.

    f. Clique em **Save** (Salvar).

4. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar o Cisco Webex Meetings**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Cisco Webex Meetings.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **Cisco Webex Meetings**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

    ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cisco-webex-meetings-sso"></a>Configurar o SSO do Cisco Webex Meetings

1. Acesse a URL `https://<customername>.webex.com/admin` com suas credenciais de administração.

2. Acesse **Configurações Comuns de Site** e navegue até **Configuração de SSO**.

    ![Captura de tela que mostra Administração do Cisco WebEx com Configurações de Site Comuns e Configuração de SSO selecionadas.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na página **Administração do Webex**, execute as seguintes etapas:

    ![Captura de tela que mostra a página Administração do WebEx com as informações descritas nesta etapa.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Selecione **SAML 2.0** como o **Protocolo de Federação**.

    b. Clique no link **Importar metadados SAML** para carregar o arquivo de metadados que você baixou do portal do Azure.

    c. Clique no botão **Exportar** para baixar o arquivo de metadados do provedor de serviço e carregue-o na seção **Configuração básica de SAML** no portal do Azure.

    d. Na caixa de texto **AuthContextClassRef**, digite `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` e, se você deseja habilitar o MFA usando o Azure AD, digite os dois valores como `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Selecione **Criação Automática de Conta**.

    >[!NOTE]
    >Para habilitar o provisionamento de usuário **Just-In-Time**, você precisa marcar a **Criação Automática de Conta**. Além disso, os atributos do token SAML precisam ser passados na resposta SAML.

    f. Clique em **Save** (Salvar).

    >[!NOTE]
    >Essa configuração destina-se apenas aos clientes que usam a ID de usuário do Webex no formato de email.

### <a name="create-cisco-webex-meetings-test-user"></a>Criar um usuário de teste do Cisco Webex Meetings

O objetivo desta seção é criar um usuário chamado B. Fernandes no Cisco Webex Meetings. O Cisco Webex Meetings dá suporte ao provisionamento **Just-In-Time**, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Cisco Webex Meetings, um novo será criado quando você tentar acessar o Cisco Webex Meetings.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Cisco Webex Meetings no Painel de Acesso, você deverá ser conectado automaticamente ao Cisco Webex Meetings para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o ServiceNow com o Azure AD](https://aad.portal.azure.com)
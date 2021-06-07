---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao iSAMS | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o iSAMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.openlocfilehash: e994e39cc916c51e3ad6b00015d710bb422cccc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459689"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-isams"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao iSAMS

Neste tutorial, você aprenderá a integrar o iSAMS ao Azure AD (Azure Active Directory). Ao integrar o iSAMS ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao iSAMS.
* Permitir que os usuários sejam conectados automaticamente ao iSAMS com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do iSAMS habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.


* O iSAMS dá suporte ao SSO iniciado por **SP e IdP**
* Depois de configurar o iSAMS, você poderá impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-isams-from-the-gallery"></a>Como adicionar o iSAMS por meio da galeria

Para configurar a integração do iSAMS ao Azure AD, você precisará adicionar o iSAMS por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **iSAMS** na caixa de pesquisa.
1. Escolha **iSAMS** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso-for-isams"></a>Configurar e testar o SSO do Azure AD para o iSAMS

Configure e teste o SSO do Azure AD com o iSAMS usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do iSAMS.

Para configurar e testar o SSO do Azure AD com o iSAMS, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do iSAMS](#configure-isams-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do iSAMS](#create-isams-test-user)** : para ter um equivalente de B.Fernandes no iSAMS que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **iSAMS**, localize a seção **Gerenciar** e escolha **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.isams.cloud/main/sso/saml2`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.isams.cloud/main/sso/saml2/acs`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://<SUBDOMAIN>.isams.cloud/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta e a URL de Logon reais. Entre em contato com a [equipe de suporte ao cliente do iSAMS](mailto:support@isams.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao iSAMS.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **iSAMS**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-isams-sso"></a>Configurar SSO do iSAMS

1. Faça logon no iSAMS como Administrador.

1. Procure o Painel de Controle e abra o módulo **Autenticação**.
1. No menu à direita, selecione **Provedores de Identidade**

    ![A captura de tela mostra a Configuração do Active Directory com a opção Provedores de Identidade selecionada.](./media/isams-tutorial/click-identity-provider.png)

1. Selecione **Adicionar Provedor**

    ![A captura de tela mostra Provedores de Identidade com a opção Adicionar Provedores selecionada.](./media/isams-tutorial/add-identity-provider.png)


1. Execute as etapas abaixo na seguinte página:

    ![A captura de tela mostra o Assistente de Provedores de Identidade em que você pode seguir as etapas descritas.](./media/isams-tutorial/configure-isams.png)

    a. Na caixa de texto **Nome**, forneça um nome válido, como `Saml2 Azure`. Esse é o nome que será exibido na página de logon.

    b. Na caixa URL de Metadados, cole o valor da **URL de Metadados de Federação do Aplicativo** copiado do portal do Azure.
    
    c. Clique em **Importar**.
    
    d. Na caixa de listagem **Aplicativos** na seção **Aplicativos Cliente Habilitados**, selecione todos os aplicativos do iSAMS nos quais deseja exibir o provedor na página de logon.

    e. Clique em **Salvar e Fechar**.

### <a name="create-isams-test-user"></a>Criar um usuário de teste do iSAMS

1. Faça logon no iSAMS como Administrador.

2.  Acesse a **Página Inicial do Painel de Controle** -> **Segurança e Permissões** -> **Contas de Usuário** -> **Opções do Usuário e Tarefas** -> **Modificar Propriedades do Usuário**

    ![A captura de tela mostra a página Contas do Usuário com a opção Modificar Propriedades do Usuário selecionada.](./media/isams-tutorial/modify-user-properties.png)


3. Na janela pop-up resultante, selecione a guia **Detalhes da Conta** e altere a **Autorização** para aquela do provedor de identidade recém-criado.

    ![A captura de tela mostra os Detalhes da Conta com um valor para autorização.](./media/isams-tutorial/account-details.png)

4. Clique em **Salvar e Fechar**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do iSAMS no Painel de Acesso, você deverá ser conectado automaticamente ao iSAMS, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o iSAMS com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
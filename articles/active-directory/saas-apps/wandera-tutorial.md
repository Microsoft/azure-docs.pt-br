---
title: 'Tutorial: Integração do Azure Active Directory ao Wandera RADAR Admin | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Wandera RADAR Admin.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: 383b49a7363fdca9327d419f5bed092d489a95d5
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951769"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Tutorial: Integrar o Wandera RADAR Admin ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o Wandera RADAR Admin ao Azure AD (Azure Active Directory). Ao integrar o Wandera RADAR Admin ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Wandera RADAR Admin.
* Permitir que os usuários sejam conectados automaticamente ao Wandera RADAR Admin com as contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Wandera RADAR Admin.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Wandera RADAR Admin é compatível com o SSO iniciado por **IDP**
* Depois de configurar o Wandera RADAR Admin, será possível impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-wandera-radar-admin-from-the-gallery"></a>Como adicionar o Wandera RADAR Admin por meio da galeria

Para configurar a integração do Wandera RADAR Admin ao Azure AD, você precisará adicionar o Wandera RADAR Admin por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Wandera RADAR Admin** na caixa de pesquisa.
1. Escolha **Wandera RADAR Admin** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-sso"></a>Configurar e testar o SSO do Azure AD

Configure e teste o SSO do Azure AD com o Wandera RADAR Admin usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Wandera RADAR Admin.

Para configurar e testar o SSO do Azure AD com o Wandera RADAR Admin, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
   * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
   * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Wandera RADAR Admin](#configure-wandera-radar-admin-sso)** – para definir as configurações de logon único no lado do aplicativo.
   * **[Criar um usuário de teste do Wandera RADAR Admin](#create-wandera-radar-admin-test-user)** – para ter um equivalente de B.Fernandes no Wandera RADAR Admin que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Wandera RADAR Admin**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de Resposta**, digite uma URL usando o seguinte padrão: `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Resposta real. Para obter o valor, entre em contato com a [equipe de suporte ao cliente do Wandera RADAR Admin](https://www.wandera.com/about-wandera/contact/#supportsection). Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure. Substitua cuidadosamente a parte <tenant id> da URL acima pela ID de locatário mostrada na página **Configurações** > **Administração** > **Logon único** de sua conta do Wandera.


1. Na página **Configurar o Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Certificado de autenticação de SAML** para editar as configurações.

    ![Opção de assinatura](common/signing-option.png)

    1. Selecione **Opção de Assinatura** como **Assinar resposta SAML e declaração**.

    1. Selecione **Algoritmo de Assinatura** como **SHA-256**.

1. Na seção **Configurar o Wandera RADAR Admin**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Wandera RADAR Admin.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Wandera RADAR Admin**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-wandera-radar-admin-sso"></a>Configurar SSO do Wandera RADAR Admin

1. Para automatizar a configuração no Wandera RADAR Admin, é necessário instalar a **Extensão do navegador de Entrada Segura dos Meus Aplicativos**, clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

2. Após adicionar a extensão ao navegador, clicar em **Configurar Wandera RADAR Admin** direcionará você ao aplicativo do Wandera RADAR Admin. Então, forneça as credenciais de administrador para entrar no Wandera RADAR Admin. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 4.

    ![Configuração da instalação](common/setup-sso.png)

3. Se quiser configurar o Wandera RADAR Admin manualmente, abra uma nova janela do navegador da Web, entre no site da empresa do Wandera RADAR Admin como administrador e execute as seguintes etapas:

4. No canto superior direito da página, clique em **Configurações** > **Administração** > **Logon único** e, em seguida, marque a opção **Habilitar SAML 2.0** para executar as etapas a seguir.

    ![Configuração do Wandera RADAR Admin](./media/wandera-tutorial/config01.png)

    a. Clique em **Ou inserir manualmente os campos obrigatórios**.

    b. Na caixa de texto **ID da Entidade do IdP**, cole o valor do **Identificador do Azure AD** copiado do portal do Azure.

    c. Abra o XML de Metadados de Federação no bloco de notas, copie o conteúdo e cole-o na caixa de texto **Certificado Público X.509 do IdP**.

    d. Clique em **Save** (Salvar).

### <a name="create-wandera-radar-admin-test-user"></a>Criar usuário de teste do Wandera RADAR Admin

Nesta seção, você criará um usuário chamado B.Fernandes no Wandera RADAR Admin. Trabalhe com a [equipe de suporte do Wandera RADAR Admin](https://www.wandera.com/about-wandera/contact/#supportsection) para adicionar os usuários na plataforma do Wandera RADAR Admin. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Wandera RADAR Admin no Painel de Acesso, você deverá ser conectado automaticamente ao Wandera RADAR Admin no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Lenses.io | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o logon único entre o Azure Active Directory e o Lenses.io.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458601"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao portal de DataOps do Lenses.io

Neste tutorial, você aprenderá a integrar o portal de DataOps do [Lenses.io](https://lenses.io/) ao Azure AD (Azure Active Directory). Depois de integrar o Lenses.io ao Azure AD, você poderá:

* Controlar, no Azure AD, quem tem acesso ao portal do Lenses.io.
* Permitir que os usuários sejam conectados automaticamente ao Lenses com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e o logon único com o Azure AD](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma instância de um portal do Lenses. Você pode escolher entre várias [opções de implantação](https://lenses.io/product/deployment/).
* Uma [licença](https://lenses.io/product/pricing/) do Lenses.io que dê suporte ao SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Lenses.io dá suporte ao SSO iniciado pelo SP (provedor de serviços).

* Você poderá impor o controle de sessão depois de configurar o Lenses.io. O controle de sessão fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-lensesio-from-the-gallery"></a>Adicionar o Lenses.io por meio da galeria

Para configurar a integração do Lenses.io ao Azure AD, adicione o Lenses.io à lista de aplicativos SaaS gerenciados:

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal Microsoft.
1. No painel esquerdo, selecione o serviço **Microsoft Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Selecione **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, insira **Lenses.io** na caixa de pesquisa.
1. No painel de resultados, selecione **Lenses.io** e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Configurar e testar o SSO do Azure AD para o Lenses.io

Você criará um usuário de teste chamado *B.Fernandes* para configurar e testar o SSO do Azure AD com o portal do Lenses.io. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Lenses.io.

Concluir as seguintes etapas:

1. [Configurar o SSO do Azure AD](#configure-azure-ad-sso) – para permitir que os usuários usem esse recurso.
    1. [Crie um usuário e um grupo de teste do Azure AD](#create-an-azure-ad-test-user-and-group) para testar o SSO do Azure AD com B.Fernandes.
    1. [Atribua o usuário de teste do Azure AD](#assign-the-azure-ad-test-user) para permitir que B.Fernandes use o SSO do Azure AD.
1. [Configure o SSO do Lenses.io](#configure-lensesio-sso) para definir as configurações de SSO no lado do aplicativo.
    1. [Crie permissões do grupo de teste do Lenses.io](#create-lensesio-test-group-permissions) para controlar o que B.Fernandes pode acessar no Lenses.io (autorização).
1. [Teste o SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure:

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Lenses.io**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone editar/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Captura de tela que mostra o ícone para edição da configuração básica do SAML.](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira valores nas seguintes caixas de entrada de texto:

    a. **URL de Logon**: insira uma URL que tenha o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>`. Um exemplo é `https://lenses.my.company.com`.

    b. **Identificador (ID da Entidade)** : insira uma URL que tenha o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>`. Um exemplo é `https://lenses.my.company.com`.

    c. **URL de Resposta**: insira uma URL que tenha o seguinte padrão: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`. Um exemplo é `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    > [!NOTE]
    > Esses valores não são reais. Atualize-os com a URL de logon, a URL de resposta e o identificador reais da URL base da instância do portal do Lenses. Confira a [documentação do SSO do Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0) para obter mais informações.

1. Na página **Configurar o logon único com o SAML**, acesse a seção **Certificado de Autenticação SAML**. Localize **XML de Metadados de Federação** e selecione **Baixar** para baixar e salvar o certificado no computador.

    ![Captura de tela que mostra o link de download do Certificado.](common/metadataxml.png)

1. Na seção **Configurar o Lenses.io**, use o arquivo XML baixado para configurar o Lenses no SSO do Azure.

### <a name="create-an-azure-ad-test-user-and-group"></a>Criar um grupo e usuário de teste do Azure AD

No portal do Azure, você criará um usuário de teste chamado B.Fernandes. Em seguida, você criará um grupo de teste que controla o acesso de B.Fernandes no Lenses.

Descubra como o Lenses usa o mapeamento de associação a um grupo para autorização na [documentação do SSO do Lenses](https://docs.lenses.io/install_setup/configuration/security.html#id3).

**Para criar o usuário de teste:**

1. no painel esquerdo no portal do Azure, selecione **Azure Active Directory**, **Usuários** e **Todos os usuários**.
1. Na parte superior da tela, selecione **Novo usuário**.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. Na caixa **Nome**, insira **B.Fernandes**.  
   1. Na caixa **Nome de usuário**, insira o username@companydomain.extension. Por exemplo, B.Simon@contoso.com.
   1. Selecione a caixa de seleção **Mostrar Senha**. Anote a senha exibida na caixa **Senha**.
   1. Selecione **Criar**.

**Para criar o grupo:**

1. Selecione **Azure Active Directory** e **Grupos**.
1. Na parte superior da tela, selecione **Novo grupo**.
1. Nas **Propriedades do grupo**, siga estas etapas:
   1. Na caixa **Tipo de grupo**, selecione **Segurança**.
   1. Na caixa **Nome do grupo**, insira **LensesUsers**.
   1. Selecione **Criar**.
1. Selecione o grupo **LensesUsers** e copie a **ID do Objeto** (por exemplo, f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Você usará essa ID no Lenses para mapear os usuários do grupo para as [permissões corretas](https://docs.lenses.io/install_setup/configuration/security.html#id3).  

**Para atribuir o grupo ao usuário de teste:**

1. acesse **Azure Active Directory** e selecione **Usuários**.
1. Selecione o usuário de teste **B.Fernandes**.
1. Selecione **Grupos**.
1. Na parte superior da tela, escolha **Adicionar associações**.
1. Pesquise e escolha **LensesUsers**.
1. Clique em **Selecionar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Lenses.io.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Lenses.io**.
1. Na página de visão geral do aplicativo, na seção **Gerenciar**, selecione **Usuários e grupos**.

   ![Captura de tela que mostra o link "Usuários e grupos".](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**.

   ![Captura de tela que mostra o link Adicionar Usuário.](common/add-assign-user.png)

1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários. Em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário na lista. Em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione o botão **Atribuir**.

## <a name="configure-lensesio-sso"></a>Configurar o SSO do Lenses.io

Para configurar o SSO no portal do **Lenses.io**, instale o **XML de Metadados de Federação** baixado na instância do Lenses e [configure o Lenses para habilitar o SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Criar permissões de grupo de teste do Lenses.io

1. Para criar um grupo no Lenses, use a **ID do Objeto** do grupo **LensesUsers**. Essa é a ID que você copiou na [seção de criação](#create-an-azure-ad-test-user-and-group) do usuário.
1. Atribua as permissões desejadas a B.Fernandes.

Para obter mais informações, confira [Azure – Mapeamento de grupo do Lenses](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de SSO do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do Lenses.io no Painel de Acesso, você deverá ser conectado automaticamente ao portal do Lenses.io. Para saber mais, confira [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Configurar o SSO na instância do Lenses.io](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [Lista de tutoriais sobre como integrar aplicativos SaaS ao Azure AD](./tutorial-list.md)

- [O que é o acesso ao aplicativo e o SSO com o Azure AD?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure AD?](../conditional-access/overview.md)

- [Experimente o Lenses.io com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Lenses.io com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Darwinbox | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Darwinbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.openlocfilehash: 70c77caebfd8f9bfd36c7384255cf7b66416a379
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012026"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-darwinbox"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory com o Darwinbox

Neste tutorial, você aprenderá a integrar o Darwinbox ao Azure AD (Azure Active Directory). Quando integrar o Darwinbox ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Darwinbox.
* Permitir que os usuários sejam conectados automaticamente ao Darwinbox com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.
Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura habilitada para SSO (logon único) do Darwinbox.
> [!NOTE]
> Essa integração também está disponível para uso do ambiente de Nuvem do Governo dos EUA do Azure AD. Encontre esse aplicativo na Galeria de Aplicativos de Nuvem do Governo dos EUA do Azure AD e configure-o da mesma forma que na nuvem pública.


## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Darwinbox dá suporte ao SSO iniciado por **SP**

## <a name="adding-darwinbox-from-the-gallery"></a>Adicionar o Darwinbox da galeria

Para configurar a integração do Darwinbox ao Azure AD, você precisará adicionar o Darwinbox da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Darwinbox** na caixa de pesquisa.
1. Selecione **Darwinbox** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-darwinbox"></a>Configurar e testar o logon único do Azure AD para o Darwinbox

Configure e teste o SSO do Azure AD com o Darwinbox usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Darwinbox.

Para configurar e testar o SSO do Azure AD com o Darwinbox, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Darwinbox](#configure-darwinbox-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Darwinbox](#create-darwinbox-test-user)** para ter um equivalente de B.Fernandes no Darwinbox que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Darwinbox**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

   1. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.darwinbox.in/`

   1. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<SUBDOMAIN>.darwinbox.in/adfs/module.php/saml/sp/metadata.php/<CUSTOMID>`

      > [!NOTE]
      > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Darwinbox](https://darwinbox.com/contact-us.php) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Darwinbox**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Darwinbox.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Darwinbox**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-darwinbox-sso"></a>Configurar o SSO do Darwinbox

Para configurar o logon único no lado do **Darwinbox**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Darwinbox](https://darwinbox.com/contact-us.php). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-darwinbox-test-user"></a>Criar usuário de teste do Darwinbox

Nesta seção, você criará um usuário chamado B.Fernandes no Darwinbox. Trabalhe com a [equipe de suporte do Darwinbox](https://darwinbox.com/contact-us.php) para adicionar os usuários na plataforma do Darwinbox. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Darwinbox no Painel de Acesso, você deverá ser conectado automaticamente ao Darwinbox no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="test-sso-for-darwinbox-mobile"></a>Testar o SSO para o Darwinbox (Móvel)

1. Abra o aplicativo móvel Darwinbox. Clique em **Inserir a URL da Organização** agora para inserir a URL da sua organização na caixa de texto e clique no botão de seta.

    ![Captura de tela que mostra o aplicativo móvel "Darwinbox" com "Inserir URL da Organização" selecionado e um exemplo de organização e botão de "Seta" realçados.](media/darwinbox-tutorial/DarwinboxMobile01.jpg)

1. Caso você tenha vários domínios, clique no seu domínio.

    ![Captura de tela que mostra o botão "Escolher seu domínio" com um domínio de exemplo selecionado.](media/darwinbox-tutorial/DarwinboxMobile02.jpg)

1. Insira seu email do Azure AD no aplicativo Darwinbox e clique em **Avançar**.

    ![Captura de tela que mostra a entrada "Entrar" com o botão "Avançar" realçado.](media/darwinbox-tutorial/DarwinboxMobile03.jpg)

1. Insira sua senha do Azure AD no aplicativo Darwinbox e clique em **Entrar**.

    ![Captura de tela que mostra a tela "Inserir senha" com o botão "Avançar" realçado.](media/darwinbox-tutorial/DarwinboxMobile04.jpg)

1. Por fim, após entrar com sucesso, a home page do aplicativo será exibida.

    ![Aplicativo móvel Darwinbox](media/darwinbox-tutorial/DarwinboxMobile05.jpg)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Darwinbox com o Azure AD](https://aad.portal.azure.com/)
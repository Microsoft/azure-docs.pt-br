---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NS1 SSO for Azure | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NS1 SSO for Azure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.openlocfilehash: a7fad0af26d15bc77a52d55309c594937ba3be13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518612"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NS1 SSO for Azure

Neste tutorial, você aprenderá a integrar o NS1 SSO ao Azure AD (Azure Active Directory). Ao integrar o NS1 SSO for Azure ao Azure AD, você pode:

* Controlar quem tem acesso ao NS1 SSO for Azure no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao NS1 SSO for Azure com as respectivas contas do Azure AD.
* gerenciar suas contas em um local central, o portal do Azure.

Para saber mais sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, confira [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do NS1 SSO for Azure.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NS1 SSO for Azure dá suporte ao SSO iniciado por SP e IDP.
* Depois de configurar o NS1 SSO for Azure, você pode impor o controle de sessão. Isso protege contra exfiltração e infiltração dos dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>Adicionar o NS1 SSO for Azure por meio da galeria

Para configurar a integração do NS1 SSO for Azure ao Azure AD, é necessário adicionar o NS1 SSO for Azure da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante, ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, selecione **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **NS1 SSO for Azure** na caixa de pesquisa.
1. Selecione **NS1 SSO for Azure** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Configurar e testar o logon único do Azure AD para o NS1 SSO for Azure

Configure e teste o SSO do Azure AD com o NS1 SSO for Azure usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, estabeleça uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do NS1 SSO for Azure.

Aqui estão as etapas gerais para configurar e testar o SSO do Azure AD com o NS1 SSO for Azure:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.

    a. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B. Fernandes.

    b. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que B. Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do NS1 SSO for Azure](#configure-ns1-sso-for-azure-sso)** para definir as configurações de logon único no lado do aplicativo.

    a. **[Criar um usuário de teste do NS1 SSO for Azure](#create-an-ns1-sso-for-azure-test-user)** para ter um equivalente de B.Fernandes no NS1 SSO for Azure. Esse equivalente é vinculado à representação do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NS1 SSO for Azure**, localize a seção **Gerenciar**. Selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, selecione o ícone de lápis da **Configuração Básica de SAML** para editar as configurações.

   ![Captura de tela da página Configurar o logon único com SAML, com o ícone de lápis realçado](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IdP**, insira os valores para os seguintes campos:

    a. Na caixa de texto **Identificador**, digite a seguinte URL: `https://api.nsone.net/saml/metadata`

    b. Na caixa de texto **URL de Resposta**, digite uma URL que use o seguinte padrão: `https://api.nsone.net/saml/sso/<ssoid>`

1. Selecione **Definir URLs adicionais** e execute a seguinte etapa se desejar configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de Logon**, digite a seguinte URL: `https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > O valor URL de Resposta não é real. Atualize o valor da URL de Resposta com a URL de Resposta real. Entre em contato com a [equipe de suporte ao cliente do NS1 SSO for Azure](mailto:techops@nsone.net) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo NS1 SSO for Azure espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos na seção **Atributos do Usuário e Declarações** na página de integração de aplicativos. Na página **Definir logon único com SAML**, selecione o ícone de lápis para abrir a caixa de diálogo **Atributos de Usuário**.

    ![Captura de tela da seção Atributos do Usuário e Declarações com o ícone de lápis realçado](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selecione o nome do atributo para editar a declaração.

    ![Captura de tela da seção Atributos do Usuário e Declarações com o nome do atributo realçado](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. Selecione **Transformação**.

    ![Captura de tela da seção Gerenciar declaração, com Transformação realçada](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Na seção **Gerenciar transformação**, execute as seguintes etapas:

    ![Captura de tela da seção Gerenciar transformação, com vários campos realçados](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecione **ExactMailPrefix()** como **Transformação**.

    1. Selecione **user.userprincipalname** como **Parâmetro 1**.

    1. Selecione **Adicionar**.

    1. Clique em **Salvar**.

1. Na página **Configurar logon único com SAML**, na seção **Certificado de Autenticação SAML**, selecione o botão de copiar. Isso copia a **URL de metadados de federação de aplicativos** e salva em seu computador.

    ![Captura de tela do Certificado de Autenticação SAML, com o botão de cópia realçado](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, selecione **Azure Active Directory** > **Usuários** > **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:

   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor mostrado no campo **Senha**.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao NS1 SSO for Azure.

1. No portal do Azure, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NS1 SSO for Azure**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e selecione **Usuários e grupos**.

   ![Captura de tela da seção Gerenciar, com Usuários e grupos realçados](common/users-groups-blade.png)

1. Selecione **Adicionar usuário**. Na caixa de diálogo **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![Captura de tela da página Usuários e grupos, com a função Adicionar usuário destacada](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista de usuários. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Se você esperar um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, selecione a função apropriada para o usuário na lista. Em seguida, escolha o botão **Selecionar** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar Atribuição**, selecione **Atribuir**.

## <a name="configure-ns1-sso-for-azure-sso"></a>Configurar o SSO do NS1 SSO for Azure

Para configurar logon único no lado do NS1 SSO for Azure, você precisa enviar a URL de metadados de federação de aplicativos para a [equipe de suporte do NS1 SSO for Azure](mailto:techops@nsone.net). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Criar um usuário de teste do NS1 SSO for Azure

Nesta seção, você criará um usuário com o nome B.Fernandes no NS1 SSO for Azure. Trabalhe com a equipe de suporte do NS1 SSO for Azure para adicionar os usuários na plataforma do NS1 SSO for Azure. Você não pode usar o logon único antes de criar e ativar usuários.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará a configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco do NS1 SSO for Azure no Painel de Acesso, você deverá ser conectado automaticamente ao NS1 SSO for Azure, para o qual configurou o SSO. Para obter mais informações, confira [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais para a integração de aplicativos SaaS ao Azure Active Directory](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o NS1 SSO for Azure com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
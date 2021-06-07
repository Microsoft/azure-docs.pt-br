---
title: 'Tutorial: Integração do Azure Active Directory com o SecureW2 JoinNow Connector | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o SecureW2 JoinNow Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.openlocfilehash: 024de5acfbc4e6c4aaa510de84c0bceb6bde2b3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92672966"
---
# <a name="tutorial-integrate-securew2-joinnow-connector-with-azure-active-directory"></a>Tutorial: Integrar o SecureW2 JoinNow Connector ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o SecureW2 JoinNow Connector ao Azure AD (Azure Active Directory). Ao integrar o SecureW2 JoinNow Connector ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao SecureW2 JoinNow Connector.
* Permitir que seus usuários entrem automaticamente no SecureW2 JoinNow Connector com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do SecureW2 JoinNow Connector.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O SecureW2 JoinNow Connector é compatível com SSO iniciado por **SP**





## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Adicionando o SecureW2 JoinNow Connector da galeria

Para configurar a integração do SecureW2 JoinNow Connector com o Azure AD, você precisará adicionar o SecureW2 JoinNow Connector da galeria para a lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **SecureW2 JoinNow Connector** na caixa de pesquisa.
1. Selecione **SecureW2 JoinNow Connector** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Configure e teste o SSO do Azure AD com o SecureW2 JoinNow Connector usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no SecureW2 JoinNow Connector.

Para configurar e testar o SSO do Azure AD com o SecureW2 JoinNow Connector, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
2. **[Configurar o SSO do SecureW2 JoinNow Connector](#configure-securew2-joinnow-connector-sso)** – para definir as configurações de logon único no lado do aplicativo.
3. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
4. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
5. **[Criar usuário de teste do SecureW2 JoinNow Connector](#create-securew2-joinnow-connector-test-user)** – para ter um equivalente de B.Fernandes no SecureW2 JoinNow Connector que esteja vinculado à representação do usuário no Azure AD.
6. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **SecureW2 JoinNow Connector**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do SecureW2 JoinNow Connector](mailto:support@securew2.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Assinatura SAML**, localize **XML de Metadados** e selecione **Baixar** para baixar o certificado e salvá-lo em seu computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o SecureW2 JoinNow Connector**, copie as URLs apropriadas com base em seu requisito.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="configure-securew2-joinnow-connector-sso"></a>Configurar SSO do SecureW2 JoinNow Connector

Para configurar o logon único no lado do **SecureW2 JoinNow Connector**, é necessário enviar o **XML de metadados** baixado e as URLs adequadas copiadas do portal do Azure para a [equipe de suporte do SecureW2 JoinNow Connector](mailto:support@securew2.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.
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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo acesso ao SecureW2 JoinNow Connector.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **SecureW2 JoinNow Connector**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="create-securew2-joinnow-connector-test-user"></a>Criar usuário de teste do SecureW2 JoinNow Connector

Nesta seção, você cria um usuário chamado Brenda Fernandes no SecureW2 JoinNow Connector. Trabalhe com a [equipe de suporte do SecureW2 JoinNow Connector](mailto:support@securew2.com) para adicionar os usuários na plataforma do SecureW2 JoinNow Connector. Os usuários devem ser criados e ativados antes de usar o logon único.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clica no bloco do SecureW2 JoinNow Connector no Painel de Acesso, deve entrar automaticamente no SecureW2 JoinNow Connector para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
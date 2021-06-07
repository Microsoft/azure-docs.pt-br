---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao Cyara CX Assurance Platform | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Cyara CX Assurance Platform.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: 18a0037f2af221101b4202883e5c145471af1183
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cyara-cx-assurance-platform"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory com o Cyara CX Assurance Platform

Neste tutorial, você aprenderá a integrar o Cyara CX Assurance Platform ao Azure AD (Azure Active Directory). Ao integrar o Cyara CX Assurance Platform ao Azure AD, você pode:

* Controle no Azure AD quem tem acesso ao Cyara CX Assurance Platform.
* Permitir que os usuários sejam conectados automaticamente ao Cyara CX Assurance Platform com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Cyara CX Assurance Platform.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Cyara CX Assurance Platform é compatível com **SSO iniciado por** de IDP
* Após configurar o Cyara CX Assurance Platform, você poderá impor controle de sessão, que protege contra exfiltração e infiltração de dados confidenciais de sua organização em tempo real. O controle da sessão é estendido do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-cyara-cx-assurance-platform-from-the-gallery"></a>Adicionar o Cyara CX Assurance Platform por meio da galeria

Para configurar a integração do Cyara CX Assurance Platform ao Azure AD, você precisará adicionar o Cyara CX Assurance Platform da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Cyara CX Assurance Platform** na caixa de pesquisa.
1. Selecione **Cyara CX Assurance Platform** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cyara-cx-assurance-platform"></a>Configurar e testar o logon único do Azure AD para o Cyara CX Assurance Platform

Configure e teste o SSO do Azure AD com o Cyara CX Assurance Platform usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Cyara CX Assurance Platform.

Para configurar e testar o SSO do Azure AD com o Cyara CX Assurance Platform, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Cyara CX Assurance Platform](#configure-cyara-cx-assurance-platform-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do Cyara CX Assurance Platform](#create-cyara-cx-assurance-platform-test-user)** – para ter um equivalente de B.Fernandes no na plataforma de garantia que esteja vinculado à declaração de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Cyara CX Assurance Platform**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na página **Configurar logon único com SAML**, insira os valores para os seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.cyaraportal.us/cyarawebidentity/identity/<provider>`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.cyaraportal.us/cyarawebidentity/identity/<provider>/Acs`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Contate a [equipe de suporte ao cliente do Cyara CX Assurance Platform](mailto:support@cyara.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o Cyara CX Assurance Platform**, copie as URLs apropriadas conforme suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Cyara CX Assurance Platform.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Cyara CX Assurance Platform**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cyara-cx-assurance-platform-sso"></a>Configurar o SSO do Cyara CX Assurance Platform

Para configurar o logon único no lado do **Cyara CX Assurance Platform**, você precisa enviar o valor **impressão digital** e URLs copiadas apropriadas do portal do Azure para a equipe de suporte do [Cyara CX Assurance Platform](mailto:support@cyara.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-cyara-cx-assurance-platform-test-user"></a>Criar usuário de teste do Cyara CX Assurance Platform

Nesta seção, você criará um usuário chamado Brenda Fernandes no Cyara CX Assurance Platform. Trabalhe com a equipe de suporte do [Cyara CX Assurance Platform](mailto:support@cyara.com) para adicionar os usuários na plataforma do Cyara CX Assurance Platform. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Cyara CX Assurance Platform no Painel de Acesso, você deverá entrar automaticamente no Cyara CX Assurance Platform para a qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Cyara CX Assurance Platform com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Cyara CX Assurance Platform com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
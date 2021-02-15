---
title: 'Tutorial: Integração do Azure Active Directory com o Jive | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Jive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 3d15e5e13b2b0defe45fd45450aee7c262052b4f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459405"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Jive

Neste tutorial, você aprenderá como integrar o Jive ao Azure AD (Azure Active Directory). Ao integrar o Jive ao Azure AD, você poderá:

* Controlar quem tem acesso ao Jive no Azure AD.
* Permitir que os usuários sejam conectados automaticamente ao Jive com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Jive.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Jive é compatível com o SSO iniciado por **SP**
* O Jive dá suporte ao [**Provisionamento** automatizado de usuários](jive-provisioning-tutorial.md)
* Após configurar o Jive, você poderá impor controles de sessão, que protegem o vazamento e a infiltração de dados confidenciais de sua organização em tempo real. Os controles da sessão são estendidos do Acesso Condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-jive-from-the-gallery"></a>Adicionar Fuse da galeria

Para configurar a integração do Jive com o Azure AD, você precisará adicionar o Jive à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory** .
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos** .
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo** .
1. Na seção **Adicionar da galeria** , digite **Jive** na caixa de pesquisa.
1. Selecione **Jive** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-jive"></a>Configurar e testar o logon único do Azure AD para o Jive

Configure e teste o SSO do Azure AD com o Jive usando uma usuária de teste chamada **B.Fernandes** . Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Jive.

Para configurar e testar o SSO do Azure AD com o Jive, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Jive](#configure-jive-sso)** – para configurar o logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Jive](#create-jive-test-user)** – para ter um equivalente de B.Fernandes no Jive que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Jive** , localize a seção **Gerenciar** e selecione **logon único** .
1. Na página **Selecionar um método de logon único** , escolha **SAML** .
1. Na página **Configurar o logon único com o SAML** , clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML** , insira os valores para os seguintes campos:

   a. Na caixa de texto **URL de Logon** , digite uma URL usando o seguinte padrão: `https://<instance name>.jivecustom.com`

   b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Jive](https://www.jivesoftware.com/services-support/) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na página **Configurar Logon Único com SAML** , na seção **Certificado de Autenticação SAML** , clique em **Baixar** para baixar o **XML de Metadados de Federação** usando as opções fornecidas de acordo com seus requisitos e salve-o no computador.

    ![O link de download do Certificado](common/metadataxml.png)

6. Na seção **Configurar Jive** , copie a URL apropriada, de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

    a. URL de logon

    b. Identificador do Azure AD

    c. URL de logoff

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory** , **Usuários** e, em seguida, **Todos os usuários** .
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário** , siga estas etapas:
   1. No campo **Nome** , insira `B.Simon`.  
   1. No campo **Nome de usuário** , insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha** .
   1. Clique em **Criar** .

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Jive.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos** .
1. Na lista de aplicativos, escolha **Jive** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos** .

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição** .

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos** , selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função** , escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição** , clique no botão **Atribuir** .

## <a name="configure-jive-sso"></a>Configurar o SSO do Jive

1. Para configurar o logon único no lado do **Jive** , faça logon em seu locatário do Jive como administrador.

1. No menu na parte superior, clique em **SAML** .

    ![A captura de tela mostra a guia SAML com a opção Habilitado selecionada.](./media/jive-tutorial/tutorial_jive_002.png)

    a. Selecione **Habilitado** na guia **Geral** .

    b. Clique no botão **SALVAR TODAS AS CONFIGURAÇÕES SAML** .

1. Navegue até a guia **METADADOS IDP** .

    ![A captura de tela mostra a guia SAML com a opção METADADOS IDP selecionada.](./media/jive-tutorial/tutorial_jive_003.png)

    a. Copie o conteúdo do arquivo XML de metadados baixado e cole-o na caixa de texto **Metadados do IDP (Provedor de Identidade)** .

    b. Clique no botão **SALVAR TODAS AS CONFIGURAÇÕES SAML** .

1. Selecione a guia **MAPEAMENTO DE ATRIBUTO DE USUÁRIO** .

    ![A captura de tela mostra a guia SAML com a opção MAPEAMENTO DE ATRIBUTOS DO USUÁRIO selecionada.](./media/jive-tutorial/tutorial_jive_004.png)

    a. Na caixa de texto **Email** , copie e cole o nome de atributo do valor **email** .

    b. Na caixa de texto **Nome** , copie e cole o nome do atributo do valor **nome** .

    c. Na caixa de texto **Sobrenome** , copie e cole o nome do atributo do valor **sobrenome** .

### <a name="create-jive-test-user"></a>Criar usuário de teste do Jive

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no Jive. O Jive dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](jive-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

Se precisar criar um usuário manualmente, trabalhe com a [Equipe de suporte ao cliente do Jive](https://www.jivesoftware.com/services-support/) para adicionar os usuários à plataforma Jive.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Jive no Painel de Acesso, você deverá entrar automaticamente no Jive no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Jive com o Azure AD](https://aad.portal.azure.com/)

- [O que é controle de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Configurar Provisionamento de Usuário](jive-provisioning-tutorial.md)

- [Como proteger o Jive com visibilidade e controles avançados](/cloud-app-security/proxy-intro-aad)
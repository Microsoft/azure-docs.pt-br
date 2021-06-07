---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Onit | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Onit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 40ed33021a892e140dfff47417137046557c0aef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92522352"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Onit

Neste tutorial, você aprenderá a integrar o Onit ao Azure AD (Azure Active Directory). Quando integrar o Onit ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Onit.
* Permitir que os usuários sejam conectados automaticamente ao Onit com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Onit.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Onit é compatível com o SSO iniciado por **IdP**

## <a name="adding-onit-from-the-gallery"></a>Adicionar o Onit da galeria

Para configurar a integração do Onit ao Azure AD, você precisará adicionar o Onit à sua lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Onit** na caixa de pesquisa.
1. Selecione **Onit** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Configurar e testar logon único do Azure AD para o Onit

Configure e teste o SSO do Azure AD com o Onit usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Onit.

Para configurar e testar o SSO do Azure AD com o Onit, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Onit](#configure-onit-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Onit](#create-onit-test-user)** – para ter um equivalente de B.Fernandes no Onit que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Onit**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<sub-domain>.onit.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do Onit](https://www.onit.com/support) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o Onit**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Onit.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Onit**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-onit-sso"></a>Configurar SSO do Onit

1. Em outra janela do navegador da Web, faça logon em seu site de empresa Onit como um administrador.

2. No menu na parte superior, clique em **Administração**.
   
    ![Uma captura de tela que mostra o menu na parte superior da página "Testar o MS SSO" com a ação "Administração" selecionada.](./media/onit-tutorial/IC791174.png "Administração")

3. Clique em **Editar Empresa**.
   
    ![Editar Empresa](./media/onit-tutorial/IC791175.png "Editar Empresa")
   
4. Clique na guia **Segurança** .
    
    ![Editar Dados da Empresa](./media/onit-tutorial/IC791176.png "Editar Informações da Empresa")

5. Na guia **Segurança** , realize as seguintes etapas:

    ![Logon único](./media/onit-tutorial/IC791177.png "Logon Único")

    a. Como **Estratégia de Autenticação**, selecione **Logn Único e Senha**.
    
    b. Na caixa de texto **URL de Destino de IdP**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **URL de logoff de IdP**, cole o valor da **URL de logoff** copiado do portal do Azure.

    d. Na caixa de texto **Impressão Digital do Certificado IdP (SHA1)** , cole o valor da **Impressão Digital** do certificado copiado do Portal do Azure.

### <a name="create-onit-test-user"></a>Criar um usuário de teste do Onit

Para permitir que os usuários do AD do Azure façam logon no Onit, eles devem ser provisionados no Onit. No caso do Onit, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento de usuários, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **Onit** como administrador.

2. Clique em **Adicionar Usuário**.

    ![Administração](./media/onit-tutorial/IC791180.png "Administração")

3. Na página do diálogo **Adicionar usuário** , realize as seguintes etapas:

    ![Adicionar Usuário](./media/onit-tutorial/IC791181.png "Adicionar usuário")

    a. Digite o **Nome** e **Endereço de Email** de uma conta válida do Azure AD que você deseja provisionar nas caixas de texto relacionadas.

    b. Clique em **Criar**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory recebe um email e segue um link para confirmar sua conta antes que ela se torne ativa.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Onit no Painel de Acesso, você será conectado automaticamente ao Onit, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Onit com o Azure AD](https://aad.portal.azure.com/)
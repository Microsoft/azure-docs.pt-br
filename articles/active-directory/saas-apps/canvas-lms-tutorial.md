---
title: 'Tutorial: Integração do Azure Active Directory com o Canvas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Canvas.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653000"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Tutorial: Integração do Azure Active Directory com o Canvas

Neste tutorial, você aprenderá a integrar o Canvas ao Azure AD (Azure Active Directory). Quando você integrar o Canvas ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Canvas.
* Permitir que os usuários sejam conectados automaticamente ao Canvas com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:
 
* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Canvas.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o logon único do Azure AD em um ambiente de teste.

* O Canvas é compatível com o SSO iniciado por **SP**

## <a name="add-canvas-from-the-gallery"></a>Adicionar o Canvas da galeria

Para configurar a integração do Canvas ao Azure AD, você precisa adicionar o Canvas à lista de aplicativos SaaS gerenciados por meio da galeria.

1. Entre no portal do Azure usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Canvas** na caixa de pesquisa.
1. Selecione **Canvas** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Configurar e testar o SSO do Azure AD para o Canvas

Configure e teste o SSO do Azure AD com o Canvas usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Canvas.

Para configurar e testar o SSO do Azure AD com o Canvas, execute as seguintes etapas:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Canvas](#configure-canvas-sso)** – para configurar o logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Canvas](#create-canvas-test-user)** – para ter um equivalente de B.Fernandes no Canvas que esteja vinculado à representação do usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No portal do Azure, na página de integração de aplicativos do **Canvas**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

4. Na seção **Configuração básica de SAML**, realize as seguintes etapas:

    ![Informações de Domínio e URLs do Canvas para logon único](common/sp-identifier.png)

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<tenant-name>.instructure.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do Canvas](https://community.canvaslms.com/community/help) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

5. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

6. Na seção **Certificado de Autenticação SAML**, copie a **IMPRESSÃO DIGITAL** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

7. Na seção **Configurar o Canvas**, copie a URL apropriada de acordo com suas necessidades.

    ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B.Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B.Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Canvas.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Canvas**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.
1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.
1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando que uma função seja atribuída aos usuários, escolha-a na lista suspensa **Selecionar uma função**. Se nenhuma função tiver sido configurada para esse aplicativo, você verá a função &quot;Acesso Padrão&quot; selecionada.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name=&quot;configure-canvas-sso&quot;></a>Configurar o SSO do Canvas

1. Em outra janela do navegador da Web, faça logon em seu site de empresa do Canvas como administrador.

2. Vá para **Cursos \> Contas Gerenciadas \> Microsoft**.

    ![Tela](./media/canvas-lms-tutorial/ic775990.png &quot;Tela")

3. No painel de navegação à esquerda, selecione **Autenticação** e clique em **Adicionar Nova Config. do SAML**.

    ![Autenticação](./media/canvas-lms-tutorial/ic775991.png "Autenticação")

4. Na página de integração atual, execute as seguintes etapas:

    ![Integração Atual](./media/canvas-lms-tutorial/save.png "Integração Atual")

    a. Na caixa de texto **ID da Entidade de IdP**, cole o valor do **Identificador do Azure AD** que você copiou do portal do Azure.

    b. Na caixa de texto **URL de Logon**, cole o valor da **URL de Logon** copiado no portal do Azure.

    c. Na caixa de texto **URL de Logoff**, cole o valor da **URL de Logoff** copiado no portal do Azure.

    d. Na caixa de texto **Link Alteração de Senha**, cole o valor da **URL de Alteração de Senha** copiado no portal do Azure.

    e. Na caixa de texto **Impressão Digital do Certificado**, cole o valor de **Impressão Digital** do certificado copiado do Portal do Azure.

    f. Na lista **Atributo de Logon**, selecione **NameID**.

    g. Na lista **Formato de Identificador**, selecione **emailAddress**.

    h. Clique em **Salvar Configurações de Autenticação**.

### <a name="create-canvas-test-user"></a>Criar um usuário de teste do Canvas

Para permitir que os usuários do Azure AD façam logon no Canvas, eles devem ser provisionados no Canvas. No caso do Canvas, o provisionamento de usuário é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Canvas** .

2. Vá para **Cursos \> Contas Gerenciadas \> Microsoft**.

   ![Tela](./media/canvas-lms-tutorial/ic775990.png "Tela")

3. Clique em **Usuários**.

   ![Captura de tela que mostra o menu Painel da Tela com Usuários selecionado.](./media/canvas-lms-tutorial/ic775995.png "Usuários")

4. Clique em **Adicionar Novo Usuário**.

   ![Captura de tela que mostra o botão Adicionar um Usuário.](./media/canvas-lms-tutorial/ic775996.png "Usuários")

5. Na página da caixa de diálogo Adicionar um Novo Usuário, execute as seguintes etapas:

   ![Adicionar Usuário](./media/canvas-lms-tutorial/ic775997.png "Adicionar usuário")

   a. Na caixa de texto **Nome Completo**, insira o nome de usuário, como **BrendaFernandes**.

   b. Na caixa de texto **Email**, insira o email do usuário como **brendafernandes\@contoso.com**.

   c. Na caixa de texto **Logon**, insira o endereço de email do Azure AD do usuário, como **brendafernandes\@contoso.com**.

   d. Selecione **Enviar email ao usuário sobre a criação desta conta**.

   e. Clique em **Adicionar Usuário**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Canvas ou as APIs fornecidas pelo Canvas para provisionar as contas de usuário do Azure AD.

### <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará a configuração de logon único do Azure AD com as opções a seguir. 

* Clique em **Testar este aplicativo** no portal do Azure. Isso vai redirecionar você à URL de Logon do Canvas na qual poderá iniciar o fluxo de logon. 

* Acesse a URL de Logon do Canvas diretamente e inicie o fluxo de logon nela.

* Você pode usar os Meus Aplicativos da Microsoft. Quando você clicar no bloco do Canvas em Meus Aplicativos, você deverá ser conectado automaticamente ao Canvas para o qual configurou o SSO. Para obter mais informações sobre os Meus Aplicativos, confira [Introdução aos Meus Aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Próximas etapas

Depois de configurar o Canvas, você poderá impor controles de sessão, que fornecem proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
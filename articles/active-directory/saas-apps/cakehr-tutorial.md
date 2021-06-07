---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CakeHR | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o CakeHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: 08e028ba057ad57f3d600bc59bf7595c0b1d354c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao CakeHR

Neste tutorial, você aprenderá a integrar o CakeHR ao Azure AD (Azure Active Directory). Ao integrar o CakeHR ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao CakeHR.
* Permitir que os usuários sejam conectados automaticamente ao CakeHR com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do CakeHR habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O CakeHR dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-cakehr-from-the-gallery"></a>Como adicionar o CakeHR por meio da galeria

Para configurar a integração do CakeHR ao Azure AD, você precisará adicionar o CakeHR por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **CakeHR** na caixa de pesquisa.
1. Selecione **CakeHR** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>Configurar e testar o logon único do Azure AD para o CakeHR

Configure e teste o SSO do Azure AD com o CakeHR usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no CakeHR.

Para configurar e testar o SSO do Azure AD com o CakeHR, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do CakeHR](#configure-cakehr-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do CakeHR](#create-cakehr-test-user)** – para ter um equivalente de B.Fernandes no CakeHR que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **CakeHR**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<yourcakedomain>.cake.hr/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<yourcakedomain>.cake.hr/services/saml/consume`
    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Resposta e a URL de Logon reais. Contate a [equipe de suporte ao cliente do CakeHR](mailto:info@cake.hr) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o valor de **IMPRESSÃO DIGITAL** e salve-o no Bloco de notas.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o CakeHR**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao CakeHR.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **CakeHR**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-cakehr-sso"></a>Configurar o SSO do CakeHR

1. Para automatizar a configuração no CakeHR, é necessário instalar a **extensão do navegador Entrada Segura dos Meus Aplicativos** clicando em **Instalar a extensão**.

    ![Extensão Meus Aplicativos](common/install-myappssecure-extension.png)

1. Após a adição da extensão ao navegador, um clique em **Configurar o CakeHR** direcionará você ao aplicativo CakeHR. Nele, forneça as credenciais de administrador para entrar no CakeHR. A extensão do navegador configurará automaticamente o aplicativo e automatizará as etapas de 3 a 5.

    ![Configuração da instalação](common/setup-sso.png)

1. Caso deseje configurar o CakeHR manualmente, abra uma nova janela do navegador da Web, entre em seu site de empresa do CakeHR como administrador e execute as seguintes etapas:

1. No canto superior direito da página, clique em **Perfil** e navegue até **Configurações**.

    ![Captura de tela mostra o Perfil com as Configurações selecionadas.](./media/cakehr-tutorial/config01.png)

1. No lado esquerdo da barra de menus, clique em **INTEGRAÇÕES** > **SSO DO SAML** e execute as seguintes etapas:

    ![Captura de tela que mostra o painel de Configuração, no qual você executa essas etapas.](./media/cakehr-tutorial/config02.png)

    a. Na caixa de texto **ID da Entidade**, digite `cake.hr`.

    b. Na caixa de texto **URL de Autenticação**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Na caixa de texto **Impressão digital da chave (formato SHA1)** , cole o valor de **IMPRESSÃO DIGITAL** copiado do portal do Azure.

    d. Marque a caixa **Habilitar Logon Único**.

    e. Clique em **Save** (Salvar).

### <a name="create-cakehr-test-user"></a>Criar um usuário de teste do CakeHR

Para permitir que os usuários do Azure AD entrem no CakeHR, eles precisam ser provisionados no CakeHR. No CakeHR, o provisionamento é uma tarefa manual.

**Para provisionar uma conta de usuário, execute as seguintes etapas:**

1. Entre no CakeHR como Administrador de segurança.

2. No lado esquerdo da barra de menus, clique em **EMPRESA** > **ADICIONAR**.

    ![Captura de tela que mostra o CakeHR com a EMPRESA e ADICIONAR selecionados.](./media/cakehr-tutorial/config03.png)

3. No pop-up **Adicionar novo funcionário**, execute as seguintes etapas:

     ![Captura de tela que mostra Adicionar novo funcionário, em que executa essas etapas.](./media/cakehr-tutorial/config04.png)

    a. Na caixa de texto **Nome completo**, insira o nome do usuário como B.Fernandes.

    b. Na caixa de texto **Email de trabalho**, insira o email do usuário como `B.Simon@contoso.com`.

    c. Clique em **CRIAR CONTA**.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do CakeHR no Painel de Acesso, você deverá ser conectado automaticamente ao CakeHR, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o CakeHR com o Azure AD](https://aad.portal.azure.com/)
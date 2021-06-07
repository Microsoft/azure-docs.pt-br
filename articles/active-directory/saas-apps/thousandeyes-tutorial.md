---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ThousandEyes | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o ThousandEyes.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.openlocfilehash: aa3c5115a5255d30decbc66691878ffbe2579a06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514581"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao ThousandEyes

Neste tutorial, você aprenderá a integrar o ThousandEyes ao Azure AD (Azure Active Directory). Ao integrar o ThousandEyes ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao ThousandEyes.
* Permitir que os usuários sejam conectados automaticamente ao ThousandEyes com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do ThousandEyes.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O ThousandEyes dá suporte ao SSO iniciado por **SP e IdP**
* O ThousandEyes é compatível com [provisionamento de usuário **Automatizado**](./thousandeyes-provisioning-tutorial.md)

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-thousandeyes-from-the-gallery"></a>Adicionar o ThousandEyes da galeria

Para configurar a integração do ThousandEyes com o Azure AD, é necessário adicionar o ThousandEyes da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **ThousandEyes** na caixa de pesquisa.
1. Selecione **ThousandEyes** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Configurar e testar o logon único do Azure AD para o ThousandEyes

Configure e teste o SSO do Azure AD com o ThousandEyes usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no ThousandEyes.

Para configurar e testar o SSO do Azure AD com o ThousandEyes, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do ThousandEyes](#configure-thousandeyes-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do ThousandEyes](#create-thousandeyes-test-user)** – para ter um equivalente de B.Fernandes no ThousandEyes que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **ThousandEyes**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de Logon**, digite uma URL: `https://app.thousandeyes.com/login/sso`

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o ThousandEyes**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao ThousandEyes.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **ThousandEyes**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-thousandeyes-sso"></a>Configurar o SSO do ThousandEyes

1. Em outra janela do navegador da Web, entre em seu site de empresa do **ThousandEyes** como administrador.

2. No menu na parte superior, clique em **Configurações**.

    ![A captura de tela mostra o site do ThousandEyes com a opção Configurações selecionada.](./media/thousandeyes-tutorial/ic790066.png "Configurações")

3. Clique em **Conta**

    ![A captura de tela mostra a opção Conta selecionada no menu Configurações.](./media/thousandeyes-tutorial/ic790067.png "Conta")

4. Clique na guia **Segurança e Autenticação**.

    ![Segurança e Autenticação](./media/thousandeyes-tutorial/ic790068.png "Segurança e Autenticação")

5. Na seção **Configurações de Logon Único** , realize as seguintes etapas:

    ![Configurações de Logon Único](./media/thousandeyes-tutorial/ic790069.png "Configurações de Logon Único")

    a. Selecione **Habilitar Logon Único**.

    b. Na caixa de texto **URL da Página de Logon**, cole a **URL de Logon** copiada do portal do Azure.

    c. Na caixa de texto **URL da Página de Logoff**, cole a **URL de Logoff** copiada do portal do Azure.

    d. Na caixa de texto **Emissor do Provedor de Identidade**, cole o **Identificador do Azure AD** copiado do portal do Azure.

    e. Em **Certificado de Verificação**, clique em **Escolher Arquivo** e carregue o certificado que você baixou do Portal do Azure.

    f. Clique em **Save** (Salvar).

### <a name="create-thousandeyes-test-user"></a>Criar usuário de teste do ThousandEyes

O objetivo desta seção é criar um usuário chamado Brenda Fernandes no ThousandEyes. O ThousandEyes dá suporte ao provisionamento automático do usuário, que está habilitado por padrão. Você pode encontrar mais detalhes [ aqui ](thousandeyes-provisioning-tutorial.md) sobre como configurar o provisionamento automático de usuários.

**Se você precisar criar o usuário manualmente, execute as seguintes etapas:**

1. Entre em seu site empresarial do ThousandEyes como um administrador.

2. Clique em **Configurações**.

    ![A captura de tela mostra o site do ThousandEyes com a opção Configurações selecionada.](./media/thousandeyes-tutorial/ic790066.png "Configurações")

3. Clique em **Conta**.

    ![A captura de tela mostra a opção Conta selecionada no menu Configurações.](./media/thousandeyes-tutorial/ic790067.png "Conta")

4. Clique na guia **Contas e Usuários**.

    ![Contas e Usuários](./media/thousandeyes-tutorial/IC790073.png "Contas e Usuários")

5. Na seção **Adicionar Usuários e Contas**, realize as seguintes etapas:

    ![Adicionar Contas de Usuário](./media/thousandeyes-tutorial/IC790074.png "Adicionar contas de usuário")

    a. Na caixa de texto **Nome**, digite o nome do usuário como **B.Fernandes**.

    b. Na caixa de texto **Email**, digite o email do usuário como b.simon@contoso.com.

    b. Clique em **Adicionar Novo Usuário à Conta**.

    > [!NOTE]
    > O titular da conta do Azure Active Directory receberá um email com um link para confirmar e ativar a conta.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ThousandEyes ou as APIs fornecidas pelo ThousandEyes para provisionar as contas de usuário do Azure Active Directory.


## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do ThousandEyes no Painel de Acesso, você deverá ser conectado automaticamente ao ThousandEyes para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o ThousandEyes com o Azure AD](https://aad.portal.azure.com/)

- [Configurar Provisionamento de Usuário](./thousandeyes-provisioning-tutorial.md)
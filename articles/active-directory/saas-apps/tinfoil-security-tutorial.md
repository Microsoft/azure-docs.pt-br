---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TINFOIL SECURITY | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o TINFOIL SECURITY.
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
ms.openlocfilehash: 5c2ac2c7bb1b60c87075a1bc62241edab2fc310e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516283"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tinfoil-security"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao TINFOIL SECURITY

Neste tutorial, você aprenderá a integrar o TINFOIL SECURITY ao Azure AD (Azure Active Directory). Ao integrar o TINFOIL SECURITY ao Azure AD, você pode:

* No Azure AD, controlar quem tem acesso ao TINFOIL SECURITY.
* Permitir que os usuários sejam conectados automaticamente ao TINFOIL SECURITY com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do TINFOIL SECURITY.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O TINFOIL SECURITY é compatível com SSO iniciado por **IDP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-tinfoil-security-from-the-gallery"></a>Adicionar o TINFOIL SECURITY da galeria

Para configurar a integração do TINFOIL SECURITY com o Azure AD, é necessário adicionar o TINFOIL SECURITY da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **TINFOIL SECURITY** na caixa de pesquisa.
1. Selecione **TINFOIL SECURITY** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tinfoil-security"></a>Configurar e testar o logon único do Azure AD para o TINFOIL SECURITY

Configure e teste o SSO do Azure AD com o TINFOIL SECURITY usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do TINFOIL SECURITY.

Para configurar e testar o SSO do Azure AD com o TINFOIL SECURIT, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do TINFOIL SECURITY](#configure-tinfoil-security-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do TINFOIL SECURITY](#create-tinfoil-security-test-user)** – para ter um equivalente de B.Fernandes no TINFOIL SECURITY que esteja vinculado à representação de usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **TINFOIL SECURITY**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. O aplicativo Visitly espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Visitly espera que mais alguns atributos sejam passados novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    > [!NOTE]
    > Você obterá o valor accountid, que será explicado posteriormente no tutorial.

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o TINFOIL SECURITY**, copie as URLs adequado de acordo com seus requisitos.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure, concedendo acesso ao TINFOIL SECURITY.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **TINFOIL SECURITY**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-tinfoil-security-sso"></a>Configurar o SSO do TINFOIL SECURITY

1. Em uma janela diferente do navegador da Web, entre no site da empresa TINFOIL SECURITY como administrador.

1. Na barra de ferramentas na parte superior, clique em **Minha Conta**.

    ![Painel](./media/tinfoil-security-tutorial/ic798971.png "Painel")

1. Clique em **Segurança**.

    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "Segurança")

1. Na página de configuração **Logon Único** , realize as seguintes etapas:

    ![Logon único](./media/tinfoil-security-tutorial/ic798973.png "Logon Único")

    a. Selecione **Habilitar SAML**.

    b. Clique em **Configuração Manual**.

    c. Na caixa de texto **URL Post do SAML**, cole o valor da **URL de Logon** copiado do portal do Azure

    d. Na caixa de texto **Impressão Digital do Certificado SAML**, cole o valor de **Impressão Digital** copiado da seção **Certificado de Autenticação SAML**.
  
    e. Copie o valor da **ID da Sua Conta** e cole-o na caixa de texto **Atributo da Fonte** na seção **Atributos e Declarações do Usuário** no portal do Azure.

    f. Clique em **Save** (Salvar).

### <a name="create-tinfoil-security-test-user"></a>Criar um usuário de teste do TINFOIL SECURITY

Para permitir que os usuários do Azure AD entrem no TINFOIL SECURITY, eles devem ser provisionados no TINFOIL SECURITY. No caso do TINFOIL SECURITY, o provisionamento é uma tarefa manual.

**Para provisionar um usuário, siga as seguintes etapas:**

1. Se o usuário fizer parte de uma conta Enterprise, será necessário [contatar a equipe de suporte do TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para que a conta de usuário seja criada.

1. Se o usuário for um usuário de SaaS do TINFOIL SECURITY regular, ele poderá adicionar um colaborador a qualquer um dos sites do usuário. Isso dispara um processo para enviar um convite ao email especificado para criar uma nova conta de usuário do TINFOIL SECURITY.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação de conta de usuário do TINFOIL SECURITY ou APIs fornecidas pelo TINFOIL SECURITY para provisionar as contas de usuário do Azure AD.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do TINFOIL SECURITY no Painel de Acesso, você deverá ser conectado automaticamente ao TINFOIL SECURITY no qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o TINFOIL SECURITY com o Azure AD](https://aad.portal.azure.com/)
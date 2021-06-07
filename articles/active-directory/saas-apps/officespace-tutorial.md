---
title: 'Tutorial: Integração de SSO (logon único) do Azure Active Directory ao OfficeSpace Software | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o OfficeSpace Software.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: bb994e154f410fb15b66588b41688326509a8f4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518523"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-officespace-software"></a>Tutorial: Integração de SSO (logon único) do Azure Active Directory ao OfficeSpace Software

Neste tutorial, você aprende a integrar o OfficeSpace Software ao Azure AD (Azure Active Directory). Quando você integra o OfficeSpace Software ao Azure AD, é possível:

* Controlar no Azure AD quem tem acesso ao OfficeSpace Software.
* Permitir que seus usuários entrem automaticamente no OfficeSpace Software com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura do OfficeSpace Software habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O OfficeSpace Software dá suporte a SSO iniciado por **SP**


* O OfficeSpace Software dá suporte a provisionamento de usuário **Just-In-Time**


## <a name="adding-officespace-software-from-the-gallery"></a>Adicionando o OfficeSpace Software por meio da galeria

Para configurar a integração do OfficeSpace Software no Azure AD, é necessário adicionar o OfficeSpace Software por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **OfficeSpace Software** na caixa de pesquisa.
1. Selecione **OfficeSpace Software** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.


## <a name="configure-and-test-azure-ad-single-sign-on-for-officespace-software"></a>Configurar e testar o logon único do Azure AD para o OfficeSpace Software

Configure e teste o SSO do Azure AD com o OfficeSpace Software usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no OfficeSpace Software.

Para configurar e testar o SSO do Azure AD com o OfficeSpace Software, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do OfficeSpace Software](#configure-officespace-software-sso)** – para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar usuário de teste do OfficeSpace Software](#create-officespace-software-test-user)** – para ter um equivalente de B.Fernandes no OfficeSpace Software que esteja vinculado à declaração do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **OfficeSpace Software**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao cliente do OfficeSpace Software](mailto:support@officespacesoftware.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo OfficeSpace Software espera declarações SAML em um formato específico, que exige a inclusão de mapeamentos de atributos personalizados na configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão, em que **nameidentifier** é mapeado com **user.userprincipalname**. O aplicativo OfficeSpace Software espera que o **nameidentifier** seja mapeado com **user.mail**, portanto, é necessário editar o mapeamento de atributos, clicando no ícone **Editar** e alterar o mapeamento de atributos.

    ![image](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo OfficeSpace Software espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados a seguir. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seu requisito.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | email | user.mail |
    | name | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie o **Valor da Impressão Digital** e salve-o em seu computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar OfficeSpace Software**, copie as URLs apropriadas conforme suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao OfficeSpace Software.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **OfficeSpace Software**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

### <a name="configure-officespace-software-sso"></a>Configurar o SSO do OfficeSpace software

1. Em outra janela do navegador da Web, entre no locatário do OfficeSpace Software como administrador.

2. Acesse **Configurações** e clique em **Conectores**.

    ![Captura de tela que mostra a lista suspensa "Configurações" com a opção "Conectores" selecionada.](./media/officespace-tutorial/tutorial_officespace_002.png)

3. Clique em **Autenticação SAML**.

    ![Captura de tela que mostra a seção "Autenticação" com ação "Autenticação SAML" selecionada.](./media/officespace-tutorial/tutorial_officespace_003.png)

4. Na seção **Autenticação SAML** , realize as seguintes etapas:

    ![Configurar o logon único no lado do aplicativo](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Na caixa de texto **URL do provedor de logoff**, cole o valor da **URL de Logoff** copiado do portal do Azure.

    b. Na caixa de texto **URL de destino do idp do cliente**, cole o valor da **URL de Logon** copiado do portal do Azure.

    c. Cole o valor da **Impressão digital** copiado do Portal do Azure na caixa de texto **Impressão digital de certificado de IdP do cliente**. 

    d. Clique em **Salvar Configurações**.

### <a name="create-officespace-software-test-user"></a>Criar usuário de teste do OfficeSpace Software

Nesta seção, um usuário chamado B.Fernandes é criado no OfficeSpace Software. O OfficeSpace Software dá suporte para provisionamento de usuário just-in-time, que é habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no OfficeSpace Software, um novo será criado após a autenticação.

> [!NOTE]
> Se precisar criar um usuário manualmente, será necessário contatar a [equipe de suporte do OfficeSpace Software](mailto:support@officespacesoftware.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do OfficeSpace Software no Painel de Acesso, você deverá entrar automaticamente no OfficeSpace Software para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o OfficeSpace software com o Azure AD](https://aad.portal.azure.com/)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NegometrixPortal Single Sign On (SSO) | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o NegometrixPortal Single Sign On (SSO).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.openlocfilehash: d972868cf9c5d67824eab781bc99a7cac5f7b313
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92507111"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-negometrixportal-single-sign-on-sso"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao NegometrixPortal Single Sign On (SSO)

Neste tutorial, você aprenderá a integrar o NegometrixPortal Single Sign On (SSO) ao Azure AD (Azure Active Directory). Ao integrar o NegometrixPortal Single Sign On (SSO) ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao NegometrixPortal Single Sign On (SSO).
* Permitir que os usuários sejam conectados automaticamente ao NegometrixPortal Single Sign On (SSO) com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para o SSO (logon único) do NegometrixPortal Single Sign On (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O NegometrixPortal Single Sign On (SSO) dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-negometrixportal-single-sign-on-sso-from-the-gallery"></a>Adicionar o NegometrixPortal Single Sign On (SSO) da galeria

Para configurar a integração do NegometrixPortal Single Sign On (SSO) ao Azure AD, você precisa adicionar o NegometrixPortal Single Sign On (SSO) da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **NegometrixPortal Single Sign On (SSO)** na caixa de pesquisa.
1. Selecione **NegometrixPortal Single Sign On (SSO)** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-negometrixportal-single-sign-on-sso"></a>Configurar e testar o logon único do Azure AD para o NegometrixPortal Single Sign On (SSO)

Configure e teste o SSO do Azure AD com o NegometrixPortal Single Sign On (SSO) usando um usuário de teste com o nome **B.Fernandes**. Para que o SSO funcione, você precisa estabelecer uma relação de vínculo entre o usuário do Azure AD e o usuário relacionado no NegometrixPortal Single Sign On (SSO).

Para configurar e testar o SSO do Azure AD com o NegometrixPortal Single Sign On (SSO), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do NegometrixPortal Single Sign On (SSO)](#configure-negometrixportal-single-sign-on-sso-sso)** – para configurar as definições de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do NegometrixPortal Single Sign On (SSO)](#create-negometrixportal-single-sign-on-sso-test-user)** – para ter um equivalente de B.Fernandes no NegometrixPortal Single Sign On (SSO) que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **NegometrixPortal Single Sign On (SSO)** , localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://portal.negometrix.com/sso/<CUSTOMURL>`

    > [!NOTE]
    > O valor não é real. Atualize o valor com a URL de Logon real. Entre em contato com a [equipe de suporte ao cliente do NegometrixPortal Single Sign On (SSO)](mailto:sander.hoek@negometrix.com) para obter o valor. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo NegometrixPortal Single Sign On (SSO) espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados à configuração dos atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo NegometrixPortal Single Sign On (SSO) espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---------------|  --------- |
    | upn | user.userprincipalname |

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, clique no botão Copiar para copiar a **URL de Metadados de Federação do Aplicativo** e salve-a no computador.

    ![O link de download do Certificado](common/copy-metadataurl.png)

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao NegometrixPortal Single Sign On (SSO).

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **NegometrixPortal Single Sign On (SSO)** .
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-negometrixportal-single-sign-on-sso-sso"></a>Configurar o SSO do NegometrixPortal Single Sign On (SSO)

Para configurar o logon único no lado do **NegometrixPortal Single Sign On (SSO)** , você precisa enviar a **URL de Metadados de Federação do Aplicativo** para a [equipe de suporte do NegometrixPortal Single Sign On (SSO)](mailto:sander.hoek@negometrix.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-negometrixportal-single-sign-on-sso-test-user"></a>Criar um usuário de teste do NegometrixPortal Single Sign On (SSO)

Nesta seção, você criará um usuário com o nome B.Fernandes no NegometrixPortal Single Sign On (SSO). Trabalhe com a [equipe de suporte do NegometrixPortal Single Sign On (SSO)](mailto:sander.hoek@negometrix.com) para adicionar os usuários na plataforma do NegometrixPortal Single Sign On (SSO). Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do NegometrixPortal Single Sign On (SSO) no Painel de Acesso, você deverá ser conectado automaticamente ao NegometrixPortal Single Sign On (SSO), para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o NegometrixPortal Single Sign On (SSO) com o Azure AD](https://aad.portal.azure.com/)
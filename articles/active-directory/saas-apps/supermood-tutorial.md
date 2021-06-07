---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Supermood | Microsoft Docs'
description: Aprenda como configurar o logon único entre o Active Directory do Azure e o Supermood.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.openlocfilehash: b1f8fb4c6586281352b6da05978c6ca4dac23ce5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92504789"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Supermood

Neste tutorial, você aprenderá a integrar o Supermood ao Azure AD (Azure Active Directory). Ao integrar o Supermood ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Supermood.
* Permitir que os usuários sejam conectados automaticamente ao Supermood com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Supermood.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Supermood é compatível com SSO iniciado por **SP e IDP**
* O Supermood dá suporte ao provisionamento de usuário **Just In Time**

## <a name="adding-supermood-from-the-gallery"></a>Adicionando Supermood da galeria

Para configurar a integração do Supermood no Azure AD, você precisa adicionar o Supermood da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Supermood** na caixa de pesquisa.
1. Selecione **Supermood** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Configurar e testar o logon único do Azure AD para o Supermood

Configure e teste o SSO do Azure AD com o Supermood usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Supermood.

Para configurar e testar o SSO do Azure AD com o Supermood, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Supermood](#configure-supermood-sso)**: para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Supermood](#create-supermood-test-user)**: para ter um equivalente de B.Fernandes no Supermood que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Supermood**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP** execute as seguintes etapas:

    a. Clique em **Definir URLs adicionais**.
    
    b. Na caixa de texto **Estado de Retransmissão**, digite uma URL: `https://supermood.co/auth/sso/saml20`

1. Clique em **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP**:

    Na caixa de texto **URL de Logon**, digite a URL: `https://supermood.co/app/#!/loginv2`

1. Clique em **Save** (Salvar).

1. O aplicativo Supermood espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Supermood espera que mais alguns atributos sejam transmitidos novamente na resposta SAML, os quais são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---------------| ------|
    | firstName | user.givenname |
    | lastName | user.surname |

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Supermood.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Supermood**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-supermood-sso"></a>Configurar o SSO do Supermood

1. Vá para o painel de administração do Supermood.co como Administrador de Segurança.

1. Clique em **Minha conta** (canto inferior esquerdo) e **Logon único (SSO)**.

    ![O único certificado](./media/supermood-tutorial/tutorial_supermood_single.png)

1. Em **suas configurações de SAML 2.0**, clique em **Adicionar uma configuração de SAML 2.0 para um domínio de e-mail**.

    ![O certificado adiciona](./media/supermood-tutorial/tutorial_supermood_add.png)

1. Em **, adicione uma configuração do SAML 2.0 para um domínio de e-mail**. seção, execute as seguintes etapas:

    ![O certificado saml](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Na caixa de texto **domínio de email para este provedor de identidade**, digite seu domínio.

    b. Na caixa de texto **Usar um URL de metadados**, cole o **URL de metadados da federação de aplicativos** que você copiou do portal do Azure.

    c. Clique em **Adicionar**.

### <a name="create-supermood-test-user"></a>Criar um usuário de teste do Supermood

Nesta seção, é criado um usuário denominado Brenda Fernandes no Supermood. O Supermood dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Supermood, um novo será criado após a autenticação. Se você precisar criar um usuário manualmente, entre em contato com a [ equipe de suporte do Supermood ](mailto:hello@supermood.fr).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco Supermood no Painel de Acesso, você deve ser conectado automaticamente ao Supermood no qual configurou o logon único. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Supermood com o Azure AD](https://aad.portal.azure.com/)
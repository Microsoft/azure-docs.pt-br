---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao 4me | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e 4me.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 241450b9a6e97953205aca5de2a7cfd30a08d254
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92319113"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao 4me

Neste tutorial, você aprenderá a integrar o 4me ao Azure AD (Azure Active Directory). Ao integrar o 4me ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao 4me.
* Permitir que os usuários sejam conectados automaticamente ao 4me com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do 4me.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O 4me é compatível com SSO iniciado por **SP**
* O 4me é compatível com o provisionamento de usuário **Just-In-Time**

## <a name="adding-4me-from-the-gallery"></a>Adição do 4me da galeria

Para configurar a integração de 4me ao Microsoft Azure Active Directory, você precisa adicionar 4me por meio da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **4me** na caixa de pesquisa.
1. Selecione **4me** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-4me"></a>Configurar e testar o logon único do Azure AD para o 4me

Configure e teste o SSO do Azure AD com o 4me usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do 4me.

Para configurar e testar o SSO do Azure AD com o 4me, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do 4me](#configure-4me-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do 4me](#create-4me-test-user)** – para ter um equivalente de B.Fernandes no 4me que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **4me**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de Logon**, digite uma URL usando o seguinte padrão:

    | Ambiente| URL|
    |---|---|
    | PRODUÇÃO | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. Na caixa de texto **Identificador (ID da Entidade)** , digite uma URL usando o seguinte padrão:

    | Ambiente| URL|
    |---|---|
    | PRODUÇÃO | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com a URL de Entrada e o Identificador reais. Contate a [equipe de suporte ao Cliente do 4me](mailto:support@4me.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. O aplicativo 4me espera as declarações SAML em um formato específico, o que exige a adição de mapeamentos de atributo personalizado à configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo 4me espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Na seção **Certificado de Autenticação SAML**, clique no botão **Editar** para abrir a caixa de diálogo **Certificado de Autenticação SAML**.

    ![Editar o Certificado de Autenticação SAML](common/edit-certificate.png)

1. Na seção **Certificado de Autenticação SAML**, copie a **IMPRESSÃO DIGITAL** e salve-a no computador.

    ![Copiar o valor da Impressão Digital](common/copy-thumbprint.png)

1. Na seção **Configurar o 4me**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao 4me.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, escolha **4me**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-4me-sso"></a>Configurar o SSO do 4me

1. Em outra janela do navegador da Web, entre no 4me como administrador.

1. No canto superior esquerdo, clique no logo **Configurações** e, na barra do lado esquerdo, clique em **Logon único**.

    ![Configurações do 4me](./media/4me-tutorial/tutorial_4me_settings.png)

1. Na página **Logon Único**, execute as seguintes etapas:

    ![4me singleasignon](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. Selecione a opção **Habilitado**.

    b. Na caixa de texto **URL de logoff remoto**, cole o valor da **URL de logoff** que você copiou do portal do Azure.

    c. Na seção **SAML**, na caixa de texto **URL de SSO do SAML**, cole o valor da **URL de Logon** que você copiou do portal do Azure.

    d. Na caixa de texto **Impressão digital do certificado** caixa de texto, cole o valor **IMPRESSÃO DIGITAL** separado por dois-pontos na ordem de duplets (AA:BB:CC:DD:EE:FF:GG:HH:II), que você copiou do portal do Azure.

    e. Clique em **Save** (Salvar).

### <a name="create-4me-test-user"></a>Criar usuário de teste do 4me

Nesta seção, é criado um usuário chamado Brenda Fernandes no 4me. O 4me é compatível com o provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no 4me, será criado um novo usuário após a autenticação.

> [!Note]
> Se você precisar criar um usuário manualmente, contate a [equipe de suporte do 4me](mailto:support@4me.com).

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do 4me no Painel de Acesso, você deverá ser conectado automaticamente ao 4me, para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o 4me com o Azure AD](https://aad.portal.azure.com/)
---
title: 'Tutorial: Integração do Azure Active Directory com o EY GlobalOne | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o EY GlobalOne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: a248718d12abf90abd80c9210b994e7d74ab111b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92448683"
---
# <a name="tutorial-integrate-ey-globalone-with-azure-active-directory"></a>Tutorial: Integrar o EY GlobalOne ao Azure Active Directory

Neste tutorial, você aprenderá a integrar o EY GlobalOne ao Azure AD (Azure Active Directory). Ao integrar o EY GlobalOne ao Azure AD, é possível:

* Controlar quem tem acesso ao EY GlobalOne no Azure AD.
* Permitir que seus usuários entrem automaticamente no EY GlobalOne com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do EY GlobalOne.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.
* O EY GlobalOne é compatível com SSO iniciado por **SP e IDP** 
* O EY GlobalOne dá suporte ao provisionamento de usuário **Just-In-Time**.
* Depois de configurar o EY GlobalOne, será possível impor o controle de sessão, que fornece proteção contra exfiltração e infiltração dos dados confidenciais da sua organização em tempo real. O controle da sessão é estendido do acesso condicional. [Saiba como impor o controle de sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ey-globalone-from-the-gallery"></a>Adicionando o EY GlobalOne da galeria

Para configurar a integração do EY GlobalOne ao Azure AD, você precisará adicionar o EY GlobalOne por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **EY GlobalOne** na caixa de pesquisa.
1. Selecione **EY GlobalOne** no painel de resultados e adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-sso-for-ey-globalone"></a>Configurar e testar o SSO do Azure AD para EU GlobalOne

Configure e teste o SSO do Azure AD no EY GlobalOne com um usuário de teste chamado **B. Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no EY GlobalOne.

Para configurar e testar o SSO do Azure AD no EY GlobalOne, conclua os seguintes blocos de construção:

1. **[Configure o SSO do Azure AD](#configure-azure-ad-sso)** para permitir que os usuários usem esse recurso.
    * **[Crie um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com Brenda Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** para permitir que Brenda Fernandes use o logon único do Azure AD.
1. **[Configure o EY GlobalOne](#configure-ey-globalone)** para definir as configurações de SSO no lado do aplicativo.
    * **[Crie um usuário de teste do EY GlobalOne](#create-ey-globalone-test-user)** para ter um equivalente de B. Fernandes no EY GlobalOne que esteja vinculado à declaração do usuário no Azure AD.
1. **[Teste o SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **EY GlobalOne**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Escolher um método de logon único**, escolha **SAML**.
1. Na página **Configurar o Logon Único com SAML**, clique no ícone editar/de caneta da **Configuração Básica de SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica de SAML**, o aplicativo é pré-configurado e as URLs necessárias já são preenchidas previamente com o Azure. É necessário que o usuário salve a configuração, clicando no botão **Salvar**.

1. O aplicativo EY GlobalOne espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos de token SAML. A captura de tela a seguir mostra a lista de atributos padrão. Clique no ícone **Editar** para abrir a caixa de diálogo Atributos de Usuário.

    ![Captura de tela que mostra a seção "Atributos do Usuário" com o ícone "Editar" selecionado.](common/edit-attribute.png)

1. Além do indicado acima, o aplicativo EY GlobalOne espera que mais alguns atributos sejam transmitidos novamente na resposta SAML. Na seção **Declarações de Usuário** da caixa de diálogo **Atributos de Usuário**, execute as seguintes etapas para adicionar o atributo de token SAML, conforme mostrado na tabela abaixo:

    | Nome | Atributo de Origem|
    | ---------------| --------------- |
    | Nome | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Empresa | `<YOUR COMPANY NAME>` |

    a. Clique em **Adicionar nova reivindicação** para abrir a caixa de diálogo **Gerenciar declarações de usuários**.

    ![Captura de tela que mostra a seção "Declarações do usuário" com as ações "Adicionar declaração" e "Salvar" realçadas.](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Deixe o **Namespace** em branco.

    d. Escolha Origem como **Atributo**.

    e. Na lista **Atributo de origem**, digite o valor do atributo mostrado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Save** (Salvar).

1. Na página **Configurar Logon Único com SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Raw)** e selecione **Baixar** para salvar o certificado no computador.

   ![O link de download do Certificado](common/certificateraw.png)

1. Na seção **Configurar o EY GlobalOne**, copie as URLs apropriadas de acordo com suas necessidades.

   ![Copiar URLs de configuração](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você criará um usuário de teste no portal do Azure chamado B. Fernandes.

1. No painel esquerdo do portal do Azure, escolha **Azure Active Directory**, **Usuários** e, em seguida, **Todos os usuários**.
1. Selecione **Novo usuário** na parte superior da tela.
1. Nas propriedades do **Usuário**, siga estas etapas:
   1. No campo **Nome**, insira `B. Simon`.  
   1. No campo **Nome de usuário**, insira username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Marque a caixa de seleção **Mostrar senha** e, em seguida, anote o valor exibido na caixa **Senha**.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que B. Fernandes use o logon único do Azure dando a ela acesso ao EY GlobalOne.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **EY GlobalOne**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B. Fernandes** na lista Usuários e, em seguida, clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-ey-globalone"></a>Configurar o EY GlobalOne

Para configurar o logon único no lado do **EY GlobalOne**, é preciso enviar o **Certificado (Raw)** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do EY GlobalOne](mailto:globalone.support@ey.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-ey-globalone-test-user"></a>Criar usuário de teste do EY GlobalOne

Nesta seção, uma usuária chamada Brenda Fernandes é criada no EY GlobalOne. O EY GlobalOne dá suporte ao provisionamento de usuário Just-In-Time, que está habilitado por padrão. Não há itens de ação para você nesta seção. Se o usuário ainda não existir no EY GlobalOne, será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Ao selecionar o bloco do EY GlobalOne no Painel de Acesso, você será conectado automaticamente ao EY GlobalOne para o qual configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)
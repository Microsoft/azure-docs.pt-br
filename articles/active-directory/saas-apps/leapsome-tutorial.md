---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Leapsome | Microsoft Docs'
description: Saiba como configurar o logon único entre o Active Directory do Azure e o Leapsome.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: ddc8cce7b56e0d9d4b3dc33dc1ad2d8c16582841
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458699"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Leapsome

Neste tutorial, você aprenderá a integrar o Leapsome ao Azure AD (Azure Active Directory). Com a integração do Leapsome ao Azure AD, você poderá:

* Controle no Azure AD quem tem acesso ao Leapsome.
* Permitir que os usuários sejam conectados automaticamente ao Leapsome com as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Leapsome com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Leapsome dá suporte ao SSO iniciado por **SP e IDP**

## <a name="adding-leapsome-from-the-gallery"></a>Adicionando o Leapsome da galeria

Para configurar a integração do Leapsome ao Azure AD, você precisa adicionar o Leapsome da galeria à sua lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Leapsome** na caixa de pesquisa.
1. Selecione **Leapsome** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Configurar e testar o logon único do Azure AD para o Leapsome

Configure e teste o SSO do Azure AD com o Leapsome usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Leapsome.

Para configurar e testar o SSO do Azure AD com o Leapsome, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Leapsome](#configure-leapsome-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Leapsome](#create-leapsome-test-user)** – para ter um equivalente de B.Fernandes no Leapsome vinculado à representação do usuário no Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Leapsome**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, digite uma URL: `https://www.leapsome.com`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite um URL usando o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Os valores da URL de Resposta e URL de Entrada anteriores não são valores reais. Você atualizará os valores com valores reais, que é explicado no tutorial posteriormente.

1. O aplicativo Leapsome espera as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de atributos do token SAML. A captura de tela a seguir mostra a lista de atributos padrão.

    ![image](common/default-attributes.png)

1. Além do indicado acima, o aplicativo Leapsome espera que mais alguns atributos sejam passados novamente na resposta SAML, que são mostrados abaixo. Esses atributos também são pré-populados, mas você pode examiná-los de acordo com seus requisitos.

    | Nome | Atributo de Origem | Namespace |
    | ---------------| --------------- | --------- |  
    | nome | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | sobrenome | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | título | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | picture | URL para a imagem do funcionário | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > O valor do atributo de imagem não é real. Atualize esse valor com a URL da imagem real. Contate a [equipe de suporte ao cliente do Leapsome](mailto:support@leapsome.com) para obter esse valor.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Leapsome**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo-lhe acesso ao Leapsome.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Leapsome**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-leapsome-sso"></a>Configurar o SSO do Leapsome

1. Em uma janela diferente do navegador da Web, faça logon no Leapsome como um administrador de segurança.

1. No canto superior direito, clique no logotipo de configurações e, em seguida, clique em **configurações do administrador**.

    ![Conjunto de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na barra de menus à esquerda, clique em **logon único (SSO)** e na **baseado no SAML-logon único (SSO)** página execute as seguintes etapas:

    ![saml de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecione **Habilitar Logon único baseado em SAML**.

    b. Copie o valor da **URL de Logon (indique aos usuários para conectar neste ponto)** e cole-o na caixa de texto **URL de Logon** na seção **Configuração SAML Básica** no portal do Azure.

    c. Copie o valor **URL de resposta (recebe a resposta do seu provedor de identidade)** e cole-o na caixa de texto **URL de resposta** na seção **Configuração SAML Básica** no portal do Azure.

    d. Na caixa de texto **URL de Logon SSO (fornecida pelo provedor de identidade)**, cole o valor da **URL de Logon** copiado do portal do Azure.

    e. Copie o certificado que você baixou do portal do Azure sem comentários `--BEGIN CERTIFICATE and END CERTIFICATE--` e cole-o na caixa de texto **Certificado (fornecido pelo provedor de identidade)**.

    f. Clique em **ATUALIZAR CONFIGURAÇÕES SSO**.

### <a name="create-leapsome-test-user"></a>Criar usuário de teste Leapsome

Nesta seção, você criará uma usuária chamada Brenda Fernandes no Leapsome. Trabalhe com a [equipe de suporte do Leapsome](mailto:support@leapsome.com) para adicionar os usuários ou o domínio que precisam estar na lista de permissões à plataforma Leapsome. Se o domínio for adicionado pela equipe, os usuários serão automaticamente provisionados à plataforma Leapsome. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar na peça do Leapsome no Painel de Acesso, você deverá ser conectado automaticamente ao Leapsome no qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Leapsome com o Azure AD](https://aad.portal.azure.com/)
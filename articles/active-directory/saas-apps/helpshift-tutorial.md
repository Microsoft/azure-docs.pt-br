---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Helpshift | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Helpshift.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.openlocfilehash: b20d47962bf5467b9a5d69327e7fc152215edaa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92445018"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Helpshift

Neste tutorial, você aprenderá como integrar o Helpshift ao Azure Active Directory (Azure AD). Ao integrar o Helpshift ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Helpshift.
* Permitir que os usuários sejam conectados automaticamente ao Helpshift com suas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Helpshift com SSO (logon único) habilitado.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Helpshift é compatível com SSO iniciado por **SP e IDP**

## <a name="adding-helpshift-from-the-gallery"></a>Como adicionar o Helpshift da galeria

Para configurar a integração do Helpshift ao Azure AD, será necessário adicionar o Helpshift da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Helpshift** na caixa de pesquisa.
1. Escolha **Helpshift** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Configurar e testar logon único do Azure AD para o Helpshift

Configure e teste o SSO do Azure AD com o Helpshift usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Helpshift.

Para configurar e testar o SSO do Azure AD com o Helpshift, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Helpshift](#configure-helpshift-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Helpshift](#create-helpshift-test-user)** – para ter um equivalente de B.Fernandes no Helpshift que esteja vinculado à representação de declaração do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Helpshift**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. No **identificador** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/`

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Clique em **Definir URLs adicionais** e execute as seguintes etapas caso deseje configurar o aplicativo no modo iniciado por **SP**:

    d. Na caixa de texto **URL de Entrada** digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. Na caixa de texto **Estado de Retransmissão**, digite uma URL usando o seguinte padrão: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de Resposta, a URL de Logon e o Estado de retransmissão reais. Contate a [equipe de suporte ao cliente do Helpshift](mailto:support@helpshift.com) para obter esses valores. Você também pode consultar os padrões exibidos na seção **Configuração Básica de SAML** no portal do Azure.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **Certificado (Base64)** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/certificatebase64.png)

1. Na seção **Configurar o Helpshift**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure concedendo a ela acesso ao Helpshift.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Helpshift**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-helpshift-sso"></a>Configurar o SSO do Helpshift

1. Em um navegador da Web diferente, entre no aplicativo Helpshift como administrador.

1. Abra o **Painel** do Helpshifte clique no ícone **Configurações**.

    ![Captura de tela que mostra o ícone de Configurações do Helpshift.](./media/helpshift-tutorial/configuration01.png)

1. Clique na guia **Integrações** e execute as seguintes etapas:

    ![Captura de tela que mostra a guia Integração em que você pode executar as etapas descritas.](./media/helpshift-tutorial/configuration02.png)

    a. Ative o **Logon único (SAML – SSO)** .

    b. Selecione **IDP (provedor de identidade)** como **Azure Active Directory**.

    c. Na caixa de texto **URL do Ponto de Extremidade SAML 2.0**, cole o valor do **URL de Logon** que você copiou do portal do Azure.

    d. Abra o arquivo **Certificado (Base64)** baixado no Bloco de Notas, copie o conteúdo do arquivo (sem usar as linhas '– –BEGIN CERTIFICATE—–‘ and ‘—–END CERTIFICATE—–') e cole-o na caixa de texto **Certificado X.509**.

    e. Na caixa de texto **Issuer URL**, cole o valor **Identificador do Microsoft Azure AD** que você copiou do portal do Azure.

    f. Clique em **APLICAR ALTERAÇÕES**.

### <a name="create-helpshift-test-user"></a>Criar usuário de teste do Helpshift

Nesta seção, você criará um usuário com o nome B.Fernandes no Helpshift. Trabalhe com a [equipe de suporte ao cliente do Helpshift](mailto:support@helpshift.com) para adicionar os usuários à plataforma do Helpshift. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Helpshift no Painel de Acesso, você deverá ser conectado automaticamente ao Helpshift, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Helpshift com o Azure AD](https://aad.portal.azure.com/)
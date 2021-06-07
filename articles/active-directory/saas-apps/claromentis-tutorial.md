---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Claromentis | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Claromentis.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.openlocfilehash: 92b068ee9b8aaf4c462002354bbb6490f4888a80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455894"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Claromentis

Neste tutorial, você aprenderá a integrar o Claromentis ao Azure AD (Azure Active Directory). Quando integrar o Claromentis ao Azure AD, você poderá:

* Controlar no Azure AD quem tem acesso ao Claromentis.
* Permitir que seus usuários se conectem automaticamente ao Claromentis usando as contas do Azure AD deles.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Uma assinatura do Claromentis habilitada para SSO (logon único).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Claromentis é compatível com o SSO iniciado por **SP e IDP**
* O Claromentis é compatível com o provisionamento de usuário **Just In Time**

## <a name="adding-claromentis-from-the-gallery"></a>Adicionar o Claromentis da galeria

Para configurar a integração do Claromentis com o Azure AD, você precisa adicionar o Claromentis da galeria à sua lista de aplicativos de SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar da galeria**, digite **Claromentis** na caixa de pesquisa.
1. Selecione **Claromentis** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Configurar e testar o logon único do Azure AD para o Claromentis

Configure e teste o SSO do Azure AD com o Claromentis usando uma usuária de teste chamada **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Claromentis.

Para configurar e testar o SSO do Azure AD com o Claromentis, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    * **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    * **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Claromentis](#configure-claromentis-sso)** – para definir as configurações de logon único no lado do aplicativo.
    * **[Criar um usuário de teste do Claromentis](#create-claromentis-test-user)** – para ter um equivalente de B.Fernandes no Claromentis que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Claromentis**, localize a seção **Gerenciar** e selecione **logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, caso deseje configurar o aplicativo no modo iniciado por **IDP**, digite os valores dos seguintes campos:

    a. Na caixa de texto **Identificador**, insira o valor do identificador de acordo com o requisito da sua organização.

    b. No **URL de resposta** caixa de texto, digite uma URL usando o seguinte padrão: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Clique em **Definir URLs adicionais** e execute o passo seguinte se quiser configurar a aplicação no modo **SP** iniciado:

    Na caixa de texto **URL de logon**, digite uma URL usando o seguinte padrão:

    ```https
    https://<customer_site_url>/login
    https://<customer_site_url>/login?no_auto=0
    ```

    > [!NOTE]
    > Esses valores não são reais. Atualize esses valores com o Identificador, a URL de resposta e a URL de logon reais, explicado mais adiante no tutorial.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Claromentis**, copie as URLs apropriadas conforme suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo a ela acesso ao Claromentis.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Claromentis**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-claromentis-sso"></a>Configurar o SSO do Claromentis

1. Em outra janela do navegador, entre no site do Claromentis como administrador.

1. Clique no **ícone de aplicativos** e selecione **Administrador**.

    ![Captura de tela que mostra o site do Claromentis com Administrador selecionado.](./media/claromentis-tutorial/config1.png)

1. Selecione a guia **Manipulador de Logon Personalizado**.

    ![Captura de tela que mostra a página Administração com o Manipulador de Logon Personalizado selecionado.](./media/claromentis-tutorial/config2.png)

1. Selecione **Configuração do SAML**.

    ![Captura de tela que mostra a página de configuração para SAML.](./media/claromentis-tutorial/config3.png)

1. Na guia **Configuração do SAML**, role para baixo até a seção **Configuração** e execute estas etapas:

    ![Captura de tela que mostra a seção Configuração da página em que você pode inserir as informações descritas nesta etapa.](./media/claromentis-tutorial/config4.png)

    a. Na caixa de texto **Nome do Contato Técnico**, insira o nome da pessoa de contato técnico.

    b. Na caixa de texto **Email do Contato Técnico**, insira o Endereço de email da pessoa de contato técnica.

    c. Forneça a senha na caixa de texto **Senha do Administrador de Autenticação**.

1. Role para baixo até **Fontes de Autenticação** e execute as etapas a seguir:

    ![Captura de tela que mostra a seção Fontes de Autenticação em que você pode inserir as informações descritas nesta etapa.](./media/claromentis-tutorial/config5.png)

    a. Na caixa de texto **IDP**, insira o valor do **Identificador do Azure AD** copiado do portal do Azure.

    b. Na caixa de texto **ID da Entidade**, insira a URL da ID da Entidade.

    c. Faça upload do arquivo **XML de Metadados de Federação**, que você baixou do portal do Azure.

    d. Clique em **Save** (Salvar).

1. Você observará que todas as URLs foram preenchidas na seção **Provedor de Identidade** na seção **Configuração do SAML**.

    ![Captura de tela que mostra a página Provedor de Identidade preenchida com URLs.](./media/claromentis-tutorial/config6.png)

    a. Copie **Identificador (ID da Entidade)** , cole esse valor na caixa de texto **Identificador** na seção **Configuração Básica de SAML** no portal do Azure.

    b. Copie a **URL de Reposta**, cole esse valor na caixa de texto **URL de Resposta** na seção **Configuração Básica de SAML** no portal do Azure.

    c. Copie a **URL de Logon**, cole esse valor na caixa de texto **URL de Logon** na seção **Configuração Básica de SAML** no portal do Azure.

### <a name="create-claromentis-test-user"></a>Criar usuário de teste do Claromentis

Nesta seção, uma usuária chamada B.Fernandes é criada no Claromentis. O Claromentis é compatível com o provisionamento de usuário Just-In-Time, habilitado por padrão. Não há itens de ação para você nesta seção. Se um usuário ainda não existir no Claromentis, um será criado após a autenticação.

## <a name="test-sso"></a>Testar o SSO

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Claromentis no Painel de Acesso, você deverá ser conectado automaticamente ao Claromentis, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Claromentis com o Azure AD](https://aad.portal.azure.com/)
---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Coralogix | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Coralogix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.openlocfilehash: e676268abb9e1a4a597ef431ea6ba6593411711e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455214"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coralogix"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Coralogix

Neste tutorial, você aprenderá a integrar o Coralogix ao Azure AD (Azure Active Directory). Ao integrar o Coralogix ao Azure AD, você pode:

* Controlar no Azure AD quem tem acesso ao Coralogix.
* Permitir que os usuários sejam conectados automaticamente ao Coralogix com as respectivas contas do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, confira [O que é o acesso de aplicativos e o logon único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará dos seguintes itens:

* Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).
* Assinatura habilitada para SSO (logon único) do Coralogix.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configurará e testará o SSO do Azure AD em um ambiente de teste.

* O Coralogix dá suporte ao SSO iniciado por **SP**

> [!NOTE]
> O identificador desse aplicativo é um valor de cadeia de caracteres fixo; portanto apenas uma instância pode ser configurada em um locatário.

## <a name="adding-coralogix-from-the-gallery"></a>Adicionando o Coralogix por meio da galeria

Para configurar a integração do Coralogix ao Azure AD, você precisará adicionar o Coralogix por meio da galeria à lista de aplicativos SaaS gerenciados.

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, escolha o serviço **Azure Active Directory**.
1. Navegue até **Aplicativos Empresariais** e, em seguida, escolha **Todos os Aplicativos**.
1. Para adicionar um novo aplicativo, escolha **Novo aplicativo**.
1. Na seção **Adicionar por meio da galeria**, digite **Coralogix** na caixa de pesquisa.
1. Selecione **Coralogix** no painel de resultados e, em seguida, adicione o aplicativo. Aguarde alguns segundos enquanto o aplicativo é adicionado ao seu locatário.

## <a name="configure-and-test-azure-ad-single-sign-on-for-coralogix"></a>Configurar e testar o logon único do Azure AD para o Coralogix

Configure e teste o SSO do Azure AD com o Coralogix usando um usuário de teste chamado **B.Fernandes**. Para que o SSO funcione, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado no Coralogix.

Para configurar e testar o SSO do Azure AD com o Coralogix, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)** – para permitir que os usuários usem esse recurso.
    1. **[Criar um usuário de teste do Azure AD](#create-an-azure-ad-test-user)** para testar o logon único do Azure AD com B.Fernandes.
    1. **[Atribuir o usuário de teste do Azure AD](#assign-the-azure-ad-test-user)** – para permitir que B.Fernandes use o logon único do Azure AD.
1. **[Configurar o SSO do Coralogix](#configure-coralogix-sso)** : para definir as configurações de logon único no lado do aplicativo.
    1. **[Criar um usuário de teste do Coralogix](#create-coralogix-test-user)** : para ter um equivalente de B.Fernandes no Coralogix que esteja vinculado à representação de usuário do Azure AD.
1. **[Testar o SSO](#test-sso)** – para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estas etapas para habilitar o SSO do Azure AD no portal do Azure.

1. No [portal do Azure](https://portal.azure.com/), na página de integração de aplicativos do **Coralogix**, localize a seção **Gerenciar** e selecione **Logon único**.
1. Na página **Selecionar um método de logon único**, escolha **SAML**.
1. Na página **Configurar o logon único com o SAML**, clique no ícone de edição/caneta da **Configuração Básica do SAML** para editar as configurações.

   ![Editar a Configuração Básica de SAML](common/edit-urls.png)

1. Na seção **Configuração Básica do SAML**, insira os valores para os seguintes campos:

    a. Na caixa **URL de Logon**, insira uma URL com o seguinte padrão: `https://<SUBDOMAIN>.coralogix.com`

    b. Na caixa de texto **Identificador (ID da Entidade)** , insira uma URL, como:
    
    `https://api.coralogix.com/saml/metadata.xml`

    ou

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > O valor da URL de logon não é real. Atualize o valor com a URL de logon real. Contate a [equipe de suporte ao cliente do Coralogix](mailto:info@coralogix.com) para obter o valor. Veja também os padrões na seção **Configuração Básica do SAML** no portal do Azure.

 1. O aplicativo Coralogix espera as declarações SAML em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção **Atributos de Usuário** na página de integração do aplicativo. Na página **Configurar Logon Único com o SAML**, selecione o botão **Editar** para abrir a caixa de diálogo **Atributos de Usuário**.

    ![Captura de tela que mostra a caixa de diálogo "Atributos de Usuário" com o botão "Editar" realçado.](common/edit-attribute.png)

1. Na seção **Declarações de Usuário**, na caixa de diálogo **Atributos de Usuário**, edite as declarações usando o ícone **Editar**. Adicione também as declarações usando **Adicionar nova declaração** para configurar o atributo do token SAML, conforme mostrado na imagem anterior. Em seguida, execute as etapas a seguir:
    
    a. Selecione o **ícone Editar** para abrir a caixa de diálogo **Gerenciar declarações de usuário**.

    ![Captura de tela que mostra a caixa de diálogo "Atributos e Declarações do Usuário" com o botão "Editar" realçado.](./media/coralogix-tutorial/tutorial_usermail.png)
    ![imagem](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Na lista **Escolher formato do identificador de nome**, selecione **Endereço de email**.

    c. Na lista **Atributo de origem**, selecione **user.mail**.

    d. Clique em **Salvar**.

1. Na página **Configurar o logon único com o SAML**, na seção **Certificado de Autenticação SAML**, localize **XML de Metadados de Federação** e selecione **Baixar** para baixar o certificado e salvá-lo no computador.

    ![O link de download do Certificado](common/metadataxml.png)

1. Na seção **Configurar o Coralogix**, copie as URLs apropriadas de acordo com suas necessidades.

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

Nesta seção, você permitirá que B.Fernandes use o logon único do Azure permitindo acesso ao Coralogix.

1. No portal do Azure, selecione **Aplicativos empresariais** e, em seguida, selecione **Todos os aplicativos**.
1. Na lista de aplicativos, selecione **Coralogix**.
1. Na página de visão geral do aplicativo, localize a seção **Gerenciar** e escolha **Usuários e grupos**.

   ![O link “Usuários e grupos”](common/users-groups-blade.png)

1. Escolha **Adicionar usuário** e, em seguida, **Usuários e grupos** na caixa de diálogo **Adicionar Atribuição**.

    ![O link Adicionar Usuário](common/add-assign-user.png)

1. Na caixa de diálogo **Usuários e grupos**, selecione **B.Fernandes** na lista Usuários e clique no botão **Selecionar** na parte inferior da tela.
1. Se você estiver esperando um valor de função na declaração SAML, na caixa de diálogo **Selecionar Função**, escolha a função apropriada para o usuário da lista e, em seguida, clique no botão **Escolher** na parte inferior da tela.
1. Na caixa de diálogo **Adicionar atribuição**, clique no botão **Atribuir**.

## <a name="configure-coralogix-sso"></a>Configurar o SSO do Coralogix

Para configurar o logon único no lado do **Coralogix**, é necessário enviar o **XML de Metadados de Federação** baixado e as URLs apropriadas copiadas do portal do Azure para a [equipe de suporte do Coralogix](mailto:info@coralogix.com). Eles definem essa configuração para ter a conexão de SSO de SAML definida corretamente em ambos os lados.

### <a name="create-coralogix-test-user"></a>Criar um usuário de teste do Coralogix

Nesta seção, você criará um usuário chamado Brenda Fernandes no Coralogix. Trabalhe com a [equipe de suporte do Coralogix](mailto:info@coralogix.com) para adicionar os usuários na plataforma do Coralogix. Os usuários devem ser criados e ativados antes de usar o logon único.

## <a name="test-sso"></a>Testar o SSO 

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco do Coralogix no Painel de Acesso, você deverá ser conectado automaticamente ao Coralogix, para o qual você configurou o SSO. Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure ](./tutorial-list.md)

- [O que é o acesso a aplicativos e logon único com o Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimentar o Coralogix com o Azure AD](https://aad.portal.azure.com/)